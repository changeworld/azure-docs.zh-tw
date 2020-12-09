---
title: Azure 應用程式閘道相關的常見問題集
description: 尋找 Azure 應用程式閘道相關常見問題集的解答。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 05/26/2020
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: d46c1d8efb5234d47c80ca4256c2f56d56ccf805
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862168"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>應用程式閘道相關的常見問題集

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下是關於 Azure 應用程式閘道的常見問題。

## <a name="general"></a>一般

### <a name="what-is-application-gateway"></a>什麼是應用程式閘道？

Azure 應用程式閘道以服務形式提供應用程式傳遞控制器 (ADC)。 提供各種第 7 層負載平衡功能，供您的應用程式使用。 這項服務具有高可用性、可調整，而且完全由 Azure 管理。

### <a name="what-features-does-application-gateway-support"></a>應用程式閘道支援哪些功能？

應用程式閘道支援自動調整、TLS 卸載、端對端 TLS、Web 應用程式防火牆 (WAF)、以 Cookie 為基礎的工作階段親和性、以 URL 路徑為基礎的路由、多網站裝載，以及其他功能。 如需完整的支援功能清單，請參閱[應用程式閘道簡介](./overview.md)。

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>應用程式閘道和 Azure Load Balancer 有何不同？

應用程式閘道是第 7 層負載平衡器，這表示只處理 web 流量 (HTTP、HTTPS、WebSocket 和 HTTP/2)。 支援 TLS 終止、以 Cookie 為基礎的工作階段親和性，以及循環配置資源等功能，以平衡流量負載。 Load Balancer 平衡第 4 層 (TCP 或 UDP) 的流量負載。

### <a name="what-protocols-does-application-gateway-support"></a>應用程式閘道支援哪些通訊協定？

應用程式閘道支援 HTTP、HTTPS、HTTP/2 和 WebSocket。

### <a name="how-does-application-gateway-support-http2"></a>應用程式閘道如何支援 HTTP/2？

