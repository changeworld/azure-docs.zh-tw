---
title: Azure 防火牆常見問題集
description: Azure 防火牆的常見問題解答。 一個受控的雲端式網路安全性服務，可保護您的「Azure 虛擬網路」資源。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 45276884d59ac8d1d876e2225ac02bb51c3f74fc
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437718"
---
# <a name="azure-firewall-faq"></a>Azure 防火牆常見問題集

## <a name="what-is-azure-firewall"></a>何謂 Azure 防火牆？

Azure 防火牆是受控、雲端式網路安全性服務，可以保護您的 Azure 虛擬網路資源。 它是完全具狀態的防火牆即服務，具有內建的高可用性和不受限制的雲端延展性。 您可以橫跨訂用帳戶和虛擬網路集中建立、強制執行以及記錄應用程式和網路連線原則。

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Azure 防火牆中支援哪些功能？

* 具狀態防火牆即服務
* 內建高可用性且不受限制的雲端延展性
* FQDN 篩選
* FQDN 標記
* 網路流量篩選規則
* 輸出 SNAT 支援
* 輸入 DNAT 支援
* 跨 Azure 訂用帳戶和 VNET，集中建立、強制執行以及記錄應用程式和網路連線原則
* 與 Azure 監視器完全整合以進行記錄和分析

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Azure 防火牆的一般部署模型是什麼？

您可以將「Azure 防火牆」部署在任何虛擬網路上，但客戶通常會將其部署在中央虛擬網路上，然後以中樞和支點模型方式，將其他虛擬網路與其對等互連。 接著，您便可以從對等互連的虛擬網路設定預設路由，使其指向此中央防火牆虛擬網路。 支援全域 VNet 對等互連,但由於跨區域的潛在性能和延遲問題,不建議使用 Global VNet 對等互連。 為獲得最佳性能,應為每個區域部署一個防火牆。

此模型的優點是能夠集中控制不同訂用帳戶上的多個分支 VNET。 這也可讓您節省成本，因為您不需要在每個 VNet 中分別部署防火牆。 若要衡量節省的成本，應根據客戶流量模式，與相關的對等成本做比較。

## <a name="how-can-i-install-the-azure-firewall"></a>如何安裝 Azure 防火牆？

您可以透過使用 Azure 入口網站、PowerShell、REST API 或範本，來設定「Azure 防火牆」。 如需逐步指示，請參閱[教學課程：使用 Azure 入口網站部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)。

## <a name="what-are-some-azure-firewall-concepts"></a>有哪些 Azure 防火牆概念？

Azure 防火牆支援規則和規則集合。 規則集合是一組共用相同順序和優先順序的規則。 規則集合會依其優先順序執行。 網路規則集合的優先順序高於應用程式規則集合，而所有規則都將終止。

有三種類型的規則集合:

* *應用程式規則*:配置可以從子網訪問的完全限定的功能變數名稱 (FQDN)。
* *網路規則*:配置包含來源位址、協議、目標埠和目標位址的規則。
* *NAT 規則*:配置 DNAT 規則以允許傳入的 Internet 連接。

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Azure 防火牆是否支援輸入流量篩選？

Azure 防火牆支援輸入和輸出篩選。 入站保護通常用於非 HTTP/S 協定。 例如 RDP、SSH 及 FTP 通訊協定。 為了獲得最佳入站 HTTP/S 保護,請使用 Web 應用程式防火牆(如[Azure Web 應用程式防火牆 (WAF))。](../web-application-firewall/overview.md)

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Azure 防火牆支援哪些記錄和分析服務？

「Azure 防火牆」已與「Azure 監視器」整合，可檢視和分析防火牆記錄。 記錄可以傳送至 Log Analytics、「Azure 儲存體」或「事件中樞」。 這些記錄可以在 Log Analytics 中進行分析，或透過不同的工具 (例如 Excel 和 Power BI) 進行分析。 如需詳細資訊，請參閱[教學課程：監視 Azure 防火牆記錄](tutorial-diagnostics.md)。

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Azure 防火牆的運作方式與市集中現有的服務 (例如 NVA) 有何不同？

Azure 防火牆是一種基本防火牆服務，可以處理特定的客戶案例。 預計您將混合使用第三方 NVA 和 Azure 防火牆。 使用上，首重相輔相成。

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>應用程式閘道 WAF 與 Azure 防火牆有什麼不同？

Web 應用程式防火牆 (WAF) 是應用程式閘道的一個功能，可提供 Web 應用程式的集中式輸入保護，免於遭遇常見的攻擊和弱點。 「Azure 防火牆」可為非 HTTP/S 通訊協定 (例如 RDP、SSH、FTP) 提供輸入保護、為所有連接埠和通訊協定提供輸出網路層級的保護，以及為輸出 HTTP/S 提供應用程式層級的保護。

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>網路安全性群組 (NSG) 和 Azure 防火牆有什麼不同？

