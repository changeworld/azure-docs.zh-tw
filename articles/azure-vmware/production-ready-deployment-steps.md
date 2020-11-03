---
title: 規劃 Azure VMware 解決方案部署
description: 本文概述 Azure VMware 解決方案部署工作流程。  最終結果是準備好用於建立和移轉虛擬機器 (VM) 的環境。
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: e30692f26af786097f3cdb81690be617bfea0c79
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517356"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>規劃 Azure VMware 解決方案部署

本文會提供您規劃程序，以識別和收集部署期間所使用的資料。 當您規劃部署時，請務必記錄您收集的資訊，以便在部署期間輕鬆參考。

此快速入門的程序會產生生產環境就緒的環境，以便建立虛擬機器 (VM) 和移轉。 

>[!IMPORTANT]
>建立 Azure VMware 解決方案資源之前，請遵循[如何啟用 Azure VMware 解決方案資源](enable-azure-vmware-solution.md)文章來提交支援票證，以配置您的節點。 一旦支援小組收到您的要求後，最多需要五個工作天的時間來確認您的要求，並配置您的節點。 如果您有現有的 Azure VMware 解決方案私人雲端，且想要配置更多節點，您將會經歷相同的程序。 


## <a name="subscription"></a>訂用帳戶

識別您打算用來部署 Azure VMware 解決方案的訂用帳戶。  您可以建立新的訂用帳戶，或重複使用現有的訂用帳戶。

>[!NOTE]
>訂用帳戶必須與 Microsoft Enterprise 合約相關聯。

## <a name="resource-group"></a>資源群組

識別您想要用於 Azure VMware 解決方案的資源群組。  一般來說，資源群組是特別針對 Azure VMware 解決方案而建立的，但您可以使用現有的資源群組。

## <a name="region"></a>區域

識別您想要部署 Azure VMware 解決方案的區域。  如需詳細資訊，請參閱[依區域提供的 Azure 產品指南](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware)。

## <a name="resource-name"></a>資源名稱

定義您將在部署期間使用的資源名稱。  資源名稱是易記且描述性的名稱，您可以在其中為您的 Azure VMware 解決方案私人雲端做為標題。

## <a name="size-nodes"></a>大小節點

