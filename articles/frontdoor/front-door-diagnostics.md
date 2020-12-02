---
title: 監視 Azure Front Door 中的計量和記錄 |Microsoft Docs
description: 本文說明 Azure Front Door 支援的不同計量和存取記錄
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: cd99be40700ab1c34176f2bf7497e4debf5cd424
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483792"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>監視 Azure Front Door 中的計量和記錄

藉由使用 Azure Front Door，您可以透過下列方式監視資源：

- **計量**。 Azure Front Door 目前有八個度量可查看效能計數器。
- **記錄**。 活動和診斷記錄可讓您從資源儲存或取用效能、存取和其他資料，以供監視之用。

### <a name="metrics"></a>計量

計量是適用于某些 Azure 資源的功能，可讓您在入口網站中查看效能計數器。 以下是 Front Door 計量的可用計量：

| 計量 | 計量顯示名稱 | 單位 | 維度 | 描述 |
| --- | --- | --- | --- | --- |
| RequestCount | 要求計數 | Count | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 由 Front Door 提供服務的用戶端要求數。  |
| RequestSize | 要求大小 | 位元組 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 從用戶端傳送到 Front Door 之要求的位元組數。 |
| ResponseSize | 回應大小 | 位元組 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 從 Front Door 傳送到用戶端之回應的位元組數。 |
| TotalLatency | 延遲總計 | 毫秒 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 從 AFD Front Door 收到的用戶端要求到最後一個回應位元組傳送至用戶端之前的總時間。 |
| BackendRequestCount | 後端要求計數 | Count | HttpStatus</br>HttpStatusGroup</br>後端 | 從 Front Door 傳送至後端的要求數。 |
| BackendRequestLatency | 後端要求延遲 | 毫秒 | 後端 | 從 Front Door 傳送要求到後端一直到 Front Door 從後端接收最後回應位元組所經歷的時間。 |
| BackendHealthPercentage | 後端健康情況百分比 | 百分比 | 後端</br>BackendPool | 從 Front Door 到後端的健康情況探查成功百分比。 |
| WebApplicationFirewallRequestCount | Web 應用程式防火牆要求計數 | Count | PolicyName</br>RuleName</br>動作 | Front Door 應用程式層安全性處理的用戶端要求數。 |

## <a name="activity-logs"></a><a name="activity-log"></a>活動記錄

活動記錄會提供在 Front Door 上完成之作業的相關資訊。 它們也會決定任何寫入作業 (put、post 或 delete) 在 Front Door 上所採取的動作、物件和時間。

>[!NOTE]
>活動記錄不包含讀取 (取得) 作業。 它們也不包含您使用 Azure 入口網站或原始管理 API 所執行的作業。

存取 Front Door 中的活動記錄，或 Azure 監視器中 Azure 資源的所有記錄。 檢視活動記錄：

1. 選取您的 Front Door 實例。
2. 選取 [活動記錄]。

    :::image type="content" source="./media/front-door-diagnostics/activity-log.png" alt-text="活動記錄檔":::

3. 選擇篩選範圍，然後選取 [套用 **]。**

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>診斷記錄
診斷記錄會提供有關作業和錯誤的豐富資訊，這些資訊對於審核和疑難排解而言很重要。 診斷記錄與活動記錄不同。

活動記錄可讓您深入瞭解在 Azure 資源上執行的作業。 診斷記錄可讓您深入瞭解資源已完成的作業。 如需詳細資訊，請參閱 [Azure 監視器診斷記錄](../azure-monitor/platform/platform-logs-overview.md)。

:::image type="content" source="./media/front-door-diagnostics/diagnostic-log.png" alt-text="診斷記錄":::

若要為您的 Front Door 設定診斷記錄：

1. 選取您的 Azure Front Door。

2. 選擇 [ **診斷設定**]。

3. 選取 [開啟診斷]。 將診斷記錄連同計量封存至儲存體帳戶、將它們串流至事件中樞，或將它們傳送至 Azure 監視器記錄。

Front Door 目前提供 (批次處理) 的診斷記錄。 診斷記錄會提供個別的 API 要求，每個專案都有下列架構：

