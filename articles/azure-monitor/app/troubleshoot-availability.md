---
title: 針對您的 Azure 應用程式 Insights 可用性測試進行疑難排解
description: 針對 Azure 應用程式 Insights 中的 web 測試進行疑難排解。 如果網站無法使用或回應緩慢，將收到警示。
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 04/28/2020
ms.reviewer: sdash
ms.openlocfilehash: 0ac8dd189bee1c1d4f5a7a4d0f7de68b085fbc56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87318142"
---
# <a name="troubleshooting"></a>疑難排解

本文將協助您針對使用可用性監視時可能會發生的常見問題進行疑難排解。

## <a name="ssltls-errors"></a>SSL/TLS 錯誤

|徵兆/錯誤訊息| 可能的原因|
|--------|------|
|無法建立 SSL/TLS 安全通道  | SSL 版本。 僅支援 TLS 1.0、1.1 和1.2。 **不支援 SSLv3。**
|Tlsv1.1 1.2 記錄層：警示 (層級：嚴重、描述：記錄 MAC) 不正確| 如需 [詳細資訊](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake)，請參閱 >stackexchange.redis 執行緒。
|失敗的 URL 會是 CDN (內容傳遞網路)  | 這可能是因為 CDN 的設定不正確所造成 |  

### <a name="possible-workaround"></a>可能的解決方法

* 如果發生問題的 Url 一律是相依的資源，建議您停用 web 測試的 **剖析相依要求** 。

## <a name="test-fails-only-from-certain-locations"></a>測試只會從特定位置失敗

|徵兆/錯誤訊息| 可能的原因|
|----|---------|
|連接嘗試失敗，因為連線的合作物件在一段時間後未正確回應  | 防火牆會封鎖特定位置的測試代理程式。|
|    |您可以透過 (負載平衡器、地理流量管理員、Azure Express Route，在特定 IP 位址的路由傳送。 )  
|    |如果使用 Azure ExpressRoute，在 [發生非對稱式路由](../../expressroute/expressroute-asymmetric-routing.md)的情況下，有可能會卸載封包。|

## <a name="test-failure-with-a-protocol-violation-error"></a>測試失敗，發生通訊協定違規錯誤

|徵兆/錯誤訊息| 可能的原因| 可能的解決方式 |
|----|---------|-----|
|伺服器認可了通訊協定違規。 Section = ResponseHeader Detail = CR 之後必須接著 LF | 偵測到格式錯誤的標頭時，就會發生此情況。 具體來說，某些標頭可能不會使用 CRLF 來指出行尾，這違反了 HTTP 規格。 Application Insights 強制執行此 HTTP 規格，但標頭格式錯誤的回應失敗。| a. 請聯絡網站主機提供者/CDN 提供者，以修正故障的伺服器。 <br> b. 如果失敗的要求是資源 (例如樣式檔案、影像、腳本) ，您可以考慮停用相依要求的剖析。 請記住，如果您這樣做，將會失去監視這些檔案) 的能力。

> [!NOTE]
> 在 HTTP 標頭驗證寬鬆的瀏覽器上，此 URL 可能不會失敗。 如需問題的詳細說明，請參閱此部落格文章：http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>常見的疑難排解問題

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>網站看似正常，但我看到測試失敗？ 為什麼 Application Insights 發出警示？

   * 您的測試是否已啟用 **剖析相依要求** ？ 這會導致對像是腳本、影像等資源的嚴格檢查。在瀏覽器中，這些類型的失敗可能不明顯。 請檢查所有映像、指令碼、樣式表和頁面載入的任何其他檔案。 如果其中任何一個失敗，則測試會回報為失敗，即使主要的 HTML 網頁載入卻沒有問題。 若要將測試 desensitize 到這類資源失敗，只要取消核取 [從測試設定剖析相依要求] 即可。

   * 若要減少暫時性網路標誌等雜訊的機率，請確認已核取 [啟用測試失敗的重試] 設定。 您也可以從更多位置進行測試並據以管理警示規則閾值，以免發生會造成過度警示的位置特定問題。

   * 從可用性體驗中按一下任何一個紅點，或從 [搜尋總管] 中按一下任何可用性失敗，查看為什麼回報失敗的詳細資料。 測試結果以及相關聯的伺服器端遙測資料 (如已啟用) 應該有助於您了解測試失敗的原因。 暫時性問題的常見原因是網路或連線問題。

   * 測試逾時？ 我們會在 2 分鐘後中止測試。 如果您進行的 ping 或多步驟測試超過 2 分鐘的時間，我們會回報失敗。 請考慮將測試分成可在較短的持續時間內完成的多個測試。

   * 所有位置都回報失敗，還是只有一部分回報？ 如果只有一部分回報失敗時，則可能是因為網路/CDN 問題。 同樣地，按一下紅點應該有助於您了解位置回報失敗的原因。

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>警示觸發和/或解決時，我未收到電子郵件？