識別您想要在部署 Azure VMware 解決方案時使用的大小節點。  如需完整清單，請參閱 [Azure VMware 解決方案私人雲端和叢集](concepts-private-clouds-clusters.md#hosts)文件。

## <a name="number-of-hosts"></a>主機數目

定義您想要部署至 Azure VMware 解決方案私人雲端的主機數目。  最小節點計數為三，而每個叢集的最大值為 16。  如需詳細資訊，請參閱 [Azure VMware 解決方案私人雲端和叢集](concepts-private-clouds-clusters.md#clusters)文件。

如果您需要超越初始部署編號，稍後可以隨時延伸叢集。

## <a name="vcenter-admin-password"></a>vCenter 管理密碼
定義 vCenter 管理密碼。  在部署期間，您將建立 vCenter 管理密碼。 在 vCenter 組建期間，管理員帳戶的密碼是 cloudadmin@vsphere.local。 您將用其登入 vCenter。

## <a name="nsx-t-admin-password"></a>NSX-T 管理密碼
定義 NSX-T 管理密碼。  在部署期間，您將建立一個 NSX-T 管理密碼。 在 NSX 組建期間，會指派密碼給 NSX 帳戶中的管理使用者。 您將用其登入 [NSX-T 管理員]。

## <a name="ip-address-segment"></a>IP 位址區段

規劃部署的第一個步驟是規劃 IP 分割。  Azure VMware 解決方案會內嵌您提供的 /22 網路。 然後將其分割為較小的區段，然後針對 vCenter、VMware HCX、NSX-T 和 vMotion 使用這些 IP 區段。

Azure VMware 解決方案會透過內部 ExpressRoute 電路連線到您的 Microsoft Azure 虛擬網路。 在大部分情況下，其會透過 ExpressRoute Global Reach 連線到您的資料中心。 

Azure VMware 解決方案、您現有的 Azure 環境，以及內部部署環境的所有 Exchange 路由 (通常是)。 就這種情況而言，您在此步驟中定義的 /22 CIDR 網路位址區塊，不應與您在內部部署或 Azure 中已有的任何專案重疊。

**範例︰** 10.0.0.0/22

如需詳細資訊，請參閱[網路規劃檢查清單](tutorial-network-checklist.md#routing-and-subnet-considerations)。

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="識別 - IP 位址區段" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>虛擬機器工作負載的 IP 位址區段

識別 IP 區段，以在私人雲端中建立您的第一個網路 (NSX 區段)。  換句話說，您想要在 Azure VMware 解決方案上建立網路區段，讓您可以將 VM 部署至 Azure VMware 解決方案。   

即使您只打算擴充 L2 網路，請建立會驗證環境的網路區段。

請記住，在您的 Azure 和內部部署磁碟使用量中，任何建立的 IP 區段都必須是唯一的。  

**範例︰** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="識別 - 虛擬機器工作負載的 IP 位址區段" border="false":::     

## <a name="optional-extend-networks"></a>(選擇性) 延伸網路

您可以將網路區段從內部部署延伸至 Azure VMware 解決方案，如果您這樣做，請立即識別這些網路。  

請注意下列事項：

- 如果您打算從內部部署環境延伸網路，這些網路必須連線到內部部署 VMware 環境中的 [vSphere 分散式交換器 (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html)。  
- 如果您打算延伸的網路在 [vSphere 標準交換器](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html)上為即時網路，則無法進行延伸。

## <a name="expressroute-global-reach-peering-network"></a>ExpressRoute Global Reach 對等互連網路

識別 ExpressRoute Global Reach 對等互連所需的 `/29` CIDR 網路位址區塊。 請記住，在您的 Azure VMware 解決方案和內部部署磁碟使用量中，任何建立的 IP 區段都必須是唯一的。 此區段中的 IP 會用於 ExpressRoute Global Reach 的每一端，以將 Azure VMware 解決方案 ExpressRoute 電路與內部部署 ExpressRoute 電路連線。 

**範例︰** 10.1.0.0/29

:::image type="content" source="media/pre-deployment/expressroute-global-reach-ip-diagram.png" alt-text="識別 - ExpressRoute Global Reach 對等互連網路" border="false":::

## <a name="azure-virtual-network-to-attach-azure-vmware-solution"></a>Azure 虛擬網路以附加 Azure VMware 解決方案

若要存取 Azure VMware 解決方案私人雲端，隨附於 Azure VMware 解決方案的 ExpressRoute 電路必須附加到 Azure 虛擬網路。  在部署期間，您可以定義新的虛擬網路，或選擇現有的虛擬網路。

來自 Azure VMware 解決方案的 ExpressRoute 電路會連線到您在此步驟中定義的 Azure 虛擬網路中的 ExpressRoute 閘道。  

>[!IMPORTANT]
>您可以使用現有的 ExpressRoute 閘道連線至 Azure VMware 解決方案，前提是不可超過每個虛擬網路四個 ExpressRoute 線路的限制。  不過，若要透過 ExpressRoute 從內部部署存取 Azure VMware 解決方案，則必須要有 ExpressRoute Global Reach，因為 ExpressRoute 閘道不會在其連線的線路之間提供可轉移的路由。  

如果您想要將 ExpressRoute 電路從 Azure VMware 解決方案連線到現有的 ExpressRoute 閘道，您可以在部署之後執行此動作。  

因此，在摘要中，您要將 Azure VMware 解決方案連線到現有的 Express Route 閘道嗎？  

* **是** = 識別部署期間未使用的虛擬網路。
* **否** = 識別現有的虛擬網路或在部署期間建立新的虛擬網路。

不論是哪一種，請記載您想要在此步驟中執行的動作。

>[!NOTE]
>您的內部部署環境和 Azure VMware 解決方案會看到此虛擬網路，因此請確定您在此虛擬網路和子網路中使用的任何 IP 區段不會重疊。

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="身分識別 - Azure 虛擬網路以附加 Azure VMware 解決方案" border="false":::

## <a name="vmware-hcx-network-segments"></a>VMware HCX 網路區段

VMware HCX 是與 Azure VMware 解決方案搭售的一項技術。 VMware HCX 的主要使用案例包括工作負載移轉和災害復原。 如果您打算執行這項操作，最好立即規劃網路功能。   否則，您可以略過並繼續進行下一個步驟。

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>下一步
既然您已收集並記載所需的資訊，請繼續進行至下一節，以建立您的 Azure VMware 解決方案私人雲端。

> [!div class="nextstepaction"]
> [部署 Azure VMware 解決方案](deploy-azure-vmware-solution.md)
