---
title: 有關 Azure 應用程式閘道的常見問題
description: 查找有關 Azure 應用程式閘道的常見問題的解答。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 290467d5d20a74f8b8b2c23f6da0dcadfd74cc56
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411019"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>有關應用程式閘道的常見問題

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下是有關 Azure 應用程式閘道的常見問題。

## <a name="general"></a>一般

### <a name="what-is-application-gateway"></a>什麼是應用程式閘道？

Azure 應用程式閘道提供應用程式提供控制器 (ADC) 作為服務。 它為您的應用程式提供各種第 7 層負載平衡功能。 此服務高度可用、可擴展且完全由 Azure 管理。

### <a name="what-features-does-application-gateway-support"></a>應用程式閘道支援哪些功能？

應用程式閘道支援自動縮放、SSL 卸載和端到端 SSL、Web 應用程式防火牆 (WAF)、基於 Cookie 的作業階段關聯性、基於 URL 路徑的路由、多網站託管和其他功能。 如需完整的支援功能清單，請參閱[應用程式閘道簡介](application-gateway-introduction.md)。

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>應用程式閘道和 Azure 負載均衡器有何不同?

應用程式閘道是第 7 層負載均衡器,這意味著它僅適用於 Web 流量(HTTP、HTTPS、WebSocket 和 HTTP/2)。 它支援 SSL 終止、基於 Cookie 的會話相關性和負載平衡流量的循環等功能。 負載均衡器負載平衡層 4 層(TCP 或 UDP)的流量。

### <a name="what-protocols-does-application-gateway-support"></a>應用程式閘道支援哪些通訊協定？

應用程式閘道支援 HTTP、HTTPS、HTTP/2 和 WebSocket。

### <a name="how-does-application-gateway-support-http2"></a>應用程式閘道如何支援 HTTP/2？

請參考[HTTP/2 支援](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support)。

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>作為後端池的一部分,支援哪些資源?

請參考[的後端介面資源](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools)。

### <a name="in-what-regions-is-application-gateway-available"></a>在哪些區域可以使用應用程序閘道?

