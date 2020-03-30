---
title: 排除 Azure 應用程式見解可用性測試的故障 |微軟文檔
description: 在 Azure 應用程式見解中排除 Web 測試故障。 如果網站無法使用或回應緩慢，將收到警示。
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/19/2019
ms.reviewer: sdash
ms.openlocfilehash: f135aa6c0a4a55f8a42fd858572cc811e25b27c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671098"
---
# <a name="troubleshooting"></a>疑難排解

本文將説明您解決使用可用性監視時可能出現的常見問題。

## <a name="ssltls-errors"></a>SSL/TLS 錯誤

|症狀/錯誤訊息| 可能的原因|
|--------|------|
|無法創建 SSL/TLS 安全通道  | SSL 版本。 僅支援 TLS 1.0、1.1 和 1.2。 **不支援 SSLv3。**
|TLSv1.2 記錄層：警報（級別：致命、描述：記錄錯誤 MAC）| [有關詳細資訊](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake)，請參閱堆疊交換執行緒。
|失敗的 URL 是指向 CDN（內容交付網路） | 這可能是由於 CDN 上的配置錯誤造成的 |  

### <a name="possible-workaround"></a>可能的解決方法

* 如果遇到此問題的 URL 始終與從屬資源有關，則建議禁用 Web 測試**的解析相關請求**。

## <a name="test-fails-only-from-certain-locations"></a>測試僅從特定位置失敗

|症狀/錯誤訊息| 可能的原因|
|----|---------|
|連接嘗試失敗，因為連接方在一段時間後未正確回應  | 某些位置的測試代理被防火牆阻止。|
|    |某些 IP 位址的重新路由是通過（負載等化器、地理流量管理器、Azure 快速路由）進行。 
|    |如果使用 Azure ExpressRoute，則在某些情況下，在發生[非對稱路由](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing)的情況下可以丟棄資料包。|

## <a name="test-failure-with-a-protocol-violation-error"></a>測試失敗與協定衝突錯誤

|症狀/錯誤訊息| 可能的原因| 可能的解決方案 |
|----|---------|-----|
|伺服器違反了協定。 節_回應標題細節=CR 後必須跟隨 LF | 當檢測到格式錯誤的標頭時，將發生這種情況。 具體而言，某些標頭可能不使用 CRLF 來指示行尾，這違反了 HTTP 規範。 應用程式見解強制執行此 HTTP 規範，並且使用格式錯誤的標頭失敗回應。| a. 請與網站主機供應商/CDN 供應商聯繫以修復故障伺服器。 <br> b. 如果失敗的請求是資源（例如樣式檔、圖像、腳本），您可以考慮禁用從屬請求的解析。 請記住，如果您這樣做，您將失去監視這些檔可用性的能力）。

> [!NOTE]
> 在 HTTP 標頭驗證寬鬆的瀏覽器上，此 URL 可能不會失敗。 如需問題的詳細說明，請參閱此部落格文章：http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>常見的疑難排解問題

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>網站看似正常，但我看到測試失敗？ 為什麼 Application Insights 發出警示？

   * 您的測試是否啟用**了分析相關請求**？ 這導致對腳本、圖像等資源進行嚴格檢查。這些類型的故障在瀏覽器上可能並不明顯。 請檢查所有映像、指令碼、樣式表和頁面載入的任何其他檔案。 如果其中任何一個失敗，則測試將報告為失敗，即使主 HTML 頁載入沒有問題。 要使測試對此類資源故障敏感，只需從測試組態中取消選中"分析依賴請求"。

   * 為了降低瞬態網路信號等雜訊的可能性，請確保檢查測試失敗配置的啟用重試。 您也可以從更多位置進行測試並據以管理警示規則閾值，以免發生會造成過度警示的位置特定問題。

   * 從可用性體驗中按一下任何一個紅點，或從 [搜尋總管] 中按一下任何可用性失敗，查看為什麼回報失敗的詳細資料。 測試結果以及相關聯的伺服器端遙測資料 (如已啟用) 應該有助於您了解測試失敗的原因。 暫時性問題的常見原因是網路或連線問題。

   * 測試逾時？ 我們會在 2 分鐘後中止測試。 如果您進行的 ping 或多步驟測試超過 2 分鐘的時間，我們會回報失敗。 請考慮將測試分成可在較短的持續時間內完成的多個測試。

   * 所有位置都回報失敗，還是只有一部分回報？ 如果只有一部分回報失敗時，則可能是因為網路/CDN 問題。 同樣地，按一下紅點應該有助於您了解位置回報失敗的原因。

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>警示觸發和/或解決時，我未收到電子郵件？

檢查傳統警示組態以確認您的電子郵件直接列出，或您列在其中的通訊群組清單已設定為接收通知。 如果是，請檢查發佈清單設定以確認可以接收外部電子郵件。 也檢查郵件系統管理員是否可能已設定會導致此問題的任何原則。

