---
title: Azure 應用程式閘道的相關常見問題
description: 尋找 Azure 應用程式閘道常見問題的解答。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/31/2019
ms.author: victorh
ms.openlocfilehash: 27048a8464fc7380a5c11ab6bbb543e35c089774
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919595"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>關於應用程式閘道的常見問題

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下是關於 Azure 應用程式閘道的常見問題。

## <a name="general"></a>一般

### <a name="what-is-application-gateway"></a>什麼是應用程式閘道？

Azure 應用程式閘道會以服務形式提供應用程式傳遞控制器（ADC）。 它為您的應用程式提供各種第7層負載平衡功能。 這項服務具有高可用性、可擴充性，而且完全由 Azure 管理。

### <a name="what-features-does-application-gateway-support"></a>應用程式閘道支援哪些功能？

應用程式閘道支援自動調整、SSL 卸載和端對端 SSL、web 應用程式防火牆（WAF）、以 cookie 為基礎的會話親和性、以 URL 路徑為基礎的路由、多網站裝載和其他功能。 如需完整的支援功能清單，請參閱[應用程式閘道簡介](application-gateway-introduction.md)。

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>應用程式閘道和 Azure Load Balancer 有何不同？

應用程式閘道是第7層負載平衡器，這表示它只適用于 web 流量（HTTP、HTTPS、WebSocket 和 HTTP/2）。 它支援諸如 SSL 終止、以 cookie 為基礎的會話親和性等功能，以及用於負載平衡流量的迴圈配置資源。 Load Balancer 在第4層（TCP 或 UDP）平衡流量負載。

### <a name="what-protocols-does-application-gateway-support"></a>應用程式閘道支援哪些通訊協定？

應用程式閘道支援 HTTP、HTTPS、HTTP/2 和 WebSocket。

### <a name="how-does-application-gateway-support-http2"></a>應用程式閘道如何支援 HTTP/2？

請參閱[HTTP/2 支援](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support)。

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>後端集區中支援哪些資源？

請參閱[支援的後端資源](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools)。

### <a name="in-what-regions-is-application-gateway-available"></a>應用程式閘道可用的區域有哪些？

