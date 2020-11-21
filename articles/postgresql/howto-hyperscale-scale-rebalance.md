---
title: 重新平衡分區-超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: 將分區平均分散到伺服器以提升效能
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: b69c4fc3ff16cf30181a3529f61af7126b92950b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026381"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>重新平衡超大規模 (Citus) server 群組中的分區

若要利用新加入的節點，您必須重新平衡分散式資料表 [分區](concepts-hyperscale-distributed-data.md#shards)，這表示將某些分區從現有節點移至新節點。 先確認新的背景工作已順利完成布建。 然後開機磁碟分割 rebalancer，方法是連接到具有 psql 的叢集協調器節點並執行：

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

[Rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards)函數會重新平衡其引數中名為之資料表[共置](concepts-hyperscale-colocation.md)群組中的所有資料表。 因此，您不需要針對每個分散式資料表呼叫函數，只要在每個共置群組的代表性資料表上呼叫該函式即可。

**後續步驟**


- 深入瞭解伺服器群組 [效能選項](concepts-hyperscale-configuration-options.md)。
- 相應增加或相應放大[伺服器群組](howto-hyperscale-scale-grow.md)
- 請參閱 [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) 參考資料
