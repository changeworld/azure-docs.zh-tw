---
title: 使用 Azure 網路服務從遠端工作
description: 此頁面說明如何使用可用來從遠端工作的 Azure 網路服務，以及如何減輕從遠端工作的人數增加而產生的流量問題。
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: e0e5806ec59cd2d75111db213d8511488d043eec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80982849"
---
# <a name="working-remotely-using-azure-networking-services"></a>使用 Azure 網路服務從遠端工作

>[!NOTE]
> 本文說明如何利用 Azure 網路服務、Microsoft 網路和 Azure 合作夥伴生態系統，從遠端工作，並減輕因 COVID-19 危機而可能面臨的網路問題。

本文說明可供組織用來為使用者設定遠端存取的選項，或在尖峰使用率期間，以額外的容量補充其現有解決方案。 網路架構設計人員面臨下列挑戰：

- 解決網路使用率的增加。
- 為公司和客戶的更多員工提供可靠的連線能力。
- 提供全球遠端位置的連線能力。

並非所有網路（例如私人 WAN 和公司核心網路）都會遭遇尖峰遠端背景工作負載的擁塞。 這些瓶頸通常只會在家庭寬頻網路和公司內部部署網路的 VPN 閘道中報告。

網路規劃人員可以藉由記住不同的流量類型需要不同的網路處理優先順序以及一些智慧型負載重新導向/散發，協助減輕瓶頸並減輕網路擁塞。 例如，醫生與病人互動的即時 telepresence medecine 流量，是高重要性和延遲/抖動的敏感性。 然而，在儲存體之間複寫相同的流量並不會延遲敏感性。 先前的流量必須透過具有更高服務品質的最佳網路路徑來路由傳送;不過，您可以透過次佳路由來路由傳送之後的流量。



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>分享我們的最佳做法-Azure 網路是針對彈性和高可用性所設計

Azure 的設計目的是為了承受資源使用率突然變化，而且可以在尖峰使用率期間大幅提供協助。 此外，Microsoft 也會維護並操作其中一個世界最大的網路。 Microsoft 的網路已針對高可用性而設計，可承受不同類型的失敗：從單一網路元素失敗到整個區域失敗。

Microsoft 網路的設計目的是為了符合需求，並針對不同類型的網路流量提供最佳效能，包括延遲 Skype 和小組、CDN、即時海量資料分析、Azure 儲存體、Bing 和 Xbox 的敏感多媒體流量。 為了針對不同類型的流量提供最佳效能，Microsoft 網路會吸引所有目的地的流量，或想要以盡可能接近流量來源的資源傳輸。

>[!NOTE] 
>使用下面所述的 Azure 網路功能，運用 Microsoft 全球網路的流量遊樂設施行為，以提供更好的客戶網路體驗。 Microsoft 網路的「流量遊樂設施」行為可協助您儘快從第一個/最後一英里網路（可能會在尖峰使用率期間遇到擁塞）停止載入流量。
>

## <a name="enable-employees-to-work-remotely"></a>讓員工可以從遠端工作

Azure VPN 閘道支援點對站（P2S）和站對站（S2S） VPN 連線。 使用 Azure VPN 閘道，您可以調整員工的連線，以安全地存取您的 Azure 已部署資源和內部部署資源。 如需詳細資訊，請參閱[如何讓使用者在遠端工作](../vpn-gateway/work-remotely-support.md)。 

如果您使用安全通訊端通道通訊協定（SSTP），則並行連線數目限制為128。 若要取得較高的連接數目，建議您轉換為 OpenVPN 或 IKEv2。 如需詳細資訊，請參閱[從 SSTP 轉換至 OpenVPN 通訊協定或 IKEv2](../vpn-gateway/ikev2-openvpn-from-sstp.md
)。

若要存取部署在 Azure 中的資源，遠端開發人員可以使用 Azure 防禦解決方案（而不是 VPN 連線）來取得安全的 shell 存取（RDP 或 SSH），而不需要存取 Vm 上的公用 Ip。 如需詳細資訊，請參閱[使用 Azure 防禦從遠端工作](../bastion/work-remotely-support.md)。

若要匯總大規模 VPN 連線，以支援不同內部部署全球位置、不同區域中樞和輪輻虛擬網路中的資源之間的任何連線，以及將多個家庭寬頻網路的使用率優化，您可以使用 Azure 虛擬 WAN。 如需詳細資訊，請參閱尋求[因應家庭需求的工作？以下是 Azure 虛擬 WAN 可提供協助的地方](../virtual-wan/work-remotely-support.md)。

另一種支援遠端工作的方法，就是部署裝載于 Azure 虛擬網路中的虛擬桌面基礎結構（VDI），並使用 Azure 防火牆進行保護。 例如，Windows 虛擬桌面（WVD）是在 Azure 中執行的桌面和應用程式虛擬化服務。 有了 Windows 虛擬桌面，您就可以在 Azure 訂用帳戶中設定可擴充且彈性的環境，而不需要執行任何額外的閘道伺服器。 您只負責虛擬網路中的 WVD 虛擬機器。 如需詳細資訊，請參閱[Azure 防火牆遠端工作支援](../firewall/remote-work-support.md)。 

Azure 也有一組豐富的 eco 系統合作夥伴。 我們的合作夥伴在 Azure 上的網路虛擬裝置也可以協助調整 VPN 連線能力。 如需詳細資訊，請參閱[遠端工作的網路虛擬裝置（NVA）考慮](../vpn-gateway/nva-work-remotely-support.md)。

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>延伸員工的連線以存取全域散發的資源

