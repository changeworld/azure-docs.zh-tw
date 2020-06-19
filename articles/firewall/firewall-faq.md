---
title: Azure 防火牆常見問題集
description: Azure 防火牆的常見問題集。 一個受控的雲端式網路安全性服務，可保護您的「Azure 虛擬網路」資源。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: victorh
ms.openlocfilehash: d1ec04a0c16feb6d404018ff9538b9572e1d71c2
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649605"
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

您可以將「Azure 防火牆」部署在任何虛擬網路上，但客戶通常會將其部署在中央虛擬網路上，然後以中樞和支點模型方式，將其他虛擬網路與其對等互連。 接著，您便可以從對等互連的虛擬網路設定預設路由，使其指向此中央防火牆虛擬網路。 支援全域 VNet 對等互連，但不建議使用，因為跨區域會有潛在的效能和延遲問題。 為了獲得最佳效能，每個區域請部署一個防火牆。

此模型的優點是能夠集中控制不同訂用帳戶上的多個分支 VNET。 這也可讓您節省成本，因為您不需要在每個 VNet 中分別部署防火牆。 若要衡量節省的成本，應根據客戶流量模式，與相關的對等成本做比較。

## <a name="how-can-i-install-the-azure-firewall"></a>如何安裝 Azure 防火牆？

您可以透過使用 Azure 入口網站、PowerShell、REST API 或範本，來設定「Azure 防火牆」。 請參閱[教學課程：使用 Azure 入口網站部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)。

## <a name="what-are-some-azure-firewall-concepts"></a>有哪些 Azure 防火牆概念？

Azure 防火牆支援規則和規則集合。 規則集合是一組共用相同順序和優先順序的規則。 規則集合會依其優先順序執行。 網路規則集合的優先順序高於應用程式規則集合，而所有規則都將終止。

規則集合有三種類型：

* *應用程式規則*：設定可從子網路存取的完整網域名稱 (FQDN)。
* *網路規則*：設定包含來源位址、通訊協定、目的地連接埠及目的地位址的規則。
* *NAT 規則*：設定 DNAT 規則以允許傳入的網際網路連線。

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Azure 防火牆是否支援輸入流量篩選？

Azure 防火牆支援輸入和輸出篩選。 連入保護通常用於非 HTTP/S 通訊協定。 例如 RDP、SSH 及 FTP 通訊協定。 如需最佳的連入 HTTP/S 保護，請使用 Web 應用程式防火牆，例如 [Azure Web 應用程式防火牆 (WAF)](../web-application-firewall/overview.md)。

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Azure 防火牆支援哪些記錄和分析服務？

「Azure 防火牆」已與「Azure 監視器」整合，可檢視和分析防火牆記錄。 記錄可以傳送至 Log Analytics、「Azure 儲存體」或「事件中樞」。 這些記錄可以在 Log Analytics 中進行分析，或透過不同的工具 (例如 Excel 和 Power BI) 進行分析。 如需詳細資訊，請參閱[教學課程：監視 Azure 防火牆記錄](tutorial-diagnostics.md)。

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Azure 防火牆的運作方式與市集中現有的服務 (例如 NVA) 有何不同？

Azure 防火牆是一種基本防火牆服務，可以處理特定的客戶案例。 預期的情況是您會混合使用協力廠商 NVA 與 Azure 防火牆。 使用上，首重相輔相成。

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>應用程式閘道 WAF 與 Azure 防火牆有什麼不同？

Web 應用程式防火牆 (WAF) 是應用程式閘道的一個功能，可提供 Web 應用程式的集中式輸入保護，免於遭遇常見的攻擊和弱點。 「Azure 防火牆」可為非 HTTP/S 通訊協定 (例如 RDP、SSH、FTP) 提供輸入保護、為所有連接埠和通訊協定提供輸出網路層級的保護，以及為輸出 HTTP/S 提供應用程式層級的保護。

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>網路安全性群組 (NSG) 和 Azure 防火牆有什麼不同？

「Azure 防火牆」服務可補足網路安全性群組功能。 兩者結合時，可提供更好的「深度防禦」網路安全性。 網路安全性群組提供分散式網路層流量篩選，可限制每個訂用帳戶中虛擬網路內資源的流量。 「Azure 防火牆」是完全具狀態的集中式網路防火牆即服務，可跨不同的訂用帳戶和虛擬網路，提供網路層級和應用程式層級的保護。

