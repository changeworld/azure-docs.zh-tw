---
title: 優化您的 Gen2 快取
description: 了解如何使用 Azure 入口網站監視 Gen2 快取。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 257b33802ea95138ef8149ef9302b14ca379e7ac
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195536"
---
# <a name="how-to-monitor-the-gen2-cache"></a>如何監視 Gen2 快取

本文說明如何藉由判斷工作負載是否有充分利用 Gen2 快取，來監視緩慢的查詢效能並加以疑難排解。

在針對 Gen2 資料倉儲所設計的 NVMe 型 SSD 上，Gen2 儲存體架構會自動在位於其中的快取內將最常查詢的資料行存放區區段分層。 當查詢擷取位於快取中的區段時，將可獲得更好的效能。
 
## <a name="troubleshoot-using-the-azure-portal"></a>使用 Azure 入口網站進行疑難排解

您可以使用 Azure 監視器來檢視 Gen2 快取計量，以針對查詢效能進行疑難排解。 請先移至 Azure 入口網站，然後按一下 [**監視**]、[**計量**] 和 [ **+ 選取範圍**]：

![Azure 監視器](./media/sql-data-warehouse-cache-portal/cache-0.png)

使用 [搜尋] 和 [下拉式] 列來尋找您的資料倉儲。 然後選取 [套用]。

![Azure 監視器](./media/sql-data-warehouse-cache-portal/cache-1.png)

用於疑難排解 Gen2 快取的關鍵計量是 [快取命中百分比] 和 [已用快取百分比]。 選取 [快取**命中百分比**]，然後使用 [**新增度量**] 按鈕來新增快取**使用百分比**。 

![快取計量](./media/sql-data-warehouse-cache-portal/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>快取命中的百分比和已用快取的百分比

以下矩陣描述以快取計量值為基礎的案例：

|                                | **高快取命中百分比** | **低快取命中百分比** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **高已用快取百分比** |          實例 1           |          案例 2          |
| **低已用快取百分比**  |          案例 3           |          案例 4          |

**案例 1：** 您充分使用快取。 針對可能會減緩查詢速度的其他區域[進行疑難排解](sql-data-warehouse-manage-monitor.md)。

**案例 2：** 您目前的工作資料集不適合該快取，導致由於實體讀取次數的關係而產生低快取命中百分比。 請考慮相應增加效能層級，然後重新執行工作負載來填入快取。

**案例 3：** 查詢可能是因為與快取無關的理由而導致執行速度緩慢。 針對可能會減緩查詢速度的其他區域[進行疑難排解](sql-data-warehouse-manage-monitor.md)。 您也可以考慮[相應減少執行個體](sql-data-warehouse-manage-monitor.md)來降低快取大小，以節省成本。 

**案例 4：** 您擁有冷快取，這可能是查詢速度緩慢的理由。 工作資料集現在應該已完成快取，所以請考慮重新執行查詢。 

> [!IMPORTANT]
> 如果重新執行您的工作負載之後，快取命中百分比或快取使用百分比未更新，則您的工作集可能已經位於記憶體中。 只會快取叢集資料行存放區資料表。

## <a name="next-steps"></a>後續步驟
如需一般查詢效能微調的詳細資訊，請參閱[監視查詢的執行](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-query-execution)。
