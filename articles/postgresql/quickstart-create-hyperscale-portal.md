---
title: 建立分散式資料表 - 超大規模 (Citus) - 適用於 PostgreSQL 的 Azure 資料庫
description: 在適用於 PostgreSQL 的 Azure 資料庫 - 超大規模 (Citus) 上建立及查詢分散式資料表的快速入門。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: 4ff80330ab6244bc9d108b7f5a1d4e4e0dbd4feb
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387399"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-in-the-azure-portal"></a>快速入門：在 Azure 入口網站中建立適用於 PostgreSQL 的 Azure 資料庫 - 超大規模 (Citus)

Azure Database for PostgreSQL 是一種受控服務，您用來在雲端執行、管理及調整高可用性的 PostgreSQL 資料庫。 此快速入門示範如何使用 Azure 入口網站，來建立適用於 PostgreSQL 的 Azure 資料庫 - 超大規模 (Citus) 伺服器群組。 您將探索分散式資料：進行跨節點的資料表分區化、擷取範例資料，以及在多個節點上執行查詢。

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>建立和散發資料表

使用 psql 連線到 Hyperscale 協調器節點後，您可以完成一些基本工作。

Hyperscale 伺服器內有三種類型的資料表：

- 分散式或分區化資料表 (已分散有助於調整效能與平行處理)
- 參考資料表 (維護多份)
- 本機資料表 (通常用於內部管理資料表)

在本快速入門中，我們主要著重於分散式資料表並且熟悉它們。

我們即將使用的資料模型十分簡單：來自 GitHub 的使用者和事件資料。 事件包括分支建立、組織相關 git 認可等等。

在您透過 psql 連線後，我們會建立我們的資料表。 在 psql 主控台中執行：

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

`github_events` 的 `payload` 欄位具有 JSONB 資料類型。 JSONB 是 Postgres 中二進位格式的 JSON 資料類型。 資料類型可讓您在單一資料行中輕鬆地儲存更有彈性的結構描述。

Postgres 可以建立此類型的 `GIN` 索引，該索引將會檢索每個索引鍵和其值。 使用索引，即可利用各種條件快速又輕鬆地查詢承載。 我們會繼續建立幾個索引，再載入我們的資料。 在 psql 中：

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

接著，我們會採用協調器節點上的 Postgres 資料表，並告知 Hyperscale 跨 背景工作角色進行這些資料表的分區化。 為了這麼做，我們會針對每個資料表執行查詢，並指定要在其中進行分區化的索引鍵。 在目前的範例中，我們會在 `user_id` 上將 events 和 users 資料表分區化：

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

我們準備好載入資料。 同樣在 psql 中，使用 curl 和 -O 選項來下載檔案：

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

接下來，將資料從檔案載入至分散式資料表：

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>執行查詢

現在輪到有趣的部分，實際執行一些查詢。 我們會從簡單的 `count (*)` 著手，以查看我們所載入的資料量：

```sql
SELECT count(*) from github_events;
```

運作良好。 我們很快會回到這種彙總，但現在讓我們看看其他幾個查詢。 JSONB `payload` 資料行內有不少資料，但資料會因事件類型而有所不同。 `PushEvent` 事件包含的大小包括推送的相異認可數目。 我們可使用它來尋找每小時的認可總數：

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

到目前為止，查詢已獨家參與 github\_events，但我們可以結合此資訊與 github\_users。 因為我們已將相同識別碼 (`user_id`) 上的 users 和 events 分區化，所以使用者識別碼相符的這兩個資料表上的資料列將[共置](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation)於相同的資料庫節點上，並可輕鬆地聯結。

如果我們在 `user_id` 上聯結，Hyperscale 可以將聯結執行向下推送到分區，進而以平行方式在背景工作角色節點上執行。 例如，讓我們找出建立最多存放庫的使用者：

```sql
SELECT gu.login, count(*)
  FROM github_events ge
  JOIN github_users gu
    ON ge.user_id = gu.user_id
 WHERE ge.event_type = 'CreateEvent'
   AND ge.payload @> '{"ref_type": "repository"}'
 GROUP BY gu.login
 ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>清除資源

在前述步驟中，您在伺服器群組中建立了 Azure 資源。 如果您認為未來不需要這些資源，請刪除伺服器群組。 在您伺服器群組的 [概觀]  頁面中，按一下 [刪除]  按鈕。 當快顯頁面上出現提示時，請確認伺服器群組的名稱，然後按一下最後一個 [刪除]  按鈕。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何佈建 Hyperscale (Citus) 伺服器群組。 您已使用 psql 連線到該群組、建立結構描述，並散發資料。

接下來，遵循教學課程以建置可擴充的多租用戶應用程式。
> [!div class="nextstepaction"]
> [設計多租用戶資料庫](https://aka.ms/hyperscale-tutorial-multi-tenant)