## <a name="are-network-security-groups-nsgs-supported-on-the-azurefirewallsubnet"></a>AzureFirewallSubnet 上是否支援網路安全性群組 (NSG)？

Azure 防火牆是受控服務，具有多個保護層，包括具備 NIC 層級 NSG (無法查看) 的平台保護。  AzureFirewallSubnet 上不需要子網路層級 NSG，並已停用以確保不會中斷服務。

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>如何使用我的服務端點設定 Azure 防火牆？

若要安全存取 PaaS 服務，建議使用服務端點。 您可以選擇在「Azure 防火牆」子網路中啟用服務端點，並在已連線的支點虛擬網路上停用它們。 如此一來，您便可以享有這兩個功能的好處：服務端點安全性和集中記錄所有流量。

## <a name="what-is-the-pricing-for-azure-firewall"></a>Azure 防火牆的定價為何？

請參閱 [Azure 防火牆定價](https://azure.microsoft.com/pricing/details/azure-firewall/)。

## <a name="how-can-i-stop-and-start-azure-firewall"></a>如何停止和啟動 Azure 防火牆？

您可以使用 Azure PowerShell 的「解除配置」和「配置」方法。

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

針對 Azure 防火牆服務限制，請參閱 [Azure 訂用帳戶和服務限制、配額與限制](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)。

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>中樞虛擬網路中的 Azure 防火牆是否可以在兩個輪幅虛擬網路之間轉寄和篩選網路流量？

是，您可以在中樞虛擬網路中，使用 Azure 防火牆來路由傳送和篩選兩個輪幅虛擬網路之間的流量。 每個輪幅虛擬網路中的子網路，都必須有 UDR 指向 Azure 防火牆作為預設閘道，此案例才能正常運作。

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Azure 防火牆可以在同一個虛擬網路或對等虛擬網路的子網路之間轉寄和篩選網路流量嗎？

是。 不過，若設定 UDR 在相同 VNET 中重新導向子網路之間的流量，則需要多加注意。 雖然對 UDR 而言，使用 VNET 位址範圍作為目標前置詞已足夠，但這也會使得所有流量透過 Azure 防火牆執行個體從一部機器路由至相同子網路中的另一部機器。 若要避免此狀況，需在下一個躍點類型為 **VNET** 的 UDR 中包括子網路的路由。 管理這些路由可能會很麻煩，而且容易出錯。 對於內部網路區隔的建議方法是使用網路安全性群組，而這不需要 UDR。

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>私人網路之間的 Azure 防火牆輸出 SNAT 嗎？

目的地 IP 位址為 [IANA RFC 1918](https://tools.ietf.org/html/rfc1918) \(英文\) 的私人 IP 範圍時，Azure 防火牆不會進行 SNAT 轉譯。 如果您的組織使用私人網路的公用 IP 位址範圍，Azure 防火牆會將流量 SNAT 轉譯到 AzureFirewallSubnet 其中一個防火牆私人 IP 位址。 您可以將 Azure 防火牆設定為**不要** SNAT 公用 IP 位址範圍。 如需詳細資訊，請參閱 [Azure 防火牆 SNAT 私人 IP 位址範圍](snat-private-range.md)。

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>受支援的網路虛擬設備是否強制進行通道/鏈結？

當您建立新的防火牆時，支援強制通道。 您無法為強制通道設定現有防火牆。 如需詳細資訊，請參閱 [Azure 防火牆強制通道](forced-tunneling.md)。 

「Azure 防火牆」必須能夠直接連線到網際網路。 如果您的 AzureFirewallSubnet 學習到透過 BGP 連至您內部部署網路的預設路由，您必須將其覆寫為 0.0.0.0/0 UDR，且 **NextHopType** 值必須設為 [網際網路]，以保有直接網際網路連線。

如果您的設定需要對內部部署網路使用強制通道，而且您可以決定網際網路目的地的目標 IP 首碼，則您可以透過 AzureFirewallSubnet 上的使用者定義路由，將使用內部部署網路的這些範圍設定為下一個躍點。 或者，您可以使用 BGP 來定義這些路由。

## <a name="are-there-any-firewall-resource-group-restrictions"></a>是否有任何防火牆資源群組的限制？

是。 防火牆、VNet 和公用 IP 位址全都必須在相同的資源群組中。

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>在設定傳入網際網路流量的 DNAT 時，是否也需要設定對應的網路規則，才能允許該流量？

否。 NAT 規則會隱含地新增對應的網路規則，以允許已轉譯的流量。 若要覆寫這個行為，您可以明確地使用符合已轉譯流量的拒絕規則來新增網路規則集合。 若要深入了解 Azure 防火牆規則處理邏輯，請參閱 [Azure 防火牆規則處理邏輯](rule-processing.md)。

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>如何在應用程式規則中將萬用字元用於目標 FQDN？

如果您設定 ***contoso.com**，其會允許*任何值*.contoso.com，但不包含 contoso.com (網域頂點)。 如果您想要允許網域頂點，您必須明確地將其設定為目標 FQDN。

## <a name="what-does-provisioning-state-failed-mean"></a>[佈建狀態:失敗] 是什麼意思？

每當套用設定變更時，Azure 防火牆會嘗試更新所有其基礎後端執行個體。 在罕見的情況下，其中一個後端執行個體可能無法使用新的設定進行更新，且更新程序會停止並處於失敗佈建狀態。 您的 Azure 防火牆仍可運作，但套用的設定可能處於不一致狀態，其中有些執行個體有先前的設定，而其他執行個體則具有已更新的規則集。 如果發生此情況，請嘗試再更新一次您的設定，直到作業成功，且您的防火牆處於 [成功] 佈建狀態。

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Azure 防火牆如何處理計劃性維護和非計畫性失敗？
Azure 防火牆是由主動-主動設定中的幾個後端節點所組成。  針對任何計劃性維護，我們都有連線清空邏輯來妥善更新節點。  更新會規劃在每個 Azure 區域的非上班時間，以進一步限制中斷的風險。  針對非計畫性問題，我們會具現化新節點，以取代失敗節點。  新節點的連線通常會在失敗後 10 秒內重新建立。

## <a name="how-does-connection-draining-work"></a>連線清空如何運作？

針對任何計劃性維護，我們都有連線清空邏輯來妥善更新後端節點。 Azure 防火牆會等待 90 秒，以便現有連線關閉。 如果需要，用戶端可以自動重新建立與另一個後端節點的連線。

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>防火牆名稱是否有字元限制？

是。 防火牆名稱有 50 個字元的限制。

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>為什麼 Azure 防火牆需要 /26 子網路大小？

Azure 防火牆必須在調整規模時佈建更多虛擬機器執行個體。 /26 位址空間可確保防火牆有足夠的 IP 位址可容納調整。

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>當服務調整時，防火牆子網路大小是否需要變更？

否。 Azure 防火牆不需要大於 /26 的子網路。

## <a name="how-can-i-increase-my-firewall-throughput"></a>如何增加防火牆輸送量？

Azure 防火牆的初始輸送量容量為 2.5 - 3 Gbps，並擴增為 30 Gbps。 其會以 CPU 使用量和輸送量為基礎來自動擴增。

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Azure 防火牆需要多久的時間來擴增？

當平均輸送量或 CPU 耗用量在 60% 時，Azure 防火牆會逐漸調整。 擴增需要五到七分鐘。 執行效能測試時，請確定您至少測試 10 到 15 分鐘，並起始新連線以利用新建立的防火牆節點。

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>根據預設，Azure 防火牆允許存取 Active Directory 嗎？

否。 根據預設，Azure 防火牆會封鎖 Active Directory 存取。 若要允許存取，請設定 AzureActiveDirectory 服務標籤。 如需詳細資訊，請參閱 [Azure 防火牆服務標籤](service-tags.md)。

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>我是否可以從以 Azure 防火牆威脅情報為基礎的篩選中排除 FQDN 或 IP 位址？

是，您可以使用 Azure PowerShell 來執行此動作：

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

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>為什麼即使 Azure 防火牆上沒有規則允許 TCP Ping 和類似的工具的流量，其也會成功連線到目標 FQDN？

TCP Ping 不會實際連線到目標 FQDN。 這是因為 Azure 防火牆的背景 Proxy 會接聽連接埠 80/443 上的連出流量。 TCP Ping 會建立與防火牆的連線，然後卸除封包並記錄連線。 此行為不會對安全性造成影響。 不過，為了避免混淆，我們正在調查此行為的可能變更。

## <a name="are-there-limits-for-the-number-of-ip-addresses-supported-by-ip-groups"></a>IP 群組支援的 IP 位址數目是否有限制？

是。 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)
