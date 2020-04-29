---
title: 依 CloudSimple 的 Azure VMware 解決方案-節點總覽
description: 深入瞭解 CloudSimple 節點和概念。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65afe26a98a53b00b72a1ea2b49799db2049b727
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024920"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple 節點總覽

節點是私用雲端的基礎構件。 節點為：

* 已安裝 VMware ESXi 程式管理程式的專用裸機計算主機  
* 您可以布建或保留以建立私人雲端的計算單位
* 可在 CloudSimple 服務可供使用的區域中布建或保留

您可以從已布建的節點建立私人雲端。 若要建立私人雲端，您至少需要相同 SKU 的三個節點。 若要擴充私人雲端，請新增其他節點。  您可以將節點新增至現有的叢集，或藉由在 Azure 入口網站中布建節點來建立新的叢集，並將它們與 CloudSimple 服務產生關聯。  所有已布建的節點都會顯示在 CloudSimple 服務底下。  

## <a name="provisioned-nodes"></a>已布建的節點

布建的節點提供隨用隨付容量。 布建節點可協助您視需要快速調整 VMware 叢集。 您可以視需要新增節點，或刪除已布建的節點，以相應減少您的 VMware 叢集。 已布建的節點會每月計費，並向其布建所在的訂用帳戶收費。

* 如果您使用信用卡支付您的 Azure 訂用帳戶費用，則卡片會立即計費。
* 如果您是按發票計費，則費用會顯示在下一個發票上。

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>依 CloudSimple 節點 SKU 的 VMware 解決方案

以下是可供布建或保留的節點類型。

| SKU           | CS28-Node                 | CS36-Node                 | CS36m-Node                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| 區域        | 美國東部、美國西部            | 美國東部、美國西部            | 西歐                 |
| CPU           | 2x 2.2 GHz，28核心（56 HT） | 2x 2.3 GHz，36核心（72 HT） | 2x 2.3 GHz，36核心（72 HT） |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| 快取磁片    | 1.6-TB NVMe                 | 3.2-TB NVMe                 | 3.2-TB NVMe                 |
| 容量磁片 | 5.625 TB 原始                | 11.25 TB 原始                | 15.36 TB 原始                |
| 儲存類型  | All Flash                   | All Flash                   | All Flash                   |

## <a name="limits"></a>限制

下列節點限制適用于私人雲端。

| 資源 | 限制 |
|----------|-------|
| 建立私人雲端的節點數目下限 | 3 |
| 私人雲端上叢集中的節點數目上限 | 16 |
| 私人雲端中的節點數目上限 | 64 |
| 新叢集上節點的最小數目 | 3 |

## <a name="next-steps"></a>後續步驟

* 瞭解如何布建[節點](create-nodes.md)
* 瞭解[私人](cloudsimple-private-cloud.md)雲端
