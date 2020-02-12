---
title: Azure VMware 解決方案（AVS）-節點總覽
description: 瞭解 AVS 節點和概念。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 08d8fc3e6f1f2f83cf3c4fee3fdafb0bd07e336c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024920"
---
# <a name="avs-nodes-overview"></a>AVS 節點總覽

節點是 AVS 私用雲端的基礎構件。 節點為：

* 已安裝 VMware ESXi 程式管理程式的專用裸機計算主機  
* 您可以購買或保留來建立 AVS 私人雲端的計算單位
* 可在有 AVS 服務的區域中購買或保留

您可以從已購買的節點建立 AVS 私人雲端。 若要建立 AVS 私人雲端，您至少需要相同 SKU 的三個節點。 若要擴充 AVS 私人雲端，請新增其他節點。 您可以將節點新增至現有的叢集，或在 Azure 入口網站中購買節點並將其與 AVS 服務產生關聯，以建立新的叢集。 所有購買的節點都會顯示在 AVS 服務底下。 

## <a name="provisioned-nodes"></a>已布建的節點

布建的節點提供隨用隨付容量。 布建節點可協助您視需要快速調整 VMware 叢集。 您可以視需要新增節點，或刪除已布建的節點，以相應減少您的 VMware 叢集。 已布建的節點會每月計費，並向其布建所在的訂用帳戶收費。

* 如果您使用信用卡支付您的 Azure 訂用帳戶費用，則卡片會立即計費。
* 如果您是按發票計費，則費用會顯示在下一個發票上。

## <a name="vmware-solution-by-avs-nodes-sku"></a>由 AVS 節點 SKU 的 VMware 解決方案

以下是可供布建或保留的節點類型。

| SKU           | CS28-Node                 | CS36-Node                 | CS36m-Node                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| 地區        | 美國東部、美國西部            | 美國東部、美國西部            | 西歐                 |
| CPU           | 2x 2.2 GHz，28核心（56 HT） | 2x 2.3 GHz，36核心（72 HT） | 2x 2.3 GHz，36核心（72 HT） |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| 快取磁碟    | 1.6-TB NVMe                 | 3.2-TB NVMe                 | 3.2-TB NVMe                 |
| 容量磁片 | 5.625 TB 原始                | 11.25 TB 原始                | 15.36 TB 原始                |
| 儲存類型  | All Flash                   | All Flash                   | All Flash                   |

## <a name="limits"></a>限制

下列節點限制適用于 AVS 私人雲端。

| 資源 | 限制 |
|----------|-------|
| 建立 AVS 私人雲端所需的最小節點數目 | 3 |
| 在 AVS 私人雲端的叢集中的節點數目上限 | 16 |
| AVS 私人雲端中的節點數目上限 | 64 |
| 新叢集上節點的最小數目 | 3 |

## <a name="next-steps"></a>後續步驟

* 瞭解如何[購買節點](create-nodes.md)
* 瞭解[AVS 私人](cloudsimple-private-cloud.md)雲端
* 瞭解如何布建[節點](create-nodes.md)
* 瞭解[私人](cloudsimple-private-cloud.md)雲端
