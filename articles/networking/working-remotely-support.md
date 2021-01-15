---
title: 使用 Azure 網路服務從遠端工作
description: 本頁面說明如何使用可讓您從遠端工作的 Azure 網路服務，以及如何減輕從遠端工作的人數增加而產生的流量問題。
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: 835be5b867826d75732c9482743ad6a4ca2900e7
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231837"
---
# <a name="working-remotely-using-azure-networking-services"></a>使用 Azure 網路服務從遠端工作

>[!NOTE]
> 本文說明如何使用 Azure 網路服務、Microsoft 網路和 Azure 合作夥伴生態系統，從遠端工作並減輕可能因 COVID-19-19 危機而面臨的網路問題。

本文說明可供組織用來為使用者設定遠端存取的選項，或在尖峰使用率期間，使用額外容量來補充現有解決方案的選項。 網路架構設計師面臨下列挑戰：

- 解決網路使用量的增加。
- 為公司和客戶的更多員工提供可靠安全的連線能力。
- 提供全球各地遠端位置的連線能力。

並非所有網路 (例如，私人 WAN 和企業核心網路) 體驗尖峰遠端背景工作負載的擁塞。 瓶頸通常只會在公司內部部署網路的家用寬頻網路和 VPN 閘道報告。

網路規劃人員有助於減輕瓶頸並減輕網路擁塞，請記住，不同的流量類型需要不同的網路處理優先順序以及某些智慧型載入重新導向/散發。 例如，對醫生的即時 telepresence medecine 交通，也就是高重要性和延遲/抖動的機密。 然而，在儲存體之間複寫相同的流量不會延遲敏感性。 先前的流量必須透過具有更高服務品質的最理想網路路徑來路由傳送;但可接受透過最接近的路由來路由傳送較新的流量。



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>共用我們的最佳作法-Azure 網路是針對彈性和高可用性所設計

Azure 的設計目的是為了承受資源使用率的突然變更，而且在尖峰使用率期間有很大的説明。 此外，Microsoft 還會維護並操作其中一個世界最大的網路。 Microsoft 的網路已針對高可用性而設計，可承受不同類型的失敗：從單一網路元素失敗到整個區域失敗。

Microsoft 網路的設計目的是為了符合需求，並為不同類型的網路流量提供最佳效能，包括 Skype 和團隊的延遲機密多媒體流量、CDN、即時的資料分析、Azure 儲存體、Bing 和 Xbox。 為了針對不同類型的流量提供最佳效能，Microsoft 網路會吸引所有目的地為或想要透過其資源盡可能接近流量來源的流量。

>[!NOTE] 
>使用下面所述的 Azure 網路功能，運用 Microsoft 全球網路的流量遊樂設施行為，以提供更好的客戶網路體驗。 Microsoft 網路的流量遊樂設施行為可協助您儘快從第一個/最後的英里網路（可能會在尖峰使用率期間遇到擁塞）中儘快載入流量。
>

## <a name="enable-employees-to-work-remotely"></a>讓員工從遠端工作

Azure VPN 閘道支援點對站 (P2S) 和站對站 (S2S) VPN 連接。 您可以使用 Azure VPN 閘道來調整員工的連線，以安全地存取您的 Azure 部署資源和內部部署資源。 如需詳細資訊，請參閱 [如何讓使用者能夠從遠端工作](../vpn-gateway/work-remotely-support.md)。 

如果您使用安全通訊端通道通訊協定 (SSTP) ，並行連線數目會限制為128。 若要取得更高的連接數目，建議您轉換為 OpenVPN 或 IKEv2。 如需詳細資訊，請參閱 [從 SSTP 轉換至 OpenVPN 通訊協定或 IKEv2](../vpn-gateway/ikev2-openvpn-from-sstp.md
)。

