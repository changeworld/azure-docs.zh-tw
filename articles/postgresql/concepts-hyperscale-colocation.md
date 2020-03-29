---
title: 表主機代管 - 超大規模（Citus） - 用於後格雷SQL的 Azure 資料庫
description: 如何將相關資訊存儲在一起以加快查詢速度
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7e4073ec45f4c21f33d20924a9948e72f961c7f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74967332"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure 資料庫中的表主機代管，用於 PostgreSQL 和超大規模（Citus）

主機代管意味著將相關資訊存儲在同一節點上。 當所有必要的資料都可用時，查詢可以進行得非常快，而沒有任何網路流量。 在不同節點上協調相關資料允許查詢在每個節點上高效並行運行。

## <a name="data-colocation-for-hash-distributed-tables"></a>雜湊分佈表的資料主機代管

在"PostgreSQL = 超大規模"（Citus）的 Azure 資料庫中，如果分佈列中值的雜湊值位於分片的雜湊範圍內，則行將存儲在分片中。 具有相同雜湊範圍的分片始終放置在同一節點上。 分佈列值相等的行始終位於表之間的同一節點上。

![碎片](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>主機代管的實際示例

請考慮以下可能是多租戶 Web 分析 SaaS 的一部分的表：

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

現在，我們要回答可能由面向客戶的儀表板發出的查詢。 依例查詢是"返回過去一周中所有頁面的訪問次數，從租戶 6 中的'/blog'開始。

如果我們的資料位於單伺服器部署選項中，我們可以輕鬆地使用 SQL 提供的豐富的關係操作集來表達我們的查詢：

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

只要此查詢[的工作集](https://en.wikipedia.org/wiki/Working_set)適合記憶體，單伺服器表就是適當的解決方案。 讓我們考慮使用超大規模 （Citus） 部署選項擴展資料模型的機會。

### <a name="distribute-tables-by-id"></a>按 ID 分發表

隨著租戶數量和為每個租戶存儲的資料增加，單伺服器查詢開始變慢。 工作集停止在記憶體中安裝，CPU 成為瓶頸。

在這種情況下，我們可以使用超大規模 （Citus） 跨多個節點對資料進行分片。 當我們決定分片時，我們需要做出的第一個也是最重要的選擇是分發列。 讓我們從對事件表和`event_id``page_id``page`表的天真選擇開始：

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

當資料分散在不同的工作人員之間時，我們無法像在單個 PostgreSQL 節點上那樣執行聯接。 相反，我們需要發出兩個查詢：

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

之後，這兩個步驟的結果需要由應用程式組合。

執行查詢必須查閱分散在節點上的分片中的資料。

![低效查詢](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

在這種情況下，資料分佈會產生重大缺陷：

-   查詢每個分片和運行多個查詢的開銷。
-   將許多行返回到用戶端的第 1 季度開銷。
-   問題 2 變大。
-   需要以多個步驟編寫查詢需要在應用程式中進行更改。

資料被分散，因此查詢可以並行化。 只有當查詢的工作量遠遠大於查詢許多分片的開銷時，才是有益的。

### <a name="distribute-tables-by-tenant"></a>按租戶分發表

在超量程 （Citus） 中，具有相同分佈列值的行保證位於同一節點上。 開始，我們可以創建表作為`tenant_id`分發列。

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

現在，超大規模 （Citus） 無需修改即可回答原始單伺服器查詢 （Q1）：

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

由於tenant_id進行篩選和聯接，Hyperscale （Citus） 知道可以使用包含該特定租戶資料的一組 coe 分片來應答整個查詢。 單個 PostgreSQL 節點可以在單個步驟中應答查詢。

![更好的查詢](media/concepts-hyperscale-colocation/colocation-better-query.png)

在某些情況下，必須更改查詢和表架構，以將租戶 ID 包含在唯一的約束和聯結條件中。 此更改通常很簡單。

## <a name="next-steps"></a>後續步驟

- 在[多租戶教程](tutorial-design-database-hyperscale-multi-tenant.md)中瞭解如何將租戶資料與位置有關。
