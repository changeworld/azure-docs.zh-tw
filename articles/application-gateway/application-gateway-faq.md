---
title: Azure 應用程式閘道相關的常見問題集
description: 尋找 Azure 應用程式閘道相關常見問題集的解答。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 05/26/2020
ms.author: victorh
ms.openlocfilehash: fd5617af2da9aa00cb75deb82f83be29db78d79d
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873496"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>應用程式閘道相關的常見問題集

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下是關於 Azure 應用程式閘道的常見問題。

## <a name="general"></a>一般

### <a name="what-is-application-gateway"></a>什麼是應用程式閘道？

Azure 應用程式閘道以服務形式提供應用程式傳遞控制器 (ADC)。 提供各種第 7 層負載平衡功能，供您的應用程式使用。 這項服務具有高可用性、可調整，而且完全由 Azure 管理。

### <a name="what-features-does-application-gateway-support"></a>應用程式閘道支援哪些功能？

應用程式閘道支援自動調整、TLS 卸載、端對端 TLS、Web 應用程式防火牆 (WAF)、以 Cookie 為基礎的工作階段親和性、以 URL 路徑為基礎的路由、多網站裝載，以及其他功能。 如需完整的支援功能清單，請參閱[應用程式閘道簡介](application-gateway-introduction.md)。

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>應用程式閘道和 Azure Load Balancer 有何不同？

應用程式閘道是第 7 層負載平衡器，這表示只處理 web 流量 (HTTP、HTTPS、WebSocket 和 HTTP/2)。 支援 TLS 終止、以 Cookie 為基礎的工作階段親和性，以及循環配置資源等功能，以平衡流量負載。 Load Balancer 平衡第 4 層 (TCP 或 UDP) 的流量負載。

### <a name="what-protocols-does-application-gateway-support"></a>應用程式閘道支援哪些通訊協定？

應用程式閘道支援 HTTP、HTTPS、HTTP/2 和 WebSocket。

### <a name="how-does-application-gateway-support-http2"></a>應用程式閘道如何支援 HTTP/2？

請參閱 [HTTP/2 支援](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support)。

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>支援哪些資源作為後端集區的一部分？

請參閱[支援的後端資源](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools)。

### <a name="in-what-regions-is-application-gateway-available"></a>應用程式閘道在哪些區域推出？