檢查傳統警示組態以確認您的電子郵件直接列出，或您列在其中的通訊群組清單已設定為接收通知。 如果是，請檢查發佈清單設定以確認可以接收外部電子郵件。 也檢查郵件系統管理員是否可能已設定會導致此問題的任何原則。

### <a name="i-did-not-receive-the-webhook-notification"></a>我並未收到 Webhook 通知？

檢查以確定接收 Webhook 通知的應用程式可供使用，而且成功處理 Webhook 要求。 如需詳細資訊，請參閱[這裡](../platform/alerts-log-webhook.md)。

### <a name="i-am-getting--403-forbidden-errors-what-does-this-mean"></a>我收到403禁止的錯誤，這代表什麼意思？

此錯誤表示您需要新增防火牆例外，以允許可用性代理程式測試您的目標 url。 如需允許的代理程式 IP 位址完整清單，請參閱 [IP 例外狀況文章](./ip-addresses.md#availability-tests)。

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>包含通訊協定違規錯誤的間歇性測試失敗？

此錯誤 (「通訊協定違規..CR 後面必須接著 LF」) 表示伺服器 (或相依性) 有問題。 這會發生於回應中設定的標頭格式不正確時。 可能是由負載平衡器或 CDN 所造成。 具體來說，某些標頭可能不會使用 CRLF 來指出行尾，這違反了 HTTP 規格，因此無法在 .NET WebRequest 層級進行驗證。 檢查回應以找出可能違反的標頭。

> [!NOTE]
> 在 HTTP 標頭驗證寬鬆的瀏覽器上，此 URL 可能不會失敗。 如需問題的詳細說明，請參閱此部落格文章：http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>我看不到任何相關的伺服器端遙測來診斷測試失敗嗎？ *

如果您已針對伺服器端應用程式設定 Application Insights，這可能是因為正在進行[取樣](./sampling.md)。 請選取不同的可用性結果。

### <a name="can-i-call-code-from-my-web-test"></a>可以從我的 Web 測試呼叫程式碼嗎？

否。 測試步驟必須在 .webtest 檔案中。 而且您不能呼叫其他 Web 測試或使用迴圈。 但是這裡有一些您會覺得有用的外掛程式。


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>「Web 測試」和「可用性測試」之間有任何差異嗎？

這兩個詞彙可能會交替參考。 可用性測試是更廣泛的詞彙，除了多重步驟 Web 測試以外，還包含單一 URL ping 測試。

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>我想要在位於防火牆後面執行的內部伺服器上使用可用性測試。

   有兩種可能的解決方法：

   * 設定防火牆以允許來自[我們 Web 測試代理程式的 IP 位址](./ip-addresses.md)所發出的內送要求。
   * 撰寫您自己的程式碼以定期測試您的內部伺服器。 執行程式碼作為您防火牆後方測試伺服器上的背景處理序。 您的測試處理序可以使用核心 SDK 套件中的 [TrackAvailability()](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) API，將其結果傳送至 Application Insights。 這需要測試伺服器具有 Application Insights 內嵌端點的連出存取，但這比起替代的允許連入要求是較小的安全性風險。 結果會出現在可用性 web 測試的 blade 中，不過體驗將會稍微簡化，因為可透過入口網站建立的測試。 自訂可用性測試也會以可用性結果形式出現在分析、搜尋和計量中。

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

* 如需可用性計量的警示，請在 [ **大量/群組** ] 核取方塊選項啟用時，傳送給訂用帳戶中具有擁有者、參與者或讀者角色的使用者。 實際上，「所有」__ 有權存取 Application Insights 資源訂用帳戶的使用者都在涵蓋範圍內，而且將會收到通知。 

> [!NOTE]
> 如果您目前使用 [大量/群組]**** 核取方塊選項並停用它，您將無法還原變更。

如果您需要根據使用者的角色來通知他們，請使用新警示體驗/近乎即時警示。 使用[動作群組](../platform/action-groups.md)，您可以為具有任一個參與者/擁有者/讀者角色 (不會結合來作為單一選項) 的使用者設定電子郵件通知。

## <a name="next-steps"></a>接下來的步驟

* [多步驟 web 測試](availability-multistep.md)
* [URL ping 測試](monitor-web-app-availability.md)

