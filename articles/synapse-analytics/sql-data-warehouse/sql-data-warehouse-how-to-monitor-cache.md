---
title: 優化 Gen2 緩存
description: 了解如何使用 Azure 入口網站監視 Gen2 快取。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: bb5560164af2b573e6aaffd4e4c62bbe0dc24a51
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350415"
---
# <a name="how-to-monitor-the-gen2-cache"></a>如何監視 Gen2 快取

本文說明如何藉由判斷工作負載是否有充分利用 Gen2 快取，來監視緩慢的查詢效能並加以疑難排解。

在針對 Gen2 資料倉儲所設計的 NVMe 型 SSD 上，Gen2 儲存體架構會自動在位於其中的快取內將最常查詢的資料行存放區區段分層。 當查詢擷取位於快取中的區段時，將可獲得更好的效能。
 
## <a name="troubleshoot-using-the-azure-portal"></a>使用 Azure 入口網站進行疑難排解

您可以使用 Azure 監視器來檢視 Gen2 快取計量，以針對查詢效能進行疑難排解。 首先轉到 Azure 門戶，然後按一下**監視器**、**指標**和 **+ 選擇範圍**：

![Azure 監視器](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

使用搜索和下拉欄查找資料倉儲。 然後選擇"應用"。

![Azure 監視器](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

用於疑難排解 Gen2 快取的關鍵計量是 [快取命中百分比]**** 和 [已用快取百分比]****。 選擇 **"緩存點擊百分比**"，然後使用**添加指標**按鈕添加**緩存使用的百分比**。 

![快取計量](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>快取命中的百分比和已用快取的百分比

以下矩陣描述以快取計量值為基礎的案例：

|                                | **高快取命中百分比** | **低快取命中百分比** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **高已用快取百分比** |          實例 1           |          案例 2          |
| **低已用快取百分比**  |          案例 3           |          案例 4          |

**案例 1：** 您充分使用快取。 [排除](sql-data-warehouse-manage-monitor.md)可能減慢查詢速度的其他問題。

**案例 2：** 您目前的工作資料集不適合該快取，導致由於實體讀取次數的關係而產生低快取命中百分比。 請考慮相應增加效能層級，然後重新執行工作負載來填入快取。

**案例 3：** 查詢可能是因為與快取無關的理由而導致執行速度緩慢。 [排除](sql-data-warehouse-manage-monitor.md)可能減慢查詢速度的其他問題。 您也可以考慮[相應減少執行個體](sql-data-warehouse-manage-monitor.md)來降低快取大小，以節省成本。 

**案例 4：** 您擁有冷快取，這可能是查詢速度緩慢的理由。 工作資料集現在應該已完成快取，所以請考慮重新執行查詢。 

> [!IMPORTANT]
> 如果重新運行工作負荷後緩存命中百分比或緩存使用的百分比未更新，則工作集已駐留在記憶體中。 僅快取叢集列存儲表。

## <a name="next-steps"></a>後續步驟
如需一般查詢效能微調的詳細資訊，請參閱[監視查詢的執行](sql-data-warehouse-manage-monitor.md#monitor-query-execution)。
