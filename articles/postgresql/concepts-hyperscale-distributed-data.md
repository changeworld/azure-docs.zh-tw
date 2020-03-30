---
title: 分散式資料 = 超大規模（Citus） - 用於後格雷SQL的 Azure 資料庫
description: 在用於 PostgreSQL 的 Azure 資料庫中瞭解分散式表、參考表、本地表和分片。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: ade7632dc042741a07bdb59e34e30b3fb464e0e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243649"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure 資料庫中的分散式資料，用於 PostgreSQL 和超大規模（Citus）

本文概述了 Azure 資料庫中的三種表類型，用於 PostgreSQL 和超大規模 （Citus）。
它顯示了分散式表如何存儲為分片，以及碎片放置在節點上的方式。

## <a name="table-types"></a>資料表類型

Hyperscale （Citus） 伺服器組中有三種類型的表，每種表都用於不同的用途。

### <a name="type-1-distributed-tables"></a>類型 1：分散式表

第一種類型（也是最常見的類型）是分散式表。 它們似乎是 SQL 語句的正常表，但它們跨工作節點水準分區。 這意味著表的行存儲在不同的節點上，在稱為分片的片段表中。

超大規模 （Citus） 不僅在整個群集中運行 SQL，還運行 DDL 語句。
更改分散式表的架構級聯以跨工作程式更新表的所有分片。

#### <a name="distribution-column"></a>分發列

超大規模 （Citus） 使用演算法分片將行分配給分片。 根據稱為分佈列的表列的值，確定分配。 分發表時，群集管理員必須指定此列。
做出正確的選擇對於性能和功能非常重要。

### <a name="type-2-reference-tables"></a>類型 2：參考表

參考表是一種分散式表，其整個內容集中到單個分片中。 分片將複製到每個工作執行緒上。 任何工作人員的查詢都可以在本地訪問引用資訊，而無需從另一個節點請求行的網路開銷。 引用表沒有分佈列，因為無需區分每行的單獨分片。

引用表通常很小，用於存儲與任何輔助節點上運行的查詢相關的資料。 例如枚舉值，如訂單狀態或產品類別。

### <a name="type-3-local-tables"></a>類型 3：本地表

使用超大規模 （Citus） 時，連接到的協調員節點是常規 PostgreSQL 資料庫。 您可以在協調器上創建普通表，並選擇不分片。

本地表的一個好候選是不參與聯接查詢的小型管理表。 例如，應用程式登錄和身份驗證的使用者表。

## <a name="shards"></a>碎片

上一節介紹了如何在輔助節點上將分散式表存儲為分片。 本節討論更多的技術細節。

協調`pg_dist_shard`器上的中繼資料表包含系統中每個分散式表的每個分片的行。 該行將分片 ID 與雜湊空間中的一系列整數（分片值、分片最大值）匹配。

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

如果協調器節點想要確定哪些分片包含一`github_events`行，它將對行中的分佈列的值進行雜湊。 然後，分片\'範圍的節點檢查包含雜湊值。 定義範圍，以便雜湊函數的圖像是它們不相交的聯合。

### <a name="shard-placements"></a>碎片放置

假設分片 102027 與有問題的行相關聯。 該行在其中一個輔助器中調用`github_events_102027`的表中讀取或寫入。 哪個工人？ 這完全由中繼資料表決定。 碎片映射到輔助角色稱為分片放置。

協調器節點將查詢重寫為引用特定表的`github_events_102027`片段，並在相應的輔助程式上運行這些片段。 下面是一個查詢在後臺運行的示例，以查找包含分片 ID 102027 的節點。

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
- 瞭解如何為分散式表[選擇分發列](concepts-hyperscale-choose-distribution-column.md)。