| 屬性  | 描述 |
| ------------- | ------------- |
| BackendHostname | 如果將要求轉送至後端，此欄位代表後端的主機名稱。 如果將要求重新導向或轉送至區域快取，則此欄位將會是空白， (當已啟用路由規則) 的快取時。 |
| CacheStatus | 針對快取案例，此欄位會在 POP 上定義快取點擊/遺漏 |
| ClientIp | 發出要求之用戶端的 IP 位址。 如果要求中有 X 轉送的標頭，則會從相同的挑選用戶端 IP。 |
| ClientPort | 提出要求之用戶端的 IP 埠。 |
| HttpMethod | 要求使用的 HTTP 方法。 |
| HttpStatusCode | 從 Proxy 傳回的 HTTP 狀態碼。 |
| HttpStatusDetails | 對要求產生的狀態。 此字串值的意義可以在狀態參考資料表中找到。 |
| HttpVersion | 要求或連線的類型。 |
| POP | 要求進入之邊緣的簡短名稱。 |
| RequestBytes | HTTP 要求訊息的大小 (以位元組為單位)，包括要求標頭和要求本文。 |
| RequestUri | 接收之要求的 URI。 |
| ResponseBytes | 後端伺服器以回應形式傳回的位元組。  |
| RoutingRuleName | 要求相符的路由規則名稱。 |
| RulesEngineMatchNames | 要求相符的規則名稱。 |
| SecurityProtocol | 要求所使用的 TLS/SSL 通訊協定版本，如果沒有加密則為 null。 |
| SentToOriginShield </br>  (已淘汰) * **請參閱下一節中的淘汰注意事項。**| 若為 true，則表示是從來源保護盾快取回答要求，而非邊緣 Pop。 原始保護盾是用來改善快取點擊率的父代快取。 |
| isReceivedFromClient | 若為 true，表示要求來自用戶端。 如果為 false，表示要求在 edge (子 POP) 中遺失，並從來源防護 (父 POP) 回應。 |
| TimeTaken | 從要求的第一個位元組到回應輸出之最後一個位元組的 Front Door 時間長度（以秒為單位）。 |
| TrackingReference | 這項唯一的參考字串可識別 Front Door 所提供的要求，也會以 X-Azure Ref 標頭的形式傳送給用戶端， 這是在特定要求的存取記錄中搜尋詳細資料時的必要項目。 |
| UserAgent | 用戶端使用的瀏覽器類型。 |
| ErrorInfo | 此欄位包含特定類型的錯誤，以進行進一步的疑難排解。 </br> 可能的值包括： </br> **>aad-userreadusingalternativesecurityid-noerror**：表示找不到錯誤。 </br> **CertificateError**：一般 SSL 憑證錯誤。</br> **CertificateNameCheckFailed**： SSL 憑證中的主機名稱無效或不相符。 </br> **ClientDisconnected**：由於用戶端網路連線而導致要求失敗。 </br> **UnspecifiedClientError**：一般用戶端錯誤。 </br> **InvalidRequest**：不正確要求。 原因可能是標頭、主體和 URL 格式不正確所造成。 </br> **DNSFailure**： DNS 失敗。 </br> **DNSNameNotResolved**：無法解析伺服器名稱或位址。 </br> **OriginConnectionAborted**：來源的連接突然停止。 </br> **OriginConnectionError**：一般來源連接錯誤。 </br> **OriginConnectionRefused**：無法建立與來源的連接。 </br> **OriginError**：一般來源錯誤。 </br> **OriginInvalidResponse**：來源傳回無效或無法辨識的回應。 </br> **OriginTimeout**：原始要求過期的超時時間。 </br> **ResponseHeaderTooBig**：來源傳回太大的回應標頭。 </br> **RestrictedIP**：因為限制 IP 而封鎖要求。 </br> **SSLHandshakeError**：無法建立與來源的連線，因為發生 SSL 手動搖動失敗。 </br> **UnspecifiedError**：發生錯誤，不符合資料表中的任何錯誤。 |

### <a name="sent-to-origin-shield-deprecation"></a>傳送至原始防護盾
原始記錄屬性 **isSentToOriginShield** 已被取代，並以新的欄位 **isReceivedFromClient** 取代。 如果您已經在使用已被取代的欄位，請使用新欄位。 

原始記錄包含從 CDN edge (子 POP) 和來源防護產生的記錄。 來源防護是指策略性地位於全球各地的父節點。 這些節點會與源伺服器通訊，並減少來源的流量負載。 

針對移至來源防護的每個要求，有2個記錄檔專案：