「Azure 防火牆」服務可補足網路安全性群組功能。 兩者結合時，可提供更好的「深度防禦」網路安全性。 網路安全性群組提供分散式網路層流量篩選，可限制每個訂用帳戶中虛擬網路內資源的流量。 「Azure 防火牆」是完全具狀態的集中式網路防火牆即服務，可跨不同的訂用帳戶和虛擬網路，提供網路層級和應用程式層級的保護。

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>Azure 防火牆子網上支援網路安全組 (NSG)嗎?

Azure 防火牆是具有多個保護層的託管服務,包括具有 NIC 級別 NSG 的平台保護(不可查看)。  Azure 防火牆子網不需要子網級別 NSG,並且已禁用以確保沒有服務中斷。

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>如何使用我的服務端點設定 Azure 防火牆？

若要安全存取 PaaS 服務，建議使用服務端點。 您可以選擇在「Azure 防火牆」子網路中啟用服務端點，並在已連線的支點虛擬網路上停用它們。 這樣,您就受益於這兩個功能:服務終結點安全性和所有流量的中央日誌記錄。

## <a name="what-is-the-pricing-for-azure-firewall"></a>Azure 防火牆的定價為何？

請參閱[Azure 防火牆定價](https://azure.microsoft.com/pricing/details/azure-firewall/)。

## <a name="how-can-i-stop-and-start-azure-firewall"></a>如何停止和啟動 Azure 防火牆？

您可以使用 Azure PowerShell 的「解除配置」** 和「配置」** 方法。

例如：

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> 您必須將防火牆和公用 IP 重新配置到原始的資源群組和訂用帳戶。

## <a name="what-are-the-known-service-limits"></a>已知的服務限制有哪些？

有關 Azure 防火牆服務限制,請參閱[Azure 訂閱和服務限制、配額和約束](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)。

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>中樞虛擬網路中的 Azure 防火牆是否可以在兩個輪幅虛擬網路之間轉寄和篩選網路流量？

是，您可以在中樞虛擬網路中，使用 Azure 防火牆來路由傳送和篩選兩個輪幅虛擬網路之間的流量。 每個分支虛擬網路中的子網必須有一個 UDR,指向 Azure 防火牆作為此方案正常工作的默認閘道。

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Azure 防火牆可以在同一個虛擬網路或對等虛擬網路的子網路之間轉寄和篩選網路流量嗎？

是。 但是,配置 UDR 以在同一 VNET 中的子網之間重定向流量需要額外的注意。 雖然對 UDR 而言，使用 VNET 位址範圍作為目標前置詞已足夠，但這也會使得所有流量透過 Azure 防火牆執行個體從一部機器路由至相同子網路中的另一部機器。 若要避免此狀況，需在下一個躍點類型為 **VNET** 的 UDR 中包括子網路的路由。 管理這些路由可能會很麻煩，而且容易出錯。 建議的內部網路分段方法是使用網路安全組,不需要 UDR。

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Azure 防火牆是否在專用網路之間出站 SNAT?

當目標 IP 位址是[IANA RFC 1918](https://tools.ietf.org/html/rfc1918)的專用 IP 範圍時,Azure 防火牆不會 SNAT。 如果您的組織對專用網路使用公共 IP 位址範圍,則 Azure 防火牆 SNAT 會將流量訪問 Azure 防火牆 Subnet 中防火牆專用 IP 位址之一。 您可以將 Azure 防火牆配置為**不**將公共 IP 位址範圍 SNAT。 有關詳細資訊,請參閱[Azure 防火牆 SNAT 專用 IP 位址範圍](snat-private-range.md)。

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>是否支援強制隧道/連結到網路虛擬設備?

支持強制隧道。 有關詳細資訊,請參閱[Azure 防火牆強制隧道(預覽)。](forced-tunneling.md) 

「Azure 防火牆」必須能夠直接連線到網際網路。 如果您的 AzureFirewallSubnet 學習到透過 BGP 連至您內部部署網路的預設路由，您必須將其覆寫為 0.0.0.0/0 UDR，且 **NextHopType** 值必須設為 [網際網路]****，以保有直接網際網路連線。

如果您的設定需要對內部部署網路使用強制通道，而且您可以決定網際網路目的地的目標 IP 首碼，則您可以透過 AzureFirewallSubnet 上的使用者定義路由，將使用內部部署網路的這些範圍設定為下一個躍點。 或者，您可以使用 BGP 來定義這些路由。

## <a name="are-there-any-firewall-resource-group-restrictions"></a>是否有任何防火牆資源群組的限制？

是。 防火牆、VNet 和公共 IP 位址都必須位於同一資源組中。

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>在為入站 Internet 網路流量配置 DNAT 時,我是否需要配置相應的網路規則以允許該流量?

否。 NAT 規則會隱含地新增對應的網路規則，以允許已轉譯的流量。 若要覆寫這個行為，您可以明確地使用符合已轉譯流量的拒絕規則來新增網路規則集合。 若要深入了解 Azure 防火牆規則處理邏輯，請參閱 [Azure 防火牆規則處理邏輯](rule-processing.md)。

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>通配符在應用程式規則目標 FQDN 中是如何工作的?

如果配置 =**.contoso.com**,它允許*任何值*.contoso.com,但不允許contoso.com(域頂點)。 如果要允許域頂點,則必須顯式將其配置為目標 FQDN。

## <a name="what-does-provisioning-state-failed-mean"></a>*預配狀態:失敗*是什麼意思?

每當應用配置更改時,Azure 防火牆都會嘗試更新其所有基礎後端實例。 在極少數情況下,這些後端實例之一可能無法使用新配置進行更新,並且更新過程以失敗的預配狀態停止。 Azure 防火牆仍在運行,但應用的配置可能處於不一致狀態,其中某些實例具有以前的配置,其中其他實例具有更新的規則集。 如果發生這種情況,請嘗試再更新一次配置,直到操作成功且防火牆處於 *「成功*預配」狀態。

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Azure 防火牆如何處理計劃的維護和計劃外故障?
Azure 防火牆由活動-活動配置中的多個後端節點組成。  對於任何計劃維護,我們有連接耗盡邏輯以正常更新節點。  在每個 Azure 區域的非工作時間計劃更新,以進一步限制中斷風險。  對於計劃外的問題,我們實例化一個新節點以替換失敗的節點。  與新節點的連接通常在故障后 10 秒內重新建立。

## <a name="how-does-connection-draining-work"></a>連接排水如何工作?

對於任何計劃維護,連接耗盡邏輯會正常更新後端節點。 Azure 防火牆等待 90 秒,以便關閉現有連接。 如果需要,用戶端可以自動重新建立與另一個後端節點的連接。

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>防火牆名稱是否有字元限制?

是。 防火牆名稱有 50 個字元的限制。

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>為什麼 Azure 防火牆需要 /26 子網大小?

Azure 防火牆必須在擴展時預配更多虛擬機器實例。 /26 位址空間可確保防火牆有足夠的 IP 位址可用於容納縮放。

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>防火牆子網大小是否需要隨服務擴展而改變?

否。 Azure 防火牆不需要大於 /26 的子網。

## <a name="how-can-i-increase-my-firewall-throughput"></a>如何提高防火牆輸送量?

Azure 防火牆的初始輸送量容量為 2.5 - 3 Gbps,並且擴展到 30 Gbps。 它根據 CPU 使用率和輸送量進行擴展。 請與支援部門聯繫,以提高防火牆的輸送量。

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Azure 防火牆要擴展多長時間?

Azure 防火牆需要 5 到 7 分鐘才能橫向擴展。如果發生需要更快自動縮放的突發,請與支援部門聯繫,以提高防火牆的初始輸送量容量。

在測試防火牆自動縮放時,應考慮以下幾點:

- 單個 TCP 流量性能限制為 1.4 Gbps。 因此,效能測試需要建立多個 TCP 流。
- 性能工具必須不斷建立新的連接,以便它們與擴展的後端防火牆實例連接。 如果測試在開始時建立一次連接,則這些連接將僅與初始後端實例連接。 即使防火牆擴展,您也不會看到任何性能提高,因為連接與初始實例相關聯。


## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>預設情況下,Azure 防火牆是否允許訪問活動目錄?

否。 默認情況下,Azure 防火牆阻止活動目錄訪問。 要允許存取,請配置 Azure ActiveDirectory 服務標記。 有關詳細資訊,請參閱[Azure 防火牆服務標記](service-tags.md)。

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>是否可以從基於 Azure 防火牆威脅智慧的篩選中排除 FQDN 或 IP 位址?

可以,您可以使用 Azure PowerShell 執行此操作:

```azurepowershell
# Add a Threat Intelligence Whitelist to an Existing Azure Firewall

## Create the Whitelist with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist.FQDNs = @("fqdn1", "fqdn2", …)
$fw.ThreatIntelWhitelist.IpAddress = @("ip1", "ip2", …)

Set-AzFirewall -AzureFirewall $fw
```

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>為什麼即使 Azure 防火牆上沒有任何規則允許該流量,TCP ping 和類似工具也能成功連接到目標 FQDN?

TCP ping 實際上未連接到目標 FQDN。 這是因為 Azure 防火牆的透明代理偵聽埠 80/443 上的出站流量。 TCP ping 與防火牆建立連接,然後丟棄數據包並記錄連接。 此行為沒有任何安全影響。 但是,為了避免混淆,我們正在調查此行為的潛在更改。 