應用程式閘道適用於全域 Azure 的所有區域。 它還在[Azure 中國 21Vianet](https://www.azure.cn/)和[Azure 政府](https://azure.microsoft.com/overview/clouds/government/)中提供。

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>此部署是專用於我的訂閱,還是跨客戶共用?

應用程式閘道是您虛擬網路中專用的部署。

### <a name="does-application-gateway-support-http-to-https-redirection"></a>應用程式閘道是否支援 HTTP 到 HTTPS 重定向?

支援重新導向。 請參考[應用程式閘道重定向概述](application-gateway-redirect-overview.md)。

### <a name="in-what-order-are-listeners-processed"></a>接聽程式的處理順序為何？

請參考[偵聽器處理的順序](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners)。

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>在哪裡可以找到應用程式閘道 IP 和 DNS?

如果使用公共 IP 位址作為終結點,您將在公共 IP 位址資源上找到 IP 和 DNS 資訊。 或者在門戶中,在應用程式閘道的概述頁上找到它。 如果您使用的是內部 IP 位址,則在概覽頁面上找到資訊。

對於 v2 SKU,打開公共 IP 資源並選擇 **「設定**」。 **DNS 名稱標籤(可選)** 欄位可用於設定 DNS 名稱。

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>保持活動超時和 TCP 空閒超時有哪些設置?

*保持活動超時*控制應用程式閘道在重用或關閉之前等待客戶端在持久連接上發送另一個 HTTP 請求的時間。 *TCP 空閒超時*控制 TCP 連接保持打開狀態的時間,以防無活動。 

應用程式閘道 v1 SKU 中的*保持活動超時*為 120 秒,在 v2 SKU 中為 75 秒。 對於應用程式閘道的 v1 和 v2 SKU 的前端虛擬 IP (VIP), *TCP 空閒超時*是 4 分鐘的預設值。 

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>IP 或 DNS 名稱是否會在應用程式閘道的生存期內更改?

在應用程式閘道 V1 SKU 中,如果停止並啟動應用程式閘道,VIP 可能會更改。 但與應用程式閘道關聯的 DNS 名稱不會在閘道的生存期內更改。 由於 DNS 名稱不會更改,因此應使用 CNAME 別名並將其指向應用程式閘道的 DNS 位址。 在應用程式閘道 V2 SKU 中,您可以將 IP 位址設定為靜態位址,因此 IP 和 DNS 名稱不會在應用程式閘道的生存期內更改。 

### <a name="does-application-gateway-support-static-ip"></a>應用程式閘道是否支援靜態 IP？

是的,應用程式閘道 v2 SKU 支援靜態公共 IP 位址。 v1 SKU 支援靜態內部 IP。

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>應用程式閘道是否在閘道上支援多個公用 IP？

應用程式閘道僅支援一個公共 IP 位址。

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>應用程式閘道的子網路應該要多大？

請參考[應用程式閘道網大小注意事項](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)。

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>可以將多個應用程式閘道資源部署到單一子網路嗎？

是。 除了給定應用程式閘道部署的多個實例外,還可以將另一個唯一的應用程式閘道資源預配到包含不同應用程式閘道資源的現有子網。

單個子網不能同時支援Standard_v2和標準應用程式閘道。

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>應用程式閘道 v2 是否支援使用者定義的路由 (UDR)?

是,但僅限於特定方案。 有關詳細資訊,請參閱[應用程式閘道設定概述](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet)。

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>應用程式閘道是否支援 x-forwarded-for 標頭？

是。 請參考[要求的變更](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request)。

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>部署應用程式閘道需要多長時間? 更新應用程式閘道時,它是否會正常工作?

新的應用程式閘道 v1 SKU 部署最多可能需要 20 分鐘的時間來佈建。 實例大小或計數的更改不會造成中斷,並且閘道在此期間保持活動狀態。

使用 v2 SKU 的大多數部署大約需要 6 分鐘才能進行預配。 但是,根據部署類型,可能需要更長時間。 例如,跨多個可用區域部署多個具有多個實例可能需要超過6分鐘的時間。 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>是否可以使用 Exchange Server 作為應用程式閘道的後端?

否。 應用程式閘道不支援電子郵件協定,如 SMTP、IMAP 和 POP3。 

## <a name="performance"></a>效能

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>應用程式閘道如何支援高可用性和延展性？

應用程式閘道 v1 SKU 支援部署兩個或多個實例時的高可用性方案。 Azure 在更新域和容錯域之間分發這些實例,以確保實例不會同時全部失敗。 v1 SKU 可藉由新增多個相同閘道的執行個體來分攤負載，以支援延展性。

v2 SKU 會自動確保將新執行個體分散在各個容錯網域和更新網域。 如果選擇區域冗餘,則最新的實例也會跨可用性區域分佈,以提供區域故障恢復能力。

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>如何使用應用程式閘道實現跨資料中心的 DR 方案?

使用流量管理器跨不同資料中心中的多個應用程式閘道分配流量。

### <a name="does-application-gateway-support-autoscaling"></a>應用程式閘道是否支援自動縮放?

是，應用程式閘道 v2 SKU 支援自動調整規模。 有關詳細資訊,請參閱[自動縮放和區域冗餘應用程式閘道](application-gateway-autoscaling-zone-redundant.md)。

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>手動或自動擴展或縮減是否會導致停機時間?

否。 執行個體已分散於升級網域和容錯網域。

### <a name="does-application-gateway-support-connection-draining"></a>應用程式閘道是否支援連線清空？

是。 您可以設定連接耗盡以更改後端池中的成員,而不會中斷。 有關詳細資訊,請參閱[應用程式閘道的連線耗盡部分](features.md#connection-draining)。

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>是否可在中斷的情況下，將執行個體大小從中型變成大型？

是。

## <a name="configuration"></a>組態

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>應用程式閘道一律部署在虛擬網路中？

是。 應用程式閘道始終部署在虛擬網路子網中。 此子網路只能包含應用程式閘道。 有關詳細資訊,請參閱[虛擬網路和子網路要求](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet)。

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>應用程式閘道能否與其虛擬網路外部的實例通信,也可以與其訂閱外部通信?

只要您具有 IP 連接,應用程式閘道就可以與它所位於的虛擬網路外部的實例進行通訊。 應用程式閘道還可以與訂閱外部的實例進行通訊。 若計劃將內部 IP 用作後端池成員,請使用[虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)或[Azure VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>是否可以在應用程式閘道子網路中部署其他任何項目？

否。 但是,您可以在子網中部署其他應用程式閘道。

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>應用程式閘道網上支援網路安全組嗎?

請參考[應用程式閘道中的網路安全群組](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet)。

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>應用程式閘道是否支援使用者定義的路由?

請參考[應用程式閘道中支援的使用者定義路由](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet)。

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>應用程式閘道的限制為何？ 是否可以增加這些限制？

請參考[應用程式閘道限制](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)。

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>我可以同時將應用程式閘道同時用於外部和內部流量嗎?

是。 應用程式閘道支援每個應用程式閘道一個內部 IP 和一個外部 IP。

### <a name="does-application-gateway-support-virtual-network-peering"></a>應用程式閘道是否支援虛擬網路對等互連?

是。 虛擬網路對等互連有助於平衡其他虛擬網路中的流量。

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>當本地伺服器通過 ExpressRoute 或 VPN 隧道連接時,我是否可以與它們進行交談?

是，只有流量允許即可。

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>一個後端池能否在不同的埠上為許多應用程式提供服務?

支援微服務體系結構。 要在不同的埠上探測,您需要配置多個 HTTP 設定。

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>自定義探測器是否支援回應數據的通配符或正則運算式?

否。 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>如何在應用程式閘道中處理路由規則?

請參考[處理規則的順序](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules)。

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>對於自定義探測器,「主機」字段表示什麼?

「主機"欄位指定在應用程式閘道上配置多網站時將探測器發送到的名稱。 否則請使用「127.0.0.1」。 此值與虛擬機主機名不同。 此格式是\<協定\>\<Tt\>\<伺服器\>\<:\>連接埠 路徑 。

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>我是否可以允許應用程式閘道僅存取幾個來源 IP 位址?

是。 請參考[限制到特定來源 IP 的存取](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips)。

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>是否可以同時為面向公共的和面向私有的偵聽器使用相同的埠?

否。

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>是否有從 v1 SKU 遷移到 v2 SKU 的指南?

是。 有關詳細資訊,請參閱[將 Azure 應用程式閘道和 Web 應用程式防火牆從 v1 移至 v2](migrate-v1-v2.md)。

### <a name="does-application-gateway-support-ipv6"></a>應用程式閘道是否支援 IPv6?

應用程式閘道 v2 目前不支援 IPv6。 它只能使用 IPv4 在雙堆疊 VNet 中運行,但閘道網必須僅 IPv4。 應用程式閘道 v1 不支援雙堆疊 VNet。 

## <a name="configuration---ssl"></a>設定 - SSL

### <a name="what-certificates-does-application-gateway-support"></a>應用程式閘道支援哪些證書?

應用程式閘道支援自簽署證書、證書頒發機構 (CA) 證書、擴展驗證 (EV) 證書和通配符證書。

### <a name="what-cipher-suites-does-application-gateway-support"></a>應用程式閘道支援哪些密碼套件?

應用程式閘道支援以下密碼套件。 

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

關於如何自訂 SSL 選項的資訊,請參考[在應用程式閘道上設定 SSL 政策版本與密碼套件](application-gateway-configure-ssl-policy-powershell.md)。

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>應用程式閘道是否支援對後端的流量重新加密?

是。 應用程式閘道支援 SSL 卸載和端到端 SSL,後者將流量重新加密到後端。

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>是否可以配置 SSL 策略來控制 SSL 協定版本?

是。 您可以將應用程式閘道配置為拒絕 TLS1.0、TLS1.1 和 TLS1.2。 默認情況下,SSL 2.0 和 3.0 已禁用,並且不可配置。

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>可以設定加密套件和原則的順序嗎？

是。 在應用程式閘道中,您可以[設定密碼套件](application-gateway-ssl-policy-overview.md)。 要定義自定義策略,請至少啟用以下密碼套件之一。 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

應用程式閘道使用 SHA256 進行後端管理。

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>應用程式閘道支援多少個 SSL 憑證?

應用程式閘道最多支援 100 個 SSL 憑證。

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>應用程式閘道支援多少個後端重新加密身份驗證證書?

應用程式閘道最多支援 100 個身份驗證證書。

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>應用程式閘道是否本機與 Azure 金鑰保管庫整合?

是的,應用程式閘道 v2 SKU 支援金鑰保管庫。 關於詳細資訊,請參閱[使用金鑰保管庫憑證的 SSL 終止](key-vault-certs.md)。

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>如何為 .com 和 .net 網站設定 HTTPS 偵聽器? 

對於多個基於域(基於主機)路由,您可以創建多站點偵聽器,設置使用 HTTPS 作為協定的偵聽器,並將偵聽器與路由規則相關聯。 有關詳細資訊,請參閱[使用應用程式閘道閘道多個網站](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview)。

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>我可以在我的 .pfx 文件密碼中使用特殊字元嗎?

否,在 .pfx 文件密碼中僅使用字母數位字元。

## <a name="configuration---web-application-firewall-waf"></a>設定 - Web 應用程式防火牆 (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>WAF SKU 是否提供標準 SKU 中提供的所有功能?

是。 WAF 支援標準 SKU 中的所有功能。

### <a name="how-do-i-monitor-waf"></a>如何監視 WAF？

通過診斷日誌記錄監視 WAF。 有關詳細資訊,請參閱[應用程式閘道的診斷紀錄紀錄和指標](application-gateway-diagnostics.md)。

### <a name="does-detection-mode-block-traffic"></a>偵測模式是否會封鎖流量？

否。 檢測模式僅記錄觸發 WAF 規則的流量。

### <a name="can-i-customize-waf-rules"></a>我可以自訂 WAF 規則嗎？

是。 有關詳細資訊,請參閱自訂[WAF 規則群組和規則](application-gateway-customize-waf-rules-portal.md)。

### <a name="what-rules-are-currently-available-for-waf"></a>WAF 當前可以使用哪些規則?

WAF 目前支援 CRS [2.2.9](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229) [、3.0](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)和[3.1](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31)。 這些規則針對開放 Web 應用程式安全專案 (OWASP) 識別的前 10 個漏洞提供基準安全性: 

* SQL 插入式攻擊保護
* 跨網站文稿保護
* 防止常見的 Web 攻擊,如命令注入、HTTP 請求走私、HTTP 回應分割和遠端檔包含攻擊
* 防範 HTTP 通訊協定違規
* 防範 HTTP 通訊協定異常行為，例如遺漏主機使用者代理程式和接受標頭
* 防範 Bot、編目程式和掃描器
* 偵測常見應用程式錯誤設定 (即 Apache、IIS 等)

有關詳細資訊,請參閱[OWASP 前 10 個漏洞](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)。

### <a name="does-waf-support-ddos-protection"></a>WAF 是否支援 DDoS 保護?

是。 您可以在部署應用程式閘道的虛擬網路上啟用 DDoS 保護。 此設定可確保 Azure DDoS 保護服務還保護應用程式閘道虛擬 IP (VIP)。

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>是否有從 v1 SKU 遷移到 v2 SKU 的指南?

是。 有關詳細資訊,請參閱[將 Azure 應用程式閘道和 Web 應用程式防火牆從 v1 移至 v2](migrate-v1-v2.md)。

## <a name="configuration---ingress-controller-for-aks"></a>設定 - AKS 的入口控制器

### <a name="what-is-an-ingress-controller"></a>什麼是入口控制器?

Kubernetes 允許`deployment``service`建立 和資源來在群集內部公開一組 pod。 為了在外部公開同一服務,定義了[`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/)提供負載平衡、SSL 終止和基於名稱的虛擬託管的資源。
為了滿足此`Ingress`資源,需要一個入口控制器,用於偵`Ingress`聽 對資源的任何更改並配置負載均衡器策略。

應用程式閘道閘道控制器允許將[Azure 應用程式閘道](https://azure.microsoft.com/services/application-gateway/)用作[Azure 庫伯奈斯服務](https://azure.microsoft.com/services/kubernetes-service/)的入口,也稱為 AKS 群集。

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>單個入口控制器實例是否可以管理多個應用程式閘道?

目前,入口控制器的一個實例只能關聯到一個應用程式閘道。

## <a name="diagnostics-and-logging"></a>診斷和記錄

### <a name="what-types-of-logs-does-application-gateway-provide"></a>應用程式閘道提供哪些類型的日誌?

應用程式閘道提供三個紀錄: 

* **應用程式閘道存取日誌**:訪問日誌包含提交到應用程式閘道前端的每個請求。 數據包括調用方的 IP、請求的 URL、回應延遲、返回代碼和進出位元組。它包含每個應用程式閘道的一條記錄。
* **應用程式閘道性能日誌**:性能日誌捕獲每個應用程式閘道的性能資訊。 資訊包括以位元組為單位的輸送量、服務的請求總數、失敗的請求計數以及正常和不正常的後端實例計數。
* **應用程式閘道防火牆日誌**:對於使用 WAF 配置的應用程式閘道,防火牆日誌包含透過檢測模式或預防模式記錄的請求。

所有日誌每 60 秒收集一次。 有關詳細資訊,請參閱[應用程式閘道的後端執行狀況、診斷日誌和指標](application-gateway-diagnostics.md)。

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>如何知道我的後端集區成員狀態良好？

使用 PowerShell cmdlet`Get-AzApplicationGatewayBackendHealth`或門戶驗證運行狀況。 有關詳細資訊,請參閱[應用程式閘道診斷](application-gateway-diagnostics.md)。

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>診斷日誌的保留策略是什麼?

診斷日誌流向客戶的存儲帳戶。 客戶可以根據其偏好設置保留策略。 診斷日誌也可以發送到事件中心或 Azure 監視器日誌。 有關詳細資訊,請參閱[應用程式閘道診斷](application-gateway-diagnostics.md)。

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>如何取得應用程式閘道的稽核記錄？

在門戶中,在應用程式閘道的功能表邊欄選項卡上,選擇**活動日誌**以訪問審核日誌。 

### <a name="can-i-set-alerts-with-application-gateway"></a>是否可以設定應用程式閘道的警示？

是。 在應用程式閘道中,警報在指標上配置。 有關詳細資訊,請參閱[應用程式閘道指標](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics)並[接收警報通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>如何分析應用程式閘道的流量統計資料？

您可以通過多種方式查看和分析訪問日誌。 使用 Azure 監視器日誌、Excel、Power BI 等。

您還可以使用資源管理器範本安裝並運行應用程式閘道存取日誌的常用[GoAccess](https://goaccess.io/)日誌分析器。 GoAccess 提供有價值的 HTTP 流量統計資訊,如唯一存取者、請求的檔案、主機、作業系統、瀏覽器和 HTTP 狀態代碼。 有關詳細資訊,在 GitHub 中,請參考[資源管理員樣本資料夾中的 Readme 檔案](https://aka.ms/appgwgoaccessreadme)。

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>是什麼原因導致後端運行狀況返回未知狀態?

通常,當應用程式閘道網上的網路安全組 (NSG)、自訂 DNS 或使用者定義的路由 (UDR) 阻止對後端的訪問時,您會看到未知狀態。 有關詳細資訊,請參閱[應用程式閘道的後端執行狀況、診斷紀錄記錄和指標](application-gateway-diagnostics.md)。

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>是否有任何情況下 NSG 串流日誌不會顯示允許的流量?

是。 如果您的設定與以下方案符合,則 NSG 串流紀錄中看不到允許的流量:
- 已部署應用程式閘道 v2
- 應用程式閘道網上有一個 NSG
- 您已啟用此 NSG 上的 NSG 串流紀錄

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>如何使用僅具有專用前端 IP 位址的應用程式閘道 V2?

應用程式閘道 V2 目前不支援專用 IP 模式。 它支援以下組合
* 私人 IP 和公共 IP
* 只限公共 IP

但是,如果您希望將應用程式閘道 V2 僅與專用 IP 一起使用,您可以按照以下步驟操作:
1. 建立使用公共和專用前端 IP 位址的應用程式閘道
2. 不要為公共前端 IP 位址創建任何偵聽器。 如果未為其創建偵聽器,則應用程式閘道將不會偵聽公共 IP 位址上的任何流量。
3. 依優先順序建立並附加有以下設定的應用程式閘道閘道的[網路安全群組](https://docs.microsoft.com/azure/virtual-network/security-overview):
    
    a. 允許從來源作為**閘道管理員**服務標記的流量,將目標作為**任意**埠和目標埠為**65200-65535**。 Azure 基礎結構通訊需要此連接埠範圍。 這些埠受證書身份驗證的保護(鎖定)。 外部實體(包括閘道使用者管理員)在沒有適當的憑證的情況下無法對這些終結點發起變更
    
    b. 允許來自源的流量作為**AzureLoad平衡器**服務標記,目標埠為 **"任意"**
    
    c. 拒絕從來源作為**Internet**服務標記的所有入站流量,將目標埠作為**Any**。 在輸入規則中給予此規則*最低優先權*
    
    d. 保留預設規則,如允許虛擬網路入站,以便不會阻止對專用 IP 位址的存取
    
    e. 無法封鎖輸出網際網路連線。 否則,您將面臨日誌記錄、指標等問題。

只用於私人 IP 存取![的 NSG 設定範例:僅用於專用 IP 存取的應用程式閘道 V2 NSG 設定](./media/application-gateway-faq/appgw-privip-nsg.png)

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>應用程式閘道關聯 Cookie 是否支援同一網站屬性?
是的,[鉻瀏覽器](https://www.chromium.org/Home) [v80 更新](https://chromiumdash.appspot.com/schedule)引入了沒有 SameSite 屬性的 HTTP Cookie 的任務,應視為 SameSite_Lax。 這意味著瀏覽器不會在第三方上下文中發送應用程式閘道關聯 Cookie。 為了支援此方案,應用程式閘道除了注入現有的*應用程式閘道關聯*Cookie 外,還注入了另一個稱為*應用程式網關 AffinityCORS*的 Cookie。  這些 Cookie 類似,但*應用程式閘道 AffinityCORS* Cookie 還有兩個屬性 *:SameSite_無;安全*。 這些屬性即使在跨源請求中也能保持粘滯會話。 有關詳細資訊,請參閱[以 Cookie 的關聯部分](configuration-overview.md#cookie-based-affinity)。

## <a name="next-steps"></a>後續步驟

要瞭解有關應用程式閘道的更多內容,請參閱[什麼是 Azure 應用程式閘道?](overview.md)
