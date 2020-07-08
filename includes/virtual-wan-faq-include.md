---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/26/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 98b82047c1744fbd63ecd3665e804e9d8f6c5221
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565975"
---
### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>使用者是否需要具有 SD-WAN/VPN 裝置的中樞與輪輻才能使用 Azure 虛擬 WAN？

虛擬 WAN 提供內建在單一窗格中的許多功能，例如站對站 VPN 連線能力、使用者/P2S 連線能力、ExpressRoute 連線能力、虛擬網路連線能力、VPN ExpressRoute 互連能力、VNet 對 VNet 可轉移連線、集中式路由、Azure 防火牆和防火牆管理員安全性、監視、ExpressRoute 加密，以及許多其他功能。 您不需要讓所有這些使用案例開始使用虛擬 WAN。 您只需要一個使用案例就可以開始使用。 虛擬 WAN 架構是一種中樞和輪輻架構，具有內建的規模和效能，其中分支（VPN/SD-WAN 裝置）、使用者（Azure VPN 用戶端、openVPN 或 IKEv2 用戶端）、ExpressRoute 線路、虛擬網路作為輪輻至虛擬中樞。 所有中樞都會在標準虛擬 WAN 中以完整網格連線，讓使用者可以輕鬆地使用 Microsoft 骨幹進行任意點對任意點 (任何輪輻) 的連線。 針對具有 SD-WAN/VPN 裝置的中樞與輪輻，使用者可以在 Azure 虛擬 WAN 入口網站中手動設定，或使用虛擬 WAN 合作夥伴 CPE (SD-WAN/VPN) 來設定與 Azure 的連線。 虛擬 WAN 合作夥伴為連線能力提供自動化功能，這是將裝置資訊匯出至 Azure、下載 Azure 設定，並建立與 Azure 虛擬 WAN 中樞的連線。 針對點對站/使用者 VPN 連線能力，我們支援[AZURE VPN 用戶端](https://go.microsoft.com/fwlink/?linkid=2117554)、OpenVPN 或 IKEv2 用戶端。 

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>您可以停用虛擬 WAN 中的全網狀中樞嗎？

虛擬 WAN 有兩種：基本和標準。 在基本虛擬 WAN 中，中樞不是網狀網格。 在標準的虛擬 WAN 中，集線器會在第一次設定虛擬 WAN 時自動連線。 使用者不需要執行任何特定動作。 使用者也不需要停用或啟用功能來取得網狀中樞。 虛擬 WAN 提供您許多路由選項，以在任何輪輻（VNet、VPN 或 ExpressRoute）之間進行流量。 它提供輕鬆的完整網狀中樞，並彈性地根據您的需求來路由傳送流量。 

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>如何在虛擬 WAN 中處理可用性區域和復原？

虛擬 WAN 是中樞內所提供的中樞和服務集合。 使用者可以根據自己的需求來擁有多個虛擬 wan。 在虛擬 WAN 中樞中，有多個服務，例如 VPN、ExpressRoute 等等。如果區域支援可用性區域，這些服務都會部署在可用性區域區域中。 如果區域在中樞的初始部署之後變成可用性區域，使用者可以重新建立閘道，這會觸發可用性區域部署。 所有閘道都會以主動-主動的形式布建在中樞內，也就是說，在中樞內會有內建的復原功能。 如果使用者想要跨區域進行復原，則可以連接到多個中樞。 雖然虛擬 WAN 的概念是全域的，但實際的虛擬 WAN 資源是以 Resource Manager 為基礎，並已部署區域內。 如果虛擬 WAN 區域本身發生問題，該虛擬 WAN 中的所有中樞都會繼續正常運作，但在虛擬 WAN 區域可供使用之前，使用者將無法建立新的中樞。

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Azure 虛擬 WAN 使用者 VPN (點對站) 支援哪些用戶端？

虛擬 WAN 支援 [Azure VPN 用戶端](https://go.microsoft.com/fwlink/?linkid=2117554)、OpenVPN 用戶端或任何 IKEv2 用戶端。 Azure VPN Client 支援 Azure AD 驗證，但至少需要 Windows 10 用戶端作業系統版本 17763.0 或更高版本。  OpenVPN 用戶端可支援以憑證為基礎的驗證。 一旦在閘道上選取了以憑證為基礎的驗證，您就會看到*vpnconfig.ovpn*檔案下載到您的裝置。 IKEv2 同時支援憑證和 RADIUS 驗證。 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>針對使用者 VPN (點對站)，為什麼 P2S 用戶端集區會分割成兩個路由？

每個閘道都有兩個執行個體，分割可讓每個閘道執行個體為連線的用戶端獨立配置用戶端 IP，而來自虛擬網路的流量也會路由回正確的閘道執行個體，以避開閘道間的執行個體躍點。

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>如何新增 P2S 用戶端的 DNS 伺服器？

有兩個選項可新增 P2S 用戶端的 DNS 伺服器。 第一個方法是慣用的，因為它會將自訂 DNS 伺服器新增至閘道，而不是用戶端。

1. 使用下列 PowerShell 腳本來新增自訂 DNS 伺服器。 取代您環境的值。

   ```powershell
   // Define variables
   $rgName = "testRG1"
   $virtualHubName = "virtualHub1"
   $P2SvpnGatewayName = "testP2SVpnGateway1"
   $vpnClientAddressSpaces = 
   $vpnServerConfiguration1Name = "vpnServerConfig1"
   $vpnClientAddressSpaces = New-Object string[] 2
   $vpnClientAddressSpaces[0] = "192.168.2.0/24"
   $vpnClientAddressSpaces[1] = "192.168.3.0/24"
   $customDnsServers = New-Object string[] 2
   $customDnsServers[0] = "7.7.7.7"
   $customDnsServers[1] = "8.8.8.8"
   $virtualHub = $virtualHub = Get-AzVirtualHub -ResourceGroupName $rgName -Name $virtualHubName
   $vpnServerConfig1 = Get-AzVpnServerConfiguration -ResourceGroupName $rgName -Name $vpnServerConfiguration1Name

   // Specify custom dns servers for P2SVpnGateway VirtualHub while creating gateway
   createdP2SVpnGateway = New-AzP2sVpnGateway -ResourceGroupName $rgname -Name $P2SvpnGatewayName -VirtualHub $virtualHub -VpnGatewayScaleUnit 1 -VpnClientAddressPool $vpnClientAddressSpaces -VpnServerConfiguration $vpnServerConfig1 -CustomDnsServer $customDnsServers

   // Specify custom dns servers for P2SVpnGateway VirtualHub while updating existing gateway
   $P2SVpnGateway = Get-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName
   $updatedP2SVpnGateway = Update-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName  -CustomDnsServer $customDnsServers 

   // Re-generate Vpn profile either from PS/Portal for Vpn clients to have the specified dns servers
   ```
2. 或者，如果您使用適用於 Windows 10 的 Azure VPN Client，您可以修改下載的設定檔 XML 檔案，並在匯入該檔案之前，新增 **\<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>** 標記。

   ```powershell
      <azvpnprofile>
      <clientconfig>

          <dnsservers>
              <dnsserver>x.x.x.x</dnsserver>
              <dnsserver>y.y.y.y</dnsserver>
          </dnsservers>
    
      </clientconfig>
      </azvpnprofile>
   ```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>使用者 VPN (點對站) 可支援多少用戶端？

每個使用者 VPN P2S 閘道都有兩個執行個體，而每個執行個體都可隨著縮放單位變更來支援特定使用者數目。 縮放單位1-3 支援500連線、縮放單位4-6 支援1000連線、縮放單位7-12 支援5000連線和縮放單位13-20 支援最多10000連線。 

例如，我們假設使用者選擇 1 作為縮放單位。 每個縮放單位都會表示已部署主動-主動閘道，而每個實例（在此案例中為2）會支援最多500個連線。 由於每個閘道可以取得500個連線 * 2，因此不表示您為此縮放單位規劃1000而不是500。 如果您超過建議的連線計數，可能需要服務實例，在這種情況中，額外500的連線可能會中斷。 此外，如果您決定在縮放單位上相應增加或相應減少，或變更 VPN 閘道上的點對站設定，請務必規劃停機時間。

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Azure 虛擬網路閘道 (VPN 閘道) 與 Azure 虛擬 WAN VPN 閘道之間有何差異？

虛擬 WAN 提供大規模站對站連線，而且是針對輸送量、延展性和易用性而建置的。 當您將網站連線到虛擬 WAN VPN 閘道時，它與使用閘道類型「VPN」的一般虛擬網路閘道不同。 同樣地，當您將 ExpressRoute 線路連線到虛擬 WAN 中樞時，它會使用與使用閘道類型「ExpressRoute」的一般虛擬網路閘道不同的 ExpressRoute 閘道資源。 

虛擬 WAN 為 VPN 和 ExpressRoute 最多支援 20 Gbps 的彙總輸送量。 虛擬 WAN 也有自動化功能，可與 CPE 分支裝置夥伴的生態系統建立連線。 CPE 分支裝置具有內建的自動化功能，可 autoprovisions 並聯機到 Azure 虛擬 WAN。 這些裝置都可以從 SD-WAN 和 VPN 合作夥伴日益成長的生態系統中取用。 請參閱[慣用的夥伴清單](../articles/virtual-wan/virtual-wan-locations-partners.md)。

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>虛擬 WAN 與 Azure 虛擬網路閘道有何不同？

虛擬網路閘道 VPN 限定為 30 個通道。 對於連線，您應將虛擬 WAN 用於大規模的 VPN。 每個區域最多可連線 1000 個分支連線 (虛擬中樞)，每個中樞有 20 Gbps 的彙總。 連線是從內部部署 VPN 裝置到虛擬中樞的主動-主動通道。 您可以在每個區域都有一個中樞，這表示您可以跨中樞連線到超過 1,000 個分支。

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>什麼是虛擬 WAN 閘道縮放單位

縮放單位是定義來挑選虛擬中樞內閘道匯總輸送量的單位。 1個縮放單位的 VPN = 500 Mbps。 1 個 ExpressRoute 縮放單位等於 2 Gbps。 範例：10個縮放單位的 VPN 會隱含 500 Mbps * 10 = 5 Gbps

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>啟動時支援哪些裝置提供者 (虛擬 WAN 合作夥伴)？

目前有許多合作夥伴支援完全自動化的虛擬 WAN 體驗。 如需詳細資訊，請參閱[虛擬 WAN 合作夥伴](../articles/virtual-wan/virtual-wan-locations-partners.md)。

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>虛擬 WAN 合作夥伴自動化步驟是什麼？

若要了解合作夥伴自動化步驟，請參閱[虛擬 WAN 合作夥伴自動化](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)。

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>我是否必須使用偏好的合作夥伴裝置？

否。 您可以使用任何支援 VPN 且符合 IKEv2/IKEv1 IPsec 支援需求的裝置。 虛擬 WAN 也有 CPE 合作夥伴解決方案，可將 Azure 虛擬 WAN 的連線自動化，讓您更輕鬆地設定大規模的 IPsec VPN 連線。

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>虛擬 WAN 合作夥伴如何將與 Azure 虛擬 WAN 的連線自動化？

軟體定義的連線解決方案通常會使用控制器或裝置佈建中心，來管理它們的分支裝置。 控制器可以使用 Azure API，將與 Azure 虛擬 WAN 的連線自動化。 自動化包括上傳分支資訊、下載 Azure 設定、將 IPSec 通道設定到 Azure 虛擬中樞，以及自動設定從分支裝置到 Azure 虛擬 WAN 的連線。 當您有數百個分支時，使用虛擬 WAN CPE 夥伴進行連線很方便，因為上線體驗可讓您不需要安裝、設定及管理大規模的 IPsec 連線。 如需詳細資訊，請參閱[虛擬 WAN 合作夥伴自動化](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)。

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>如果我使用的裝置不在虛擬 WAN 合作夥伴清單中又該如何？ 我仍可用它來連線到 Azure 虛擬 WAN VPN 嗎？

是的，只要裝置支援 IPsec IKEv1 或 IKEv2 即可。 虛擬 WAN 合作夥伴協力會將裝置到 Azure VPN 端點的連線自動化。 這表示自動化的步驟，例如「分支資訊上傳」、「IPsec 與組態」和「連線」。由於您的裝置不是來自虛擬 WAN 合作夥伴生態系統，因此您必須執行繁重的工作，以手動方式取得 Azure 組態，並更新您的裝置以設定 IPsec 連線。

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>未列在產品發佈合作夥伴清單中的新合作夥伴要如何上線？

所有虛擬 WAN API 都是開放 API。 您可以前往[虛擬 WAN 合作夥伴自動化](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)檔，以評估技術的可行性。 理想的合作夥伴是有裝置可用來佈建 IKEv1 或 IKEv2 IPSec 連線的合作夥伴。 當公司根據上述的自動化指導方針完成其 CPE 裝置的自動化工作之後，您就可以在 azurevirtualwan@microsoft.com 這裡[透過合作夥伴連接]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners)到。 如果您是想要將特定公司解決方案列為虛擬 WAN 合作夥伴的客戶，請將電子郵件傳送至，讓該公司與虛擬 WAN 聯繫 azurevirtualwan@microsoft.com 。

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>虛擬 WAN 如何支援 SD-WAN 裝置？

虛擬 WAN 合作夥伴會將 IPsec 連線到 Azure VPN 端點自動化。 如果虛擬 WAN 合作夥伴是 SD WAN 提供者，那就表示 SD-WAN 控制器會管理自動化和 IPsec 與 Azure VPN 端點的連線。 如果 SD-WAN 裝置的任何專用 SD-WAN 功能需要它自己的端點，而不是 Azure VPN，您可以在 Azure VNet 中部署 SD-WAN 端點，並與 Azure 虛擬 WAN 並存。

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>有多少部 VPN 裝置可以連線到單一中樞？

每個虛擬中樞支援多達 1,000 個連線。 每個連線是由四個連結組成，每個連結連線支援兩個其設定為主動-主動的通道。 通道會在 Azure 虛擬中樞 VPN 閘道上終止。 連結代表分支/VPN 裝置上的實體 ISP 連結。

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>什麼是 Azure 虛擬 WAN 的分支連線？

從分支或 VPN 裝置到 Azure 虛擬 WAN 的連線，不只是連線到虛擬中樞內的 VPN 網站和 Azure VPN 閘道的 VPN 連線。

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>內部部署 VPN 裝置是否可以連線到多個中樞？

是。 開始時的流量會從內部部署裝置流至最近的 Microsoft 網路邊緣，然後流至虛擬中樞。

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>虛擬 WAN 是否有任何新的 Resource Manager 資源可用？
  
是，虛擬 WAN 有新的 Resource Manager 資源。 如需詳細資訊，請參閱[概觀](../articles/virtual-wan/virtual-wan-about.md)。

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>是否可以使用 Azure 虛擬 WAN 部署及使用我的最愛網路虛擬設備 (在 NVA VNet 中)？

是，您可以將您最愛的網路虛擬設備 (NVA) VNet 連線到 Azure 虛擬 WAN。

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>我可以在虛擬中樞內建立網路虛擬設備嗎？

無法在虛擬中樞內部署網路虛擬設備 (NVA)。 不過，您可以在連線到虛擬中樞的輪輻 VNet 中建立它，並根據您的需求啟用適當的路由以導向流量。

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>輪幅 VNet 是否可以有虛擬網路閘道？

否。 如果輪幅 VNet 連線到虛擬中樞，則不能有虛擬網路閘道。

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>VPN 連線能力是否支援 BGP？

是，有支援 BGP。 當您建立 VPN 網站時，可以在其中提供 BGP 參數。 這表示為在 Azure 中該網站建立的任何連線將會針對 BGP 啟用。

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>虛擬 WAN 是否有任何授權或價格資訊？

是。 請參閱[定價](https://azure.microsoft.com/pricing/details/virtual-wan/)頁面。

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>是否可以使用 Resource Manager 範本來建構 Azure 虛擬 WAN？

可以使用[快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network)來建立一個簡易設定的虛擬 WAN，其中包含一個中樞和一個 vpnsite。 虛擬 WAN 主要是 REST 或入口網站驅動的服務。

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>連線到虛擬中樞的輪輻 VNet 之間，是否可以彼此通訊 (V2V 傳輸)？

是。 標準虛擬 WAN 透過 Vnet 所連接的虛擬 WAN 中樞，支援 VNet 對 VNet 可轉移的連線能力。 在虛擬 WAN 術語中，我們將這些路徑稱為「本機虛擬 WAN VNet 傳輸」(若 Vnet 連線至單一區域內的虛擬 WAN 中樞)，以及「全域虛擬 WAN VNet 傳輸」(若透過多個虛擬 WAN 中樞跨兩個或更多區域連接 Vnet)。 在某些情況下，除了本機或全域虛擬 WAN VNet 傳輸外，輪輻 Vnet 也可以使用[虛擬網路對等互連](../articles/virtual-network/virtual-network-peering-overview.md)彼此直接對等互連。 在此情況下，VNet 對等互連會優先于透過虛擬 WAN 中樞的可轉移連線。

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>虛擬 WAN 中是否允許分支對分支連線？

是，在虛擬 WAN 中可使用分支對分支連線。 分支在概念上適用于 VPN 網站、ExpressRoute 線路，或點對站/使用者 VPN 使用者。 預設會啟用 [分支至分支]，而且可以在 [WAN 設定] 中找到。 這可讓 VPN 分支/使用者連線到其他 VPN 分支，以及在 VPN 和 ExpressRoute 使用者之間啟用傳輸連線能力。

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>分支對分支流量是否會透過 Azure 虛擬 WAN 而周遊？

是。

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>虛擬 WAN 是否需要來自每個網站的 ExpressRoute？

否。 虛擬 WAN 不需要來自每個網站的 ExpressRoute。 您的網站可以使用 ExpressRoute 線路連線到提供者網路。 對於使用 ExpressRoute 連線至虛擬中樞以及將 IPsec VPN 連接到相同中樞的網站，虛擬中樞會提供 VPN 和 ExpressRoute 使用者之間的傳輸連接。

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>使用 Azure 虛擬 WAN 時，是否有網路輸送量或連線限制？

網路輸送量是虛擬 WAN 中樞中的每個服務。 雖然您可以擁有任意數量的虛擬 Wan，但每個虛擬 WAN 每個區域允許1個中樞。 在每個中樞中，VPN 匯總輸送量最高可達 20 Gbps，而 ExpressRoute 匯總輸送量最高可達 20 Gbps，而使用者 VPN/點對站 VPN 匯總輸送量最高可達 20 Gbps。 虛擬中樞內的路由器最多可支援 50 Gbps 的 VNet 對 VNet 流量流程，並假設虛擬 WAN 中樞的所有 Vnet 都有 2000 VM 工作負載。

當 VPN 網站連線到中樞時，會使用連線來執行此動作。 虛擬 WAN 支援每個虛擬中樞最多1000個連線或2000個 IPsec 通道。 當遠端使用者連線到虛擬中樞時，他們會連線到 P2S VPN 閘道，它最多可支援10000個使用者，這取決於針對虛擬中樞內的 P2S VPN 閘道所選擇的縮放單位（頻寬）。

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>一個 VPN 通道和一個連線的 VPN 輸送量總計是多少？

根據所選 VPN 閘道的縮放單位，中樞的 VPN 輸送量總計為 20 Gbps。 輸送量會由所有現有的連線共用。 連線中的每個通道最多可支援 1 Gbps。

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>我在入口網站中看不到虛擬中樞的 20 Gbps 設定。 我要如何設定？

在入口網站中，導覽至中樞內的 VPN 閘道，並按一下縮放單位，以將其變更為適當的設定。

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>虛擬 WAN 是否允許內部部署裝置以平行方式使用多個 ISP，還是一律為單一 VPN 通道？

內部部署裝置解決方案可以套用流量原則，將多個通道的流量引導至 Azure 虛擬 WAN 中樞（虛擬中樞中的 VPN 閘道）。

### <a name="what-is-global-transit-architecture"></a>什麼是全域傳輸架構？

如需全域傳輸架構的詳細資訊，請參閱[全域傳輸網路架構和虛擬 WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md)。

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>流量在 Azure 骨幹上的路由傳送方式為何？

流量會依循下列模式：分支裝置->ISP->Microsoft 網路邊緣->Microsoft DC (中樞 VNet)->Microsoft 網路邊緣->ISP->分支裝置

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>在此模型中，每個站台需要什麼？ 有網際網路連線就可以嗎？

是。 支援 IPsec 的網際網路連線與實體裝置，最好是我們的整合式[虛擬 WAN 夥伴](../articles/virtual-wan/virtual-wan-locations-partners.md)所提供的。 或者，您可以從慣用的裝置手動管理 Azure 的設定與連線。

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>如何在連線 (VPN、ExpressRoute 或虛擬網路) 中啟用預設路由 (0.0.0.0/0)：

如果連線上的旗標為「啟用」，則虛擬中樞可以將學習到的預設路由傳播到虛擬網路/站對站 VPN/ExpressRoute 連線。 當使用者編輯虛擬網路連線、VPN 連線或 ExpressRoute 連線時，此旗標為可見。 根據預設，當網站或 ExpressRoute 線路連線至中樞時，會停用此旗標。 新增虛擬網路連線以將 VNet 連線到虛擬中樞時，依預設會啟用旗標。 預設路由不是源自於虛擬 WAN 中樞；如果虛擬 WAN 中樞由於在中樞內部署防火牆而學習預設路由，或其他連線的網站已啟用強制通道，則會傳播預設路由。

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>虛擬 WAN 中的虛擬中樞如何從多個中樞選取路由的最佳路徑

如果虛擬中樞從多個遠端中樞學習相同的路由，它所決定的順序如下所示：

1. 最長前置詞相符。
2. 透過 interhub 的本機路由。
3. 透過 BGP 的靜態路由：這是由虛擬中樞路由器所進行的決策所組成的內容。 不過，如果決策者是透過 BGP 通告路由的 VPN 閘道，或提供靜態位址首碼，則靜態路由可能會優先于 BGP 路由。
4. 透過 VPN 的 ExpressRoute （ER）：當內容為本機中樞時，比起 VPN 優先使用 ER。 ExpressRoute 線路之間的傳輸連線只能透過全球範圍提供。 因此，在 ExpressRoute 線路連線到一個中樞，而且有另一個 ExpressRoute 線路連接到具有 VPN 連線的不同集線器的情況下，VPN 可能會慣用於中樞間的案例。
5. 做為路徑長度。

### <a name="does-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>虛擬 WAN 中樞允許 ExpressRoute 線路之間的連線。

ER 到 ER 之間的傳輸一律透過全球範圍進行。 虛擬中樞閘道會部署在 DC 或 Azure 區域中。 當兩個 ExpressRoute 線路透過全球範圍連線時，就不需要從邊緣路由器到虛擬中樞 DC 的所有流量。

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-circuits-or-vpn-connections"></a>是否有 Azure 虛擬 WAN 線路或 VPN 連線中的權數概念

當有多個 ExpressRoute 線路連線到虛擬中樞時，連接上的路由權數會提供一個機制，讓虛擬中樞的 ExpressRoute 能夠優先使用一個線路。 沒有任何機制可在 VPN 連線上設定權數。 Azure 一律偏好透過單一中樞內的 VPN 連線使用 ExpressRoute 連線。

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>當兩個集線器（中樞1和2）連線，而且有連線到兩個中樞的 ExpressRoute 線路時，連接到中樞1的 VNet 路徑會連線到中樞2中連線的 VNet 嗎？

目前的行為是偏好透過中樞對中樞的 ExpressRoute 線路路徑來進行 VNet 對 VNet 連線。 不過，在虛擬 WAN 設定中不鼓勵這麼做。 虛擬 WAN 小組正致力於修正，以透過 ExpressRoute 路徑啟用中樞對中樞的喜好設定。 建議適用于多個 ExpressRoute 線路（不同的提供者）來連線到一個中樞，並使用虛擬 WAN 提供的中樞對中樞連線來進列區域間流量流程。

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>虛擬 WAN 中是否支援 IPv6？

虛擬 WAN 中樞和其閘道不支援 IPv6。 如果您有具有 IPv6 支援的 VNet，而且想要將 VNet 連線到虛擬 WAN，則目前不支援此案例。

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>虛擬 WAN 類型 (基本和標準) 之間有何差異？

請參閱[基本和標準虛擬 wan](../articles/virtual-wan/virtual-wan-about.md#basicstandard)。 如需價格資訊，請參閱 [價格](https://azure.microsoft.com/pricing/details/virtual-wan/) 頁面。