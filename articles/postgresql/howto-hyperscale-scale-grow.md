---
title: 調整伺服器群組-超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: 調整伺服器群組記憶體、磁片和 CPU 資源以處理增加的負載
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 59e6e73c99569b0a35c56d65c1a7ccdfcb394c0f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026415"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>調整超大規模 (Citus) 伺服器群組

適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 提供自助調整以處理增加的負載。 Azure 入口網站可讓您輕鬆地新增背景工作節點，以及增加現有節點的虛擬核心。 新增節點不會造成停機時間，甚至會將分區移至新節點 (稱為 [分區重新平衡](howto-hyperscale-scale-rebalance.md)) 會發生，而不會中斷查詢。

## <a name="add-worker-nodes"></a>新增背景工作節點

若要新增節點，請移至超大規模 (Citus) 伺服器群組中的 [ **計算 + 儲存體** ] 索引標籤。  拖曳背景 **工作節點計數** 的滑杆會變更該值。

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="資源滑杆":::

按一下 [ **儲存** ] 按鈕，讓變更的值生效。

> [!NOTE]
> 一旦增加並儲存之後，就無法使用滑杆減少背景工作節點的數目。

> [!NOTE]
> 若要利用新加入的節點，您必須重新 [平衡分散式資料表分區](howto-hyperscale-scale-rebalance.md)，這表示將某些 [分區](concepts-hyperscale-distributed-data.md#shards) 從現有節點移至新節點。

## <a name="increase-or-decrease-vcores-on-nodes"></a>增加或減少節點的虛擬核心

除了新增節點以外，您還可以增加現有節點的功能。 向上和向下調整計算容量，對於效能實驗以及對流量需求的短期或長期變更都很有用。

若要變更所有背景工作節點的虛擬核心，請調整 [**每個背景工作節點** 的設定 (]) 的 [**虛擬核心**] 滑杆。 協調器節點的虛擬核心可以獨立調整。 調整 [ **Configuration (協調器] 節點)** 下的 [**虛擬核心**] 滑杆。

## <a name="next-steps"></a>下一步

- 深入瞭解伺服器群組 [效能選項](concepts-hyperscale-configuration-options.md)。
- 重新[平衡分散式資料表分區](howto-hyperscale-scale-rebalance.md)，讓所有背景工作節點都能參與平行查詢
