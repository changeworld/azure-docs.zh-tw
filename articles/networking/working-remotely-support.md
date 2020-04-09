---
title: 使用 Azure 網路服務遠端工作
description: 本頁介紹如何使用可用於遠端工作的 Azure 網路服務,以及如何緩解因遠端工作人數增加而導致的流量問題。
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: e0e5806ec59cd2d75111db213d8511488d043eec
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982849"
---
# <a name="working-remotely-using-azure-networking-services"></a>使用 Azure 網路服務遠端工作

>[!NOTE]
> 本文介紹如何利用 Azure 網路服務、Microsoft 網路和 Azure 合作夥伴生態系統遠端工作並緩解因 COVID-19 危機而可能面臨的網路問題。

本文介紹了組織可用於為其使用者設置遠端訪問或在高峰使用期間用額外容量補充其現有解決方案的選項。 網路架構師面臨以下挑戰:

- 解決網路利用率的提高問題。
- 為公司和客戶中的更多員工提供可靠的安全連接。
- 提供與全球遠端位置的連接。

並非所有網路(例如,專用廣域網和企業核心網路)都遇到來自峰值遠端工作負載的擁塞。 瓶頸通常只報告在家庭寬頻網路和公司本地網路的VPN閘道。

網路規劃人員可以通過記住不同的流量類型需要不同的網路處理優先順序以及一些智慧負載重定向/分發來幫助緩解瓶頸並緩解網路擁塞。 例如,醫生與患者互動的即時遠端電敏流量非常重要,並且對延遲/抖動非常敏感。 但是,在存儲之間複製相同的流量並不敏感。 以前的流量必須通過最佳網路路徑路由,服務品質更高;而通過次優路線路由後續流量是可以接受的。



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>共用我們的最佳實務 - Azure 網路專為彈性和高可用性而設計

Azure 旨在承受資源利用率的突然變化,在高峰利用期間可以大有説明。 此外,微軟維護並運營著世界上最大的網路之一。 Microsoft 的網路專為高可用性而設計,可以承受不同類型的故障:從單個網路元素故障到整個區域的故障。

Microsoft 網路旨在滿足各種網路流量的要求,並為不同類型的網路流量提供最佳性能,包括 Skype 和 Teams、CDN、即時大數據分析、Azure 存儲、必應和 Xbox 的延遲敏感多媒體流量。 為了為不同類型的流量提供最佳性能,Microsoft 網路吸引了所有注定傳輸或想要傳輸的流量,其資源盡可能接近流量的來源。

>[!NOTE] 
>使用下面描述的 Azure 網路功能利用 Microsoft 全球網路的流量吸引行為提供更好的客戶網路體驗。 Microsoft 網络的流量吸引行為有助於儘快從在高峰使用期間可能會遇到擁塞的第一英里/最後一英里網路卸載流量。
>

## <a name="enable-employees-to-work-remotely"></a>使員工能夠遠端工作

Azure VPN 閘道同時支援點到網站 (P2S) 和網站到網站 (S2S) VPN 連接。 使用 Azure VPN 閘道可以縮放員工的連接以安全地存取 Azure 部署的資源和本地資源。 有關詳細資訊,請參閱[如何允許使用者遠端工作](../vpn-gateway/work-remotely-support.md)。 

如果使用安全套接字隧道協定 (SSTP),則併發連接數限制為 128。 為了獲得更多的連接,我們建議過渡到 OpenVPN 或 IKEv2。 有關詳細資訊,請參閱從[SSTP 過渡到 OpenVPN 協定或 IKEv2。](../vpn-gateway/ikev2-openvpn-from-sstp.md
)

要造訪部署在 Azure 中的資源,遠端開發人員可以使用 Azure Bastion 解決方案,而不是 VPN 連接來獲取安全外殼訪問(RDP 或 SSH),而無需存取 VM 上的公共 IP。 有關詳細資訊,請參閱使用[Azure 堡壘 遠端工作](../bastion/work-remotely-support.md)。

對於聚合大規模 VPN 連接,支援不同本地全域位置、不同區域中心和分支虛擬網路中的資源之間的任意連接,以及優化多個家庭寬頻網路的利用率,您可以使用 Azure 虛擬 WAN。 有關詳細資訊,請參閱[從家庭需求中努力滿足工作需求?這裡是 Azure 虛擬 WAN 可以提供説明的地方](../virtual-wan/work-remotely-support.md)。

支援遠端工作人員的另一種方法是部署託管在 Azure 虛擬網路中的虛擬桌面基礎結構 (VDI),該基礎結構(VDI)通過 Azure 防火牆進行保護。 例如,Windows 虛擬桌面 (WVD) 是在 Azure 中運行的桌面和應用虛擬化服務。 使用 Windows 虛擬桌面,您可以在 Azure 訂閱中設定可擴展且靈活的環境,而無需運行任何其他閘道伺服器。 您只對虛擬網路中的 WVD 虛擬機器負責。 有關詳細資訊,請參閱[Azure 防火牆遠端工作支援](../firewall/remote-work-support.md)。 

Azure 還具有一組豐富的生態系統合作夥伴。 我們的合作夥伴 Azure 上的網路虛擬裝置還可以幫助擴展 VPN 連接。 有關詳細資訊,請參閱[遠端工作的網路虛擬裝置 (NVA) 注意事項](../vpn-gateway/nva-work-remotely-support.md)。

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>擴充員工連線以存取全球分布的資源