* 一個用於邊緣節點
* 一個用於來源盾牌。 

若要區別邊緣節點與來源防護之間的輸出或回應，您可以使用欄位 **isReceivedFromClient** 來取得正確的資料。 

如果值為 false，則表示要求會從來源防護回應至邊緣節點。 這種方法適用于比較原始記錄與計費資料。 從原始防護站輸出到邊緣節點時，不會產生費用。 從邊緣節點至用戶端的輸出會產生費用。 

**Kusto 查詢範例，以排除記錄分析中原始防護板上產生的記錄。**

`AzureDiagnostics 
| where Category == "FrontdoorAccessLog" and isReceivedFromClient_b == true`

> [!NOTE]
> 針對各種路由設定和流量行為，某些欄位（例如 backendHostname、cacheStatus、isReceivedFromClient 和 POP 欄位）可能會以不同的值回應。 下表說明這些欄位適用于各種案例的不同值：

| 案例 | 記錄專案計數 | POP | BackendHostname | isReceivedFromClient | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| 未啟用快取的路由規則 | 1 | Edge POP 碼 | 轉寄要求的後端 | 是 | CONFIG_NOCACHE |
| 啟用快取的路由規則。 邊緣 POP 上的快取點擊 | 1 | Edge POP 碼 | 空白 | 是 | 打 |
| 啟用快取的路由規則。 在邊緣 POP 快取遺漏，但在父快取 POP 快取命中 | 2 | 1. Edge POP 碼</br>2. 父快取 POP 碼 | 1. 父快取 POP 主機名稱</br>2. 空白 | 1. True</br>2. False | 1. 遺漏</br>2. 點擊 |
| 啟用快取的路由規則。 快取遺漏 edge POP，但在父快取 POP 發生部分快取點擊 | 2 | 1. Edge POP 碼</br>2. 父快取 POP 碼 | 1. 父快取 POP 主機名稱</br>2. 可協助填入快取的後端 | 1. True</br>2. False | 1. 遺漏</br>2. PARTIAL_HIT |
| 啟用快取的路由規則。 在邊緣 POP 快取 PARTIAL_HIT，但在父快取 POP 快取點擊 | 2 | 1. Edge POP 碼</br>2. 父快取 POP 碼 | 1. Edge POP 碼</br>2. 父快取 POP 碼 | 1. True</br>2. False | 1. PARTIAL_HIT</br>2. 點擊 |
| 啟用快取的路由規則。 邊緣和父快取 POPP 的快取遺漏 | 2 | 1. Edge POP 碼</br>2. 父快取 POP 碼 | 1. Edge POP 碼</br>2. 父快取 POP 碼 | 1. True</br>2. False | 1. 遺漏</br>2. 遺漏 |

> [!NOTE]
> 在快取案例中，當要求的某些位元組從 Front Door edge 或來源防護快取中提供時，快取狀態的值將會 partial_hit，而某些位元組是從大型物件的來源提供。

Front Door 會使用稱為物件區塊化的技術。 當要求大型檔案時，Front Door 會從來源抓取檔案的較小部分。 Front Door POP 伺服器收到所要求檔案的完整或位元組範圍之後，Front Door edge server 就會以 8 MB 的區塊要求來源的檔案。

當區塊抵達 Front Door edge 之後，它就會快取並立即提供給使用者。 Front Door 接著會以平行方式預先提取下一個區塊。 這項預先提取可確保內容在使用者前保持一個區塊，以降低延遲。 此程式會繼續進行，直到 () 要求下載整個檔案為止，如果) 要求，則 (可使用所有的位元組範圍，或用戶端關閉連接。 如需位元組範圍要求的詳細資訊，請參閱 RFC 7233。 Front Door 會在收到任何區塊時加以快取。 不需要在 Front Door 快取上快取整個檔案。 系統會從 Front Door 快取提供檔案或位元組範圍的後續要求。 如果未在 Front Door 上快取所有區塊，則會使用預先提取來要求來源的區塊。 此最佳化依賴原始伺服器的功能，支援位元組範圍的要求。 如果原始伺服器不支援位元組範圍要求，此最佳化就不會生效。

## <a name="next-steps"></a>後續步驟

- [建立 Front Door 設定檔](quickstart-create-front-door.md)
- [Front Door 的運作方式](front-door-routing-architecture.md)