應用程式閘道適用於全域 Azure 的所有區域。 它也適用于[Azure 中國的世紀](https://www.azure.cn/)和[Azure Government](https://azure.microsoft.com/overview/clouds/government/)。

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>此部署是否專屬於我的訂用帳戶，或是否跨客戶共用？

應用程式閘道是您虛擬網路中專用的部署。

### <a name="does-application-gateway-support-http-to-https-redirection"></a>應用程式閘道是否支援 HTTP 對 HTTPS 重新導向？

支援重新導向。 請參閱[應用程式閘道重新導向總覽](application-gateway-redirect-overview.md)。

### <a name="in-what-order-are-listeners-processed"></a>接聽程式的處理順序為何？

請參閱接聽程式[處理的順序](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners)。

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>哪裡可以找到應用程式閘道的 IP 和 DNS？

如果您使用公用 IP 位址作為端點，您會在公用 IP 位址資源上找到 IP 和 DNS 資訊。 或在入口網站中，于應用程式閘道的 [總覽] 頁面上找到它。 如果您使用的是內部 IP 位址，請在 [總覽] 頁面上尋找相關資訊。

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Keep-alive timeout 和 TCP 閒置超時的設定為何？

*Keep-alive timeout*會控制應用程式閘道等待用戶端在持續連線上傳送另一個 HTTP 要求的時間長度，然後再重複使用或關閉它。 *Tcp 閒置時間*會控制在沒有活動的情況下，tcp 連線保持開啟的時間長度。 

應用程式閘道 v1 SKU 中的*保持連線超時時間*為120秒，而在 v2 sku 中為75秒。 在應用程式閘道 v1 和 v2 SKU 的前端虛擬 IP （VIP）上， *TCP 閒置超時*是4分鐘的預設值。 

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>IP 或 DNS 名稱是否會在應用程式閘道的存留期內變更？

在應用程式閘道 V1 SKU 中，如果您停止並啟動應用程式閘道，VIP 可能會變更。 但是與應用程式閘道相關聯的 DNS 名稱不會在閘道的存留期內變更。 由於 DNS 名稱不會變更，因此您應該使用 CNAME 別名，並將它指向應用程式閘道的 DNS 位址。 在應用程式閘道 V2 SKU 中，您可以將 IP 位址設定為靜態，因此 IP 和 DNS 名稱不會在應用程式閘道的存留期內變更。 

### <a name="does-application-gateway-support-static-ip"></a>應用程式閘道是否支援靜態 IP？

是，應用程式閘道 v2 SKU 支援靜態公用 IP 位址。 v1 SKU 支援靜態內部 IP。

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>應用程式閘道是否在閘道上支援多個公用 IP？

應用程式閘道僅支援一個公用 IP 位址。

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>應用程式閘道的子網路應該要多大？

請參閱[應用程式閘道子網大小考慮](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)。

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>可以將多個應用程式閘道資源部署到單一子網路嗎？

是。 除了給定應用程式閘道部署的多個實例以外，您還可以將另一個唯一的應用程式閘道資源布建到包含不同應用程式閘道資源的現有子網。

單一子網不能同時支援 Standard_v2 和標準應用程式閘道。

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>應用程式閘道是否支援 x-forwarded-for 標頭？

是。 請參閱對[要求的修改](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request)。

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>部署應用程式閘道需要多久的時間？ 我的應用程式閘道會在更新時工作嗎？

新的應用程式閘道 v1 SKU 部署最多可能需要 20 分鐘的時間來佈建。 實例大小或計數的變更不會造成干擾，而且閘道會在這段時間內保持作用中狀態。

使用 v2 SKU 的大部分部署大約需要6分鐘的時間來布建。 不過，視部署類型而定，可能會花費較長的時間。 例如，跨多個具有多個實例的可用性區域部署可能需要超過6分鐘的時間。 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>我可以使用 Exchange Server 做為應用程式閘道的後端嗎？

否。 應用程式閘道不支援電子郵件通訊協定，例如 SMTP、IMAP 和 POP3。 

## <a name="performance"></a>效能

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>應用程式閘道如何支援高可用性和延展性？

當您部署了兩個或多個實例時，應用程式閘道 v1 SKU 可支援高可用性案例。 Azure 會將這些實例分散到更新和容錯網域，以確保實例不會同時失敗。 v1 SKU 可藉由新增多個相同閘道的執行個體來分攤負載，以支援延展性。

v2 SKU 會自動確保將新執行個體分散在各個容錯網域和更新網域。 如果您選擇 [區域冗余]，最新的實例也會分散到可用性區域，以提供區域性失敗復原功能。

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>如何? 使用應用程式閘道來達成跨資料中心的 DR 案例嗎？

使用流量管理員將流量分散到不同資料中心的多個應用程式閘道。

### <a name="does-application-gateway-support-autoscaling"></a>應用程式閘道是否支援自動調整？

是，應用程式閘道 v2 SKU 支援自動調整規模。 如需詳細資訊，請參閱自動調整[和區域-多餘的應用程式閘道](application-gateway-autoscaling-zone-redundant.md)。

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>手動或自動相應增加或相應減少會造成停機時間嗎？

否。 執行個體已分散於升級網域和容錯網域。

### <a name="does-application-gateway-support-connection-draining"></a>應用程式閘道是否支援連線清空？

是。 您可以設定連線清空來變更後端集區中的成員，而不會中斷。 如需詳細資訊，請參閱[應用程式閘道的連接清空一節](overview.md#connection-draining)。

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>是否可在中斷的情況下，將執行個體大小從中型變成大型？

是。

## <a name="configuration"></a>組態

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>應用程式閘道一律部署在虛擬網路中？

是。 應用程式閘道一律會部署在虛擬網路子網中。 這個子網只能包含應用程式閘道。 如需詳細資訊，請參閱[虛擬網路和子網需求](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet)。

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>應用程式閘道可以與其虛擬網路以外或其訂用帳戶以外的實例進行通訊嗎？

只要您有 IP 連線，應用程式閘道就可以與位於其所在的虛擬網路外的實例進行通訊。 應用程式閘道也可以與位於其所在的訂用帳戶外的實例進行通訊。 如果您打算使用內部 Ip 做為後端集區成員，請使用[虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)或[Azure VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>是否可以在應用程式閘道子網路中部署其他任何項目？

否。 但是，您可以在子網中部署其他應用程式閘道。

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>應用程式閘道子網是否支援網路安全性群組？

請參閱[應用程式閘道子網中的網路安全性群組](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet)。

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>應用程式閘道子網是否支援使用者定義的路由？

請參閱[應用程式閘道子網中支援的使用者定義路由](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet)。

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>應用程式閘道的限制為何？ 是否可以增加這些限制？

請參閱[應用程式閘道限制](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)。

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>我可以同時針對外部和內部流量使用應用程式閘道嗎？

是。 應用程式閘道支援每個應用程式閘道一個內部 IP 和一個外部 IP。

### <a name="does-application-gateway-support-virtual-network-peering"></a>應用程式閘道是否支援虛擬網路對等互連？

是。 虛擬網路對等互連可協助對其他虛擬網路中的流量進行負載平衡。

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>當內部部署伺服器是透過 ExpressRoute 或 VPN 通道進行連線時，我可以與它們溝通嗎？

是，只有流量允許即可。

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>一個後端集區可以在不同的埠上提供許多應用程式嗎？

支援微服務架構。 若要在不同的埠上進行探查，您需要設定多個 HTTP 設定。

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>自訂探查在回應資料上是否支援萬用字元或 RegEx？

否。 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>應用程式閘道中的路由規則如何處理？

請參閱[處理規則的順序](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules)。

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>若為自訂探查，[主機] 欄位代表什麼？

[主機] 欄位會指定當您在應用程式閘道上設定了多個網站時，要傳送探查的名稱。 否則，請使用 ' 127.0.0.1 '。 此值與虛擬機器主機名稱不同。 其格式為 \<通訊協定\>：//\<主機\>..\<埠\>\<路徑\>。

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>我可以只允許應用程式閘道存取一些來源 IP 位址嗎？

是。 請參閱[限制對特定來源 ip 的存取](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips)。

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>公用和私用的接聽程式是否可以使用相同的埠？

否。

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>是否有可從 v1 SKU 遷移至 v2 SKU 的指導方針？

是。 如需詳細資訊，請參閱[將 Azure 應用程式閘道和 Web 應用程式防火牆從 V1 遷移至 v2](migrate-v1-v2.md)。

### <a name="does-application-gateway-support-ipv6"></a>應用程式閘道是否支援 IPv6？

應用程式閘道 v2 目前不支援 IPv6。 它只能使用 IPv4 在雙重堆疊 VNet 中運作，但閘道子網必須是僅 IPv4。 應用程式閘道 v1 不支援雙重堆疊 Vnet。 

## <a name="configuration---ssl"></a>設定-SSL

### <a name="what-certificates-does-application-gateway-support"></a>應用程式閘道支援哪些憑證？

應用程式閘道支援自我簽署憑證、憑證授權單位單位（CA）憑證、擴充驗證（EV）憑證和萬用字元憑證。

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

如需如何自訂 SSL 選項的詳細資訊，請參閱[在應用程式閘道上設定 ssl 原則版本和加密套件](application-gateway-configure-ssl-policy-powershell.md)。

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>應用程式閘道是否支援將流量重新加密至後端？

是。 應用程式閘道支援 SSL 卸載和端對端 SSL，這會 vm 對後端的流量。

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>我可以設定 SSL 原則來控制 SSL 通訊協定版本嗎？

是。 您可以設定應用程式閘道拒絕 TLS 1.0、TLS 1.1 和 TLS 1.2。 根據預設，SSL 2.0 和3.0 已停用且無法設定。

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>可以設定加密套件和原則的順序嗎？

是。 在應用程式閘道中，您可以[設定加密套件](application-gateway-ssl-policy-overview.md)。 若要定義自訂原則，請啟用至少一個下列的加密套件。 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

應用程式閘道使用 SHA256 來進行後端管理。

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>應用程式閘道支援多少個 SSL 憑證？

應用程式閘道支援最多100的 SSL 憑證。

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>應用程式閘道支援後端重新加密的驗證憑證數目？

應用程式閘道支援最多100個驗證憑證。

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>應用程式閘道原本就與 Azure Key Vault 整合嗎？

是，應用程式閘道 v2 SKU 支援 Key Vault。 如需詳細資訊，請參閱[使用 Key Vault 憑證的 SSL 終止](key-vault-certs.md)。

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>如何? 為 .com 和 .net 網站設定 HTTPS 接聽程式嗎？ 

針對多個網域型（主機型）路由，您可以建立多網站接聽程式、設定使用 HTTPS 做為通訊協定的接聽程式，以及建立接聽程式與路由規則的關聯。 如需詳細資訊，請參閱[使用應用程式閘道來裝載多個網站](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview)。

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>我可以在 .pfx 檔案密碼中使用特殊字元嗎？

否，在 .pfx 檔案密碼中只使用英數位元。

## <a name="configuration---web-application-firewall-waf"></a>設定-web 應用程式防火牆（WAF）

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>WAF SKU 是否提供標準 SKU 中所有可用的功能？

是。 WAF 支援標準 SKU 中的所有功能。

### <a name="how-do-i-monitor-waf"></a>如何監視 WAF？

透過診斷記錄來監視 WAF。 如需詳細資訊，請參閱[應用程式閘道的診斷記錄和計量](application-gateway-diagnostics.md)。

### <a name="does-detection-mode-block-traffic"></a>偵測模式是否會封鎖流量？

否。 偵測模式只會記錄觸發 WAF 規則的流量。

### <a name="can-i-customize-waf-rules"></a>我可以自訂 WAF 規則嗎？

是。 如需詳細資訊，請參閱[自訂 WAF 規則群組和規則](application-gateway-customize-waf-rules-portal.md)。

### <a name="what-rules-are-currently-available-for-waf"></a>哪些規則目前可供 WAF？

WAF 目前支援 CRS [2.2.9](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229)、 [3.0](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)和[3.1](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31)。 這些規則會針對開啟 Web 應用程式安全性專案（OWASP）識別的大多數前10大弱點，提供基準安全性： 

* SQL 插入式攻擊保護
* 跨網站腳本保護
* 防止常見的 web 攻擊，例如命令插入、HTTP 要求走私、HTTP 回應分割和遠端檔案包含攻擊
* 防範 HTTP 通訊協定違規
* 防範 HTTP 通訊協定異常行為，例如遺漏主機使用者代理程式和接受標頭
* 防範 Bot、編目程式和掃描器
* 偵測常見的應用程式錯誤（也就是 Apache、IIS 等）

如需詳細資訊，請參閱[OWASP 前10大弱點](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)。

### <a name="does-waf-support-ddos-protection"></a>WAF 是否支援 DDoS 保護？

是。 您可以在部署應用程式閘道的虛擬網路上啟用 DDoS 保護。 此設定可確保 Azure DDoS 保護服務也會保護應用程式閘道虛擬 IP （VIP）。

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>是否有可從 v1 SKU 遷移至 v2 SKU 的指導方針？

是。 如需詳細資訊，請參閱[將 Azure 應用程式閘道和 Web 應用程式防火牆從 V1 遷移至 v2](migrate-v1-v2.md)。

## <a name="configuration---ingress-controller-for-aks"></a>設定-AKS 的輸入控制器

### <a name="what-is-an-ingress-controller"></a>什麼是輸入控制器？

Kubernetes 可讓您建立 `deployment` 和 `service` 資源，以在叢集內部公開一組 pod。 為了在外部公開相同的服務，會定義一個[`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/)資源，以提供負載平衡、SSL 終止和以名稱為基礎的虛擬主控。
為了滿足此 `Ingress` 資源，需要輸入控制器，以接聽 `Ingress` 資源的任何變更，並設定負載平衡器原則。

應用程式閘道輸入控制器可讓[Azure 應用程式閘道](https://azure.microsoft.com/services/application-gateway/)當做[Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) （也稱為 AKS 叢集）的輸入使用。

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>單一輸入控制器實例可以管理多個應用程式閘道嗎？

目前，輸入控制器的一個實例只能與一個應用程式閘道相關聯。

## <a name="diagnostics-and-logging"></a>診斷和記錄

### <a name="what-types-of-logs-does-application-gateway-provide"></a>應用程式閘道提供哪種類型的記錄？

應用程式閘道提供三個記錄： 

* **ApplicationGatewayAccessLog**：存取記錄包含提交至應用程式閘道前端的每個要求。 資料包括呼叫端的 IP、要求的 URL、回應延遲、傳回碼，以及傳入和傳出的位元組。每300秒會收集一次存取記錄檔。 它包含每個應用程式閘道的一筆記錄。
* **ApplicationGatewayPerformanceLog**：效能記錄會捕獲每個應用程式閘道的效能資訊。 資訊包含輸送量（以位元組為單位）、服務的總要求數、失敗的要求計數，以及狀況良好和狀況不良的後端實例計數。
* **ApplicationGatewayFirewallLog**：對於您使用 WAF 設定的應用程式閘道，防火牆記錄包含透過偵測模式或預防模式記錄的要求。

如需詳細資訊，請參閱[應用程式閘道的後端健康情況、診斷記錄和計量](application-gateway-diagnostics.md)。

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>如何知道我的後端集區成員狀態良好？

使用 PowerShell Cmdlet `Get-AzApplicationGatewayBackendHealth` 或入口網站來驗證健全狀況。 如需詳細資訊，請參閱[應用程式閘道診斷](application-gateway-diagnostics.md)。

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>診斷記錄的保留原則為何？

診斷記錄會流向客戶的儲存體帳戶。 客戶可以根據自己的喜好設定保留原則。 診斷記錄也可以傳送至事件中樞或 Azure 監視器記錄。 如需詳細資訊，請參閱[應用程式閘道診斷](application-gateway-diagnostics.md)。

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>如何取得應用程式閘道的稽核記錄？

在入口網站中，于應用程式閘道的功能表分頁上，選取 [**活動記錄**] 以存取 audit 記錄檔。 

### <a name="can-i-set-alerts-with-application-gateway"></a>是否可以設定應用程式閘道的警示？

是。 在應用程式閘道中，系統會在計量上設定警示。 如需詳細資訊，請參閱[應用程式閘道計量](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics)和[接收警示通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>如何分析應用程式閘道的流量統計資料？

您可以透過數種方式來查看和分析存取記錄。 使用 Azure 監視器記錄、Excel、Power BI 等等。

您也可以使用 Resource Manager 範本來安裝及執行適用于應用程式閘道存取記錄的熱門[GoAccess](https://goaccess.io/)記錄分析器。 GoAccess 提供寶貴的 HTTP 流量統計資料，例如唯一的訪客、要求的檔案、主機、作業系統、瀏覽器和 HTTP 狀態碼。 如需詳細資訊，請參閱 GitHub 中的[Resource Manager 範本資料夾中的讀我檔案](https://aka.ms/appgwgoaccessreadme)。

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>哪些原因會導致後端健康情況傳回未知的狀態？

通常當應用程式閘道子網上的網路安全性群組（NSG）、自訂 DNS 或使用者定義路由（UDR）封鎖對後端的存取權時，您會看到 [不明] 狀態。 如需詳細資訊，請參閱[應用程式閘道的後端健康情況、診斷記錄和計量](application-gateway-diagnostics.md)。

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>在任何情況下，NSG 流量記錄不會顯示允許的流量？

是。 如果您的設定符合下列案例，您將不會在 NSG 流量記錄中看到允許的流量：
- 您已部署應用程式閘道 v2
- 您在應用程式閘道子網上有 NSG
- 您已在該 NSG 上啟用 NSG 流量記錄

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>如何? 使用只有私人前端 IP 位址的應用程式閘道 V2？

應用程式閘道 V2 目前不支援私人 IP 模式。 它支援下列組合
* 私人 IP 和公用 IP
* 僅限公用 IP

但是，如果您想要使用只有私人 IP 的應用程式閘道 V2，您可以遵循下列程式：
1. 建立具有公用和私人前端 IP 位址的應用程式閘道
2. 請勿建立公用前端 IP 位址的任何接聽程式。 如果沒有為其建立接聽程式，應用程式閘道將不會在公用 IP 位址上接聽任何流量。
3. 使用下列設定，以優先順序建立並附加應用程式閘道子網的[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview)：
    
    a. 允許來自來源作為**GatewayManager**服務標籤和目的地的流量，做為**任何**和目的地埠，例如**65200-65535**。 Azure 基礎結構通訊需要此連接埠範圍。 這些埠會受到憑證驗證的保護（鎖定）。 外部實體（包括閘道使用者系統管理員）無法在沒有適當憑證的情況下，對這些端點起始變更
    
    b. 允許從來源作為**AzureLoadBalancer**服務標籤和目的地和目的地**埠的流量**
    
    c. 拒絕來自來源的所有輸入流量，做為**網際網路**服務標籤，以及目的地和目的地**埠。** 為此規則提供輸入規則中的*最高優先順序*
    
    d. 保留預設規則，例如允許 VirtualNetwork 輸入，如此就不會封鎖私人 IP 位址上的存取
    
    e. 無法封鎖輸出網際網路連線。 否則，您會面臨記錄、計量等問題。

僅限私人 IP 存取的範例 NSG 設定： ![應用程式閘道 V2 NSG 設定僅供私人 IP 存取](./media/application-gateway-faq/appgw-privip-nsg.png)

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>應用程式閘道親和性 cookie 是否支援 SameSite 屬性？
是， [Chromium browser](https://www.chromium.org/Home) [V80 update](https://chromiumdash.appspot.com/schedule)在 HTTP cookie 上引進了強制，而不會將 SameSite 屬性視為 SameSite = 不嚴格。 這表示應用程式閘道親和性 cookie 不會由瀏覽器在第三 pary 內容中傳送。 為了支援此案例，應用程式閘道除了現有的*ApplicationGatewayAffinity* cookie 之外，也會插入另一個稱為*ApplicationGatewayAffinityCORS*的 cookie。  這些 cookie 很類似，但*ApplicationGatewayAffinityCORS* cookie 已新增兩個屬性： *SameSite = None;安全*。 即使是跨原始來源要求，這些屬性仍會維護粘滯會話。 如需詳細資訊，請參閱以[cookie 為基礎的相似性一節](configuration-overview.md#cookie-based-affinity)。

## <a name="next-steps"></a>後續步驟

若要深入瞭解應用程式閘道，請參閱[什麼是 Azure 應用程式閘道？](overview.md)。
