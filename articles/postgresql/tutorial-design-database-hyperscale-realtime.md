---
title: 教學課程：設計即時儀表板 - 超大規模 (Citus) - 適用於 PostgreSQL 的 Azure 資料庫
description: 本教學課程說明如何使用適用於 PostgreSQL 的超大規模 Azure 資料庫 (Citus) 將即時儀表板查詢平行化。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 41ac1cb856011d9730f7b49bbf85350f4dbccdbd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90895237"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus"></a>教學課程：使用適用於 PostgreSQL 的 Azure 資料庫 – Hyperscale (Citus) 設計即時分析儀表板

在本教學課程中，您可以使用適用於 PostgreSQL 的 Azure 資料庫 – 超大規模 (Citus) 了解如何：

> [!div class="checklist"]
> * 建立 Hyperscale (Citus) 伺服器群組
> * 使用 psql 公用程式建立結構描述
> * 進行跨節點的資料表分區化
> * 產生範例資料
> * 執行彙總
> * 查詢原始和彙總資料
> * 使資料過期

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>使用 psql 公用程式建立結構描述

使用 psql 連線到適用於 PostgreSQL 的 Azure 資料庫 - 超大規模 (Citus) 後，您可以完成一些基本工作。 本教學課程會引導您擷取 Web 分析中的流量資料，然後彙總資料，以便根據該資料提供即時儀表板。

我們會建立一個資料表，該資料表將取用所有未經處理的 Web 流量資料。 在 psql 終端機中執行下列命令：

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

我們也會建立一個可保存每分鐘彙總的資料表，以及一個可維護最後一個彙總位置的資料表。 也在 psql 中執行下列命令：

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

您現在可以使用此 psql 命令來查看資料表清單中新建立的資料表：

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>進行跨節點的資料表分區化

超大規模部署會根據使用者指定的資料行值，在不同的節點上儲存資料表資料列。 此「散發資料行」表示跨節點的資料分區化方式。

讓我們將散發資料行設定為 site\_id，即分區索引鍵。 在 psql 中，執行下列函式：

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

## <a name="generate-sample-data"></a>產生範例資料

我們的伺服器群組現在應已準備好擷取一些資料。 我們可以在本機從我們的 `psql` 連線執行下列命令，以持續插入資料。

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    COMMIT;
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

查詢會每一秒插入大約八個資料列。 如散發資料行 `site_id` 所引導，資料列會儲存在不同的背景工作角色節點上。

   > [!NOTE]
   > 讓資料產生查詢保持執行，並針對本教學課程中的剩餘命令，開啟第二個 psql 連線。
   >

## <a name="query"></a>查詢

超大規模裝載選項可讓多個節點平行處理查詢以加快速度。 例如，資料庫會在背景工作角色節點上計算 SUM 和 COUNT 等彙總，並將結果結合成最終的答案。

以下的查詢可計算每分鐘的 Web 要求以及一些統計資料。
嘗試在 psql 中執行該查詢並觀察結果。

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="rolling-up-data"></a>彙總資料

上述查詢在早期階段可正常運作，但其效能會隨著您的資料擴充而下降。 即使使用分散式處理，預先計算資料的速度會比重複重新計算還要快。

我們可以定期將未經處理的資料彙總到彙總資料表中，以確保儀表板保持快速。 您可以試驗彙總持續時間。 我們已使用每分鐘彙總資料表，但您可以改為將資料分成 5、15 或 60 分鐘。

為了更輕鬆地執行此彙總，我們將其放入 plpgsql 函式。 在 psql 中執行下列命令以建立 `rollup_http_request` 函式。

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

備妥我們的函式，加以執行來彙總資料：

```sql
SELECT rollup_http_request();
```

然後將資料放在預先彙總的表單中，我們即可查詢彙總資料表，以取得與稍早相同的報告。 執行下列查詢：

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>使舊資料過期

彙總讓查詢速度變快，但我們仍需要使舊資料過期，以避免不受控制的儲存體成本。 決定您要針對每種細微性保留資料多久，並使用標準查詢來刪除過期的資料。 在下列範例中，我們已決定將未經處理的資料保留一天，並將每分鐘的彙總保留一個月：

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

在生產環境中，您可將這些查詢包裝在函式中，並且在 cron 作業中每分鐘呼叫該函式。

## <a name="clean-up-resources"></a>清除資源

在前述步驟中，您在伺服器群組中建立了 Azure 資源。 如果您認為未來不需要這些資源，請刪除伺服器群組。 在您伺服器群組的 [概觀]  頁面中，按一下 [刪除]  按鈕。 當快顯頁面上出現提示時，請確認伺服器群組的名稱，然後按一下最後一個 [刪除]  按鈕。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何佈建 Hyperscale (Citus) 伺服器群組。 您已使用 psql 連線到該群組、建立結構描述，並散發資料。 您已了解如何查詢未經處理格式的資料、定期彙總該資料、查詢彙總的資料表，以及使舊資料過期。

- 了解伺服器群組[節點類型](https://aka.ms/hyperscale-concepts)
- 判斷伺服器群組的最佳[初始大小](howto-hyperscale-scaling.md#picking-initial-size)
