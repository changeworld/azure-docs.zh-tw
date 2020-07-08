---
title: 概念-整合中樞和輪輻架構中的 Azure VMware 解決方案（AVS）部署
description: 深入瞭解在 Azure 上的現有或新的中樞和輪輻架構中整合 Azure VMware 解決方案（AVS）部署的建議。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 82937e04fc0a5101c353702b92b6b068d027d7ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85374957"
---
# <a name="integrate-azure-vmware-solution-avs-in-a-hub-and-spoke-architecture"></a>整合中樞和輪輻架構中的 Azure VMware 解決方案（AVS）

在本文中，我們會提供建議，讓您在 Azure 上的現有或新的[中樞和輪輻架構](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/shared-services)中整合 Azure VMware 解決方案（AVS）部署。 

中樞和輪輻案例假設混合式雲端環境的工作負載在：

* 使用 IaaS 或 PaaS 服務的原生 Azure
* AVS 
* vSphere 內部部署

## <a name="architecture"></a>架構

*中樞*是 Azure 虛擬網路，可作為內部部署和 AVS 私人雲端的連線中心點。 *輪輻*是與中樞對等互連的虛擬網路，可啟用跨虛擬網路通訊。

內部部署資料中心、AVS 私人雲端和中樞之間的流量會透過 ExpressRoute 連線進行。 輪輻虛擬網路通常包含以 IaaS 為基礎的工作負載，但可以有 PaaS 服務（例如[App Service 環境](../app-service/environment/intro.md)），其與虛擬網路的直接整合，或已啟用[Azure 私人連結](https://docs.microsoft.com/azure/private-link/)的其他 paas 服務。 

此圖顯示 Azure 中透過 ExpressRoute 連線到內部部署和 AVS 的中樞和輪輻部署範例。

:::image type="content" source="./media/hub-spoke/avs-hub-and-spoke-deployment.png" alt-text="AVS 中樞和輪輻整合部署":::




此架構具有下列主要元件：

-   **內部部署網站：** 透過 Express Route 連線連接至 Azure 的客戶內部部署資料中心。

-   **AVS 私用雲端：** 由一或多個 vSphere 叢集形成的 AVS SDDC，每一個叢集最多16個節點。

-   **ExpressRoute 閘道：** 可讓 AVS 私人雲端、內部部署網路、中樞虛擬網路上的共用服務，以及在輪輻虛擬網路上執行的工作負載之間進行通訊。

    > [!NOTE]
    > **S2S VPN 考慮：** 對於 AVS 生產部署，由於 HCX 的網路需求，因此不支援 Azure S2S。 不過，針對不需要 HCX 的 PoC 或非生產部署，您可以使用它。

-   **中樞虛擬網路：** 作為內部部署網路和 AVS 私人雲端的連線中心點。

-   **輪輻虛擬網路**

    -   **IaaS 輪輻：** IaaS 輪輻會裝載以 Azure IaaS 為基礎的工作負載，包括 VM 可用性設定組和虛擬機器擴展集，以及對應的網路元件。

    -   **PaaS 輪輻：** 由於[私用端點](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)和[私人連結](https://docs.microsoft.com/azure/private-link/private-link-overview)，PaaS 輪輻會使用私人定址來裝載 Azure PaaS 服務。

-   **Azure 防火牆：** 做為分割輪輻、內部內部部署和 AVS 之間流量的主要部分。

-   **應用程式閘道：** 會公開並保護在 Azure IaaS/PaaS 或 AVS 虛擬機器（Vm）上執行的 web 應用程式。 它會與其他服務整合，例如 API 管理。

## <a name="network-and-security-considerations"></a>網路和安全性考慮

ExpressRoute 連線可讓流量在內部部署、AVS 和 Azure 網路網狀架構之間流動。 AVS 使用[ExpressRoute 全球](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)觸達來實行此連線能力。

內部部署連線能力可能也會使用 ExpressRoute 全球範圍，但並非必要。

* **內部部署至 AVS 流量流程**

  :::image type="content" source="media/hub-spoke/on-prem-to-avs-traffic-flow.png" alt-text="內部部署至 AVS 流量流程":::


* **AVS 至中樞 VNET 流量流程**

  :::image type="content" source="media/hub-spoke/avs-to-hub-vnet-traffic-flow.png" alt-text="AVS 至中樞虛擬網路流量流程":::


您可以在[avs 產品檔](https://docs.microsoft.com/azure/azure-vmware/concepts-networking)中找到有關 avs 網路和互連能力概念的更多詳細資料。

### <a name="traffic-segmentation"></a>流量分割

[Azure 防火牆](https://docs.microsoft.com/azure/firewall/)是中樞和輪輻拓撲的中央部分，部署在中樞虛擬網路上。 使用 Azure 防火牆或另一個 Azure 支援的網路虛擬裝置來建立流量規則，並將不同輪輻、內部部署和 AVS 工作負載之間的通訊分割。

建立路由表以將流量導向至 Azure 防火牆。  針對輪輻虛擬網路，請建立路由來設定 Azure 防火牆內部介面的預設路由，如此一來，當虛擬網路中的工作負載需要連線到 AVS 位址空間時，防火牆就會進行評估，並將對應的流量規則套用至允許或拒絕它。  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="建立路由表以將流量導向至 Azure 防火牆":::


> [!IMPORTANT]
> 不支援**GatewaySubnet**設定上位址前置詞為 0.0.0.0/0 的路由。

在對應的路由表上設定特定網路的路由。 例如，路由從內部部署連線到 AVS 管理和工作負載 IP 首碼，反之亦然，會透過 Azure 防火牆將所有流量從內部部署路由傳送至 AVS 私人雲端。

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="在對應的路由表上設定特定網路的路由":::

使用輪輻和中樞內的網路安全性群組來分割第二層的流量，以建立更細微的流量原則。 


### <a name="application-gateway"></a>應用程式閘道

Azure 應用程式閘道 V1 和 V2 已經過測試，並已使用在 AVS Vm 上執行的 web 應用程式做為後端集區。 應用程式閘道是目前唯一支援的方法，可將在 AVS Vm 上執行的 web 應用程式公開至網際網路。 它也可以安全地向內部使用者公開應用程式。

:::image type="content" source="media/hub-spoke/avs-second-level-traffic-segmentation.png" alt-text="使用網路安全性群組分割的第二層流量":::


### <a name="jumpbox-and-azure-bastion"></a>Jumpbox 和 Azure 防禦

使用 Jumpbox 存取 AVS 環境，這是部署在中樞虛擬網路內共用服務子網中的 Windows 10 或 Windows Server VM。

作為安全性最佳作法，請在中樞虛擬網路中部署[Microsoft Azure](https://docs.microsoft.com/azure/bastion/)防禦服務。 Azure 防禦提供無縫的 RDP 和 SSH 存取部署在 Azure 上的 Vm，而不需要為這些資源布建公用 IP 位址。 布建 Azure 防禦服務之後，您就可以從 [Azure 入口網站] 存取選取的 VM。 建立連線之後，新索引標籤隨即開啟，其中顯示 Jumpbox 桌面，而從該桌面，您可以存取 AVS 私用雲端管理平面。

> [!IMPORTANT]
> 請勿將公用 IP 位址提供給 Jumpbox VM，或將 3389/TCP 埠公開至公用網際網路。 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Azure 防禦中樞虛擬網路":::


## <a name="azure-dns-resolution-considerations"></a>Azure DNS 解析考慮

針對 Azure DNS 解決方案，有兩個選項可供使用：

-   使用在中樞上部署的 Azure Active Directory （Azure AD）網域控制站（如身分[識別考慮](#identity-considerations)所述）做為名稱伺服器。

-   部署和設定 Azure DNS 私人區域。

最佳方法是結合這兩個，以提供適用于 AVS、內部部署和 Azure 的可靠名稱解析。

作為一般設計建議，使用現有的 Azure DNS 基礎結構（在此案例中為 Active Directory 整合的 DNS），部署到至少部署于中樞虛擬網路中的兩個 Azure Vm，並設定在輪輻虛擬網路中，以在 DNS 設定中使用這些 Azure DNS 伺服器。

Azure 私人 DNS 仍然可以在 Azure 私人 DNS 區域連結至虛擬網路的地方使用，而 DNS 伺服器則會做為混合式解析程式使用，搭配使用客戶 Azure 私人 DNS 基礎結構的條件式轉送至內部部署/AVS 執行 DNS 名稱。

Azure DNS 私人區域有幾個考慮事項：

* 應啟用自動註冊以 Azure DNS，以自動管理在輪輻虛擬網路中部署之 Vm 的 DNS 記錄生命週期。
* 已啟用自動註冊的虛擬網路可以連結的私人 DNS 區域數目上限為1。
* 可以連結虛擬網路的私人 DNS 區域數目上限為1000，而不啟用自動註冊。

內部部署和 AVS 伺服器可以使用條件轉寄站來設定，以便在 azure 中針對 Azure 私人 DNS 區域解析 Vm。

## <a name="identity-considerations"></a>身分識別考量

基於身分識別目的，最好的方法是使用共用服務子網，在中樞上部署至少一個 AD 網域控制站，最好是以區域分散式方式或 VM 可用性設定組的其中兩個。 請參閱將內部部署 AD 網域擴充至 Azure 的[Azure 架構中心](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain)。

此外，在 AVS 端部署另一個網域控制站，以作為 vSphere 環境內的身分識別和 DNS 來源。

針對 vCenter 和 SSO，請在 [**管理 \> 識別身分 \> 識別來源**] 上設定 Azure 入口網站中的身分識別來源。

建議的最佳作法是整合[AD 網域與 Azure Active Directory](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/azure-ad)。

<!-- LINKS - external -->
[Azure Architecture Center]: https://docs.microsoft.com/azure/architecture/

[Hub & Spoke topology]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: https://docs.microsoft.com/azure/networking/

<!-- LINKS - internal -->


