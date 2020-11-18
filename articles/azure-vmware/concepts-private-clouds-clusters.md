---
title: 概念-私用雲端和叢集
description: 深入瞭解 Azure VMware 解決方案軟體定義的資料中心和 vSphere 叢集的主要功能。
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 0454ade503ce40de46363f65d36a64340219c0b1
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874010"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Azure VMware 解決方案私用雲端和叢集概念

Azure VMware 解決方案可在 Azure 中提供以 VMware 為基礎的私用雲端。 私人雲端包含以專用的裸機 Azure 主機建立的叢集。 它們是透過 Azure 入口網站、CLI 或 PowerShell 進行部署和管理。  私人雲端中布建的叢集包括 VMware vSphere、vCenter、vSAN 和 NSX 軟體。 Azure VMware 解決方案私用雲端硬體和軟體部署在 Azure 中完全整合並自動化。

Azure 訂用帳戶、Azure VMware 解決方案私人雲端、vSAN 叢集和主機之間有邏輯關聯性。 此圖顯示單一 Azure 訂用帳戶，其中包含代表開發和生產環境的兩個私人雲端。  在每個私人雲端中，都有兩個群集。 

本文說明所有這些概念。

![客戶訂用帳戶中兩個私用雲端的影像](./media/hosts-clusters-private-clouds-final.png)

>[!NOTE]
>由於開發環境的潛在需求較低，因此請使用較小的叢集搭配較低容量主機。 

## <a name="private-clouds"></a>私人雲端

私人雲端包含以專用的裸機 Azure 主機建立的 vSAN 叢集。 每個私用雲端都可以有多個叢集，由相同的 vCenter server 及 NSX-T 管理員來管理。 您可以在入口網站、CLI 或 PowerShell 中部署和管理私人雲端。 

如同其他資源，私用雲端會從 Azure 訂用帳戶內安裝及管理。 訂用帳戶內的私用雲端數目是可調整的。 一開始，每個訂用帳戶會有一個私用雲端的限制。

## <a name="clusters"></a>叢集
針對每個建立的私人雲端，預設會有一個 vSAN 叢集。 您可以使用 Azure 入口網站或透過 API 來新增、刪除及調整叢集。  所有叢集的預設大小為三部主機，最多可擴大至16部主機。  叢集中使用的主機必須是相同的主機類型。

試用叢集可供評估，且僅限三部主機使用。 每個私人雲端都有一個試用叢集。 您可以在評估期間由單一主機調整試用叢集。

您可以使用 vSphere 和 NSX-T 管理員來管理叢集設定或作業的大部分其他層面。 叢集中每個主機的所有本機儲存體都受到 vSAN 的控制。

## <a name="hosts"></a>主機

Azure VMware 解決方案私用雲端叢集使用超交集的裸機基礎結構節點。 下表顯示主機的 RAM、CPU 和磁片容量。 

| 主機類型              |             CPU             |   RAM (GB)   |  vSAN NVMe 快取層 (TB、原始)   |  vSAN SSD 容量層 (TB、原始)   |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| High-End (他)           |  雙 Intel 18 核心 2.3 GHz  |     576      |                3.2               |                15.20               |

用來建立或調整叢集的主機來自于隔離的主機集區。 這些主機已通過硬體測試，並已安全地刪除所有資料。 

## <a name="vmware-software-versions"></a>VMware 軟體版本

Azure VMware 解決方案私人雲端叢集中所使用 VMware 軟體的目前軟體版本如下：

| 軟體              |    版本   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6.7 U3    | 
| ESXi                  |    6.7 U3    | 
| vSAN                  |    6.7 U3    |
| NSX-T                 |      2.5     |

針對私人雲端中的任何新叢集，軟體版本會符合目前正在執行的專案。 針對訂用帳戶中的任何新私人雲端，會安裝軟體堆疊的最新版本。

您可以針對 [私人雲端更新和升級](concepts-upgrades.md)中所述的 Azure VMware 解決方案平臺軟體，找到一般升級原則和流程。

## <a name="host-maintenance-and-lifecycle-management"></a>主機維護和生命週期管理

主機維護和生命週期管理不會影響私用雲端叢集的容量或效能。  自動化主機維護的範例包括固件升級和硬體修復或更換。

Microsoft 負責管理 NSX-T 設備的生命週期，例如 NSX-T 管理員和 NSX-T Edge。 它們也負責啟動網路設定，例如建立第0層閘道，以及啟用 North-South 路由。 您必須負責 NSX-T SDN 設定。 例如，網路區段、分散式防火牆規則、第1層閘道和負載平衡器。

> [!IMPORTANT]
> 請勿修改 NSX-T Edge 或第0層閘道的設定，因為這可能會導致服務中斷。

## <a name="backup-and-restoration"></a>備份與還原

私用雲端 vCenter 和 NSX-T 設定是以每小時的備份排程為依據。  備份會保留三天。 如果您需要從備份還原，請在 Azure 入口網站中開啟 [支援要求](https://rc.portal.azure.com/#create/Microsoft.Support) ，以要求還原。

## <a name="next-steps"></a>後續步驟

下一步是學習 [網路和互連能力概念](concepts-networking.md)。

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

