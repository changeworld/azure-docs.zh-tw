---
title: 規劃 Azure VMware 解決方案部署
description: 本文概述 Azure VMware 解決方案部署工作流程。  最終結果是準備好用於建立和移轉虛擬機器 (VM) 的環境。
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: cdf4ddd6166920fa7461bfd85e01ef0efd6dfbb9
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704554"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>規劃 Azure VMware 解決方案部署

本文會提供您規劃程序，以識別和收集部署期間所使用的資料。 當您規劃部署時，請務必記錄您收集的資訊，以便在部署期間輕鬆參考。

此快速入門的程序會產生生產環境就緒的環境，以便建立虛擬機器 (VM) 和移轉。 

>[!IMPORTANT]
>建立 Azure VMware 解決方案資源之前，請遵循[如何啟用 Azure VMware 解決方案資源](enable-azure-vmware-solution.md)文章來提交支援票證，以配置您的主機。 一旦支援小組收到您的要求後，最多需要五個工作天的時間來確認您的要求，並配置您的主機。 如果您有現有的 Azure VMware 解決方案私人雲端，且想要配置更多主機，您將會經歷相同的程序。 


## <a name="subscription"></a>訂用帳戶

識別您打算用來部署 Azure VMware 解決方案的訂用帳戶。  您可以建立新的訂用帳戶，或重複使用現有的訂用帳戶。

>[!NOTE]
>訂用帳戶必須與 Microsoft Enterprise 合約或雲端解決方案提供者的 Azure 方案相關聯。 如需詳細資訊，請參閱[如何啟用 Azure VMware 解決方案資源](enable-azure-vmware-solution.md)。

## <a name="resource-group"></a>資源群組

識別您想要用於 Azure VMware 解決方案的資源群組。  一般來說，資源群組是特別針對 Azure VMware 解決方案而建立的，但您可以使用現有的資源群組。

## <a name="region"></a>區域

識別您想要部署 Azure VMware 解決方案的區域。  如需詳細資訊，請參閱[依區域提供的 Azure 產品指南](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware)。

## <a name="resource-name"></a>資源名稱

定義您將在部署期間使用的資源名稱。  資源名稱是易記且描述性的名稱，您可以在其中為您的 Azure VMware 解決方案私人雲端做為標題。

>[!IMPORTANT]
>名稱長度不可超過 40 個字元。 如果名稱超過此限制，您將無法建立公用 IP 位址以用於私人雲端。 

## <a name="size-hosts"></a>主機大小

識別您想要在部署 Azure VMware 解決方案時使用的主機大小。  如需完整清單，請參閱 [Azure VMware 解決方案私人雲端和叢集](concepts-private-clouds-clusters.md#hosts)文件。

## <a name="number-of-hosts"></a>主機數目

定義您想要部署至 Azure VMware 解決方案私人雲端的主機數目。  每個叢集的最小主機數量為 3 個，最大為 16 個。  如需詳細資訊，請參閱 [Azure VMware 解決方案私人雲端和叢集](concepts-private-clouds-clusters.md#clusters)文件。

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

## <a name="azure-virtual-network-to-attach-azure-vmware-solution"></a>Azure 虛擬網路以附加 Azure VMware 解決方案

在此步驟中，您將識別 ExpressRoute 虛擬網路閘道，並支援用來連線到 Azure VMware 解決方案 ExpressRoute 線路的 Azure 虛擬網路。  ExpressRoute 線路可加速 Azure VMware 解決方案私人雲端與其他 Azure 服務、Azure 資源和內部部署環境之間的連線。

您可以使用 *現有* 或 *新* 的 ExpressRoute 虛擬網路閘道。

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="身分識別 - Azure 虛擬網路以附加 Azure VMware 解決方案" border="false":::

### <a name="use-an-existing-expressroute-virtual-network-gateway"></a>使用現有的 ExpressRoute 虛擬網路閘道

如果您使用 *現有* 的 ExpressRoute 虛擬網路閘道，則在部署私用雲端之後，就會建立 Azure VMware 解決方案 ExpressRoute 電路。  因此，您不需要填入 [ **虛擬網路** ] 欄位。  

請記下您將使用的 ExpressRoute 虛擬網路閘道，然後繼續進行下一個步驟。

### <a name="create-a-new-expressroute-virtual-network-gateway"></a>建立新的 ExpressRoute 虛擬網路閘道

如果建立 *新* 的 ExpressRoute 虛擬網路閘道，則可以使用現有的 azure 虛擬網路，也可以建立新的 Azure 虛擬網路。  

如果選擇使用現有的 Azure 虛擬網路，請確認虛擬網路中沒有預先存在的 ExpressRoute 虛擬網路閘道，並在 [建立私人雲端部署] 畫面的 [虛擬網路] 下拉式清單中選取它。

如果您選擇建立新的 Azure 虛擬網路，則可以在建立私人雲端部署畫面的 [建立新的虛擬網路選項] 區段中，事先或在部署期間建立它。

以下是 [ **建立私用雲端** 部署] 畫面的影像，並以紅色說明，這是本節所提及的 Azure **虛擬網路** 欄位，供您參考。

:::image type="content" source="media/pre-deployment/azure-vmware-solution-deployment-screen-vnet-circle.png" alt-text="Azure VMware 解決方案部署畫面的螢幕擷取畫面，其中已將虛擬網路閘道圈起來。":::

>[!NOTE]
>您的內部部署環境和 Azure VMware 解決方案可能會看到任何即將使用或建立的虛擬網路，因此請確定您在此虛擬網路和子網中使用的任何 IP 區段都不會重迭。

## <a name="vmware-hcx-network-segments"></a>VMware HCX 網路區段

VMware HCX 是與 Azure VMware 解決方案搭售的一項技術。 VMware HCX 的主要使用案例包括工作負載移轉和災害復原。 如果您打算執行這項操作，最好立即規劃網路功能。   否則，您可以略過並繼續進行下一個步驟。

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>下一步
既然您已收集並記載所需的資訊，請繼續進行至下一節，以建立您的 Azure VMware 解決方案私人雲端。

> [!div class="nextstepaction"]
> [部署 Azure VMware 解決方案](deploy-azure-vmware-solution.md)
