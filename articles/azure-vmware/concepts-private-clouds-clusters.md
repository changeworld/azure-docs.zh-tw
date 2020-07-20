---
title: 概念-私人雲端和叢集
description: 瞭解 vmware 在 Azure 上 vmware 解決方案中的 Azure VMware 軟體定義資料中心和 vSphere 叢集的主要功能。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 09e1fd45b1dd873509f942ef8b524783acfed4ce
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84906984"
---
# <a name="azure-vmware-solution-avs-preview-private-cloud-and-cluster-concepts"></a>Azure VMware 解決方案（AVS）預覽私用雲端和叢集概念

Azure VMware 解決方案（AVS）在 Azure 中提供 VMware 型私用雲端。 私人雲端是由專用裸機主機的叢集所建立，並透過 Azure 入口網站進行部署和管理。 私人雲端中的叢集會以 VMware vSphere、vCenter、vSAN 和 NSX 軟體布建。 AVS 私用雲端硬體和軟體部署在 Azure 中完全整合並自動化。

Azure 訂用帳戶、AVS 私人雲端、vSAN 叢集和主機之間有邏輯關聯性。 在圖表中，會顯示單一 Azure 訂用帳戶中的兩個私人雲端。 私用雲端代表一個開發和一個生產環境，每個都有自己的私用雲端。 在每個私人雲端中，都有兩個叢集。 為了顯示較低的開發環境潛在需求，會使用較低容量主機的小型叢集。 下列各節將說明這些概念。

![客戶訂用帳戶中兩個私人雲端的影像](./media/hosts-clusters-private-clouds-final.png)

## <a name="private-clouds"></a>私人雲端

私人雲端包含以專屬的裸機 Azure 主機建立的 vSAN 叢集。 每個私人雲端都可以有多個叢集，全都由相同的 vCenter server 和 [NSX-T 管理員] 管理。 您可以在入口網站、CLI 或 PowerShell 中部署和管理私人雲端。 就像其他資源一樣，私人雲端也會從 Azure 訂用帳戶內安裝和管理。

訂用帳戶內的私人雲端數目是可調整的。 一開始，每個訂用帳戶有一個私用雲端的限制。

## <a name="clusters"></a>叢集

您會在每個私人雲端中建立至少一個 vSAN 叢集。 當您建立私人雲端時，預設會有一個叢集。 您可以使用 Azure 入口網站或透過 API，將額外的叢集新增至私人雲端。 所有叢集都有三部主機的預設大小，而且可以調整為3到16部主機。 叢集中使用的主機類型必須是相同的類型。 下一節將說明這些主機類型。

試用叢集可供評估，而且僅限於三部主機和每個私人雲端一個試用叢集。 在評估期間，您可以透過單一主機來調整試用叢集。

您可以透過入口網站或 API 來建立、刪除和調整叢集。 您仍然可以使用 vSphere 和 NSX-T Manager 來管理叢集設定或作業的大部分其他層面。 叢集中每部主機的所有本機存放裝置都受到 vSAN 的控制。

## <a name="hosts"></a>主機

超級交集、裸機基礎結構節點用於 AVS 私人雲端叢集中。 下表提供主機的 RAM、CPU 和磁片容量。 

| 主機類型              |             CPU             |   RAM (GB)   |  vSAN NVMe 快取層（TB，原始）  |  vSAN SSD 容量層（TB，原始）  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| 高階（他）          |  雙重 Intel 18 核心 2.3 GHz  |     576      |                3.2               |                15.20               |

用來建立或調整叢集的主機是從隔離的主機集區取得。 這些主機已通過硬體測試，並已將所有資料安全地從快閃磁片中刪除。 當您從叢集中移除主機時，系統會安全地抹除內部磁片，並將主機放入隔離的主機集區。 當您將主機新增至叢集時，會使用來自隔離集區的經過清理主機。

## <a name="vmware-software-versions"></a>VMware 軟體版本

在 AVS 私人雲端叢集中使用的 VMware 軟體目前軟體版本如下：

| 軟體              |    版本   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6.7 U2    | 
| ESXi                  |    6.7 U2    | 
| vSAN                  |    6.7 U2    |
| NSX-T                 |      2.5     |

針對私人雲端中的任何新叢集，軟體版本會符合私人雲端中目前正在執行的功能。 針對客戶訂用帳戶中的任何新私人雲端，會安裝最新版的軟體堆疊。

升級概念檔中說明了 AVS 平臺軟體的一般升級原則和程式。

## <a name="host-maintenance-and-lifecycle-management"></a>主機維護和生命週期管理

主機維護和生命週期管理已完成，不會影響私人雲端叢集的容量或效能。 自動化主機維護的範例包括固件升級與硬體修復或更換。

Microsoft 負責管理 NSX-t 設備的生命週期，例如 NSX-T Manager 和 NSX-T Edge。 Microsoft 也負責啟動網路設定，例如建立第0層閘道，以及啟用北南部路由。 身為您的 AVS 私用雲端的系統管理員，您必須負責 NSX-T SDN 設定，例如網路區段、分散式防火牆規則、第1層閘道和負載平衡器。

> [!IMPORTANT]
> AVS 系統管理員不得修改 NSX-T Edge 或第0層閘道的設定。 這可能會導致服務遺失。

## <a name="backup-and-restoration"></a>備份與還原

私用雲端 vCenter 和 NSX-T 設定會每小時備份一次。 備份會保留三天。 透過 Azure 入口網站中的服務要求，要求從備份還原。

## <a name="next-steps"></a>後續步驟

下一步是瞭解[網路和連線能力的概念](concepts-networking.md)。

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

