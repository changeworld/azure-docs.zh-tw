---
title: 何謂 Azure 防火牆？
description: Azure 防火牆是受控、雲端式網路安全性服務，可以保護您的 Azure 虛擬網路資源。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 05/22/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 2f466c71673c9239f6f984f838d050af8bf52182
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816058"
---
# <a name="what-is-azure-firewall"></a>何謂 Azure 防火牆？

![ICSA 認證](media/overview/icsa-cert-firewall-small.png)

Azure 防火牆是受控、雲端式網路安全性服務，可以保護您的 Azure 虛擬網路資源。 它是完全具狀態的防火牆即服務，具有內建的高可用性和不受限制的雲端延展性。

![防火牆概觀](media/overview/firewall-threat.png)

您可以橫跨訂用帳戶和虛擬網路集中建立、強制執行以及記錄應用程式和網路連線原則。 Azure 防火牆會針對您的虛擬網路資源使用靜態公用 IP 位址，允許外部防火牆識別源自您虛擬網路的流量。  此服務與 Azure 監視器會完全整合，以進行記錄和分析。

Azure 防火牆提供下列功能：

## <a name="built-in-high-availability"></a>內建高可用性

高可用性是內建的，因此不需要額外的負載平衡器，您也不需要進行任何設定。

## <a name="availability-zones"></a>可用性區域

Azure 防火牆可在部署期間進行設定，以跨越多個可用性區域來增加可用性。 透過可用性區域，您的可用性會增加到 99.99% 運作時間。 如需詳細資訊，請參閱 Azure 防火牆[服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/)。 選取兩個或多個可用性區域時，會提供 99.99% 運作時間 SLA。

您也可以單純基於鄰近性而將 Azure 防火牆關聯到特定區域，並使用服務標準的 99.95% SLA。