若要存取部署在 Azure 中的資源，遠端開發人員可以使用 Azure 防禦解決方案，而不是使用 VPN 連線來 (RDP 或 SSH) 取得安全的 shell 存取，而不需要存取 Vm 上的公用 Ip。 如需詳細資訊，請參閱 [使用 Azure 防禦從遠端工作](../bastion/work-remotely-support.md)。

若要匯總大規模 VPN 連線，以支援不同內部部署全域位置、不同區域中樞和輪輻虛擬網路中的資源之間的任何連線，以及優化多個家用寬頻網路的使用率，您可以使用 Azure 虛擬 WAN。 如需詳細資訊，請參閱 [如何滿足家用需求？以下是 Azure 虛擬 WAN 可提供協助的地方](../virtual-wan/work-remotely-support.md)。

另一種支援遠端工作的方式，就是將虛擬桌面基礎結構 (部署在 Azure 虛擬網路中，並使用 Azure 防火牆保護的 VDI) 。 例如，Windows 虛擬桌面 (WVD) 是在 Azure 中執行的桌面和應用程式虛擬化服務。 使用 Windows 虛擬桌面，您可以在 Azure 訂用帳戶中設定可擴充且彈性的環境，而不需要執行任何額外的閘道伺服器。 您只需負責虛擬網路中的 WVD 虛擬機器。 如需詳細資訊，請參閱 [Azure 防火牆遠端工作支援](../firewall/remote-work-support.md)。 

Azure 也有一組豐富的 eco 系統合作夥伴。 Azure 上的夥伴網路虛擬裝置也可協助調整 VPN 連線能力。 如需詳細資訊，請參閱 [網路虛擬裝置 (NVA 遠端工作的) 考慮](../vpn-gateway/nva-work-remotely-support.md)。

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>延伸員工的連線以存取全域散發的資源

下列 Azure 服務可協助員工存取您的全球散發資源。 您的資源可以位於任何 Azure 區域、內部部署網路，甚至其他公用或私用雲端中。 

- **Azure 虛擬網路對等互連**：如果您在多個 azure 區域中部署資源，並（或）如果您使用多個虛擬網路來匯總遠端工作員工的連線能力，您可以使用虛擬網路對等互連來建立多個 azure 虛擬網路之間的連線能力。 如需詳細資訊，請參閱[虛擬網路對等互連][VNet-peer]。

- 以 **AZURE vpn 為基礎的解決方案**：適用于透過 P2S 或 S2S VPN 連線到 Azure 的遠端員工，您可以在內部部署網路與 azure VPN 閘道之間設定 S2S VPN，藉以啟用對內部部署網路的存取。 如需詳細資訊，請參閱 [建立站對站][S2S]連線。

- **Expressroute**：使用 expressroute 私人對等互連，您可以在您的 Azure 部署與內部部署基礎結構或共置設施中的基礎結構之間啟用私人連線。 ExpressRoute （透過 Microsoft 對等互連）也允許從您的內部部署網路存取 Microsoft 中的公用端點。 ExpressRoute 連線不會經過公用網際網路。 它們提供安全的連線能力、可靠性、更高的輸送量，並具有比網際網路一般連線更低且一致的延遲。 如需詳細資訊，請參閱 [ExpressRoute 概觀][ExR]。 運用您現有的網路提供者（也就是 [ExpressRoute 合作夥伴生態系統][ExR-eco] 的一部分），可協助縮短與 Microsoft 進行大型頻寬連接的時間。  使用 [ExpressRoute Direct][ExR-D] ，您可以直接將內部部署網路連線到 Microsoft 骨幹。 ExpressRoute Direct 提供兩種不同的線路速率選項，雙達 10 Gbps 或 100 Gbps。 

- **Azure 虛擬 wan**： AZURE 虛擬 wan 允許在您的 VPN 連線與 ExpressRoute 線路之間進行順暢的互通性。 如先前所述，Azure 虛擬 WAN 也支援不同區域中樞和輪輻虛擬網路中不同內部內部部署全球位置的資源之間的任何連接

