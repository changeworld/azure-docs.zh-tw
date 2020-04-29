---
title: 分散式資料–超大規模資料庫（Citus）-適用於 PostgreSQL 的 Azure 資料庫
description: 瞭解適用於 PostgreSQL 的 Azure 資料庫中的分散式資料表、參考資料表、本機資料表和分區。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: ade7632dc042741a07bdb59e34e30b3fb464e0e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79243649"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>適用於 PostgreSQL 的 Azure 資料庫中的分散式資料–超大規模資料庫（Citus）

本文概述適用於 PostgreSQL 的 Azure 資料庫–超大規模資料庫（Citus）中的三個數據表類型。
它會顯示如何將分散式資料表儲存為分區，以及將分區放在節點上的方式。

## <a name="table-types"></a>資料表類型

超大規模資料庫（Citus）伺服器群組中有三種類型的資料表，分別用於不同用途。

### <a name="type-1-distributed-tables"></a>類型1：分散式資料表

第一種類型（最常見）是分散式資料表。 它們看起來就像是 SQL 語句的一般資料表，但是它們會在背景工作節點之間水準分割。 這表示資料表的資料列會儲存在不同的節點上，稱為分區的片段表中。

超大規模資料庫（Citus）不僅會在整個叢集中執行 SQL，而是 DDL 語句。
變更分散式資料表的架構，以在背景工作角色之間更新所有資料表的分區。

#### <a name="distribution-column"></a>散發資料行

超大規模資料庫（Citus）使用演算法分區化將資料列指派給分區。 指派是根據稱為散發資料行之資料表資料行的值來做出決定性的。 散發資料表時，叢集系統管理員必須指定這個資料行。
做出正確的選擇對於效能和功能非常重要。

### <a name="type-2-reference-tables"></a>類型2：參考資料表

參考資料表是一種分散式資料表，其完整內容會集中在單一分區中。 分區會在每個背景工作上複寫。 任何工作者的查詢都可以在本機存取參考資訊，而不需要從另一個節點要求資料列的網路額外負荷。 參考資料表沒有散發資料行，因為不需要區分每個資料列的個別分區。

參考資料表通常很小，用來儲存與任何背景工作節點上執行之查詢相關的資料。 範例是列舉的值，例如訂單狀態或產品類別。

### <a name="type-3-local-tables"></a>類型3：本機資料表

當您使用超大規模資料庫（Citus）時，您連接的協調器節點是一般的于 postgresql 資料庫。 您可以在協調器上建立一般資料表，並選擇不分區它們。

對於本機資料表而言，理想的候選就是不參與聯結查詢的小型系統管理資料表。 例如，應用程式登入和驗證的使用者資料表。

## <a name="shards"></a>分區

上一節說明如何在背景工作節點上將分散式資料表儲存為分區。 本節討論更多的技術詳細資料。

協調`pg_dist_shard`器上的中繼資料表會針對系統中每個分散式資料表的每個分區，各包含一個資料列。 資料列符合雜湊空間（shardminvalue，shardmaxvalue）中具有整數範圍的分區識別碼。

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

如果協調器節點想要判斷哪些分區包含的資料列`github_events`，它會雜湊資料列中散發資料行的值。 然後，節點會檢查哪個\'分區 s 範圍包含雜湊值。 系統會定義範圍，讓雜湊函式的影像為其脫離聯集。

### <a name="shard-placements"></a>分區放置

假設分區102027與有問題的資料列相關聯。 資料列會讀取或寫入其中一個背景工作`github_events_102027`角色中名為的資料表。 哪個背景工作角色？ 這完全取決於中繼資料資料表。 分區與背景工作角色的對應稱為分區位置。

協調器節點會將查詢重寫為參考特定資料表（例如`github_events_102027` ）的片段，並在適當的背景工作上執行這些片段。 以下是在幕後執行查詢的範例，以找出持有分區識別碼102027的節點。

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

    ┌─────────┬───────────┬──────────┐
    │ shardid │ nodename  │ nodeport │
    ├─────────┼───────────┼──────────┤
    │  102027 │ localhost │     5433 │
    └─────────┴───────────┴──────────┘

## <a name="next-steps"></a>後續步驟
- 瞭解如何為分散式資料表[選擇散發資料行](concepts-hyperscale-choose-distribution-column.md)。
