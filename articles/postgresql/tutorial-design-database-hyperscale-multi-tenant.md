---
title: 教學課程：設計多租用戶資料庫 - 超大規模 (Citus) - 適用於 PostgreSQL 的 Azure 資料庫
description: 本教學課程說明如何使用適用於 PostgreSQL 的超大規模 Azure 資料庫 (Citus) 提供可調整多租用戶應用程式更強大的功能。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: ef0f542a27f378b6132d8ef19b55f386d4102d53
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90895256"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>教學課程：使用適用於 PostgreSQL 的 Azure 資料庫 – 超大規模 (Citus) 設計多租用戶資料庫

在本教學課程中，您可以使用適用於 PostgreSQL 的 Azure 資料庫 – 超大規模 (Citus) 了解如何：

> [!div class="checklist"]
> * 建立 Hyperscale (Citus) 伺服器群組
> * 使用 psql 公用程式建立結構描述
> * 進行跨節點的資料表分區化
> * 內嵌範例資料
> * 查詢租用戶資料
> * 在租用戶之間共用資料
> * 自訂每一租用戶的結構描述

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>使用 psql 公用程式建立結構描述

使用 psql 連線到適用於 PostgreSQL 的 Azure 資料庫 - 超大規模 (Citus) 後，您可以完成一些基本工作。 本教學課程會引導您建立可讓廣告商追蹤其行銷活動的 Web 應用程式。

此應用程式可供多家公司使用，因此我們將建立一個資料表來保存公司，並且為其行銷活動建立另一個資料表。 在 psql 主控台中，執行下列命令：

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

每個行銷活動都會付費以執行廣告。 完成上述程式碼後也請在 psql 中執行下列程式碼，以新增廣告的資料表：

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

最後，我們會追蹤每個廣告的點擊率和廣告曝光數的相關統計資料：

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

現在，您可以在 psql 中執行下列命令，以查看在資料表清單中新建的資料表：

```postgres
\dt
```

多租用戶應用程式只能就個別的租用戶強制執行唯一性，這正是所有主要和外部索引鍵都包含公司識別碼的原因。

## <a name="shard-tables-across-nodes"></a>進行跨節點的資料表分區化

超大規模部署會根據使用者指定的資料行值，在不同的節點上儲存資料表資料列。 這個「散發資料行」標記會標示哪個租用戶擁有哪些資料列。

我們要將散發資料行設定為 company\_id，即租用戶識別碼。 在 psql 中，執行下列函式：

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

## <a name="ingest-sample-data"></a>內嵌範例資料

現在，在 psql 外部的一般命令列中，下載範例資料集：

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

回到 psql 內，進行資料的大量載入。 請務必在您下載資料檔案的相同目錄中執行 psql。

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

這項資料現在會分散至背景工作節點。

## <a name="query-tenant-data"></a>查詢租用戶資料

當應用程式要求單一租用戶的資料時，資料庫可在單一背景工作節點上執行查詢。 單一租用戶查詢會依單一租用戶識別碼進行篩選。 例如，下列查詢會篩選 `company_id = 5` 的廣告和曝光數。 請嘗試在 psql 中執行該查詢，並查看結果。

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>在租用戶之間共用資料

到目前為止，所有資料表都已藉由 `company_id` 散發，但某些資料並非原本就特別「屬於」任何租用戶，而可以共用。 例如，範例廣告平台中的所有公司可能都會想要根據 IP 位址取得其對象的地理資訊。

請建立資料表來保存共用的地理資訊。 在 psql 中執行下列命令：

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

接著，使 `geo_ips` 成為「參考資料表」，用以儲存每個背景工作節點上的資料表複本。

```sql
SELECT create_reference_table('geo_ips');
```

使用範例資料加以載入。 請務必從您下載資料集的目錄內執行 psql 的此命令。

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

在所有節點上，聯結點擊率資料表與 geo\_ips 都有其效用。
以下聯結可用來尋找每個點擊廣告的使用者所在的位置
290. 請嘗試在 psql 中執行查詢。

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>自訂每一租用戶的結構描述

每個租用戶都可能需要儲存其他人不需要的特殊資訊。 不過，所有租用戶會共用具有相同資料庫結構描述的通用基礎結構。 其他資料的去處為何？

方法之一是使用開放式資料行類型，例如 PostgreSQL 的 JSONB。  我們的結構描述在 `clicks` 中有名為 `user_data` 的 JSONB 欄位。
一家公司 (例如公司五) 可使用資料行來追蹤使用者是否位於行動裝置上。

以下查詢可找出哪些人點擊了更多項目：行動裝置或傳統訪客。

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

我們也可以建立[部分索引](https://www.postgresql.org/docs/current/static/indexes-partial.html)，為單一公司最佳化此查詢。

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

在更廣泛的層面，我們可以建立資料行中每個索引鍵和值的 [GIN 索引](https://www.postgresql.org/docs/current/static/gin-intro.html)。

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>清除資源

在前述步驟中，您在伺服器群組中建立了 Azure 資源。 如果您認為未來不需要這些資源，請刪除伺服器群組。 在您伺服器群組的 [概觀]  頁面中，按一下 [刪除]  按鈕。 當快顯頁面上出現提示時，請確認伺服器群組的名稱，然後按一下最後一個 [刪除]  按鈕。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何佈建 Hyperscale (Citus) 伺服器群組。 您已使用 psql 連線到該群組、建立結構描述，並散發資料。 您已了解如何在租用戶內部和之間查詢資料，以及如何自訂每個租用戶的結構描述。

- 了解伺服器群組[節點類型](https://aka.ms/hyperscale-concepts)
- 判斷伺服器群組的最佳[初始大小](howto-hyperscale-scaling.md#picking-initial-size)