以下 Azure 服務可幫助員工訪問全域分佈的資源。 您的資源可能位於任何 Azure 區域、本地網路,甚至位於其他公共或私有雲中。 

- **Azure 虛擬網路對等**:如果將資源部署在多個 Azure 區域和/或者使用多個虛擬網路聚合遠端工作員工的連接,則可以使用虛擬網路對等互連在多個 Azure 虛擬網路之間建立連接。 如需詳細資訊，請參閱[虛擬網路對等互連][VNet-peer]。

- **基於 Azure VPN 的解決方案**:對於透過 P2S 或 S2S VPN 連接到 Azure 的遠端員工,可以透過在本地網路和 Azure VPN 閘道之間配置 S2S VPN 來啟用對本地網路的訪問。 有關詳細資訊,請參閱[建立網站到網站的連線][S2S]。

- **ExpressRoute**:使用 ExpressRoute 專用對等互連,可以在 Azure 部署與本地基礎結構或位於主機位置設施中的基礎結構之間啟用專用連接。 ExpressRoute 透過Microsoft對等互連,還允許從本地網路存取Microsoft中的公共終結點。 ExpressRoute 連線不會經過公用網際網路。 它們提供安全的連接、可靠性、更高的輸送量,比 Internet 上的典型連接具有更低且一致的延遲。 如需詳細資訊，請參閱 [ExpressRoute 概觀][ExR]。 利用您現有的網路供應商,這已經是我們[ExpressRoute 合作夥伴生態系統][ExR-eco]的一部分,可以幫助縮短與 Microsoft 進行大型頻寬連接的時間。  使用[ExpressRoute Direct,][ExR-D]您可以將本地網路直接連接到 Microsoft 主幹。 ExpressRoute Direct 提供兩種不同的線路速率選項,雙 10 Gbps 或 100 Gbps。 

- **Azure 虛擬 WAN**:Azure 虛擬 WAN 允許 VPN 連接和 ExpressRoute 電路之間的無縫互操作性。 如前所述,Azure 虛擬 WAN 還支援不同預部署全域位置、不同區域中心和分支虛擬網路中的資源之間的任意連接

## <a name="scale-customer-connectivity-to-frontend-resources"></a>擴充客戶與前端資源的連線

在越來越多的人上網的時候,許多公司網站的客戶流量都有所增加。 Azure 應用程式閘道可説明管理此增加的前端工作負荷。 有關詳細資訊,請參閱[應用程式閘道高流量支援](../application-gateway/high-traffic-support.md)。

## <a name="microsoft-support-for-multi-cloud-traffic"></a>微軟支援多雲流量

對於在其他公共雲中的部署,Microsoft 可以提供全域連接。 Azure 虛擬廣域網、VPN 或 ExpressRoute 在這方面可以提供説明。 要將連接從 Azure 擴展到其他雲端,可以在兩個雲端之間配置 S2S VPN。 您還可以使用 ExpressRoute 建立從 Azure 到其他公共雲端的連接。 Oracle 雲端是 ExpressRoute 合作夥伴生態系統的一部分。 您可以在[Azure 與 Oracle 雲端結構之間建立直接互連][Az-OCI]。 屬於 ExpressRoute 合作夥伴生態系統的大多數服務提供者還提供與其他公共雲的專用連接。 利用這些服務提供者,您可以通過 ExpressRoute 在 Azure 中的部署與其他雲端之間建立專用連接。

## <a name="next-steps"></a>後續步驟

以下文章討論了如何使用不同的 Azure 網路功能來擴展使用者以遠端工作:

| **文章** | **說明** |
| --- | --- |
| [如何使用使用者能夠遠端工作](../vpn-gateway/work-remotely-support.md) | 查看可用選項,為使用者設置遠程存訪問,或為組織補充其現有解決方案。|
| [努力滿足家庭需求的工作?這裡是 Azure 虛擬 WAN 可以提供説明的地方](../virtual-wan/work-remotely-support.md) | 使用 Azure 虛擬 WAN 滿足組織的遠端連接需求。|
| [應用程式閘道高流量支援](../application-gateway/high-traffic-support.md) | 將應用程式閘道與 Web 應用程式防火牆 (WAF) 一起用於可擴展且安全的方式來管理 Web 應用程式的流量。 |
| [遠端工作的網路虛擬裝置 (NVA) 注意事項](../vpn-gateway/nva-work-remotely-support.md)|查看有關利用 Azure 中的 NVA 提供遠端存取解決方案的指導。 |
| [從 SSTP 過渡到 OpenVPN 協定或 IKEv2](https://go.microsoft.com/fwlink/?linkid=2124112) | 通過過渡到 OpenVPN 協定或 IKEv2,克服了 SSTP 的 128 個併發連接限制。|
| [使用 Azure 堡壘遠端工作](../bastion/work-remotely-support.md) | 直接在 Azure 門戶中向 Azure 虛擬網路中的虛擬機提供安全和無縫的 RDP/SSH 連接,而無需使用公共 IP 位址。 |
| [使用 Azure 快速路由建立混合連線以支援遠端使用者](../expressroute/work-remotely-support.md) | 將 ExpressRoute 用於混合連接,使組織中的用戶能夠遠端工作。|
| [Azure 防火牆遠端工作支援](../firewall/remote-work-support.md)|使用 Azure 防火牆保護 Azure 虛擬網路資源。 |

<!--Link References-->
[VNet-peer]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[S2S]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[ExR-eco]: https://docs.microsoft.com/azure/expressroute/expressroute-locations
[ExR-D]: https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about
[Az-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking
