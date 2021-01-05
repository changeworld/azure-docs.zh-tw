---
title: VMware 軟體版本
description: Azure VMware 解決方案支援的 VMware 軟體版本。
ms.topic: include
ms.date: 12/29/2020
ms.openlocfilehash: c6ba2904bab6c6f44001cafed1bd4cbdeb786373
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825073"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Azure VMware 解決方案私人雲端叢集中所使用 VMware 軟體的目前軟體版本如下：

| 軟體              |    版本   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6.7 U3    | 
| ESXi                  |    6.7 U3    | 
| vSAN                  |    6.7 U3    |
| NSX-T                 |      2.5     |


>[!NOTE]
>NSX-T 是唯一支援的 NSX 版本。

針對私人雲端中的任何新叢集，軟體版本會符合目前正在執行的專案。 針對訂用帳戶中的任何新私人雲端，會安裝軟體堆疊的最新版本。 如需詳細資訊，請參閱 [VMware 軟體版本需求](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)。

私用雲端軟體套件組合升級會將軟體保留在 VMware 的最新軟體套件組合版本的某個版本內。 私用雲端軟體版本可能與個別軟體元件的最新版本不同 (ESXi、NSX-T、vCenter、vSAN) 。 您可以針對 [私人雲端更新和升級](../concepts-upgrades.md)中所述的 Azure VMware 解決方案平臺軟體，找到一般升級原則和流程。

