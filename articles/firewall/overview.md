---
title: 何謂 Azure 防火牆？
description: Azure 防火牆是受控、雲端式網路安全性服務，可以保護您的 Azure 虛擬網路資源。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc, contperfq1
ms.date: 08/25/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 914f267edd5a8168fc11af7186e322c306718a4a
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852641"
---
# <a name="what-is-azure-firewall"></a>何謂 Azure 防火牆？

![ICSA 認證](media/overview/icsa-cert-firewall-small.png)

Azure 防火牆是受控、雲端式網路安全性服務，可以保護您的 Azure 虛擬網路資源。 它是完全具狀態的防火牆即服務，具有內建的高可用性和不受限制的雲端延展性。

![防火牆概觀](media/overview/firewall-threat.png)

您可以橫跨訂用帳戶和虛擬網路集中建立、強制執行以及記錄應用程式和網路連線原則。 Azure 防火牆會針對您的虛擬網路資源使用靜態公用 IP 位址，允許外部防火牆識別源自您虛擬網路的流量。  此服務與 Azure 監視器會完全整合，以進行記錄和分析。

## <a name="features"></a>特性

若要了解 Azure 防火牆功能，請參閱 [Azure 防火牆功能](features.md)。

## <a name="known-issues"></a>已知問題

Azure 防火牆有下列已知問題：

|問題  |描述  |降低  |
|---------|---------|---------|
非 TCP/UDP 通訊協定 (例如 ICMP) 的網路篩選規則，不適用於流向網際網路的流量|非 TCP/UDP 通訊協定的網路篩選規則，無法與 SNAT 搭配用於您的公用 IP 位址。 在輪輻子網路與 VNet 之間支援非 TCP/UDP 通訊協定。|Azure 防火牆會使用 Standard Load Balancer，[目前針對 IP 通訊協定不支援 SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。 我們正在探索選項，以在未來的版本中支援這種案例。|
|對於 ICMP 缺少 PowerShell 和 CLI 支援|Azure PowerShell 和 CLI 不支援在網路規則中將 ICMP 作為有效的通訊協定。|您仍可透過入口網站和 REST API 來使用 ICMP 作為通訊協定。 我們正努力盡快在 PowerShell 和 CLI 中新增 ICMP。|
|FQDN 標籤需要設定「通訊協定:連接埠」|具有 FQDN 標籤的應用程式規則需要「連接埠: 通訊協定」定義。|您可以使用 **https** 作為「連接埠:通訊協定」值。 我們正努力讓此欄位在使用 FQDN 標籤時可作為選擇性欄位。|
|不支援將防火牆移動到不同的資源群組或訂用帳戶|不支援將防火牆移動到不同的資源群組或訂用帳戶。|在我們的規劃中，未來會支援這項功能。 若要將防火牆移動到不同的資源群組或訂用帳戶，您必須刪除目前的執行個體，並將其重新建立在新的資源群組或訂用帳戶中。|
|威脅情報警示可能會遮罩處理|目的地為 80/443 的網路規則，可供輸出篩選遮罩處理設定為僅限警示模式的威脅情報警示。|使用應用程式規則建立 80/443 的輸出篩選。 或者，將威脅情報模式變更為 [警示並拒絕]。|
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
|Azure 防火牆會使用 SNI TLS 標頭來篩選 HTTPS 和 MSSQL 流量|如果瀏覽器或伺服器軟體不支援伺服器名稱指示 (SNI) 擴充功能，您就無法透過 Azure 防火牆連線。|如果瀏覽器或伺服器軟體不支援 SNI，您或許能夠使用網路規則 (而不是應用程式規則) 來控制連線。 如需可支援 SNI 的軟體，請參閱[伺服器名稱指示](https://wikipedia.org/wiki/Server_Name_Indication)。|
|自訂 DNS (預覽) 無法與強制通道搭配運作|如果已啟用強制通道，自訂 DNS (預覽) 就無法運作。|我們正在調查提供修正程式的可能性。

## <a name="next-steps"></a>後續步驟

- [教學課程：使用 Azure 入口網站部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)
- [使用範本來部署 Azure 防火牆](deploy-template.md)
- [建立 Azure 防火牆測試環境](scripts/sample-create-firewall-test.md)
