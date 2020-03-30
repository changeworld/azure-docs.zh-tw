---
title: Azure VMware 解決方案（按雲簡單 - 節點概述）
description: 瞭解雲簡單節點和概念。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65afe26a98a53b00b72a1ea2b49799db2049b727
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024920"
---
# <a name="cloudsimple-nodes-overview"></a>雲簡單節點概述

節點是私有雲的構建基塊。 節點為：

* 安裝 VMware ESXi 虛擬機器管理程式的專用裸機計算主機  
* 可以預配或保留以創建私有雲的計算單元
* 可在雲簡單服務可用的區域提供或保留

從預配節點創建私有雲。 要創建私有雲，至少需要同一 SKU 的三個節點。 要擴展私有雲，添加其他節點。  通過將節點預配到 Azure 門戶中並將節點與 CloudSimple 服務相關聯，可以將節點添加到現有群集，也可以創建新群集。  所有預配的節點在 CloudSimple 服務下可見。  

## <a name="provisioned-nodes"></a>預配節點

預配節點提供即用即付容量。 預配節點可説明您按需快速擴展 VMware 群集。 您可以根據需要添加節點或刪除預配節點以縮小 VMware 群集。 預配節點按月計費，並在預配時向訂閱收費。

* 如果使用信用卡為 Azure 訂閱付費，則該卡將立即計費。
* 如果您按發票計費，則費用將顯示在下一張發票上。

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware 解決方案（雲簡單節點 SKU）

以下類型的節點可用於預配或預留。

| SKU           | CS28 - 節點                 | CS36 - 節點                 | CS36m - 節點                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| 區域        | 美國東部、美國西部            | 美國東部、美國西部            | 西歐                 |
| CPU           | 2x2.2 GHz，28 個內核 （56 HT） | 2x2.3 GHz，36 個內核 （72 HT） | 2x2.3 GHz，36 個內核 （72 HT） |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| 緩存磁片    | 1.6 TB NVMe                 | 3.2 TB NVMe                 | 3.2 TB NVMe                 |
| 容量磁片 | 5.625 TB 原始                | 11.25 TB 原始                | 15.36 TB 原始                |
| 儲存類型  | All Flash                   | All Flash                   | All Flash                   |

## <a name="limits"></a>限制

以下節點限制適用于私有雲。

| 資源 | 限制 |
|----------|-------|
| 創建私有雲的最小節點數 | 3 |
| 私有雲群集中的最大節點數 | 16 |
| 私有雲中的最大節點數 | 64 |
| 新群集上的節點最小數量 | 3 |

## <a name="next-steps"></a>後續步驟

* 瞭解如何[預配節點](create-nodes.md)
* 瞭解[私有雲](cloudsimple-private-cloud.md)
