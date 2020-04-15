---
title: Azure 前門 ─ 常見問題
description: 本頁提供有關 Azure 前門的常見問題的解答
services: frontdoor
documentationcenter: ''
author: sohamnchatterjee
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2020
ms.author: sohamnc
ms.openlocfilehash: e2785baab27f5bfc996b57607816062195a19b2b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313761"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Azure 前門的常見問題

本文回答了有關 Azure 前門特性和功能的常見問題。 如果您找不到問題的答案，可透過下列管道 (依先後順序) 和我們連絡：

1. 本文的留言區。
2. [Azure 前門使用者語音](https://feedback.azure.com/forums/217313-networking?category_id=345025)。
3. **微軟支援:** 要建立新的支援請求,請在 Azure 門戶中「**説明」** 選項卡上選擇 **「幫助 + 支援**」按鈕,然後選擇 **「新建支援請求**」。

## <a name="general"></a>一般

### <a name="what-is-azure-front-door"></a>Azure Front Door 是什麼？

Azure 前門是應用程式交付網路 (ADN) 作為服務,為您的應用程式提供各種第 7 層負載平衡功能。 它提供動態網站加速 (DSA)以及全域負載平衡和近乎即時故障轉移。 它是一個高度可用且可擴展的服務,完全由 Azure 管理。

### <a name="what-features-does-azure-front-door-support"></a>Azure 前門支援哪些功能?

Azure 前門支援動態網站加速 (DSA)、TLS/SSL 卸載和端到端 TLS、Web 應用程式防火牆、基於 Cookie 的作業階段關聯性、基於 URL 路徑的路由、免費證書和多個域管理等。 有關支援功能的完整清單,請參閱[Azure 前門概述](front-door-overview.md)。

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Azure 前門和 Azure 應用程式閘道之間的區別是什麼?

雖然前門網關和應用程式閘道都是第 7 層 (HTTP/HTTPS) 負載均衡器,但主要區別是前門是一個全域服務,而應用程式閘道是區域服務。 雖然前門可以跨區域在不同比例單位/群集/標記單元之間負載平衡,但應用程式閘道允許您在比例單位內的 VM/容器等之間進行負載平衡。

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>我們何時應該在前門後面部署應用程式閘道?

使用前門後的應用程式閘道的關鍵方案包括:

- 前門只能在全域級別執行基於路徑的負載平衡,但如果希望在虛擬網路 (VNET) 內進一步載入流量,則應使用應用程式閘道。
- 由於前門在 VM/容器級別不工作,因此無法執行連接耗盡。 但是,應用程式閘道允許您執行連接耗盡。 
- 借助 AFD 背後的應用程式閘道,可以實現 100% TLS/SSL 卸載,並且僅路由其虛擬網路 (VNET) 中的 HTTP 請求。
- 前門和應用程式閘道都支援會話相關性。 雖然前門可以將後續流量從使用者會話定向到給定區域中的同一群集或後端,但應用程式網關可以將流量定向到群集中的同一伺服器。  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>我們可以在前門後面部署 Azure 負載平衡器嗎?

Azure 前門需要公共 VIP 或公開可用的 DNS 名稱才能將流量路由到。 在前門後面部署 Azure 負載均衡器是一個常見的用例。

### <a name="what-protocols-does-azure-front-door-support"></a>Azure 前門支援哪些協定?

Azure 前門支援 HTTP、HTTPS 和 HTTP/2。

### <a name="how-does-azure-front-door-support-http2"></a>Azure 前門如何支援 HTTP/2?

HTTP/2 協定支援僅適用於連接到 Azure 前門的用戶端。 與後端池後端的通訊通過 HTTP/1.1。 默認情況下啟用 HTTP/2 支援。

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>目前支援哪些資源做為後端集區的一部分？

後端介面可以由儲存、Web 應用、庫伯奈斯實例或任何其他具有公共連接的自訂主機名組成。 Azure 前門要求透過公共 IP 或公開解析的 DNS 主機名定義後端。 後端池的成員可以跨區域、區域,甚至 Azure 外部,只要它們具有公共連接。

### <a name="what-regions-is-the-service-available-in"></a>哪些區域提供此服務？

Azure 前門是一個全域服務,不綁定到任何特定的 Azure 區域。 創建前門時需要指定的唯一位置是資源組位置,它基本上指定資源組的元數據存儲位置。 前門資源本身創建為全域資源,配置全域部署到所有持久性有機污染物(存在點)。 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Azure 前門的 POP 位置是什麼?

Azure 前門的 POP(存在點)位置列表與來自 Microsoft 的 Azure CDN 的位置清單相同。 有關持久性有機污染物的完整清單,請參閱 Microsoft[的 Azure CDN POP 位置](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)。

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Azure 前門是應用程式的專用部署,還是跨客戶共用?

Azure 前門是一種全域分佈的多租戶服務。 因此,前門的基礎設施在所有客戶之間共用。 但是,通過創建前門配置檔,您可以定義應用程式所需的特定配置,並且對前門所做的任何更改不會影響其他前門配置。

### <a name="is-http-https-redirection-supported"></a>是否支援 HTTP->HTTPS 重新導向？

是。 事實上,Azure 前門支援主機、路徑和查詢字串重定向以及 URL 重定向的一部分。 瞭解有關[URL 重定向](front-door-url-redirect.md)的更多。 

### <a name="in-what-order-are-routing-rules-processed"></a>路由規則的處理方式是什麼?

未訂購前門的路線,並根據最佳匹配選擇特定路線。 詳細瞭解[前門如何將要求符合到路由規則](front-door-route-matching.md)。

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>如何鎖定對後端的訪問許可權,僅訪問 Azure 前門?

要鎖定應用程式僅接受來自特定前門的流量,您需要為後端設置 IP ACL,然後將後端上的流量限制為前門發送的標頭「X-Azure-FDID」的特定值。 這些步驟如下:

- 為後端介面設定 IP 交流,以便僅接受來自 Azure 前門後端 IP 位址空間和 Azure 基礎結構服務的流量。 有關換回後端,請參閱下面的 IP 詳細資訊:
 
    - 在[Azure IP 範圍和服務標記](https://www.microsoft.com/download/details.aspx?id=56519)中為前門的 IPv4 後端 IP 位址範圍請參閱*Azure FrontDoor.後端*部分,也可以使用服務標記*AzureFrontDoor.* 在[網路安全組中](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)的後端或與[Azure 防火牆](https://docs.microsoft.com/azure/firewall/service-tags)一起。
    - 前門的**IPv6**後端 IP 空間,而涵蓋在服務標記中,不列在 Azure IP 範圍 JSON 檔中。 如果您要尋找顯示式 IPv6 位址範圍,它目前限制為`2a01:111:2050::/44`
    - Azure 透過虛擬化主機 IP 位址提供[的基本基礎結構服務](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations): `168.63.129.16``169.254.169.254`

    > [!WARNING]
    > 前門的後端 IP 空間可能會稍後更改,但是,我們將確保在此之前,我們將與[Azure IP 範圍和服務標記](https://www.microsoft.com/download/details.aspx?id=56519)集成。 對於任何變更或更新,我們建議您訂閱[Azure IP 範圍和服務標記](https://www.microsoft.com/download/details.aspx?id=56519)。

-    使用 API`2020-01-01`版本或更高版本在前門執行 GET 操作。 在 API 呼叫`frontdoorID`中, 尋找欄位。 前門發送到後端的傳入標頭 **「X-Azure-FDID」** 上的篩選器,該值與`frontdoorID`字段的值相同。 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>在我的前門的生命週期內,任何轉換的 IP 是否可以更改?

前門的前端任何投射 IP 通常不應更改,並且在前門的使用壽命內可能保持靜態。 但是,沒有同樣的**保證**。 請不要對知識產權採取任何直接依賴關係。

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Azure 前門是否支援靜態 IP 或專用 IP?

否,Azure 前門當前不支援靜態或專用前端任何廣播 IP。 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Azure 前門是否支援 x 轉發標頭?

是的,Azure 前門支援 X-前轉-For、X 轉發主機和 X 轉發-Proto 標頭。 對於 X-前轉 -For,如果標頭已存在,則前門會將用戶端套接字 IP 追加到它。 否則,它將標頭添加用戶端套接字 IP 作為值。 對於 X 轉寄主機和 X 轉發 Proto,該值將被覆蓋。

詳細瞭解[前門支援的 HTTP 標頭](front-door-http-headers-protocol.md)。  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>部署 Azure 前門需要多長時間? 更新時,我的前門是否仍然正常工作?

新的前門創建或對現有前門的任何更新大約需要 3 到 5 分鐘進行全域部署。 這意味著在大約 3 到 5 分鐘內,您的前門配置將部署到我們全球的所有持久性有機污染物中。

注意 - 自定義 TLS/SSL 證書更新大約需要 30 分鐘才能全域部署。

對路由或後端池等的任何更新都是無縫的,將導致零停機時間(如果新配置正確)。 證書更新也是原子的,不會導致任何中斷,除非從"AFD 託管"切換到"使用您自己的證書",反之亦然。


## <a name="configuration"></a>組態

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Azure 前門負載能否在虛擬網路中平衡或路由流量?

Azure 前門 (AFD) 需要公共 IP 或公開解析的 DNS 名稱來路由流量。 因此,答案是沒有 AFD 不能直接路由在虛擬網路中,但使用應用程式網路或 Azure 負載均衡器之間將解決此方案。

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Azure 前門有哪些不同的超時和限制?

瞭解 Azure 前門的所有記錄[超時和限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-front-door-service-limits)。

## <a name="performance"></a>效能

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Azure 前門如何支援高可用性和可擴充性?

Azure 前門是一個全球分佈的多租戶平臺,具有巨大的容量,可滿足應用程式的可擴充性需求。 前門從 Microsoft 全球網路的邊緣交付,提供全域負載平衡功能,允許您跨區域或不同雲端故障接管整個應用程式,甚至單個微服務。

## <a name="tls-configuration"></a>TLS 設定

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Azure 前門支援哪些 TLS 版本?

2019 年 9 月之後創建的所有前門配置檔均使用 TLS 1.2 作為預設最小值。

前門支援 TLS 版本 1.0、1.1 和 1.2。 TLS 1.3 尚不受支援。

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Azure 前門支援哪些證書?

要啟用 HTTPS 協定,以便在前門自定義域上安全地傳遞內容,可以選擇使用由 Azure 前門管理的證書或使用自己的證書。
前門託管選項通過 Digicert 提供標準的 TLS/SSL 證書,並存儲在前門的鑰匙庫中。 如果選擇使用自己的證書,則可以從受支援的 CA 上載證書,也可以是標準 TLS、擴展驗證證書,甚至是通配符證書。 不支援自簽名證書。 [瞭解如何為自定義域啟用 HTTPS。](https://aka.ms/FrontDoorCustomDomainHTTPS)

### <a name="does-front-door-support-autorotation-of-certificates"></a>前門是否支援證書的自動旋轉?

對於前門託管證書選項,證書由前門自動旋轉。 如果您使用的是前門託管證書,並且看到證書到期日期少於 60 天,則提交支援票證。
</br>對於您自己的自定義 TLS/SSL 證書,不支援自動旋轉。 與首次為給定自定義域設置前門類似,您需要將前門指向密鑰保管庫中的右證書版本,並確保前門的服務主體仍有權訪問密鑰保管庫。 前門此更新的證書推出操作是原子的,不會造成任何生產影響,前提是證書的主題名稱或 SAN 不會更改。

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Azure 前門支援的當前密碼套件有哪些?

對 TLS1.2 支援以下密碼套件

TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256TLS_DHE_RSA_WITH_AES_256_GCM_SHA384TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

開啟 TLS1.0/1.1 的自訂網域時,支援以下密碼套件:

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

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>我可以配置 TLS 策略來控制 TLS 協定版本嗎?

您可以通過 Azure 門戶或[Azure REST API](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion)在自訂網功能 HTTPS 設定中配置 Azure 前門中的最低 TLS 版本。 目前,您可以選擇 1.0 和 1.2。

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>是否可以將前門配置為僅支援特定密碼套件?

否,不支援為特定密碼套件配置前門。 但是,您可以從證書頒發機構(例如威瑞信、委託或 Digicert)獲取您自己的自定義 TLS/SSL 證書,並在生成證書時在證書上標記特定的密碼套件。 

### <a name="does-front-door-support-ocsp-stapling"></a>前門是否支援 OCSP 卡帶?

是的,前門預設支援 OCSP 卡帶,無需配置。

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure 前門是否還支援對後端的流量進行重新加密?

是的,Azure 前門支援 TLS/SSL 卸載,以及端到端 TLS,後者重新加密到後端的流量。 事實上,由於與後端的連接透過公共 IP 進行,因此建議您設定前門以使用 HTTPS 作為轉發協定。

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>前門是否支援 HTTPS 連接後端上的自簽名證書?

否,前門不支援自簽名證書,並且限制同時適用於:

1. **後端**介面 :當您將流量轉發為 HTTPS 或 HTTPS 運行狀況探測或從源填充緩存以啟用緩存的路由規則時,不能使用自簽名證書。
2. **前端**:在使用自己的自定義 TLS/SSL 證書在自訂網域上啟用 HTTPS 時,不能使用自簽名證書。

### <a name="why-is-https-traffic-to-my-backend-failing"></a>為什麼到後端的 HTTPS 流量失敗?

對於成功連接到後端的 HTTPS 連接,無論是用於運行狀況探測還是用於轉發請求,HTTPS 流量可能失敗的原因可能有兩個:

1. **憑證主題名稱不匹配**:對於 HTTPS 連接,前門希望後端提供具有與後端主機名匹配的主題名稱的有效 CA 的證書。 例如,如果後端主機名設置為,`myapp-centralus.contosonews.net`並且後端在 TLS 握手期間呈現的`myapp-centralus.contosonews.net`證書既`*myapp-centralus*.contosonews.net`沒有 ,也未在主題名稱中,則前門將拒絕連接並導致錯誤。 
    1. **解決方案**:雖然從合規性角度來看不建議這樣做,但您可以通過禁用前門的證書主題名稱檢查來解決此錯誤。 這在 Azure 門戶中的「設定」下和 API 中的後端池設置下存在。
2. **來自無效 CA 的後端託管證書**:只有[來自有效 CA](/azure/frontdoor/front-door-troubleshoot-allowed-ca)的證書才能在前門的後端使用。 不允許來自內部 C 或自簽名證書的證書。

## <a name="diagnostics-and-logging"></a>診斷和記錄

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Azure 前門提供哪些類型的指標和日誌?

有關日誌和其他診斷功能的資訊,請參閱[前門的監視指標和日誌](front-door-diagnostics.md)。

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>診斷記錄的保留原則為何？

診斷記錄會送至客戶儲存體帳戶，而客戶可以根據其喜好來設定保留原則。 診斷記錄也可以傳送至事件中樞或 Azure 監視器記錄。 有關詳細資訊,請參閱[Azure 前門診斷](front-door-diagnostics.md)。

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>如何獲取 Azure 前門的審核日誌?

審核日誌可用於 Azure 前門。 在門戶中,按一下前門功能表邊欄選項卡中的 **「活動日誌」** 以訪問審核日誌。 

### <a name="can-i-set-alerts-with-azure-front-door"></a>是否可以使用 Azure 前門設置警報?

是的,Azure 前門確實支援警報。 您可以針對計量設定警示。 

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
