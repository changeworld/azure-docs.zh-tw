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
ms.date: 09/18/2018
ms.author: duau
ms.openlocfilehash: 6f5051dd7dedcc49320557f17148bcdc9bf539ab
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399747"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>監視 Azure Front Door 中的計量和記錄

藉由使用 Azure Front Door，您可以透過下列方式監視資源：

- **計量**。 Azure Front Door 目前有七個計量可查看效能計數器。
- **記錄**。 活動和診斷記錄可讓您從資源儲存或取用效能、存取和其他資料，以供監視之用。

### <a name="metrics"></a>計量

計量是適用于某些 Azure 資源的功能，可讓您在入口網站中查看效能計數器。 以下是 Front Door 計量的可用計量：

| 計量 | 計量顯示名稱 | 單位 | 維度 | 描述 |
| --- | --- | --- | --- | --- |
| RequestCount | 要求計數 | Count | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 由 Front Door 提供服務的用戶端要求數。  |
| RequestSize | 要求大小 | 位元組 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 從用戶端傳送到 Front Door 之要求的位元組數。 |
| ResponseSize | 回應大小 | 位元組 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 從 Front Door 傳送到用戶端之回應的位元組數。 |
| TotalLatency | 延遲總計 | 毫秒 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 從 Front Door 收到的用戶端要求計算的時間，直到用戶端從 Front Door 確認最後一個回應位元組為止。 |
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

    ![活動記錄檔](./media/front-door-diagnostics/activity-log.png)

3. 選擇篩選範圍，然後選取 [套用 **]。**

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>診斷記錄
診斷記錄會提供有關作業和錯誤的豐富資訊，這些資訊對於審核和疑難排解而言很重要。 診斷記錄與活動記錄不同。

活動記錄可讓您深入瞭解在 Azure 資源上執行的作業。 診斷記錄可讓您深入瞭解您的資源所執行的作業。 如需詳細資訊，請參閱 [Azure 監視器診斷記錄](../azure-monitor/platform/platform-logs-overview.md)。

![診斷記錄](./media/front-door-diagnostics/diagnostic-log.png)

若要為您的 Front Door 設定診斷記錄：

1. 選取您的 Azure Front Door。

2. 選擇 [ **診斷設定**]。

3. 選取 [開啟診斷]。 將診斷記錄連同計量封存至儲存體帳戶、將它們串流至事件中樞，或將它們傳送至 Azure 監視器記錄。

Front Door 目前提供 (批次處理) 的診斷記錄。 診斷記錄會提供個別的 API 要求，每個專案都有下列架構：

| 屬性  | 描述 |
| ------------- | ------------- |
| BackendHostname | 如果將要求轉送至後端，此欄位代表後端的主機名稱。 如果已將要求重新導向或轉送至區域快取，則此欄位將會是空白， (在路由規則) 啟用快取時。 |
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
| SentToOriginShield | 表示第一個環境中是否有快取遺漏，而且要求已傳送至區域快取的布林值欄位。 如果路由規則是重新導向或未啟用快取，請忽略此欄位。 |
| TimeTaken | 從要求的第一個位元組到回應輸出之最後一個位元組的 Front Door 時間長度（以秒為單位）。 |
| TrackingReference | 這項唯一的參考字串可識別 Front Door 所提供的要求，也會以 X-Azure Ref 標頭的形式傳送給用戶端， 這是在特定要求的存取記錄中搜尋詳細資料時的必要項目。 |
| UserAgent | 用戶端使用的瀏覽器類型。 |

**注意：** 針對各種路由設定和流量行為，某些欄位（例如 backendHostname、cacheStatus、sentToOriginShield 和 POP 欄位）可能會以不同的值回應。 下表說明不同的值，這些欄位將適用于各種案例：

| 案例 | 記錄專案計數 | POP | BackendHostname | SentToOriginShield | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| 未啟用快取的路由規則 | 1 | Edge POP 碼 | 轉寄要求的後端 | 否 | CONFIG_NOCACHE |
| 啟用快取的路由規則。 邊緣 POP 上的快取點擊 | 1 | Edge POP 碼 | 空白 | 否 | 打 |
| 啟用快取的路由規則。 邊緣 POP 快取遺漏，但在父快取 POP 快取點擊 | 2 | 1. Edge POP 碼</br>2. 父快取 POP 碼 | 1. 父快取 POP 主機名稱</br>2. 空白 | 1. True</br>2. False | 1. 遺漏</br>2. PARTIAL_HIT |
| 啟用快取的路由規則。 邊緣和父快取 POP 上的快取遺漏 | 2 | 1. Edge POP 碼</br>2. 父快取 POP 碼 | 1. 父快取 POP 主機名稱</br>2. 可協助填入快取的後端 | 1. True</br>2. False | 1. 遺漏</br>2. 遺漏 |


## <a name="next-steps"></a>接下來的步驟

- [建立 Front Door 設定檔](quickstart-create-front-door.md)
- [Front Door 的運作方式](front-door-routing-architecture.md)