針對部署在「可用性區域」中的防火牆並不會產生額外費用。 不過，與可用性區域相關聯的輸入和輸出資料轉送會有額外成本。 如需詳細資訊，請參閱[頻寬價格詳細資料](https://azure.microsoft.com/pricing/details/bandwidth/)。

Azure 防火牆可用性區域可用於支援可用性區域的區域中。 如需詳細資訊，請參閱 [Azure 中支援可用性區域的區域](../availability-zones/az-region.md)

> [!NOTE]
> 可用性區域只能在部署期間進行設定。 您無法對現有的防火牆設定包含可用性區域。

如需可用性區域的詳細資訊，請參閱 [Azure 中的區域和可用性區域](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>不受限制的雲端延展性

Azure 防火牆可以隨著您的需求擴大，以容納多變的網路流量，因此您不需要為尖峰流量編列預算。

## <a name="application-fqdn-filtering-rules"></a>應用程式 FQDN 篩選規則

您可以將輸出 HTTP/S 流量或 Azure SQL 流量 (預覽) 限制為包含萬用字元的特殊完整網域名稱 (FQDN) 清單。 這項功能不需要 TLS 終止。

## <a name="network-traffic-filtering-rules"></a>網路流量篩選規則

您可以依據來源和目的地 IP 位址、連接埠及通訊協定，集中建立「允許」或「拒絕」網路篩選規則。 Azure 防火牆是完全具狀態的，因此能夠分辨不同連線類型的合法封包。 規則會橫跨多個訂用帳戶和虛擬網路強制執行及記錄。

## <a name="fqdn-tags"></a>FQDN 標記

[FQDN 標籤](fqdn-tags.md)讓您輕鬆就能允許已知的 Azure 服務網路流量通過您的防火牆。 例如，假設您想要允許 Windows Update 網路流量通過您的防火牆。 您可以建立應用程式規則並包含 Windows Update 標籤。 來自 Windows Update 的網路流量現在就能通過您的防火牆。

## <a name="service-tags"></a>服務標籤

[服務標籤](service-tags.md)表示一組 IP 位址前置詞，有助於降低建立安全性規則的複雜性。 您無法建立自己的服務標籤，也無法指定標籤中包含哪些 IP 位址。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

## <a name="threat-intelligence"></a>威脅情報

您可為防火牆啟用[威脅情報](threat-intel.md)型篩選，以警示並拒絕來自/傳向已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。

## <a name="outbound-snat-support"></a>輸出 SNAT 支援

所有輸出虛擬網路流量 IP 位址都會轉譯成 Azure 防火牆公用 IP (來源網路位址轉譯)。 您可以識別源自您虛擬網路的流量，並且允許其流向遠端網際網路目的地。 目的地 IP 為 [IANA RFC 1918](https://tools.ietf.org/html/rfc1918) 的私人 IP 範圍時，Azure 防火牆不會進行 SNAT 轉譯。 

如果您的組織使用私人網路的公用 IP 位址範圍，Azure 防火牆會將流量 SNAT 轉譯到 AzureFirewallSubnet 其中一個防火牆私人 IP 位址。 您可以將 Azure 防火牆設定為**不要** SNAT 公用 IP 位址範圍。 如需詳細資訊，請參閱 [Azure 防火牆 SNAT 私人 IP 位址範圍](snat-private-range.md)。

## <a name="inbound-dnat-support"></a>輸入 DNAT 支援

傳送到您防火牆公用 IP 位址的輸入網際網路流量會轉譯 (目的地網路位址轉譯)，並篩選至您虛擬網路上的私人 IP 位址。

## <a name="multiple-public-ip-addresses"></a>多個公用 IP 位址

您可以將[多個公用 IP 位址](deploy-multi-public-ip-powershell.md) (最多 250 個) 與您的防火牆相關聯。

這適用於下列案例：

- **DNAT** - 您可以將多個標準連接埠執行個體轉譯到後端伺服器。 例如，如果您有兩個公用 IP 位址，您可以為這兩個 IP 位址轉譯 TCP 通訊埠 3389 (RDP)。
- **SNAT** - 其他連接埠可用於輸出 SNAT 連線，降低 SNAT 連接埠耗盡的可能性。 目前，Azure 防火牆會隨機選取來源公用 IP 位址以用於連線。 如果您的網路上有任何下游篩選，則您必須允許與防火牆相關聯的所有公用 IP 位址。 請考慮使用[公用 IP 位址首碼](../virtual-network/public-ip-address-prefix.md)來簡化此設定。

## <a name="azure-monitor-logging"></a>Azure 監視器記錄

所有事件都會與 Azure 監視器整合，讓您可以將記錄封存至儲存體帳戶、將事件串流至事件中樞，或者將它們傳送到 Azure 監視器記錄。 如需詳細資訊，請參閱[教學課程：監視 Azure 防火牆記錄和計量](tutorial-diagnostics.md)。

## <a name="forced-tunneling"></a>強制通道

您可以設定 Azure 防火牆，將所有網際網路繫結流量路由傳送至指定的下一個躍點，而不是直接前往網際網路。 例如，您可能有內部部署邊緣防火牆或其他網路虛擬設備 (NVA) 能先處理網路流量，再將其傳遞至網際網路。 如需詳細資訊，請參閱 [Azure 防火牆強制通道](forced-tunneling.md)。

## <a name="certifications"></a>認證

Azure 防火牆符合支付卡產業 (PCI)、服務組織控制 (SOC)、國際標準化組織 (ISO) 規範和符合 ICSA 實驗室規範。 如需詳細資訊，請參閱 [Azure 防火牆合規性認證](compliance-certifications.md)。


## <a name="known-issues"></a>已知問題

Azure 防火牆有下列已知問題：

|問題  |描述  |降低  |
|---------|---------|---------|
非 TCP/UDP 通訊協定 (例如 ICMP) 的網路篩選規則，不適用於流向網際網路的流量|非 TCP/UDP 通訊協定的網路篩選規則，無法與 SNAT 搭配用於您的公用 IP 位址。 在輪輻子網路與 VNet 之間支援非 TCP/UDP 通訊協定。|Azure 防火牆會使用 Standard Load Balancer，[目前針對 IP 通訊協定不支援 SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。 我們正在探索選項，以在未來的版本中支援這種案例。|
|對於 ICMP 缺少 PowerShell 和 CLI 支援|Azure PowerShell 和 CLI 不支援在網路規則中將 ICMP 作為有效的通訊協定。|您仍可透過入口網站和 REST API 來使用 ICMP 作為通訊協定。 我們正努力盡快在 PowerShell 和 CLI 中新增 ICMP。|
|FQDN 標籤需要設定「通訊協定:連接埠」|具有 FQDN 標籤的應用程式規則需要「連接埠: 通訊協定」定義。|您可以使用 **https** 作為「連接埠:通訊協定」值。 我們正努力讓此欄位在使用 FQDN 標籤時可作為選擇性欄位。|
|不支援將防火牆移動到不同的資源群組或訂用帳戶|不支援將防火牆移動到不同的資源群組或訂用帳戶。|在我們的規劃中，未來會支援這項功能。 若要將防火牆移動到不同的資源群組或訂用帳戶，您必須刪除目前的執行個體，並將其重新建立在新的資源群組或訂用帳戶中。|
|威脅情報警示可能會遮罩處理|目的地為 80/443 的網路規則，可供輸出篩選遮罩處理設定為僅限警示模式的威脅情報警示。|使用應用程式規則建立 80/443 的輸出篩選。 或者，將威脅情報模式變更為 [警示並拒絕]。|
|Azure 防火牆只會使用 Azure DNS 來解析名稱|Azure 防火牆只會使用 Azure DNS 解析 FQDN。 不支援自訂的 DNS 伺服器。 對其他子網路上的 DNS 解析沒有任何影響。|我們正努力放寬這項限制。|
|Azure 防火牆 DNAT 不適用於私人 IP 目的地|Azure 防火牆 DNAT 支援受限於網際網路輸出/輸入。 DNAT 目前不適用於私人 IP 目的地。 例如，輪輻至輪輻。|這是目前的限制。|
|無法移除第一個公用 IP 設定|每個 Azure 防火牆公用 IP 位址會指派給一個 *IP 設定*。  第一個 IP 設定會在防火牆部署期間指派，且通常也會包含防火牆子網路的參考 (除非透過範本部署明確做了不同的設定)。 您無法刪除此 IP 設定，因為這樣會將防火牆解除配置。 如果至少還有一個其他公用 IP 位址可供使用，您仍然可以變更或移除與此 IP 設定相關聯的公用 IP 位址。|這是原廠設定。|
|可用性區域只能在部署期間進行設定。|可用性區域只能在部署期間進行設定。 在部署防火牆之後，您無法設定可用性區域。|這是原廠設定。|
|輸入連線上的 SNAT|除了 DNAT，透過防火牆公用 IP 位址 (輸入) 的連線已對其中一個防火牆私人 IP 進行 SNAT 轉譯。 這項需求現在也適用於主動/主動 NVA 以確保對稱式路由。|若要保留 HTTP/S 的原始來源，請考慮使用 [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For) 標題。 例如，在防火牆前使用 [Azure Front Door](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) 或 [Azure 應用程式閘道](../application-gateway/rewrite-http-headers.md)等服務。 您也可以將 WAF 新增為 Azure Front Door 的一部分和防火牆鏈結。
|SQL FQDN 篩選支援僅限於 Proxy 模式 (連接埠 1433)|針對 Azure SQL Database、Azure SQL 資料倉儲和 Azure SQL 受控執行個體：<br><br>在預覽期間，只有 Proxy 模式可支援 SQL FQDN 篩選 (連接埠 1433)。<br><br>針對 Azure SQL IaaS：<br><br>如果您使用非標準連接埠，您可以在應用程式規則中指定這些連接埠。|在重新導向模式中使用 SQL 時 (這是從 Azure 內連線時的預設值)，您可以改為使用 SQL 服務標籤作為 Azure 防火牆網路規則的一部分來篩選存取。
|不允許 TCP 連接埠 25 上的輸出流量| 使用 TCP 連接埠 25 的輸出 SMTP 連線會遭到封鎖。 連接埠 25 主要用於未經驗證的電子郵件傳遞。 這是虛擬機器的預設平台行為。 如需詳細資訊，請參閱[針對 Azure 中的輸出 SMTP 連線能力問題進行疑難排解](../virtual-network/troubleshoot-outbound-smtp-connectivity.md)。 不過，與虛擬機器不同的是，目前無法在 Azure 防火牆上啟用此功能。 注意：若要允許經過驗證的 SMTP (連接埠 587)，或允許 SMTP 通過連接埠 25 以外的連接埠，請確定您已設定網路規則，而不是應用程式規則，因為系統目前不支援 SMTP 檢查。|依照 SMTP 疑難排解文章中所述的建議方式來傳送電子郵件。 或者，從預設傳送至防火牆的路由中排除需要輸出 SMTP 存取的虛擬機器， 並將輸出存取改為直接以網際網路為目標。
|不支援主動式 FTP|Azure 防火牆上的主動式 FTP 已停用，以防禦使用 FTP PORT 命令進行 FTP 彈跳式攻擊。|您可以改用被動式 FTP。 您仍然必須在防火牆上明確開啟 TCP 連接埠 20 和 21。
|SNAT 連接埠使用率記量顯示 0%|即使已使用 SNAT 連接埠，Azure 防火牆 SNAT 連接埠使用率計量仍可能會顯示 0%。 在此情況下，若使用計量作為防火牆健康情況計量的一部分，則會提供不正確的結果。|此問題已修正，而正式推出日期訂於 2020 年 5 月。 在某些情況下，防火牆重新部署會解決此問題，但並不會一直都是如此。 作為中繼因應措施，請只使用防火牆健全狀態來尋找 status=degraded，而不是 status=unhealthy。 連接埠耗盡時會顯示為 degraded (已降級)。 Not healthy (狀態不良) 會保留，以在日後有更多計量影響防火牆健康情況時使用。
|啟用強制通道時不支援 DNAT|因為非對稱式路由，部署了強制通道的防火牆無法支援來自網際網路的輸入存取。|這是因為非對稱式路由的設計。 輸入連線的傳回路徑會透過內部部署防火牆進行，而此防火牆並未顯示已建立的連線。
|輸出被動 FTP 不適用於具有多個公用 IP 位址的防火牆|被動 FTP 會針對控制和資料通道建立不同的連線。 當具有多個公用 IP 位址的防火牆傳送輸出資料時，會為來源 IP 位址隨機選取其中一個公用 IP 位址。 當資料和控制通道使用不同的來源 IP 位址時，FTP 會失敗。|已規劃了明確的 SNAT 組態。 在這種情況下，也請考慮使用單一 IP 位址。|
|NetworkRuleHit 計量缺少通訊協定維度|ApplicationRuleHit 計量允許篩選型的通訊協定，但對應的 NetworkRuleHit 計量中缺少這項功能。|我們正在調查提供修正程式的可能性。|
|不支援在 64000 與 65535 之間使用連接埠的 NAT 規則|Azure 防火牆允許網路和應用程式規則使用 1-65535 範圍中的任何連接埠，不過 NAT 規則只支援 1-63999 範圍中的連接埠。|這是目前的限制。
|組態更新平均可能需要 5 分鐘|Azure 防火牆組態更新平均可能需要三到五分鐘，而且不支援平行更新。|我們正在調查提供修正程式的可能性。|
|Azure 防火牆會使用 SNI TLS 標頭來篩選 HTTPS 和 MSSQL 流量|如果瀏覽器或伺服器軟體不支援伺服器名稱指示 (SNI) 擴充功能，您就無法透過 Azure 防火牆連線。|如果瀏覽器或伺服器軟體不支援 SNI，您或許能夠使用網路規則 (而不是應用程式規則) 來控制連線。 如需可支援 SNI 的軟體，請參閱[伺服器名稱指示](https://wikipedia.org/wiki/Server_Name_Indication)。

## <a name="next-steps"></a>後續步驟

- [教學課程：使用 Azure 入口網站部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)
- [使用範本來部署 Azure 防火牆](deploy-template.md)
- [建立 Azure 防火牆測試環境](scripts/sample-create-firewall-test.md)
