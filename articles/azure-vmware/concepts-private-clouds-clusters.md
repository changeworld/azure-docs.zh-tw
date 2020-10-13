---
title: 概念-私用雲端和叢集
description: 瞭解 VMware 在 Azure 上的 VMware 解決方案中 Azure VMware 軟體定義資料中心和 vSphere 叢集的主要功能。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 715293d9951876ff0f794f8f6b580093f89571b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316863"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Azure VMware 解決方案私用雲端和叢集概念

Azure VMware 解決方案可在 Azure 中提供以 VMware 為基礎的私用雲端。 私用雲端是由專用裸機主機的叢集所建立，並透過 Azure 入口網站部署及管理。 私人雲端中的叢集是使用 VMware vSphere、vCenter、vSAN 和 NSX 軟體布建的。 Azure VMware 解決方案私用雲端硬體和軟體部署在 Azure 中完全整合並自動化。

Azure 訂用帳戶、Azure VMware 解決方案私人雲端、vSAN 叢集和主機之間有邏輯關聯性。 在此圖中，會顯示單一 Azure 訂用帳戶中的兩個私人雲端。 私用雲端代表開發環境和生產環境，每個環境都有自己的私用雲端。 在每個私人雲端中，都有兩個群集。 為了顯示較低的開發環境潛在需求，會使用較低容量主機的較小叢集。 下列各節將說明這些概念。

![客戶訂用帳戶中兩個私用雲端的影像](./media/hosts-clusters-private-clouds-final.png)

## <a name="private-clouds"></a>私人雲端

私人雲端包含以專用的裸機 Azure 主機建立的 vSAN 叢集。 每個私用雲端都可以有多個叢集，全都由相同的 vCenter server 及 NSX-T 管理員來管理。 您可以透過入口網站、CLI 或 PowerShell 來部署和管理私人雲端。 如同其他資源，私用雲端會從 Azure 訂用帳戶內安裝及管理。

訂用帳戶內的私用雲端數目是可調整的。 一開始，每個訂用帳戶會有一個私用雲端的限制。

## <a name="clusters"></a>叢集

您將在每個私人雲端中建立至少一個 vSAN 叢集。 當您建立私人雲端時，預設會有一個叢集。 您可以使用 Azure 入口網站或透過 API，將其他叢集新增至私人雲端。 所有叢集的預設大小為三部主機，而且可以從3個主機調整為16部。 叢集中使用的主機類型必須是相同的類型。 下一節將說明主機類型。

試用叢集可供評估，且每個私人雲端只能有三部主機和單一試用叢集。 您可以在評估期間由單一主機調整試用叢集。

您可以透過入口網站或 API 來建立、刪除及調整叢集。 您仍然可以使用 vSphere 和 NSX-T 管理員來管理叢集設定或作業的大部分其他層面。 叢集中每個主機的所有本機儲存體都會受到 vSAN 的控制。

## <a name="hosts"></a>主機

Azure VMware 解決方案私用雲端叢集中使用超交集的裸機基礎結構節點。 下表提供主機的 RAM、CPU 和磁片容量。 

| 主機類型              |             CPU             |   RAM (GB)   |  vSAN NVMe 快取層 (TB、原始)   |  vSAN SSD 容量層 (TB、原始)   |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| High-End (他)           |  雙 Intel 18 核心 2.3 GHz  |     576      |                3.2               |                15.20               |

用來建立或調整叢集的主機是從隔離的主機集區取得。 這些主機已通過硬體測試，並已從 flash 磁片安全地刪除所有資料。 當您從叢集中移除主機時，系統會安全地抹除內部磁片，並將主機放入主機的隔離集區中。 當您將主機新增至叢集時，會使用隔離式集區中的清理主機。

## <a name="vmware-software-versions"></a>VMware 軟體版本

Azure VMware 解決方案私人雲端叢集中所使用 VMware 軟體的目前軟體版本如下：

| 軟體              |    版本   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6.7 U2    | 
| ESXi                  |    6.7 U2    | 
| vSAN                  |    6.7 U2    |
| NSX-T                 |      2.5     |

針對私人雲端中的任何新叢集，軟體版本會與私人雲端中目前正在執行的版本相符。 針對客戶訂用帳戶中的任何新私人雲端，會安裝最新版本的軟體堆疊。

升級概念檔中說明了 Azure VMware 解決方案平臺軟體的一般升級原則和程式。

## <a name="host-maintenance-and-lifecycle-management"></a>主機維護和生命週期管理

主機維護和生命週期管理已完成，而不會影響私人雲端叢集的容量或效能。 自動化主機維護的範例包括固件升級和硬體修復或更換。

Microsoft 負責管理 NSX-T 設備（例如 NSX-T 管理員和 NSX-T Edge）的生命週期管理。 Microsoft 也負責啟動網路設定，例如建立第0層閘道，以及啟用 North-South 路由。 以系統管理員身分使用您的 Azure VMware 解決方案私人雲端，您必須負責 NSX-T SDN 設定。 例如，網路區段、分散式防火牆規則、第1層閘道和負載平衡器。

> [!IMPORTANT]
> Azure VMware 解決方案系統管理員不得修改 NSX-T Edge 或第0層閘道的設定。 這可能會導致服務中斷。

## <a name="backup-and-restoration"></a>備份與還原

私用雲端 vCenter 和 NSX-T 設定會每小時備份一次。 備份會保留三天。 您可以透過 Azure 入口網站中的服務要求，要求從備份還原。

## <a name="next-steps"></a>後續步驟

下一步是學習 [網路和連線能力的概念](concepts-networking.md)。

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

