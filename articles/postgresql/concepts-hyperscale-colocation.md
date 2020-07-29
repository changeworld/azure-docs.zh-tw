---
title: 資料表共置-超大規模資料庫（Citus）-適用於 PostgreSQL 的 Azure 資料庫
description: 如何將相關資訊儲存在一起，以加快查詢速度
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7e4073ec45f4c21f33d20924a9948e72f961c7f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74967332"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>適用於 PostgreSQL 的 Azure 資料庫中的資料表共置–超大規模資料庫（Citus）

共置表示將相關資訊一起儲存在相同的節點上。 當所有必要的資料都可供使用，且沒有任何網路流量時，查詢可以快速完成。 在不同節點上共置相關資料，可讓查詢在每個節點上以平行方式執行。

## <a name="data-colocation-for-hash-distributed-tables"></a>雜湊分散式資料表的資料共置

在適用於 PostgreSQL 的 Azure 資料庫–超大規模資料庫（Citus）中，如果散發資料行中值的雜湊落在分區的雜湊範圍內，則會將資料列儲存在分區中。 具有相同雜湊範圍的分區一律會放在相同的節點上。 具有相等散發資料行值的資料列一定會在資料表間的相同節點上。

![分區](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>共置的實際範例

請考慮下列可能屬於多租使用者 web 分析 SaaS 的資料表：

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

現在我們想要回答客戶面向儀表板可能發出的查詢。 範例查詢是「傳回過去一周內所有頁面的造訪次數（以租使用者六的 '/blog ' 為開頭）。」

如果我們的資料是在單一伺服器部署選項中，我們可以使用 SQL 所提供的一組豐富關聯式作業，輕鬆地表達查詢：

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

只要此查詢的[工作集](https://en.wikipedia.org/wiki/Working_set)符合記憶體，單一伺服器資料表就是適當的解決方案。 讓我們考慮使用超大規模資料庫（Citus）部署選項來調整資料模型的機會。

### <a name="distribute-tables-by-id"></a>依識別碼散發資料表

單一伺服器查詢開始會因為租使用者數目和為每個租使用者儲存的資料成長而變慢。 工作集會停止在記憶體中進行調整，而 CPU 會變成瓶頸。

在此情況下，我們可以使用超大規模資料庫（Citus）來分區多個節點之間的資料。 在我們決定分區時，第一個和最重要的選擇是散發資料行。 讓我們從使用 `event_id` 事件資料表和資料表的簡單選擇開始 `page_id` `page` ：

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

當資料散佈在不同的背景工作時，我們無法像在單一于 postgresql 節點上一樣執行聯結。 相反地，我們需要發出兩個查詢：

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

之後，這兩個步驟的結果必須由應用程式結合。

執行查詢必須查閱分散在各個節點的分區中的資料。

![效率不佳的查詢](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

在此情況下，資料散發會產生顯著的缺點：

-   查詢每個分區並執行多個查詢的額外負荷。
-   Q1 將許多資料列傳回給用戶端的額外負荷。
-   Q2 變得很大。
-   需要在多個步驟中撰寫查詢的需求，需要在應用程式中進行變更。

資料散佈在一起，因此可以平行處理查詢。 只有當查詢執行的工作量明顯大於查詢許多分區的額外負荷時，才會有好處。

### <a name="distribute-tables-by-tenant"></a>依租使用者散發資料表

在超大規模資料庫（Citus）中，具有相同散發資料行值的資料列保證會在相同的節點上。 從開始，我們可以使用來建立資料表， `tenant_id` 做為散發資料行。

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Now 超大規模資料庫（Citus）可以回答原始單一伺服器查詢，而不需要修改（Q1）：

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

由於 tenant_id 的篩選和聯結，超大規模資料庫（Citus）知道可以使用一組共置的分區（其中包含該特定租使用者的資料）來回答整個查詢。 單一于 postgresql 節點可以在單一步驟中回答查詢。

![更好的查詢](media/concepts-hyperscale-colocation/colocation-better-query.png)

在某些情況下，必須變更查詢和資料表架構，以在唯一的條件約束和聯結條件中包含租使用者識別碼。 這種變更通常是直接的。

## <a name="next-steps"></a>後續步驟

- 請參閱[多租使用者教學](tutorial-design-database-hyperscale-multi-tenant.md)課程中的租使用者資料的共存方式。