## <a name="scale-customer-connectivity-to-frontend-resources"></a>調整客戶對前端資源的連線能力

在更多使用者上線的時候，許多公司網站都有提高客戶流量的效率。 Azure 應用程式閘道可協助管理這個增加的前端工作負載。 如需詳細資訊，請參閱 [應用程式閘道高流量支援](../application-gateway/high-traffic-support.md)。

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Microsoft 對多雲端流量的支援

針對您在其他公用雲端中的部署，Microsoft 可以提供全球連線能力。 Azure 虛擬 WAN、VPN 或 ExpressRoute 可提供這方面的協助。 若要將連線從 Azure 延伸至其他雲端，您可以設定兩個雲端之間的 S2S VPN。 您也可以使用 ExpressRoute 建立從 Azure 到其他公用雲端的連線能力。 Oracle cloud 是 ExpressRoute 合作夥伴生態系統的一部分。 您可以 [設定 Azure 與 Oracle 雲端基礎結構之間的直接互連][Az-OCI]。 大部分屬於 ExpressRoute 合作夥伴生態系統一部分的服務提供者也會提供與其他公用雲端的私人連線能力。 利用這些服務提供者，您可以在 Azure 中的部署與透過 ExpressRoute 的其他雲端之間建立私人連線。

## <a name="next-steps"></a>後續步驟

下列文章會討論如何使用不同的 Azure 網路功能來調整使用者，以從遠端工作：

| **發行項** | **描述** |
| --- | --- |
| [如何讓使用者從遠端工作](../vpn-gateway/work-remotely-support.md) | 請參閱可用的選項，為使用者設定遠端存取，或使用您組織的額外容量來補充其現有解決方案。|
| [要努力滿足家用需求嗎？以下是 Azure 虛擬 WAN 可提供協助的地方](../virtual-wan/work-remotely-support.md) | 使用 Azure 虛擬 WAN 來滿足您組織的遠端連線需求。|
| [應用程式閘道高流量支援](../application-gateway/high-traffic-support.md) | 使用應用程式閘道搭配 Web 應用程式防火牆 (WAF) ，以可擴充且安全的方式來管理 Web 應用程式的流量。 |
| [網路虛擬裝置 (NVA 遠端工作的) 考慮](../vpn-gateway/nva-work-remotely-support.md)|瞭解如何在 Azure 中利用 Nva 來提供遠端存取解決方案的指引。 |
| [從 SSTP 轉換至 OpenVPN 通訊協定或 IKEv2](../vpn-gateway/ikev2-openvpn-from-sstp.md) | 藉由轉換成 OpenVPN 通訊協定或 IKEv2 來克服 SSTP 的128並行連線限制。|
| [使用 Azure 防禦從遠端工作](../bastion/work-remotely-support.md) | 直接在 Azure 入口網站中，提供安全且順暢的 RDP/SSH 連線到 Azure 虛擬網路內的虛擬機器，而不需使用公用 IP 位址。 |
| [使用 Azure ExpressRoute 建立混合式連線能力，以支援遠端使用者](../expressroute/work-remotely-support.md) | 使用 ExpressRoute 的混合式連線能力，讓您組織中的使用者可以從遠端工作。|
| [Azure 防火牆遠端工作支援](../firewall/remote-work-support.md)|使用 Azure 防火牆來保護您的 Azure 虛擬網路資源。 |

<!--Link References-->
[VNet-peer]: ../virtual-network/virtual-network-peering-overview.md
[S2S]: ../vpn-gateway/tutorial-site-to-site-portal.md
[ExR]: ../expressroute/expressroute-introduction.md
[ExR-eco]: ../expressroute/expressroute-locations.md
[ExR-D]: ../expressroute/expressroute-erdirect-about.md
[Az-OCI]: ../virtual-machines/workloads/oracle/configure-azure-oci-networking.md