### <a name="i-did-not-receive-the-webhook-notification"></a>我並未收到 Webhook 通知？

檢查以確定接收 Webhook 通知的應用程式可供使用，而且成功處理 Webhook 要求。 如需詳細資訊，請參閱[這裡](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)。

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>包含通訊協定違規錯誤的間歇性測試失敗？

此錯誤 (「通訊協定違規..CR 後面必須接著 LF」) 表示伺服器 (或相依性) 有問題。 這會發生於回應中設定的標頭格式不正確時。 可能是由負載平衡器或 CDN 所造成。 具體而言，某些標頭可能不使用 CRLF 指示行尾，這違反了 HTTP 規範，因此在 .NET WebRequest 級別驗證失敗。 檢查對可能違反的發現標頭的回應。

> [!NOTE]
> 在 HTTP 標頭驗證寬鬆的瀏覽器上，此 URL 可能不會失敗。 如需問題的詳細說明，請參閱此部落格文章：http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>我沒有看到任何相關的伺服器端遙測來診斷測試失敗？

如果您已針對伺服器端應用程式設定 Application Insights，這可能是因為正在進行[取樣](../../azure-monitor/app/sampling.md)。 請選取不同的可用性結果。

### <a name="can-i-call-code-from-my-web-test"></a>可以從我的 Web 測試呼叫程式碼嗎？

否。 測試步驟必須在 .webtest 檔案中。 而且您不能呼叫其他 Web 測試或使用迴圈。 但是這裡有一些您會覺得有用的外掛程式。


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>「Web 測試」和「可用性測試」之間有任何差異嗎？

這兩個詞彙可能會交替參考。 可用性測試是更廣泛的詞彙，除了多重步驟 Web 測試以外，還包含單一 URL ping 測試。

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>我想要在位於防火牆後面執行的內部伺服器上使用可用性測試。

   有兩種可能的解決方法：

   * 設定防火牆以允許來自[我們 Web 測試代理程式的 IP 位址](../../azure-monitor/app/ip-addresses.md)所發出的內送要求。
   * 撰寫您自己的程式碼以定期測試您的內部伺服器。 執行程式碼作為您防火牆後方測試伺服器上的背景處理序。 您的測試處理序可以使用核心 SDK 套件中的 [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) API，將其結果傳送至 Application Insights。 這需要測試伺服器具有 Application Insights 內嵌端點的連出存取，但這比起替代的允許連入要求是較小的安全性風險。 結果將顯示在可用性 Web 測試刀片中，但體驗將從可通過門戶創建的測試中稍微簡化。 自訂可用性測試也會在分析、搜索和指標中顯示為可用性結果。

### <a name="uploading-a-multi-step-web-test-fails"></a>上傳多步驟 Web 測試失敗

這可能是由於一些原因而發生：
   * 有 300 K 的大小限制。
   * 不支援迴圈。
   * 不支援其他 Web 測試的參考。
   * 不支援資料來源。

### <a name="my-multi-step-test-doesnt-complete"></a>多步驟測試未完成

每個測試有 100 個要求的限制。 另外，如果執行時間超過兩分鐘，就會停止測試。

### <a name="how-can-i-run-a-test-with-client-certificates"></a>如何使用用戶端憑證執行測試？

目前不支援。

## <a name="who-receives-the-classic-alert-notifications"></a>誰會收到 (傳統) 警示通知？

本節僅適用於傳統警示，並協助您將警示通知最佳化，以確保只有您所需的收件者會收到通知。 若要深入了解[傳統警示](../platform/alerts-classic.overview.md)和新警示體驗之間的差異，請參閱[警示概觀文章](../platform/alerts-overview.md)。 若要控制新警示體驗中的警示通知，請使用[動作群組](../platform/action-groups.md)。

* 我們建議針對傳統警示通知使用特定的收件者。

* 如果是有關 Y 個位置之中有 X 個失敗的警示，[大量/群組]**** 核取方塊選項 (如已啟用) 就會傳送給具有管理員/共同管理員角色的使用者。  基本上，「訂用帳戶」__ 的「所有」__ 管理員都將收到通知。

* 對於可用性指標的警報，如果啟用批量 **/組**核取方塊選項，則發送給訂閱中具有擁有者、參與者或讀取器角色的使用者。 實際上，「所有」__ 有權存取 Application Insights 資源訂用帳戶的使用者都在涵蓋範圍內，而且將會收到通知。 

> [!NOTE]
> 如果您目前使用 [大量/群組]**** 核取方塊選項並停用它，您將無法還原變更。

如果您需要根據使用者的角色來通知他們，請使用新警示體驗/近乎即時警示。 使用[動作群組](../platform/action-groups.md)，您可以為具有任一個參與者/擁有者/讀者角色 (不會結合來作為單一選項) 的使用者設定電子郵件通知。

## <a name="next-steps"></a>後續步驟

* [多步驟 Web 測試](availability-multistep.md)
* [URL ping 測試](monitor-web-app-availability.md)