下列 Azure 服務有助於讓員工存取您的全域散發資源。 您的資源可以位於任何 Azure 區域、內部部署網路，甚至是其他公用或私人雲端。 

- **Azure 虛擬網路對等互連**：如果您在多個 azure 區域中部署資源，以及（或）使用多個虛擬網路匯總遠端工作員工的連線能力，您可以使用虛擬網路對等互連，在多個 azure 虛擬網路之間建立連線能力。 如需詳細資訊，請參閱[虛擬網路對等互連][VNet-peer]。

- **AZURE VPN 解決方案**：針對透過 P2S 或 S2S VPN 連線至 Azure 的遠端員工，您可以設定內部部署網路和 Azure VPN 閘道之間的 S2S VPN，以啟用對內部部署網路的存取。 如需詳細資訊，請參閱[建立站對站][S2S]連線。

- **Expressroute**：使用 expressroute 私人對等互連，您可以在您的 Azure 部署與內部部署基礎結構或共置設施中的基礎結構之間啟用私人連線能力。 透過 Microsoft 對等互連的 ExpressRoute 也允許從您的內部部署網路存取 Microsoft 中的公用端點。 ExpressRoute 連線不會經過公用網際網路。 這些服務提供安全的連線能力、可靠性、更高的輸送量，且延遲比一般透過網際網路的連接更低且一致。 如需詳細資訊，請參閱 [ExpressRoute 概觀][ExR]。 利用現有的網路提供者，您已經成為[ExpressRoute 合作夥伴生態系統][ExR-eco]的一部分，有助於減少取得與 Microsoft 的大型頻寬連線的時間。  使用[ExpressRoute Direct][ExR-D] ，您可以直接將內部部署網路連線到 Microsoft 骨幹。 ExpressRoute Direct 提供兩個不同的線路費率選項：雙 10 Gbps 或 100 Gbps。 

- **Azure 虛擬 wan**： AZURE 虛擬 wan 可讓您的 VPN 連線與 ExpressRoute 線路之間有順暢的互通性。 如先前所述，在不同的區域中樞和輪輻虛擬網路中，Azure 虛擬 WAN 也支援不同內部內部部署全球位置的資源之間的任何連線

## <a name="scale-customer-connectivity-to-frontend-resources"></a>調整客戶對前端資源的連線能力

當有更多人上線時，許多公司網站都會增加客戶流量。 Azure 應用程式閘道可以協助管理這種增加的前端工作負載。 如需詳細資訊，請參閱[應用程式閘道高流量支援](../application-gateway/high-traffic-support.md)。

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Microsoft 對多雲端流量的支援

針對您在其他公用雲端中的部署，Microsoft 可以提供全域連線能力。 Azure 虛擬 WAN、VPN 或 ExpressRoute 在此方面可以提供協助。 若要將從 Azure 到其他雲端的連線能力擴充，您可以設定兩個雲端之間的 S2S VPN。 您也可以使用 ExpressRoute 來建立從 Azure 到其他公用雲端的連線能力。 Oracle 雲端是 ExpressRoute 合作夥伴生態系統的一部分。 您可以[設定 Azure 和 Oracle 雲端基礎結構之間的直接互連][Az-OCI]。 大部分屬於 ExpressRoute 合作夥伴生態系統的服務提供者，也提供與其他公用雲端的私人連線能力。 利用這些服務提供者，您可以在 Azure 中的部署與透過 ExpressRoute 的其他雲端之間建立私人連線。

## <a name="next-steps"></a>後續步驟

下列文章將討論如何使用不同的 Azure 網路功能來調整使用者，以進行遠端工作：

| **篇** | **說明** |
| --- | --- |
| [如何讓使用者從遠端工作](../vpn-gateway/work-remotely-support.md) | 查看可用的選項來設定使用者的遠端存取，或為您的組織提供額外的容量來補充其現有解決方案。|
| [要努力滿足家庭需求的工作嗎？以下是 Azure 虛擬 WAN 可提供協助的地方](../virtual-wan/work-remotely-support.md) | 使用 Azure 虛擬 WAN 來解決組織的遠端連線需求。|
| [應用程式閘道高流量支援](../application-gateway/high-traffic-support.md) | 使用應用程式閘道搭配 Web 應用程式防火牆（WAF），以提供可擴充且安全的方式來管理 web 應用程式的流量。 |
| [遠端工作的網路虛擬裝置（NVA）考慮](../vpn-gateway/nva-work-remotely-support.md)|請參閱在 Azure 中利用 Nva 來提供遠端存取解決方案的指引。 |
| [從 SSTP 轉換至 OpenVPN 通訊協定或 IKEv2](https://go.microsoft.com/fwlink/?linkid=2124112) | 藉由轉換成 OpenVPN 通訊協定或 IKEv2，克服 SSTP 的128同時連線限制。|
| [使用 Azure 防禦從遠端工作](../bastion/work-remotely-support.md) | 直接在 Azure 入口網站中，為 Azure 虛擬網路內的虛擬機器提供安全且順暢的 RDP/SSH 連線，而不需要使用公用 IP 位址。 |
| [使用 Azure ExpressRoute 建立混合式連線以支援遠端使用者](../expressroute/work-remotely-support.md) | 使用 ExpressRoute 進行混合式連線，讓您組織中的使用者可以從遠端工作。|
| [Azure 防火牆遠端工作支援](../firewall/remote-work-support.md)|使用 Azure 防火牆來保護您的 Azure 虛擬網路資源。 |

<!--Link References-->
[VNet-peer]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[S2S]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[ExR-eco]: https://docs.microsoft.com/azure/expressroute/expressroute-locations
[ExR-D]: https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about
[Az-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking
