---
title: Azure Front Door-常見問題
description: 此頁面提供有關 Azure Front Door 常見問題的解答
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2020
ms.author: duau
ms.openlocfilehash: abc4529d6076496b34859eec2b931a8dcbd1ce93
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296585"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Azure Front Door 的常見問題

本文將回答有關 Azure Front Door 特性和功能的常見問題。 如果您找不到問題的答案，可透過下列管道 (依先後順序) 和我們連絡：

1. 本文的留言區。
2. [Azure Front Door UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025)。
3. **Microsoft 支援服務：** 若要建立新的支援要求，請在 **Azure 入口網站的 [說明] 索引** 標籤上，選取 [說明 **+ 支援** ] 按鈕，然後選取 [ **新增支援要求**]。

## <a name="general"></a>一般

### <a name="what-is-azure-front-door"></a>Azure Front Door 是什麼？

Azure Front Door 是一種應用程式傳遞網路 (ADN) 即服務，可為您的應用程式提供各種第7層負載平衡功能。 可提供動態網站加速 (DSA) 以及具有近乎即時容錯移轉的全域負載平衡。 這是高度可用且可調整的服務，其完全由 Azure 管理。

### <a name="what-features-does-azure-front-door-support"></a>Azure Front Door 支援哪些功能？

Azure Front Door 支援動態網站加速 (DSA) 、TLS/SSL 卸載和端對端 TLS、Web 應用程式防火牆、以 cookie 為基礎的會話親和性、url 路徑型路由、免費憑證和多個網域管理，以及其他。 如需完整的支援功能清單，請參閱 [Azure Front Door 的總覽](front-door-overview.md)。

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Azure Front Door 與 Azure 應用程式閘道之間有何差異？

Front Door 和應用程式閘道都是第7層 (HTTP/HTTPS) 負載平衡器，主要的差異在於 Front Door 是全域服務，而應用程式閘道是區域服務。 雖然 Front Door 可以在不同區域之間的不同縮放單位/叢集/戳記單位之間進行負載平衡，但應用程式閘道可讓您在您的 Vm/容器之間進行負載平衡（位於縮放單位內）。

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>何時應該在 Front Door 後方部署應用程式閘道？

其中一個應使用應用程式閘道的主要案例如下 Front Door：

- Front Door 只能在全域層級執行以路徑為基礎的負載平衡，但如果您想要在其虛擬網路 (的) VNET 中進一步平衡流量的負載，則應該使用應用程式閘道。
- 由於 Front Door 無法在 VM/容器層級運作，因此它無法進行連線清空。 不過，應用程式閘道可讓您進行連接清空。 
- 在 Front Door 的應用程式閘道之後，您可以達到100% 的 TLS/SSL 卸載，並只在其虛擬網路內路由傳送 HTTP 要求 (VNET) 。
- Front Door 和應用程式閘道都支援會話親和性。 雖然 Front Door 可以將來自使用者會話的後續流量導向至指定區域中的相同叢集或後端，但應用程式閘道可以將流量導向至叢集中的相同伺服器將相似化為。  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>是否可以將 Azure Load Balancer 部署 Front Door？

Azure Front Door 需要公用 VIP 或公開可用的 DNS 名稱，才能將流量路由傳送至。 在 Front Door 上部署 Azure Load Balancer 是常見的使用案例。

### <a name="what-protocols-does-azure-front-door-support"></a>Azure Front Door 支援哪些通訊協定？

Azure Front Door 支援 HTTP、HTTPS 和 HTTP/2。

### <a name="how-does-azure-front-door-support-http2"></a>Azure Front Door 如何支援 HTTP/2？

只有連接到 Azure Front Door 的用戶端可以使用 HTTP/2 通訊協定支援。 後端集區中後端的通訊是透過 HTTP/1.1。 預設會啟用 HTTP/2 支援。

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>目前支援哪些資源做為後端集區的一部分？

後端集區可以包含儲存體、Web 應用程式、Kubernetes 實例，或任何其他具有公用連線的自訂主機名稱。 Azure Front Door 要求必須透過公用 IP 或可公開解析的 DNS 主機名稱來定義後端。 後端集區的成員可以跨區域、區域，甚至是在 Azure 外部，只要它們具有公用連線即可。

### <a name="what-regions-is-the-service-available-in"></a>哪些區域提供此服務？

Azure Front Door 是全域服務，且未系結至任何特定的 Azure 區域。 建立 Front Door 時，唯一需要指定的位置是資源群組位置，基本上是指定將儲存資源群組中繼資料的位置。 Front Door 資源本身會建立為全域資源，且會將設定全域部署到所有 Pop (狀態點) 。 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Azure Front Door 的 POP 位置有哪些？

Azure Front Door 與 Microsoft 的 Azure CDN 具有相同的 POP (點) 位置清單。 如需完整的 Pop 清單，請參閱 [Microsoft 的 AZURE CDN POP 位置](../cdn/cdn-pop-locations.md)。

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Azure Front Door 專用的應用程式部署，還是在客戶間共用？

Azure Front Door 是全域散發的多租使用者服務。 因此，Front Door 的基礎結構會在其所有客戶間共用。 不過，藉由建立 Front Door 設定檔，您可以定義應用程式所需的特定設定，而且不會對您的 Front Door 造成任何變更影響其他 Front Door 設定。

### <a name="is-http-https-redirection-supported"></a>是否支援 HTTP->HTTPS 重新導向？

是。 事實上，Azure Front Door 支援主機、路徑和查詢字串重新導向，以及 URL 重新導向的一部分。 深入瞭解 [URL](front-door-url-redirect.md)重新導向。 

### <a name="in-what-order-are-routing-rules-processed"></a>路由規則的處理順序為何？

您 Front Door 的路由不會排序，並根據最符合的選項選取特定的路由。 深入瞭解 [Front Door 如何與路由規則的要求相符](front-door-route-matching.md)。

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>如何? 將我的後端存取權鎖定為僅 Azure Front Door？

若要鎖定您的應用程式只接受來自特定 Front Door 的流量，您必須為後端設定 IP Acl，然後將後端的流量限制為 Front Door 傳送的標頭 ' X-Azure-FDID ' 的特定值。 這些步驟會詳細說明，如下所示：

- 為您的後端設定 IP 執行 acl，以接受來自 Azure Front Door 後端 IP 位址空間和 Azure 基礎結構服務的流量。 請參閱下面的 IP 詳細資料，以執行 acl 您的後端：
 
    - 請參閱 [AZURE IP 範圍](https://www.microsoft.com/download/details.aspx?id=56519)中的 *AzureFrontDoor 後端* 區段和 Front Door 的 IPv4 後端 IP 位址範圍的服務標籤，或者您也可以使用 [網路安全性群組](../virtual-network/network-security-groups-overview.md#security-rules)中的服務標記 *AzureFrontDoor。*
    - 透過虛擬化主機 IP 位址的 Azure [基本基礎結構服務](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) ： `168.63.129.16` 和 `169.254.169.254`

    > [!WARNING]
    > Front Door 的後端 IP 空間可能會在稍後變更，不過，我們會確保在這之前，我們會將其與 [AZURE IP 範圍和服務標記](https://www.microsoft.com/download/details.aspx?id=56519)整合。 建議您訂閱 [AZURE IP 範圍和服務](https://www.microsoft.com/download/details.aspx?id=56519) 標籤，以取得任何變更或更新。

-    使用 API 版本或更高版本，在您的 Front Door 上執行 GET 作業 `2020-01-01` 。 在 [API 呼叫] 中，尋找 [ `frontdoorID` 欄位]。 篩選傳入的標頭 '**FDID**'，並將 Front Door 傳送給您的後端，其值與欄位的值相同 `frontdoorID` 。 您也可以 `Front Door ID` 從 Front Door 入口網站頁面的 [總覽] 區段下找到值。 

- 將規則篩選套用至後端 web 伺服器，以根據產生的 ' FDID ' 標頭值來限制流量。

  以下是 [Microsoft Internet Information Services (IIS) ](https://www.iis.net/)的範例：

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="Filter_X-Azure-FDID" patternSyntax="Wildcard" stopProcessing="true">
                        <match url="*" />
                        <conditions>
                            <add input="{HTTP_X_AZURE_FDID}" pattern="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" negate="true" />
                        </conditions>
                        <action type="AbortRequest" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration>
    ```



### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>多點傳播 IP 是否可以在我的 Front Door 存留期內變更？

Front Door 的前端任意傳播 IP 通常不會變更，而且在 Front Door 的存留期間可能會保持靜態。 但是，並沒有相同的 **保證** 。 請不要對 IP 採取任何直接的相依性。

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Azure Front Door 是否支援靜態或專用 Ip？

否，Azure Front Door 目前不支援靜態或專用前端任意傳播 Ip。 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Azure Front Door 是否支援標頭的 x 轉送？

是，Azure Front Door 支援 X 轉送的、X 轉送的主機和 X 轉送的-Proto 標頭。 若為 X 轉送-如果標頭已存在，則 Front Door 會將用戶端通訊端 IP 附加至該標頭。 否則，它會新增具有用戶端通訊端 IP 的標頭做為值。 若為 X 轉送的主機和 X 轉送的-Proto，則會覆寫此值。

深入瞭解 [Front Door 支援的 HTTP 標頭](front-door-http-headers-protocol.md)。  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>部署 Azure Front Door 需要多久的時間？ 我的 Front Door 在更新時仍能運作嗎？

新的 Front Door 建立或現有 Front Door 的任何更新大約需要3到5分鐘的時間進行全域部署。 這表示在大約3到5分鐘的時間內，您的 Front Door 設定將會全球部署到所有的 Pop。

注意：自訂 TLS/SSL 憑證更新需要大約30分鐘的時間才能全域部署。

路由或後端集區等的任何更新都是順暢的，將會導致零停機 (如果新的設定正確) 。 憑證更新也是不可部分完成的，而且不會造成任何中斷，除非從「AFD 受控」切換至「使用您自己的憑證」，反之亦然。


## <a name="configuration"></a>設定

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>可以 Azure Front Door 負載平衡或在虛擬網路內路由傳送流量嗎？

Azure Front Door (AFD) 需要公用 IP 或可公開解析的 DNS 名稱來路由傳送流量。 因此，答案不是 AFD 直接無法在虛擬網路內進行路由，但使用應用程式閘道或兩者之間的 Azure Load Balancer 將可解決此案例。

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Azure Front Door 有何不同的超時和限制？

瞭解 Azure Front Door 的所有記載的 [超時和限制](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits)。

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>將規則新增至 Front Door 規則引擎之後，規則需要多久的時間才會生效？

規則引擎設定大約需要10到15分鐘的時間才能完成更新。 您可以預期當更新完成時，規則就會生效。 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-vice-versa"></a>我可以設定 Front Door 設定檔後方的 Azure CDN，反之亦然？

Azure Front Door 和 Azure CDN 無法設定在一起，因為這兩個服務會在回應要求時使用相同的 Azure edge 網站。 

## <a name="performance"></a>效能

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Azure Front Door 如何支援高可用性和擴充性？

Azure Front Door 是全域散發的多租使用者平臺，具有大量容量可滿足您應用程式的擴充性需求。 Front Door 提供全球負載平衡功能，可讓您容錯移轉整個應用程式，甚至是跨區域或不同雲端的個別微服務。

## <a name="tls-configuration"></a>TLS 設定

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Azure Front Door 支援哪些 TLS 版本？

在2019年9月之後建立的所有 Front Door 設定檔都會使用 TLS 1.2 作為預設的最小值。

Front Door 支援 TLS 版本1.0、1.1 和1.2。 尚不支援 TLS 1.3。

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Azure Front Door 支援哪些憑證？

若要啟用 HTTPS 通訊協定以在 Front Door 自訂網域上安全地傳遞內容，您可以選擇使用由 Azure Front Door 管理的憑證，或使用您自己的憑證。
Front Door 受控選項會透過 Digicert 布建標準的 TLS/SSL 憑證，並儲存在 Front Door 的 Key Vault 中。 如果您選擇使用自己的憑證，則您可以將憑證從支援的 CA 上線，而且可以是標準的 TLS、延伸驗證憑證或甚至是萬用字元憑證。 不支援自我簽署憑證。 瞭解 [如何為自訂網域啟用 HTTPS](./front-door-custom-domain-https.md)。

### <a name="does-front-door-support-autorotation-of-certificates"></a>Front Door 是否支援憑證的 autorotation？

針對 Front Door 受控憑證選項，Front Door 會 autorotated 憑證。 如果您使用 Front Door 受控憑證，並看到憑證到期日小於60天，請提出支援票證。
</br>針對您自己的自訂 TLS/SSL 憑證，不支援 autorotation。 與第一次為特定自訂網域設定的方式類似，您必須將 Front Door 指向 Key Vault 中正確的憑證版本，並確定 Front Door 的服務主體仍具有 Key Vault 的存取權。 Front Door 的這項更新憑證推出作業是不可部分完成的，而且不會造成任何實際執行的影響，因為憑證的主體名稱或 SAN 不會變更。

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Azure Front Door 目前支援哪些加密套件？

針對 TLS 1.2，支援下列加密套件： 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

使用已啟用 TLS 1.0/1.1 的自訂網域時，支援下列加密套件：

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>我可以設定 TLS 原則來控制 TLS 通訊協定版本嗎？

您可以透過 Azure 入口網站或 [Azure REST API](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion)，在自訂網域 HTTPS 設定的 Azure Front Door 中設定最低的 TLS 版本。 目前，您可以選擇1.0 和1.2。

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>是否可以將 Front Door 設定為僅支援特定的加密套件？

否，不支援設定特定加密套件的 Front Door。 不過，您可以從憑證授權單位單位取得自己的自訂 TLS/SSL 憑證 (說是 Verisign、Entrust 或 Digicert) ，並且在產生憑證時，將特定的加密套件標記在憑證上。 

### <a name="does-front-door-support-ocsp-stapling"></a>Front Door 是否支援 OCSP 裝訂？

是，Front Door 預設支援 OCSP 裝訂，且不需要進行任何設定。

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure Front Door 也支援對後端的流量重新加密嗎？

是，Azure Front Door 支援 TLS/SSL 卸載，以及端對端 TLS，會重新加密後端的流量。 事實上，因為後端的連線是透過其公用 IP 進行，建議您將 Front Door 設定為使用 HTTPS 作為轉送通訊協定。

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Front Door 是否支援後端上的自我簽署憑證以進行 HTTPS 連線？

否，Front Door 不支援自我簽署憑證，而且此限制適用于兩者：

1. **後端**：當您將流量轉送為 HTTPS 或 HTTPs 健康情況探查，或針對已啟用快取的路由規則，從來源填滿快取時，您無法使用自我簽署憑證。
2. **前端**：當您使用自訂的 TLS/SSL 憑證在自訂網域上啟用 HTTPS 時，無法使用自我簽署的憑證。

### <a name="why-is-https-traffic-to-my-backend-failing"></a>為什麼我的後端 HTTPS 流量失敗？

如果您的後端有成功的 HTTPS 連線，無論是健康情況探查或轉送要求，HTTPS 流量可能會失敗的原因有兩個：

1. **憑證主體名稱不相符**：對於 HTTPS 連線，Front Door 預期您的後端會從主體名稱 (s 的有效 CA 出示憑證，) 符合後端主機名稱。 舉例來說，如果您的後端主機名稱是設定為 `myapp-centralus.contosonews.net` ，而您的後端在 TLS 信號交換期間所呈現的憑證和主體名稱都不相同 `myapp-centralus.contosonews.net` `*myapp-centralus*.contosonews.net` ，Front Door 將拒絕連線並導致錯誤。 
    1. **解決方案**：雖然不建議從合規性的觀點來看，但您可以藉由停用 Front Door 的憑證主體名稱檢查來解決此錯誤。 這會出現在 Azure 入口網站的設定下，以及在 API 中的 BackendPoolsSettings 下。
2. **來自無效 ca 的後端裝載憑證**：只有來自 [有效 ca](./front-door-troubleshoot-allowed-ca.md) 的憑證可以在具有 Front Door 的後端使用。 不允許來自內部 Ca 或自我簽署憑證的憑證。

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>我可以搭配 Azure Front Door 使用用戶端/相互驗證嗎？

否。 雖然 Azure Front Door 支援在 [RFC 5246](https://tools.ietf.org/html/rfc5246)中引入用戶端/相互驗證的 TLS 1.2，但目前 Azure Front Door 不支援用戶端/相互驗證。

## <a name="diagnostics-and-logging"></a>診斷和記錄

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Azure Front Door 可以使用哪些類型的計量和記錄？

如需記錄和其他診斷功能的詳細資訊，請參閱 [監視 Front Door 的計量和記錄](front-door-diagnostics.md)。

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>診斷記錄的保留原則為何？

診斷記錄會送至客戶儲存體帳戶，而客戶可以根據其喜好來設定保留原則。 診斷記錄也可以傳送至事件中樞或 Azure 監視器記錄。 如需詳細資訊，請參閱 [Azure Front Door 診斷](front-door-diagnostics.md)。

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>如何? 取得 Azure Front Door 的審核記錄？

Audit 記錄檔可用於 Azure Front Door。 在入口網站中，按一下您 Front Door 的功能表分頁中的 [ **活動記錄** ]，以存取 audit 記錄檔。 

### <a name="can-i-set-alerts-with-azure-front-door"></a>我可以使用 Azure Front Door 設定警示嗎？

是，Azure Front Door 支援警示。 您可以針對計量設定警示。 

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