應用程式閘道適用於全域 Azure 的所有區域。 [Azure China 21Vianet](https://www.azure.cn/) 和 [Azure Government](https://azure.microsoft.com/overview/clouds/government/) 中也提供此服務。

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>此部署是我的訂用帳戶專用，還是客戶共用？

應用程式閘道是您虛擬網路中專用的部署。

### <a name="does-application-gateway-support-http-to-https-redirection"></a>應用程式閘道是否 HTTP 到 HTTPS 重新導向？

支援重新導向。 請參閱[應用程式閘道重新導向概觀](application-gateway-redirect-overview.md)。

### <a name="in-what-order-are-listeners-processed"></a>接聽程式的處理順序為何？

請參閱[接聽程式處理順序](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners)。

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>哪裡可以找到應用程式閘道的 IP 和 DNS？

如果您使用公用 IP 位址作為端點，則可以在公用 IP 位址資源上找到 IP 和 DNS 資訊。 在入口網站的應用程式閘道概觀頁面上也可以找到。 如果您使用內部 IP 位址，請在概觀頁面頁面上尋找此資訊。

若為 v2 SKU，請開啟公用 IP 資源，然後選取 [設定]。 [DNS 名稱標籤 (選用)] 欄位可用來設定 DNS 名稱。

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>保持連線逾時和 TCP 閒置逾時的設定為何？

「保持連線逾時」控制應用程式閘道在重複使用或關閉持續連線之前，以多少時間來等待用戶端傳送另一個 HTTP 要求。 「TCP 閒置逾時」控制在沒有活動的情況下，TCP 連線保持開啟的時間長度。 

在應用程式閘道 v1 SKU 中，「保持連線逾時」是 120 秒，而在 v2 SKU 中是 75 秒。 在應用程式閘道 v1 和 v2 SKU 的前端虛擬 IP (VIP) 上，「TCP 閒置逾時」都預設為 4 分鐘。 您無法變更這些值。

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>在應用程式閘道的存留期內，IP 或 DNS 名稱是否會變更？

在應用程式閘道 V1 SKU 中，如果您停止又啟動應用程式閘道，VIP 可能會變更。 但在閘道的存留期內，與應用程式閘道相關聯的 DNS 名稱不會變更。 由於 DNS 名稱不會變更，您應該使用 CNAME 別名，並指向應用程式閘道的 DNS 位址。 在應用程式閘道 V2 SKU 中，您可以將 IP 位址設定為靜態，如此，在應用程式閘道的存留期內，IP 和 DNS 名稱就不會變更。 

### <a name="does-application-gateway-support-static-ip"></a>應用程式閘道是否支援靜態 IP？

是，應用程式閘道 v2 SKU 支援靜態公用 IP 位址。 v1 SKU 支援靜態內部 IP。

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>應用程式閘道是否在閘道上支援多個公用 IP？

一個應用程式閘道只支援一個公用 IP 位址。

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>應用程式閘道的子網路應該要多大？

請參閱[應用程式閘道子網路大小考量](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)。

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>可以將多個應用程式閘道資源部署到單一子網路嗎？

是。 除了特定應用程式閘道部署的多個執行個體以外，您還可以將另一個獨特的應用程式閘道資源，佈建到含有不同應用程式閘道資源的現有子網路。

單一子網路無法同時支援 v2 和 v1 應用程式閘道 SKU。

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>應用程式閘道 v2 是否支援使用者定義的路由 (UDR)？

是，但僅限於特殊情況。 如需詳細資訊，請參閱[應用程式閘道設定概觀](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet)。

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>應用程式閘道是否支援 x-forwarded-for 標頭？

是。 請參閱[修改要求](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request)。

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

否。 應用程式閘道 V2 尚不支援使用 NTLM 驗證來通過 Proxy 處理要求。

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>應用程式閘道親和性 Cookie 是否支援 SameSite 屬性？
是，[Chromium 瀏覽器](https://www.chromium.org/Home) [v80 更新](https://chromiumdash.appspot.com/schedule)強制規定不含 SameSite 屬性的 HTTP Cookie 視同 SameSite=Lax。 這表示在協力廠商環境下，瀏覽器不會傳送應用程式閘道親和性 Cookie。 

為了支援這種情況，除了現有的 *ApplicationGatewayAffinity* Cookie，應用程式閘道還會插入另一個名為 *ApplicationGatewayAffinityCORS* 的 Cookie。  這些 Cookie 很類似，但 *ApplicationGatewayAffinityCORS* Cookie 多增加兩個屬性：*SameSite=None; Secure*。 即使是跨原始來源要求，這些屬性仍然會維護黏性工作階段。 如需詳細資訊，請參閱[以 Cookie 為基礎的親和性](configuration-overview.md#cookie-based-affinity)一節。

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

是。 應用程式閘道一律部署在虛擬網路子網路中。 這個子網路只能包含應用程式閘道。 如需詳細資訊，請參閱[虛擬網路和子網路需求](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet)。

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>應用程式閘道能否與虛擬網路外面或訂用帳戶外面的執行個體進行通訊？

只要您有 IP 連線能力，應用程式閘道就可以與所在虛擬網路外面的執行個體進行通訊。 應用程式閘道也可以與所在訂用帳戶外面的執行個體進行通訊。 如果您打算使用內部 IP 作為後端集區成員，請使用[虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)或 [Azure VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>是否可以在應用程式閘道子網路中部署其他任何項目？

否。 但是，您可以在子網路中部署其他應用程式閘道。

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>應用程式閘道子網路是否支援網路安全性群組？

[應用程式閘道子網路中的網路安全性群組](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet)。

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>應用程式閘道子網路是否支援使用者定義路由？

請參閱[應用程式閘道子網路中支援的使用者定義路由](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet)。

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

請參閱[處理規則的順序](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules)。

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>自訂探查的 [主機] 欄位代表什麼？

當您在應用程式閘道上已設定多網站時，[主機] 欄位指定探查送往的名稱。 否則，請使用 '127.0.0.1'。 此值與虛擬機器主機名稱不同。 格式為\<通訊協定\>://\<主機\>:\<連接埠\>\<路徑\>。

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>我可以只允許少數幾個來源 IP 位址存取應用程式閘道嗎？

是。 請參閱[限制特定來源 IP 的存取](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips)。

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
3. 依優先順序使用下列設定，針對應用程式閘道子網路建立並附加[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview)：
    
    a. 允許來源為 **GatewayManager** 服務標記、目的地為 **Any**、目的地連接埠為 **65200-65535** 的流量。 Azure 基礎結構通訊需要此連接埠範圍。 這些連接埠由憑證驗證保護 (鎖定)。 如果沒有適當的憑證，外部實體 (包括閘道使用者管理員) 無法變更這些端點
    
    b. 允許來源為 **AzureLoadBalancer** 服務標記、目的地和目的地連接埠為 **Any** 的流量
    
    c. 拒絕來源為 **Internet** 服務標記、目的地和目的地連接埠為 **Any** 的所有輸入流量。 在輸入規則中，將此規則設定為「最低優先順序」
    
    d. 保留預設規則，例如允許 VirtualNetwork 輸入，以免封鎖私人 IP 位址的存取
    
    e. 無法封鎖輸出網際網路連線。 否則，您會遇到記錄、計量等問題。

僅限私人 IP 存取的 NSG 設定範例：![僅限私人 IP 存取的應用程式閘道 V2 NSG 設定](./media/application-gateway-faq/appgw-privip-nsg.png)

## <a name="configuration---tls"></a>設定 - TLS

### <a name="what-certificates-does-application-gateway-support"></a>應用程式閘道支援哪些憑證？

應用程式閘道支援自我簽署憑證、憑證授權單位 (CA) 憑證、擴充功能驗證 (EV) 憑證和萬用字元憑證。

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

針對多個網域型 (主機型) 路由，您可以建立多網站接聽程式、設定以 HTTPS 為通訊協定的接聽程式，以及將接聽程式和路由規則建立關聯。 如需詳細資訊，請參閱[使用應用程式閘道來裝載多個網站](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview)。

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>我可以在 .pfx 檔案密碼中使用特殊字元嗎？

否，在 .pfx 檔案密碼中只能使用英數位元。

## <a name="configuration---ingress-controller-for-aks"></a>設定 - AKS 的輸入控制器

### <a name="what-is-an-ingress-controller"></a>什麼是輸入控制器？

Kubernetes 可讓您建立 `deployment` 和 `service` 資源，以在叢集內部公開一組 Pod。 為了對外公開同樣的服務，已定義一項 [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) 資源，可提供負載平衡、TLS 終止和以名稱為基礎的虛擬裝載。
為了滿足此 `Ingress` 資源，需要輸入控制器，以接聽 `Ingress` 資源的任何變更，並設定負載平衡器原則。

應用程式閘道輸入控制器允許 [Azure 應用程式閘道](https://azure.microsoft.com/services/application-gateway/)作為 [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (也稱為 AKS 叢集) 的輸入。

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>單一輸入控制器執行個體可以管理多個應用程式閘道嗎？

目前，一個輸入控制器執行個體只能與一個應用程式閘道相關聯。

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

是。 在應用程式閘道中，您可以在計量上設定警示。 如需詳細資訊，請參閱[應用程式閘道計量](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics)和[接收警示通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>如何分析應用程式閘道的流量統計資料？

有幾種方式可讓您檢視和分析存取記錄。 使用 Azure 監視器記錄、Excel、Power BI 等等。

您也可以使用 Resource Manager 範本，安裝並執行熱門的 [GoAccess](https://goaccess.io/) 記錄分析器，以分析應用程式閘道存取記錄。 GoAccess 提供實用的 HTTP 流量統計資料，例如非重複訪客、所要求的檔案、主機、作業系統、瀏覽器和 HTTP 狀態碼。 如需詳細資訊，請參閱 GitHub 中的[Resource Manager 範本資料夾中的讀我檔案](https://aka.ms/appgwgoaccessreadme)。

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>哪些原因會導致後端健康情況傳回未知狀態？

存取後端時，若遭到應用程式閘道子網路上的網路安全性群組 (NSG)、自訂 DNS 或使用者定義路由 (UDR) 封鎖，您通常會看到不明狀態。 如需詳細資訊，請參閱[應用程式閘道的後端健康情況、診斷記錄和計量](application-gateway-diagnostics.md)。

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>是否有任何情況會導致 NSG 流量記錄不顯示允許的流量？

是。 如果您的設定符合下列情況，則在 NSG 流量記錄中就看不到允許的流量：
- 您已部署應用程式閘道 v2
- 您在應用程式閘道子網路上有 NSG
- 您已在該 NSG 上啟用 NSG 流量記錄

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>如何使用只有私人前端 IP 位址的應用程式閘道 V2？

應用程式閘道 V2 目前不支援私人 IP 模式。 支援下列組合
* 私人 IP 和公用 IP
* 僅限公用 IP

但是，如果想要使用只有私人 IP 的應用程式閘道 V2，您可以遵循下列程序：
1. 建立具有公用和私人前端 IP 位址的應用程式閘道
2. 請不要為公用前端 IP 位址建立任何接聽程式。 如果沒有為公用 IP 位址建立接聽程式，應用程式閘道就不會此位址上接聽任何流量。
3. 依優先順序使用下列設定，針對應用程式閘道子網路建立並附加[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview)：
    
    a. 允許來源為 **GatewayManager** 服務標記、目的地為 **Any**、目的地連接埠為 **65200-65535** 的流量。 Azure 基礎結構通訊需要此連接埠範圍。 這些連接埠由憑證驗證保護 (鎖定)。 如果沒有適當的憑證，外部實體 (包括閘道使用者管理員) 無法變更這些端點
    
    b. 允許來源為 **AzureLoadBalancer** 服務標記、目的地連接埠為 **Any** 的流量
    
    c. 拒絕來源為 **Internet** 服務標記、目的地連接埠為 **Any** 的所有輸入流量。 在輸入規則中，將此規則設定為「最低優先順序」
    
    d. 保留預設規則，例如允許 VirtualNetwork 輸入，以免封鎖私人 IP 位址的存取
    
    e. 無法封鎖輸出網際網路連線。 否則，您會遇到記錄、計量等問題。

僅限私人 IP 存取的 NSG 設定範例：![僅限私人 IP 存取的應用程式閘道 V2 NSG 設定](./media/application-gateway-faq/appgw-privip-nsg.png)

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>應用程式閘道親和性 Cookie 是否支援 SameSite 屬性？
是，[Chromium 瀏覽器](https://www.chromium.org/Home) [v80 更新](https://chromiumdash.appspot.com/schedule)強制規定不含 SameSite 屬性的 HTTP Cookie 視同 SameSite=Lax。 這表示在協力廠商環境下，瀏覽器不會傳送應用程式閘道親和性 Cookie。 為了支援這種情況，除了現有的 *ApplicationGatewayAffinity* Cookie，應用程式閘道還會插入另一個名為 *ApplicationGatewayAffinityCORS* 的 Cookie。  這些 Cookie 很類似，但 *ApplicationGatewayAffinityCORS* Cookie 多增加兩個屬性：*SameSite=None; Secure*。 即使是跨原始來源要求，這些屬性仍然會維護黏性工作階段。 如需詳細資訊，請參閱[以 Cookie 為基礎的親和性](configuration-overview.md#cookie-based-affinity)一節。

## <a name="next-steps"></a>後續步驟

若要深入了解應用程式閘道，請參閱[什麼是 Azure 應用程式閘道？](overview.md)。
