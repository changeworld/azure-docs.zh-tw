---
title: 節點-超大規模資料庫 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: 瞭解伺服器群組中適用於 PostgreSQL 的 Azure 資料庫的節點和資料表類型。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2019
ms.openlocfilehash: a02583825f4a1ef15992aa2307e7f666d5abeaba
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136451"
---
# <a name="nodes-and-tables-in-azure-database-for-postgresql--hyperscale-citus"></a>適用於 PostgreSQL 的 Azure 資料庫中的節點和資料表–超大規模資料庫 (Citus) 

## <a name="nodes"></a>節點

超大規模資料庫 (Citus) 裝載類型可讓適用於 PostgreSQL 的 Azure 資料庫伺服器 (稱為節點，) 在「無共用」架構中彼此協調。 伺服器群組中的節點會共同保存較多的資料，並使用比在單一伺服器上可能更多的 CPU 核心。 此架構也可讓您將更多節點新增至伺服器群組來調整資料庫。

### <a name="coordinator-and-workers"></a>協調員和背景工作

每個伺服器群組都有協調器節點和多個背景工作角色。 應用程式會將其查詢傳送至協調器節點，以將其轉送至相關的背景工作並累計其結果。 應用程式無法直接連接到背景工作角色。

超大規模資料庫 (Citus) 可讓資料庫管理員*散發*資料表，並將不同的資料列儲存在不同的背景工作節點上。 分散式資料表是超大規模資料庫效能的關鍵。 無法散發資料表會將它們完全保留在協調器節點上，而且無法利用跨電腦的平行處理原則。

針對分散式資料表上的每個查詢，協調器會將其路由傳送至單一背景工作節點，或根據所需的資料是否存在於單一節點上或多個來平行處理。 協調器會透過諮詢中繼資料資料表來決定要執行的動作。 這些資料表會追蹤背景工作節點的 DNS 名稱和健康情況，以及跨節點的資料分佈。

## <a name="table-types"></a>資料表類型

 (超大規模資料庫中有三種類型的資料表) 伺服器群組，每個都以不同的方式儲存在節點上，並用於不同的用途。

### <a name="type-1-distributed-tables"></a>類型1：分散式資料表

第一種類型（最常見）是分散式資料表。 它們看起來就像是 SQL 語句的一般資料表，但是它們會在背景工作節點之間水準分割。 這表示資料表的資料列會儲存在不同的節點上，稱為分區的片段表中。

超大規模資料庫 (Citus) 不只會執行 SQL，而是整個叢集中的 DDL 語句。
變更分散式資料表的架構，以在背景工作角色之間更新所有資料表的分區。

#### <a name="distribution-column"></a>散發資料行

超大規模資料庫 (Citus) 使用演算法分區化將資料列指派給分區。 指派是根據稱為散發資料行之資料表資料行的值來做出決定性的。 散發資料表時，叢集系統管理員必須指定這個資料行。
做出正確的選擇對於效能和功能非常重要。

### <a name="type-2-reference-tables"></a>類型2：參考資料表

參考資料表是一種分散式資料表，其完整內容會集中在單一分區中。 分區會在每個背景工作上複寫。 任何工作者的查詢都可以在本機存取參考資訊，而不需要從另一個節點要求資料列的網路額外負荷。 參考資料表沒有散發資料行，因為不需要區分每個資料列的個別分區。

參考資料表通常很小，用來儲存與任何背景工作節點上執行之查詢相關的資料。 範例是列舉的值，例如訂單狀態或產品類別。

### <a name="type-3-local-tables"></a>類型3：本機資料表

當您使用超大規模資料庫 (Citus) 時，您連接的協調器節點就是一般的于 postgresql 資料庫。 您可以在協調器上建立一般資料表，並選擇不分區它們。

對於本機資料表而言，理想的候選就是不參與聯結查詢的小型系統管理資料表。 例如，應用程式登入和驗證的使用者資料表。

## <a name="shards"></a>分區

上一節說明如何在背景工作節點上將分散式資料表儲存為分區。 本節討論更多的技術詳細資料。

`pg_dist_shard`協調器上的中繼資料表會針對系統中每個分散式資料表的每個分區，各包含一個資料列。 資料列符合雜湊空間中具有整數範圍的分區識別碼 (shardminvalue，shardmaxvalue) 。

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

如果協調器節點想要判斷哪些分區包含的資料 `github_events` 列，它會雜湊資料列中散發資料行的值。 然後，節點會檢查哪個分區 \' s 範圍包含雜湊值。 系統會定義範圍，讓雜湊函式的影像為其脫離聯集。

### <a name="shard-placements"></a>分區放置

假設分區102027與有問題的資料列相關聯。 資料列會讀取或寫入其中一個背景工作角色中名為的資料表 `github_events_102027` 。 哪個背景工作角色？ 這完全取決於中繼資料資料表。 分區與背景工作角色的對應稱為分區位置。

協調器節點會將查詢重寫為參考特定資料表（例如）的片段， `github_events_102027` 並在適當的背景工作上執行這些片段。 以下是在幕後執行查詢的範例，以找出持有分區識別碼102027的節點。

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

```output
┌─────────┬───────────┬──────────┐
│ shardid │ nodename  │ nodeport │
├─────────┼───────────┼──────────┤
│  102027 │ localhost │     5433 │
└─────────┴───────────┴──────────┘
```

## <a name="next-steps"></a>後續步驟

- [判斷您的應用程式類型](concepts-hyperscale-app-type.md)，以準備進行資料模型化
