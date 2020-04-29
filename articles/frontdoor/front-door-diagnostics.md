---
title: 監視 Azure Front 中的計量和記錄 |Microsoft Docs
description: 本文說明 Azure 前端支援的不同計量和存取記錄
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: b935355cce36a6e26b168db286ab40248f8f0f68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79471722"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>監視 Azure Front 中的計量和記錄

藉由使用 Azure Front，您可以透過下列方式來監視資源：

- **計量**。 Azure Front 大門目前有七個計量可供您查看效能計數器。
- **記錄**。 活動和診斷記錄可讓您儲存或取用資源的效能、存取和其他資料，以供監視之用。

### <a name="metrics"></a>計量

計量是某些 Azure 資源的功能，可讓您在入口網站中查看效能計數器。 下列為可用的 Front 的計量：

| 計量 | 計量顯示名稱 | 單位 | 維度 | 描述 |
| --- | --- | --- | --- | --- |
| RequestCount | 要求計數 | 計數 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 由 Front Door 提供服務的用戶端要求數。  |
| RequestSize | 要求大小 | 位元組 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 從用戶端傳送到 Front Door 之要求的位元組數。 |
| ResponseSize | 回應大小 | 位元組 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 從 Front Door 傳送到用戶端之回應的位元組數。 |
| TotalLatency | 延遲總計 | 毫秒 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 從 Front 門收到的用戶端要求計算的時間，直到用戶端認可來自 Front 的最後一個回應位元組為止。 |
| BackendRequestCount | 後端要求計數 | 計數 | HttpStatus</br>HttpStatusGroup</br>後端 | 從 Front Door 傳送至後端的要求數。 |
| BackendRequestLatency | 後端要求延遲 | 毫秒 | 後端 | 從 Front Door 傳送要求到後端一直到 Front Door 從後端接收最後回應位元組所經歷的時間。 |
| BackendHealthPercentage | 後端健康情況百分比 | 百分比 | 後端</br>BackendPool | 從 Front Door 到後端的健康情況探查成功百分比。 |
| WebApplicationFirewallRequestCount | Web 應用程式防火牆要求計數 | 計數 | PolicyName</br>RuleName</br>動作 | Front Door 應用程式層安全性處理的用戶端要求數。 |

## <a name="activity-logs"></a><a name="activity-log"></a>活動記錄

活動記錄會提供有關 Front 的作業資訊。 它們也會決定要在 Front 上執行的任何寫入作業（put、post 或 delete）的內容、物件和時機。

>[!NOTE]
>活動記錄不包含讀取（get）作業。 它們也不包含您使用 Azure 入口網站或原始管理 API 執行的作業。

在 Azure 監視器中，存取您的 Front 或 Azure 資源的所有記錄中的活動記錄。 檢視活動記錄：

1. 選取您的 Front 門板實例。
2. 選取 [**活動記錄**]。

    ![活動記錄檔](./media/front-door-diagnostics/activity-log.png)

3. 選擇篩選範圍，然後選取 [套用 **]。**

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>診斷記錄
診斷記錄提供有關進行審核和疑難排解的重要作業和錯誤的豐富資訊。 診斷記錄與活動記錄不同。

活動記錄可讓您深入瞭解在 Azure 資源上完成的作業。 診斷記錄可讓您深入瞭解您的資源所執行的作業。 如需詳細資訊，請參閱[Azure 監視器診斷記錄](../azure-monitor/platform/platform-logs-overview.md)。

![診斷記錄](./media/front-door-diagnostics/diagnostic-log.png)

若要設定您的前門的診斷記錄：

1. 選取您的 Azure Front 門。

2. 選擇 [**診斷設定**]。

3. 選取 [開啟診斷]****。 將診斷記錄連同計量封存至儲存體帳戶、將其串流至事件中樞，或將它們傳送至 Azure 監視器記錄。

Front 門目前提供診斷記錄（每小時批次）。 診斷記錄會提供個別的 API 要求，其中每個專案都具有下列架構：

| 屬性  | 說明 |
| ------------- | ------------- |
| BackendHostname | 如果將要求轉送到後端，此欄位代表後端的主機名稱。 如果要求已重新導向或轉送到區域快取（針對路由規則啟用快取），此欄位將會是空白。 |
| CacheStatus | 針對快取案例，此欄位會在 POP 上定義快取點擊/遺漏 |
| ClientIp | 發出要求之用戶端的 IP 位址。 如果要求中有 X 轉送的標頭，則會從相同的中挑選用戶端 IP。 |
| ClientPort | 提出要求之用戶端的 IP 埠。 |
| HttpMethod | 要求使用的 HTTP 方法。 |
| HttpStatusCode | 從 proxy 傳回的 HTTP 狀態碼。 |
| HttpStatusDetails | 要求的結果狀態。 此字串值的意義可以在狀態參照資料表中找到。 |
| HttpVersion | 要求或連接的類型。 |
| POP | 要求進入之邊緣的簡短名稱。 |
| RequestBytes | HTTP 要求訊息的大小（以位元組為單位），包括要求標頭和要求主體。 |
| RequestUri | 接收之要求的 URI。 |
| ResponseBytes | 後端伺服器傳送的位元組作為回應。  |
| RoutingRuleName | 要求所符合之路由規則的名稱。 |
| SecurityProtocol | 要求所使用的 TLS/SSL 通訊協定版本，如果沒有加密，則為 null。 |
| SentToOriginShield | 布林值欄位，表示第一個環境中是否有快取遺漏，而且要求已傳送至區域快取。 如果路由規則是重新導向，或未啟用快取，請忽略此欄位。 |
| TimeTaken | 動作所花費的時間長度（以毫秒為單位）。 |
| TrackingReference | 唯一的參考字串，可識別 Front 門所提供的要求，也會以 X-Azure Ref 標頭的形式傳送給用戶端。 在特定要求的存取記錄中搜尋詳細資料時的必要項。 |
| UserAgent | 用戶端使用的瀏覽器類型。 |

**注意：** 針對各種路由設定和流量行為，某些欄位（例如 backendHostname、cacheStatus、sentToOriginShield 和 POP 欄位）可能會以不同的值回應。 下表說明不同的值，這些欄位將適用于各種案例：

| 案例 | 記錄專案計數 | POP | BackendHostname | SentToOriginShield | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| 未啟用快取的路由規則 | 1 | 邊緣 POP 程式碼 | 轉送要求的後端 | False | CONFIG_NOCACHE |
| 已啟用快取的路由規則。 邊緣 POP 上的快取點擊 | 1 | 邊緣 POP 程式碼 | 空白 | False | 找到 |
| 已啟用快取的路由規則。 邊緣 POP 上的快取遺漏，但在父快取 POP 快取命中 | 2 | 1. 邊緣 POP 代碼</br>2. 父快取 POP 程式碼 | 1. 父快取 POP 主機名稱</br>2. 空白 | 1. True</br>2. False | 1. 遺漏</br>2. PARTIAL_HIT |
| 已啟用快取的路由規則。 邊緣和父快取 POP 的快取遺漏 | 2 | 1. 邊緣 POP 代碼</br>2. 父快取 POP 程式碼 | 1. 父快取 POP 主機名稱</br>2. 可協助填入快取的後端 | 1. True</br>2. False | 1. 遺漏</br>2. 遺漏 |


## <a name="next-steps"></a>後續步驟

- [建立 Front Door 設定檔](quickstart-create-front-door.md)
- [Front 門板的運作方式](front-door-routing-architecture.md)
