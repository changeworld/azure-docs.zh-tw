---
title: 概念-將 Azure VMware 解決方案部署整合到中樞和輪輻架構中
description: 瞭解如何將 Azure VMware 解決方案部署整合到 Azure 上的中樞和輪輻架構。
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: da4a5f5ec6fa2b0a2733e8d1bf0243ec75f38d55
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577714"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>整合中樞和輪輻架構中的 Azure VMware 解決方案

本文提供將 Azure VMware 解決方案部署整合到 Azure 上現有或新的 [中樞和輪輻架構](/azure/architecture/reference-architectures/hybrid-networking/#hub-spoke-network-topology) 的建議。 


中樞和輪輻案例假設有工作負載的混合式雲端環境：

* 使用 IaaS 或 PaaS 服務的原生 Azure
* Azure VMware 解決方案 
* vSphere 內部部署

## <a name="architecture"></a>架構

*中樞* 是 Azure 虛擬網路，可作為內部部署和 Azure VMware 解決方案私人雲端的連線中心點。 *輪輻* 是與中樞對等互連的虛擬網路，可啟用跨虛擬網路通訊。

內部部署資料中心、Azure VMware 解決方案私人雲端和中樞之間的流量會透過 Azure ExpressRoute 連線進行。 輪輻虛擬網路通常包含以 IaaS 為基礎的工作負載，但可以有像是 [App Service 環境](../app-service/environment/intro.md)的 PaaS 服務，其與虛擬網路的直接整合，或是已啟用 [Azure Private Link](../private-link/index.yml) 的其他 paas 服務。

>[!IMPORTANT]
>您可以使用現有的 ExpressRoute 閘道連線至 Azure VMware 解決方案，前提是不可超過每個虛擬網路四個 ExpressRoute 線路的限制。  不過，若要透過 ExpressRoute 從內部部署存取 Azure VMware 解決方案，則必須要有 ExpressRoute Global Reach，因為 ExpressRoute 閘道不會在其連線的線路之間提供可轉移的路由。

此圖顯示 Azure 中的中樞和輪輻部署範例，並透過 ExpressRoute 全球接觸來連線至內部部署和 Azure VMware 解決方案。

:::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png" alt-text="Azure VMware 解決方案中樞和輪輻整合部署" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png":::

架構具有下列主要元件：

- **內部部署網站：** 客戶的內部部署資料中心 (s) 透過 ExpressRoute 連線連線到 Azure。

- **Azure VMware 解決方案私用雲端：** 由一或多個 vSphere 叢集所組成的 Azure VMware Solution SDDC，每個叢集最多16個節點。

- **ExpressRoute 閘道：** 啟用 Azure VMware 解決方案私人雲端、中樞虛擬網路上的共用服務，以及在輪輻虛擬網路上執行的工作負載之間的通訊。

- **ExpressRoute 全球接觸：** 啟用內部部署與 Azure VMware 解決方案私人雲端之間的連線。 Azure VMware 解決方案與 Azure 網狀架構之間的連線能力是透過 ExpressRoute Global 觸及。 您無法選取 ExpressRoute Fast Path 以外的任何選項。  不支援 ExpressRoute Direct。


- **S2S VPN 考慮事項：** 針對 Azure VMware 解決方案生產部署，因為 VMware HCX 的網路需求，所以不支援 Azure S2S VPN。 不過，您可以將它用於 PoC 部署。


- **中樞虛擬網路：** 作為內部部署網路和 Azure VMware 解決方案私人雲端的連線中心點。

- **輪輻虛擬網路**

    - **IaaS 輪輻：** IaaS 輪輻裝載以 Azure IaaS 為基礎的工作負載，包括 VM 可用性設定組和虛擬機器擴展集，以及對應的網路元件。

    - **PaaS 輪輻：** PaaS 輪輻會使用私用定址來裝載 Azure PaaS 服務，而不需要 [私人端點](../private-link/private-endpoint-overview.md) 和 [Private Link](../private-link/private-link-overview.md)。

- **Azure 防火牆：** 作為將輪輻和 Azure VMware 解決方案之間的流量分割的核心。

- **應用程式閘道：** 公開及保護在 Azure IaaS/PaaS 或 Azure VMware 解決方案虛擬機器 (Vm) 上執行的 web 應用程式。 它會與其他服務（如 API 管理）整合。

## <a name="network-and-security-considerations"></a>網路和安全性考慮

ExpressRoute 連線可讓流量在內部部署、Azure VMware 解決方案和 Azure 網路網狀架構之間流動。 Azure VMware 解決方案使用 [ExpressRoute 全球接觸](../expressroute/expressroute-global-reach.md) 來實行此連線能力。

由於 ExpressRoute 閘道不會在其連線線路之間提供可轉移的路由，因此內部部署連線能力也必須使用 ExpressRoute 全球觸達，才能在內部部署 vSphere 環境與 Azure VMware 解決方案之間進行通訊。 

* **內部部署至 Azure VMware 解決方案的流量流程**

  :::image type="content" source="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png" alt-text="內部部署至 Azure VMware 解決方案的流量流程" border="false" lightbox="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png":::


* **Azure VMware 解決方案至中樞 VNET 流量流程**

  :::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png" alt-text="Azure VMware 解決方案至中樞虛擬網路流量流程" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png":::


您可以在 [Azure Vmware 解決方案產品檔](./concepts-networking.md)中找到更多有關 Azure vmware 解決方案網路功能和連線能力概念的詳細資料。

### <a name="traffic-segmentation"></a>流量分割

[Azure 防火牆](../firewall/index.yml) 是中樞和輪輻拓撲的中央片段，部署于中樞虛擬網路上。 使用 Azure 防火牆或其他 Azure 支援的網路虛擬裝置來建立流量規則，並分割不同輪輻與 Azure VMware 解決方案工作負載之間的通訊。

建立路由表，以將流量導向至 Azure 防火牆。  針對輪輻虛擬網路，建立路由，將預設路由設定為 Azure 防火牆的內部介面。 如此一來，當虛擬網路中的工作負載需要連接到 Azure VMware 解決方案位址空間時，防火牆可以進行評估，並將對應的流量規則套用至允許或拒絕它。  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="建立路由表以將流量導向至 Azure 防火牆" lightbox="media/hub-spoke/create-route-table-to-direct-traffic.png":::


> [!IMPORTANT]
> 不支援 **GatewaySubnet** 設定上位址首碼為 0.0.0.0/0 的路由。

在對應的路由表上設定特定網路的路由。 例如，從輪輻工作負載路由傳送到 Azure VMware 解決方案管理和工作負載 IP 首碼，反之亦然。

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="在對應的路由表上設定特定網路的路由" lightbox="media/hub-spoke/specify-gateway-subnet-for-route-table.png":::

使用輪輻和中樞內的網路安全性群組來建立更細微的流量原則，以進行第二層的流量分割。

> [!NOTE]
> **從內部部署到 Azure VMware 解決方案的流量：** 內部部署工作負載（以 vSphere 為基礎或其他工作負載）之間的流量會由全球觸達啟用，但流量不會通過中樞上的 Azure 防火牆。 在此案例中，您必須在內部部署或 Azure VMware 解決方案中執行流量分割機制。

### <a name="application-gateway"></a>應用程式閘道

Azure 應用程式閘道 V1 和 V2 已透過在 Azure VMware 解決方案 Vm 上執行的 web 應用程式進行測試，以作為後端集區。 應用程式閘道目前是將 Azure VMware 解決方案 Vm 上執行的 web 應用程式公開至網際網路的唯一支援方法。 它也可以安全地將應用程式公開給內部使用者。

如需詳細資訊和需求，請參閱關於 [應用程式閘道](./protect-azure-vmware-solution-with-application-gateway.md) 的 Azure VMware 解決方案特定文章。

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="使用網路安全性群組的第二層流量分割" border="false":::


### <a name="jump-box-and-azure-bastion"></a>跳躍箱和 Azure 防禦

使用跳躍方塊存取 Azure VMware 解決方案環境，也就是在中樞虛擬網路內的共用服務子網中部署的 Windows 10 或 Windows Server VM。

>[!IMPORTANT]
>Azure 防禦服務建議連線到跳躍方塊，以防止將 Azure VMware 解決方案公開到網際網路。 您無法使用 Azure 防禦來連接至 Azure VMware 解決方案 Vm，因為它們不是 Azure IaaS 物件。  

作為安全性最佳作法，請在中樞虛擬網路內部署 [Microsoft Azure](../bastion/index.yml) 防禦服務。 Azure 防禦可為部署在 Azure 上的 Vm 提供順暢的 RDP 和 SSH 存取，而不需要將公用 IP 位址布建至這些資源。 布建 Azure 防禦服務之後，您就可以從 Azure 入口網站存取選取的 VM。 建立連線之後，新的索引標籤隨即開啟，並顯示跳躍箱桌面，而您可以從該桌面存取 Azure VMware 解決方案私人雲端管理平面。

> [!IMPORTANT]
> 請勿將公用 IP 位址提供給跳躍箱 VM，或將 3389/TCP 埠公開至公用網際網路。 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Azure 防禦中樞虛擬網路" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Azure DNS 解析考慮

針對 Azure DNS 解析度，有兩個可用的選項：

-   使用 Azure Active Directory (Azure AD) 部署于中樞的網域控制站 (作為名稱伺服器的身分 [識別考慮](#identity-considerations)) 中所述。

-   部署及設定 Azure DNS 私用區域。

最佳方法是結合這兩個方法，為 Azure VMware 解決方案、內部部署和 Azure 提供可靠的名稱解析。

作為一般設計建議，請使用現有的 Azure DNS 基礎結構 (在此案例中，Active Directory 整合的 DNS) 部署至中樞虛擬網路中至少部署的兩個 Azure Vm，並設定在輪輻虛擬網路中，以在 DNS 設定中使用這些 Azure DNS 伺服器。

您可以使用 Azure 私人 DNS，其中 Azure 私人 DNS 區域會連結至虛擬網路。  DNS 伺服器可作為混合式解析程式使用，以將條件式轉送至內部部署或 Azure VMware 解決方案，以利用客戶的 Azure 私人 DNS 基礎結構來執行 DNS。 

若要為輪輻虛擬網路內部署的 Vm 自動管理 DNS 記錄的生命週期，請啟用自動註冊。 啟用時，私人 DNS 區域的最大數目只是一個。 如果停用，則最大值為1000。

您可以使用條件轉寄站設定內部部署和 Azure VMware 解決方案伺服器，以在 azure 中針對 Azure 私人 DNS 區域解析 Vm。

## <a name="identity-considerations"></a>身分識別考量

基於身分識別用途，最佳方法是在中樞上至少部署一個 AD 網域控制站。 使用區域分散方式或 VM 可用性設定組中的兩個共用服務子網。 請參閱將內部部署 AD 網域擴充至 Azure 的 [Azure 架構中心](/azure/architecture/reference-architectures/identity/adds-extend-domain) 。

此外，在 Azure VMware 解決方案端部署另一個網域控制站，以作為 vSphere 環境內的身分識別和 DNS 來源。

建議的最佳作法是將 [AD 網域與 Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad)整合。

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
