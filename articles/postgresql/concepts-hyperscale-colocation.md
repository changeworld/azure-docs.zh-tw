---
title: 資料表共置-超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: 如何將相關資訊儲存在一起以加快查詢速度
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 842563319e09a001fd6e85403d8aee6fb14690ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90884423"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>適用於 PostgreSQL 的 Azure 資料庫中的資料表共置–超大規模 (Citus) 

共置表示將相關的資訊一起儲存在相同的節點上。 當所有必要的資料都可供使用時，如果沒有任何網路流量，查詢就可以快速完成。 在不同的節點上共置相關資料，可讓查詢在每個節點上以平行方式有效率地執行。

## <a name="data-colocation-for-hash-distributed-tables"></a>雜湊分散式資料表的資料共置

在適用於 PostgreSQL 的 Azure 資料庫–超大規模 (Citus) 中，如果散發資料行中的值雜湊落在分區的雜湊範圍內，資料列就會儲存在分區中。 具有相同雜湊範圍的分區一律會放置在相同的節點上。 具有相等分佈資料行值的資料列一律會在資料表之間的相同節點上。

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-shards.png" alt-text="碎片":::

## <a name="a-practical-example-of-colocation"></a>共置的實際範例

請考慮下列可能是多租使用者 web 分析 SaaS 一部分的資料表：

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

現在我們想要回答客戶面向儀表板可能發出的查詢。 查詢範例是「傳回過去一周內，租使用者6中以 '/blog ' 開頭的所有頁面的造訪次數」。

如果我們的資料位於 Single-Server 部署選項，我們可以使用 SQL 提供的一組豐富關聯式作業，輕鬆地表達我們的查詢：

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

只要此查詢的 [工作集](https://en.wikipedia.org/wiki/Working_set) 符合記憶體，單一伺服器資料表就是適當的解決方案。 讓我們考慮使用超大規模 (Citus) 部署選項來調整資料模型的機會。

### <a name="distribute-tables-by-id"></a>依識別碼散發資料表

當租使用者數目和為每個租使用者儲存的資料成長時，單一伺服器查詢的啟動速度會變慢。 工作集會停止在記憶體中進行調整，而 CPU 會變成瓶頸。

在此情況下，我們可以使用超大規模 (Citus) ，在多個節點之間分區資料。 當我們決定要分區的第一個和最重要的選擇是散發資料行時，必須進行這項選擇。 讓我們從 `event_id` 針對事件資料表和資料表使用的簡單選擇開始 `page_id` `page` ：

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

當資料分散在不同的背景工作時，我們無法像我們在單一于 postgresql 節點上一樣地執行聯結。 相反地，我們需要發出兩個查詢：

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

之後，應用程式必須結合兩個步驟的結果。

執行查詢必須查閱分散在各節點間分區的資料。

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-inefficient-queries.png" alt-text="碎片":::

在此情況下，資料散發會產生顯著的缺點：

-   查詢每個分區並執行多個查詢的額外負荷。
-   Q1 將許多資料列傳回用戶端的額外負荷。
-   第2季變得很大。
-   需要在多個步驟中撰寫查詢，需要在應用程式中進行變更。

資料會分散，因此可以平行處理查詢。 只有當查詢所做的工作量大幅大於查詢許多分區的額外負荷時，才有説明。

### <a name="distribute-tables-by-tenant"></a>依租使用者散發資料表

在超大規模 (Citus) 中，具有相同散發資料行值的資料列保證會在相同的節點上。 從頭開始，我們可以使用 `tenant_id` 做為散發資料行來建立資料表。

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

現在超大規模 (Citus) 可以回答原始單一伺服器查詢，而不需要修改 (Q1) ：

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

由於 tenant_id 的篩選和聯結，超大規模 (Citus) 知道可以使用包含該特定租使用者資料的共置分區集合來回答整個查詢。 單一于 postgresql 節點可以單一步驟回答查詢。

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-better-query.png" alt-text="碎片":::

在某些情況下，必須變更查詢和資料表架構，以在 unique 條件約束和聯結條件中包含租使用者識別碼。 這項變更通常很簡單。

## <a name="next-steps"></a>後續步驟

- 查看租使用者資料如何在 [多租使用者教學](tutorial-design-database-hyperscale-multi-tenant.md)課程中共置。