請參閱 [HTTP/2 支援](./configuration-listeners.md#http2-support)。

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>支援哪些資源作為後端集區的一部分？

請參閱[支援的後端資源](./application-gateway-components.md#backend-pools)。

### <a name="in-what-regions-is-application-gateway-available"></a>應用程式閘道在哪些區域推出？

應用程式閘道 v1 (Standard 和 WAF) 適用于全域 Azure 的所有區域。 [Azure China 21Vianet](https://www.azure.cn/) 和 [Azure Government](https://azure.microsoft.com/overview/clouds/government/) 中也提供此服務。

針對應用程式閘道 v2 (Standard_v2 和 WAF_v2) 可用性，請參閱 [應用程式閘道 v2 支援的區域](./application-gateway-autoscaling-zone-redundant.md#supported-regions)

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>此部署是我的訂用帳戶專用，還是客戶共用？

應用程式閘道是您虛擬網路中專用的部署。

### <a name="does-application-gateway-support-http-to-https-redirection"></a>應用程式閘道是否 HTTP 到 HTTPS 重新導向？

支援重新導向。 請參閱[應用程式閘道重新導向概觀](./redirect-overview.md)。

### <a name="in-what-order-are-listeners-processed"></a>接聽程式的處理順序為何？

請參閱[接聽程式處理順序](./configuration-listeners.md#order-of-processing-listeners)。

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>哪裡可以找到應用程式閘道的 IP 和 DNS？

如果您使用公用 IP 位址作為端點，則可以在公用 IP 位址資源上找到 IP 和 DNS 資訊。 在入口網站的應用程式閘道概觀頁面上也可以找到。 如果您使用內部 IP 位址，請在概觀頁面頁面上尋找此資訊。

若為 v2 SKU，請開啟公用 IP 資源，然後選取 [設定]。 [DNS 名稱標籤 (選用)] 欄位可用來設定 DNS 名稱。

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>保持連線逾時和 TCP 閒置逾時的設定為何？

「保持連線逾時」控制應用程式閘道在重複使用或關閉持續連線之前，以多少時間來等待用戶端傳送另一個 HTTP 要求。 「TCP 閒置逾時」控制在沒有活動的情況下，TCP 連線保持開啟的時間長度。 

在應用程式閘道 v1 SKU 中，「保持連線逾時」是 120 秒，而在 v2 SKU 中是 75 秒。 在應用程式閘道 v1 和 v2 SKU 的前端虛擬 IP (VIP) 上，「TCP 閒置逾時」都預設為 4 分鐘。 您可以將 v1 和 v2 應用程式閘道上的 TCP 閒置超時值設定為4分鐘到30分鐘之間的任何位置。 針對 v1 和 v2 應用程式閘道，您必須流覽至應用程式閘道的公用 IP，並在入口網站上的公用 IP 的 [設定] 分頁下變更 TCP 閒置時間。 您可以執行下列命令，以透過 PowerShell 設定公用 IP 的 TCP 閒置超時值： 

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>在應用程式閘道的存留期內，IP 或 DNS 名稱是否會變更？

在應用程式閘道 V1 SKU 中，如果您停止又啟動應用程式閘道，VIP 可能會變更。 但在閘道的存留期內，與應用程式閘道相關聯的 DNS 名稱不會變更。 由於 DNS 名稱不會變更，您應該使用 CNAME 別名，並指向應用程式閘道的 DNS 位址。 在應用程式閘道 V2 SKU 中，您可以將 IP 位址設定為靜態，如此，在應用程式閘道的存留期內，IP 和 DNS 名稱就不會變更。 

### <a name="does-application-gateway-support-static-ip"></a>應用程式閘道是否支援靜態 IP？

是，應用程式閘道 v2 SKU 支援靜態公用 IP 位址。 v1 SKU 支援靜態內部 IP。

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>應用程式閘道是否在閘道上支援多個公用 IP？

一個應用程式閘道只支援一個公用 IP 位址。

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>應用程式閘道的子網路應該要多大？

請參閱[應用程式閘道子網路大小考量](./configuration-infrastructure.md#size-of-the-subnet)。

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>可以將多個應用程式閘道資源部署到單一子網路嗎？

是。 除了特定應用程式閘道部署的多個執行個體以外，您還可以將另一個獨特的應用程式閘道資源，佈建到含有不同應用程式閘道資源的現有子網路。

單一子網路無法同時支援 v2 和 v1 應用程式閘道 SKU。

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>應用程式閘道 v2 是否支援使用者定義的路由 (UDR)？

是，但僅限於特殊情況。 如需詳細資訊，請參閱 [應用程式閘道基礎結構](configuration-infrastructure.md#supported-user-defined-routes)設定。

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>應用程式閘道是否支援 x-forwarded-for 標頭？

是。 請參閱[修改要求](./how-application-gateway-works.md#modifications-to-the-request)。

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>部署應用程式閘道需要多久的時間？ 應用程式閘道在更新當時是否運作？

新的應用程式閘道 v1 SKU 部署最多可能需要 20 分鐘的時間來佈建。 變更執行個體大小或計數不會產生干擾，在這段期間，閘道仍持續運作。

大部分使用 v2 SKU 的部署，大約需要 6 分鐘來佈建。 但可能更久，視部署類型而定。 例如，部署到已有許多執行個體的多個可用性區域時，可能會超過 6 分鐘。 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>我可以使用 Exchange Server 作為應用程式閘道的後端嗎？

否。 應用程式閘道不支援電子郵件通訊協定，例如 SMTP、IMAP 和 POP3。

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>從 v1 SKU 遷移至 v2 SKU 時，是否有可供參考的指導？

是。 如需詳細資訊，請參閱[將 Azure 應用程式閘道和 Web 應用程式防火牆從 v1 遷移至 v2](migrate-v1-v2.md)。

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>是否會繼續支援應用程式閘道 v1 SKU？

是。 我們將繼續支援應用程式閘道 v1 SKU。 但強烈建議您移轉至 v2，以享受該 SKU 的功能更新。 如需詳細資訊，請參閱[自動調整和區域備援應用程式閘道 v2](application-gateway-autoscaling-zone-redundant.md)。

### <a name="does-application-gateway-v2-support-proxying-requests-with-ntlm-authentication"></a>應用程式閘道 V2 是否支援使用 NTLM 驗證來通過 Proxy 處理要求？

否。 應用程式閘道 V2 不支援使用 NTLM 驗證的 proxy 要求。

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>應用程式閘道親和性 Cookie 是否支援 SameSite 屬性？
是，[Chromium 瀏覽器](https://www.chromium.org/Home) [v80 更新](https://chromiumdash.appspot.com/schedule)強制規定不含 SameSite 屬性的 HTTP Cookie 視同 SameSite=Lax。 這表示在協力廠商環境下，瀏覽器不會傳送應用程式閘道親和性 Cookie。 

為了支援這種情況，除了現有的 *ApplicationGatewayAffinity* Cookie，應用程式閘道還會插入另一個名為 *ApplicationGatewayAffinityCORS* 的 Cookie。  這些 Cookie 很類似，但 *ApplicationGatewayAffinityCORS* Cookie 多增加兩個屬性：*SameSite=None; Secure*。 即使是跨原始來源要求，這些屬性仍然會維護黏性工作階段。 如需詳細資訊，請參閱[以 Cookie 為基礎的親和性](configuration-http-settings.md#cookie-based-affinity)一節。

## <a name="performance"></a>效能

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>應用程式閘道如何支援高可用性和延展性？

如果您已部署兩個以上的執行個體，則應用程式閘道 v1 SKU 支援高可用性案例。 Azure 會將這些執行個體分散到更新和容錯網域，以確保執行個體不會全部同時失敗。 v1 SKU 可藉由新增多個相同閘道的執行個體來分攤負載，以支援延展性。

v2 SKU 會自動確保將新執行個體分散在各個容錯網域和更新網域。 如果您選擇區域備援，則最新的執行個體也會分散到各個可用性區域，以支援區域失敗復原能力。

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>如何使用應用程式閘道來達成跨資料中心的災害復原案例？

使用流量管理員，將流量分散到不同資料中心的多個應用程式閘道。

### <a name="does-application-gateway-support-autoscaling"></a>應用程式閘道是否支援自動調整？

是，應用程式閘道 v2 SKU 支援自動調整規模。 如需詳細資訊，請參閱[自動調整和區域備援應用程式閘道](application-gateway-autoscaling-zone-redundant.md)。

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>手動或自動擴大或縮小是否會造成停機？

否。 執行個體已分散於升級網域和容錯網域。

### <a name="does-application-gateway-support-connection-draining"></a>應用程式閘道是否支援連線清空？

是。 您可以設定連線清空來變更後端集區內的成員，而不會中斷運作。 如需詳細資訊，請參閱[應用程式閘道的連線清空](features.md#connection-draining)一節。

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>是否可在中斷的情況下，將執行個體大小從中型變成大型？

是。

## <a name="configuration"></a>組態

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>應用程式閘道一律部署在虛擬網路中？

是。 應用程式閘道一律部署在虛擬網路子網路中。 這個子網路只能包含應用程式閘道。 如需詳細資訊，請參閱[虛擬網路和子網路需求](./configuration-infrastructure.md#virtual-network-and-dedicated-subnet)。

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>應用程式閘道能否與虛擬網路外面或訂用帳戶外面的執行個體進行通訊？

只要您有 IP 連線能力，應用程式閘道就可以與所在虛擬網路外面的執行個體進行通訊。 應用程式閘道也可以與所在訂用帳戶外面的執行個體進行通訊。 如果您打算使用內部 IP 作為後端集區成員，請使用[虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)或 [Azure VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>是否可以在應用程式閘道子網路中部署其他任何項目？

否。 但是，您可以在子網路中部署其他應用程式閘道。

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>應用程式閘道子網路是否支援網路安全性群組？

[應用程式閘道子網路中的網路安全性群組](./configuration-infrastructure.md#network-security-groups)。

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>應用程式閘道子網路是否支援使用者定義路由？

請參閱[應用程式閘道子網路中支援的使用者定義路由](./configuration-infrastructure.md#supported-user-defined-routes)。

### <a name="are-service-endpoint-policies-supported-in-the-application-gateway-subnet"></a>應用程式閘道子網是否支援服務端點原則？

不會。 應用程式閘道子網不支援儲存體帳戶的[服務端點原則](../virtual-network/virtual-network-service-endpoint-policies-overview.md)，並設定它將會封鎖 Azure 基礎結構流量。

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>應用程式閘道的限制為何？ 是否可以增加這些限制？

請參閱[應用程式閘道限制](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)。

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>是否可以同時對外部和內部流量使用應用程式閘道？

是。 是，應用程式閘道支援每個應用程式閘道各有一個內部 IP 和一個外部 IP。

### <a name="does-application-gateway-support-virtual-network-peering"></a>應用程式閘道是否支援虛擬網路對等互連？

是。 虛擬網路對等互連可協助平衡其他虛擬網路中的流量負載。

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>是否可以與經由 ExpressRoute 或 VPN 通道連線的內部部署伺服器進行通訊？

是，只有流量允許即可。

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>一個後端集區可以在不同連接埠上處理許多應用程式嗎？

支援微服務架構。 若要在不同的連接埠上探查，您需要設定多個 HTTP 設定。

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>自訂探查是否支援回應資料使用萬用字元或 RegEx？

否。 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>應用程式閘道中如何處理路由規則？

請參閱[處理規則的順序](./configuration-request-routing-rules.md#order-of-processing-rules)。

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>自訂探查的 [主機] 欄位代表什麼？

當您在應用程式閘道上已設定多網站時，[主機] 欄位指定探查送往的名稱。 否則，請使用 '127.0.0.1'。 此值與虛擬機器主機名稱不同。 其格式為 \<protocol\> ：// \<host\> ： \<port\> \<path\> 。

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>我可以只允許少數幾個來源 IP 位址存取應用程式閘道嗎？

是。 請參閱[限制特定來源 IP 的存取](./configuration-infrastructure.md#allow-access-to-a-few-source-ips)。

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>公開和私人接聽程式是否可以使用相同的連接埠？

否。

### <a name="does-application-gateway-support-ipv6"></a>應用程式閘道是否支援 IPv6？

應用程式閘道 v2 目前不支援 IPv6。 只能使用 IPv4 在雙重堆疊 VNet 中運作，但閘道子網路只能是 IPv4。 應用程式閘道 v1 不支援雙重堆疊 VNet。 

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>如何使用只有私人前端 IP 位址的應用程式閘道 V2？

應用程式閘道 V2 目前不支援私人 IP 模式。 支援下列組合
* 私人 IP 和公用 IP
* 僅限公用 IP

但是，如果想要使用只有私人 IP 的應用程式閘道 V2，您可以遵循下列程序：
1. 建立具有公用和私人前端 IP 位址的應用程式閘道
2. 請不要為公用前端 IP 位址建立任何接聽程式。 如果沒有為公用 IP 位址建立接聽程式，應用程式閘道就不會此位址上接聽任何流量。
3. 依優先順序使用下列設定，針對應用程式閘道子網路建立並附加[網路安全性群組](../virtual-network/network-security-groups-overview.md)：
    
    a. 允許來源為 **GatewayManager** 服務標記、目的地為 **Any**、目的地連接埠為 **65200-65535** 的流量。 Azure 基礎結構通訊需要此連接埠範圍。 這些連接埠由憑證驗證保護 (鎖定)。 如果沒有適當的憑證，外部實體 (包括閘道使用者管理員) 無法變更這些端點
    
    b. 允許來源為 **AzureLoadBalancer** 服務標記、目的地和目的地連接埠為 **Any** 的流量
    
    c. 拒絕來源為 **Internet** 服務標記、目的地和目的地連接埠為 **Any** 的所有輸入流量。 在輸入規則中，將此規則設定為「最低優先順序」
    
    d. 保留預設規則，例如允許 VirtualNetwork 輸入，以免封鎖私人 IP 位址的存取
    
    e. 無法封鎖輸出網際網路連線。 否則，您會遇到記錄、計量等問題。

僅限私人 IP 存取的 NSG 設定範例：![僅限私人 IP 存取的應用程式閘道 V2 NSG 設定](./media/application-gateway-faq/appgw-privip-nsg.png)

## <a name="configuration---tls"></a>設定 - TLS

### <a name="what-certificates-does-application-gateway-support"></a>應用程式閘道支援哪些憑證？

應用程式閘道支援自我簽署憑證、憑證授權單位單位 (CA) 憑證、延伸驗證 (EV) 憑證、多重網域 (SAN) 憑證和萬用字元憑證。

### <a name="what-cipher-suites-does-application-gateway-support"></a>應用程式閘道支援哪些加密套件？

應用程式閘道支援下列加密套件。 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

如需如何自訂 TLS 選項的相關資訊，請參閱[在應用程式閘道上設定 TLS 原則版本和加密套件](application-gateway-configure-ssl-policy-powershell.md)。

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>應用程式閘道是否支援重新加密流向後端的流量？

是。 應用程式閘道支援 TLS 卸載和端對端 TLS，還會重新加密流向後端的流量。

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>是否可設定 TLS 原則來控制 TLS 通訊協定版本？

是。 您可以將應用程式閘道設定為拒絕 TLS1.0、TLS1.1 和 TLS1.2。 根據預設，SSL 2.0 和 3.0 已停用，無法設定。

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>可以設定加密套件和原則的順序嗎？

是。 在應用程式閘道中，您可以[設定加密套件](application-gateway-ssl-policy-overview.md)。 若要定義自訂原則，請至少啟用下列其中一個加密套件。 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

應用程式閘道使用 SHA256 進行後端管理。

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>應用程式閘道支援多少個 TLS/SSL 憑證？

應用程式閘道最多支援 100 個 TLS/SSL 憑證。

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>應用程式閘道在後端重新加密方面支援多少個驗證憑證？

應用程式閘道最多支援 100 個驗證憑證。

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>應用程式閘道是否原本就與 Azure Key Vault 整合？

是，應用程式閘道 v2 SKU 支援 Key Vault。 如需詳細資訊，請參閱[使用 Key Vault 憑證終止 TLS](key-vault-certs.md)。

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>如何為 .com 和 .net 網站設定 HTTPS 接聽程式？ 

針對多個網域型 (主機型) 路由，您可以建立多網站接聽程式、設定以 HTTPS 為通訊協定的接聽程式，以及將接聽程式和路由規則建立關聯。 如需詳細資訊，請參閱[使用應用程式閘道來裝載多個網站](./multiple-site-overview.md)。

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>我可以在 .pfx 檔案密碼中使用特殊字元嗎？

否，在 .pfx 檔案密碼中只能使用英數位元。

### <a name="my-ev-certificate-is-issued-by-digicert-and-my-intermediate-certificate-has-been-revoked-how-do-i-renew-my-certificate-on-application-gateway"></a>我的 EV 憑證是由 DigiCert 發出，而我的中繼憑證已撤銷。 如何? 更新應用程式閘道上的憑證？

憑證授權單位單位 (CA) 瀏覽器成員最近發行的報表，詳述由我們的客戶、Microsoft 和較大的技術團體所發出的多個憑證，這些憑證不符合公開信任之 Ca 的產業標準。關於不符合規範之 Ca 的報告可在這裡找到：  

* [Bug 1649951](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951)
* [Bug 1650910](https://bugzilla.mozilla.org/show_bug.cgi?id=1650910)

根據產業的合規性需求，CA 廠商會開始撤銷不符合規範的 Ca，併發行符合規範的 Ca，要求客戶重新發出其憑證。Microsoft 與這些廠商密切合作，以將潛在的影響降至最低， **但您在「攜帶您自己的憑證」中使用的自我簽發憑證或憑證 (BYOC) 案例仍有意外撤銷的風險**。

若要檢查您的應用程式所使用的憑證是否已撤銷參考 [DigiCert 的公告](https://knowledge.digicert.com/alerts/DigiCert-ICA-Replacement) 和 [憑證撤銷追蹤](https://misissued.com/#revoked)程式。 如果您的憑證已被撤銷或將遭到撤銷，您將需要向您的應用程式中使用的 CA 廠商要求新憑證。 若要避免應用程式的可用性因為未預期地撤銷憑證而中斷，或要更新已撤銷的憑證，請參閱我們的 Azure 更新文章，以取得支援 BYOC 的各種 Azure 服務的補救連結： https://azure.microsoft.com/updates/certificateauthorityrevocation/

如需應用程式閘道的特定資訊，請參閱下方-

如果您使用其中一個已撤銷 ICAs 所發行的憑證，則您的應用程式可用性可能會中斷，而且視您的應用程式而定，您可能會收到各種錯誤訊息，包括但不限於： 

1.  不正確憑證/撤銷憑證
2.  連線逾時
3.  HTTP 502

若要避免因為此問題而中斷應用程式，或重新發行已撤銷的 CA，您必須採取下列動作： 

1.  請洽詢您的憑證提供者，以瞭解如何重新發行您的憑證
2.  重新發行之後，請在 Azure 應用程式 Gateway/WAF 上更新您的憑證，並搭配完整 [的信任鏈](/windows/win32/seccrypto/certificate-chains) (分葉、中繼、根憑證) 。 根據您使用憑證的位置，在接聽程式或應用程式閘道的 HTTP 設定上，依照下列步驟來更新憑證，並查看所述的檔連結以取得詳細資訊。
3.  更新您的後端應用程式伺服器，以使用重新發行的憑證。 根據您所使用的後端伺服器而定，您的憑證更新步驟可能會有所不同。 請查看廠商提供的檔。

若要更新您接聽程式中的憑證：

1.  在 [Azure 入口網站](https://portal.azure.com/)中，開啟您的應用程式閘道資源
2.  開啟與您的憑證相關聯的接聽程式設定
3.  按一下 [更新或編輯選取的憑證]
4.  以密碼上傳新的 PFX 憑證，然後按一下 [儲存]
5.  存取網站，並確認網站是否如預期般運作。如需詳細資訊，請參閱 [此處](./renew-certificates.md)的檔。

如果您在應用程式閘道接聽程式中參考來自 Azure KeyVault 的憑證，建議您遵循下列步驟來進行快速變更–

1.  在 [Azure 入口網站](https://portal.azure.com/)中，流覽至已與應用程式閘道相關聯的 Azure KeyVault 設定
2.  在您的存放區中新增/匯入重新發行的憑證。 如需如何進行的詳細資訊，請參閱 [此處](../key-vault/certificates/quick-create-portal.md) 的檔。
3.  匯入憑證後，流覽至您的應用程式閘道接聽程式設定，然後在 [從 Key Vault 選擇憑證] 下，按一下 [憑證] 下拉式清單，然後選擇最近新增的憑證
4.  按一下 [儲存] 以取得具有 Key Vault 憑證的應用程式閘道上 TLS 終止的詳細資訊，請參閱 [此處](./key-vault-certs.md)的檔。


若要更新 HTTP 設定中的憑證：

如果您使用應用程式閘道/WAF 服務的 V1 SKU，則必須上傳新的憑證作為後端驗證憑證。
1.  在 [Azure 入口網站](https://portal.azure.com/)中，開啟您的應用程式閘道資源
2.  開啟與您的憑證相關聯的 HTTP 設定
3.  按一下 [新增憑證] 並上傳重新發行的憑證，然後按一下 [儲存]
4.  您稍後可以按一下 [...] 來移除舊憑證 在舊憑證旁的 [選項] 按鈕，然後選取 [刪除]，再按一下 [儲存]。
如需詳細資訊，請參閱 [此處](./end-to-end-ssl-portal.md#add-authenticationtrusted-root-certificates-of-back-end-servers)的檔。

如果您使用應用程式閘道/WAF 服務的 V2 SKU，就不需要上傳 HTTP 設定中的新憑證，因為 V2 SKU 會使用「受信任的根憑證」，而不需要採取任何動作。

## <a name="configuration---ingress-controller-for-aks"></a>設定 - AKS 的輸入控制器

### <a name="what-is-an-ingress-controller"></a>什麼是輸入控制器？

Kubernetes 可讓您建立 `deployment` 和 `service` 資源，以在叢集內部公開一組 Pod。 為了對外公開同樣的服務，已定義一項 [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) 資源，可提供負載平衡、TLS 終止和以名稱為基礎的虛擬裝載。
為了滿足此 `Ingress` 資源，需要輸入控制器，以接聽 `Ingress` 資源的任何變更，並設定負載平衡器原則。

應用程式閘道輸入控制器 (AGIC) 可讓 [Azure 應用程式閘道](https://azure.microsoft.com/services/application-gateway/) 作為 [AZURE KUBERNETES SERVICE](https://azure.microsoft.com/services/kubernetes-service/) 也稱為 AKS 叢集的輸入。

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>單一輸入控制器執行個體可以管理多個應用程式閘道嗎？

目前，一個輸入控制器執行個體只能與一個應用程式閘道相關聯。

### <a name="why-is-my-aks-cluster-with-kubenet-not-working-with-agic"></a>為什麼我的 AKS 叢集的 kubenet 無法與 AGIC 搭配運作？

AGIC 會嘗試自動建立路由表資源與應用程式閘道子網的關聯性，但因為缺少 AGIC 的許可權，所以可能無法這麼做。 如果 AGIC 無法將路由表與應用程式閘道子網建立關聯，AGIC 記錄中會出現錯誤訊息，在這種情況下，您必須手動將 AKS 叢集建立的路由表與應用程式閘道的子網產生關聯。 如需詳細資訊，請參閱 [支援的使用者定義路由](configuration-infrastructure.md#supported-user-defined-routes)。

### <a name="can-i-connect-my-aks-cluster-and-application-gateway-in-separate-virtual-networks"></a>我可以將 AKS 叢集和應用程式閘道連接在不同的虛擬網路嗎？ 

是的，只要虛擬網路已對等互連，而且沒有重迭的位址空間。 如果您執行的是 AKS with kubenet，請務必將 AKS 所產生的路由表與應用程式閘道子網建立關聯。 

### <a name="what-features-are-not-supported-on-the-agic-add-on"></a>AGIC 附加元件不支援哪些功能？ 

請參閱這裡透過 Helm 部署的 AGIC 與在[此](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)部署為 AKS 附加元件之間的差異

### <a name="when-should-i-use-the-add-on-versus-the-helm-deployment"></a>我何時應該使用附加元件與 Helm 部署？ 

請查看透過 Helm 部署的 AGIC 與在 [此](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)部署為 AKS 附加元件之間的差異，特別是記錄) 支援的案例 (s 的資料表，而 AGIC 是透過 Helm 部署，而不是 AKS 附加元件。 一般來說，透過 Helm 部署可讓您在正式發行之前測試 Beta 版功能和發行候選版本。 

### <a name="can-i-control-which-version-of-agic-will-be-deployed-with-the-add-on"></a>我可以控制要使用附加元件部署的 AGIC 版本嗎？

否，AGIC 附加元件是受控服務，這表示 Microsoft 會自動將附加元件更新為最新的穩定版本。 

## <a name="diagnostics-and-logging"></a>診斷和記錄

### <a name="what-types-of-logs-does-application-gateway-provide"></a>應用程式閘道提供哪幾種記錄？

應用程式閘道提供三個記錄： 

* **ApplicationGatewayAccessLog**：存取記錄包含提交給應用程式閘道前端的每個要求。 資料包括呼叫者的 IP、所要求的 URL、回應延遲、傳回碼，以及輸入和輸出位元組。其中，每個應用程式閘道各一筆記錄。
* **ApplicationGatewayPerformanceLog**：效能記錄擷取每個應用程式閘道的效能資訊。 資訊包括輸送量 (以位元組為單位)、已處理的要求總數、失敗要求計數，以及狀況良好和不良的後端執行個體計數。
* **ApplicationGatewayFirewallLog**：對於您以 WAF 設定的應用程式閘道，防火牆記錄包含透過偵測模式或預防模式所記錄的要求。

所有記錄每 60 秒收集一次。 如需詳細資訊，請參閱[應用程式閘道的後端健康情況、診斷記錄和計量](application-gateway-diagnostics.md)。

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>如何知道我的後端集區成員狀態良好？

使用 PowerShell Cmdlet `Get-AzApplicationGatewayBackendHealth` 或入口網站來驗證健康情況。 如需詳細資訊，請參閱[應用程式閘道診斷](application-gateway-diagnostics.md)。

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>診斷記錄的保留原則為何？

診斷記錄會傳送至客戶的儲存體帳戶。 客戶可以根據自己的喜好來設定保留原則。 診斷記錄也可以傳送至事件中樞或 Azure 監視器記錄。 如需詳細資訊，請參閱[應用程式閘道診斷](application-gateway-diagnostics.md)。

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>如何取得應用程式閘道的稽核記錄？

在入口網站中，請在應用程式閘道的功能表刀鋒視窗中選取 [活動記錄]，以存取稽核記錄。 

### <a name="can-i-set-alerts-with-application-gateway"></a>是否可以設定應用程式閘道的警示？

是。 在應用程式閘道中，您可以在計量上設定警示。 如需詳細資訊，請參閱[應用程式閘道計量](./application-gateway-metrics.md)和[接收警示通知](../azure-monitor/platform/alerts-overview.md)。

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>如何分析應用程式閘道的流量統計資料？

有幾種方式可讓您檢視和分析存取記錄。 使用 Azure 監視器記錄、Excel、Power BI 等等。

您也可以使用 Resource Manager 範本，安裝並執行熱門的 [GoAccess](https://goaccess.io/) 記錄分析器，以分析應用程式閘道存取記錄。 GoAccess 提供實用的 HTTP 流量統計資料，例如非重複訪客、所要求的檔案、主機、作業系統、瀏覽器和 HTTP 狀態碼。 如需詳細資訊，請參閱 GitHub 中的[Resource Manager 範本資料夾中的讀我檔案](https://aka.ms/appgwgoaccessreadme)。

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>哪些原因會導致後端健康情況傳回未知狀態？

存取後端時，若遭到應用程式閘道子網路上的網路安全性群組 (NSG)、自訂 DNS 或使用者定義路由 (UDR) 封鎖，您通常會看到不明狀態。 如需詳細資訊，請參閱[應用程式閘道的後端健康情況、診斷記錄和計量](application-gateway-diagnostics.md)。

### <a name="are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet"></a>Nsg 是否支援與應用程式閘道 v2 子網相關聯的 NSG 流量記錄？

由於目前的平臺限制，如果您在應用程式閘道 v2 上有 NSG (Standard_v2，WAF_v2) 子網，而且如果您已在其上啟用 NSG 流量記錄，您將會看到不具決定性的行為，而且目前不支援此案例。

### <a name="does-application-gateway-store-customer-data"></a>應用程式閘道是否會儲存客戶資料？

否，應用程式閘道不會儲存客戶資料。

## <a name="next-steps"></a>後續步驟

若要深入了解應用程式閘道，請參閱[什麼是 Azure 應用程式閘道？](overview.md)。
