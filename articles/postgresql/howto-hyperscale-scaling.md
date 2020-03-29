---
title: 縮放伺服器組 - 超大規模（Citus） - 用於後格雷SQL的 Azure 資料庫
description: 調整伺服器組記憶體、磁片和 CPU 資源，以應對增加的負載
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: fa48ca287c248155a0271b5134be782d8db1c785
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063106"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>縮放超大規模（Citus）伺服器組

用於 PostgreSQL - 超大規模 （Citus） 的 Azure 資料庫提供自助服務擴展，以處理增加的負載。 Azure 門戶便於添加新輔助節點，並增加現有節點的 vCore。

## <a name="add-worker-nodes"></a>添加輔助角色節點

要添加節點，請轉到超大規模 （Citus） 伺服器組中的 **"配置**"選項卡。  拖動**輔助角色節點計數**的滑塊會更改值。

![資源滑塊](./media/howto-hyperscale-scaling/01-sliders-workers.png)

按一下"**保存**"按鈕以使更改的值生效。

> [!NOTE]
> 增加並保存後，無法使用滑塊減少輔助節點的數量。

### <a name="rebalance-shards"></a>重新平衡分片

要利用新添加的節點，必須重新平衡分散式表[分片](concepts-hyperscale-distributed-data.md#shards)，這意味著將一些分片從現有節點移動到新節點。 首先驗證新工作人員是否已成功完成預配。 然後啟動分片重新平衡器，通過連接到具有 psql 的群集協調器節點並運行：

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

函數會重新平衡其引數中名為之資料表的[共置群組](concepts-hyperscale-colocation.md)中的所有資料表。`rebalance_table_shards` 因此，您不必為每個分散式表調用函數，只需在每個主機代管組中調用它。

## <a name="increase-or-decrease-vcores-on-nodes"></a>增加或減少節點上的 vCore

> [!NOTE]
> 此功能目前為預覽狀態。 要請求更改伺服器組中的節點的 vCore，[請與 Azure 支援 。](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

除了添加新節點外，還可以增加現有節點的功能。 上下調整計算容量可用於性能實驗以及流量需求的短期或長期變化。

要更改所有輔助節點的 vCore，請調整配置下的**vCores**滑塊 **（每個輔助節點）。** 協調節點的 vCore 可以獨立調整。 按一下**協調器節點**下的 **"更改配置**"連結。 將顯示一個對話方塊，其中包含協調器的 vCore 和存儲容量的滑塊。 根據需要更改滑塊，然後選擇 **"確定**"。

## <a name="next-steps"></a>後續步驟

瞭解有關伺服器組[性能選項](concepts-hyperscale-configuration-options.md)的詳細資訊。
