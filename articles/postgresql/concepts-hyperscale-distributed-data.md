---
title: 分散式資料-超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: 瞭解適用於 PostgreSQL 的 Azure 資料庫中的分散式資料表、參考資料表、本機資料表和分區。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7757fdb4953640597a805c3d74a9e1ef08ef2c07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86114494"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>適用於 PostgreSQL 的 Azure 資料庫中的分散式資料–超大規模 (Citus) 

本文概述適用於 PostgreSQL 的 Azure 資料庫-超大規模 (Citus) 中的三個數據表類型。
它會顯示如何將分散式資料表儲存為分區，以及分區在節點上的放置方式。

## <a name="table-types"></a>資料表類型

超大規模 (Citus) server 群組中有三種類型的資料表，每個都用於不同用途。

### <a name="type-1-distributed-tables"></a>類型1：分散式資料表

第一個類型和最常見的是分散式資料表。 它們看起來就像是 SQL 語句的一般資料表，但它們會在背景工作節點之間進行水準分割。 也就是說，資料表的資料列會儲存在不同的節點上，稱為分區的片段資料表中。

超大規模 (Citus) 只會在整個叢集中執行 SQL 但 DDL 語句。
變更分散式資料表的架構，以在背景工作角色間更新所有資料表的分區。

#### <a name="distribution-column"></a>散發資料行

超大規模 (Citus) 使用演算法分區化將資料列指派給分區。 指派是根據名為散發資料行之資料表資料行的值，以決定性的方式進行。 叢集系統管理員必須在散發資料表時指定此資料行。
做出正確的選擇對效能和功能而言很重要。

### <a name="type-2-reference-tables"></a>類型2：參考資料表

參考資料表是一種分散式資料表，其整個內容會集中在單一分區中。 分區會在每個背景工作上複寫。 任何背景工作的查詢都可以在本機存取參考資訊，而不需要從另一個節點要求資料列的網路額外負荷。 參考資料表沒有散發資料行，因為不需要區分個別的分區每個資料列。

參考資料表通常很小，可用來儲存與在任何背景工作節點上執行之查詢相關的資料。 例如，訂單狀態或產品類別之類的列舉值。

### <a name="type-3-local-tables"></a>類型3：本機資料表

當您使用超大規模 (Citus) 時，您所連接的協調器節點是一般于 postgresql 資料庫。 您可以在協調器上建立一般資料表，並選擇不要分區它們。

本機資料表很適合用來做為不參與聯結查詢的小型系統管理資料表。 例如，應用程式登入和驗證的使用者資料表。

## <a name="shards"></a>碎片

上一節描述如何在背景工作節點上將分散式資料表儲存為分區。 本節將討論更多技術詳細資料。

`pg_dist_shard`協調器上的中繼資料資料表針對系統中每個分散式資料表的每個分區，各包含一個資料列。 資料列符合雜湊空間中的整數範圍 (shardminvalue、shardmaxvalue) 的分區識別碼。

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

如果協調器節點想要判斷哪些分區保留資料 `github_events` 列，它會雜湊資料列中的散發資料行值。 然後，節點會檢查哪個分區的 \' 範圍包含雜湊值。 系統會定義範圍，讓雜湊函式的影像成為它們的不相鄰聯集。

### <a name="shard-placements"></a>分區放置

假設分區102027與有問題的資料列相關聯。 資料列是在其中一個背景工作中呼叫的資料表中讀取或寫入 `github_events_102027` 。 哪個工作者？ 這完全取決於中繼資料資料表。 分區與 worker 的對應稱為分區放置。

協調器節點會將查詢重寫為參考特定資料表（例如）的片段， `github_events_102027` 並在適當的背景工作上執行這些片段。 以下是在幕後執行的查詢範例，以找出持有分區識別碼102027的節點。

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

## <a name="next-steps"></a>接下來的步驟

- 瞭解如何選擇分散式資料表的 [散發資料行](concepts-hyperscale-choose-distribution-column.md) 。
