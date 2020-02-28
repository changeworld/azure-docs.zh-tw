---
title: JavaScript web 應用程式的 Azure 應用程式 Insights
description: 取得頁面流覽和會話計數、web 用戶端資料、單一頁面應用程式（SPA），以及追蹤使用模式。 Detect exceptions and performance issues in JavaScript web pages.
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: 00e8cdbbd765d6baf83f64848030d08d6e712ca1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661340"
---
# <a name="application-insights-for-web-pages"></a>適用於網頁的 Application Insights

了解您的網頁或應用程式的效能和使用量。 如果您將[Application Insights](app-insights-overview.md)新增至頁面腳本，您會取得頁面載入和 ajax 呼叫、計數和瀏覽器例外狀況與 ajax 失敗的詳細資料，以及使用者和會話計數的時間。 這些項目可以依據頁面、用戶端作業系統和瀏覽器版本、地區位置和其他維度分割。 您可以對失敗計數或緩慢頁面載入設定警示。 而在 JavaScript 程式碼中插入追蹤呼叫，即可追蹤網頁應用程式的各種功能使用方式。

Application Insights 可以使用於任何網頁 - 您剛剛新增 JavaScript 的簡短片段。 如果您的 web 服務是[JAVA](java-get-started.md)或[ASP.NET](asp-net.md)，您可以使用伺服器端 Sdk 搭配用戶端 JavaScript SDK 來取得應用程式效能的端對端瞭解。

## <a name="adding-the-javascript-sdk"></a>新增 JavaScript SDK

1. 首先，您需要 Application Insights 資源。 如果您還沒有資源和檢測金鑰，請遵循[建立新的資源指示](create-new-resource.md)。
2. 從您想要傳送 JavaScript 遙測的資源複製檢測金鑰。
3. 透過下列兩個選項的其中一個，將 Application Insights JavaScript SDK 新增至您的網頁或應用程式：
    * [npm 設定](#npm-based-setup)
    * [JavaScript 程式碼片段](#snippet-based-setup)

> [!IMPORTANT]
> 僅使用一種方法將 JavaScript SDK 新增至您的應用程式。 如果您使用 NPM 安裝程式，請不要使用程式碼片段，反之亦然。

> [!NOTE]
> NPM 安裝程式會安裝 JavaScript SDK 作為您專案的相依性，啟用 IntelliSense，而程式碼片段則會在執行時間提取 SDK。 兩者都支援相同的功能。 不過，想要有更多自訂事件和設定的開發人員通常會選擇 NPM 安裝，而使用者需要快速啟用現成的 web 分析來選擇程式碼片段。

### <a name="npm-based-setup"></a>以 npm 為基礎的設定

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>以程式碼片段為基礎的設定

如果您的應用程式不使用 npm，您可以在每個頁面的頂端貼上此程式碼片段，直接使用 Application Insights 檢測您的網頁。 最好是 `<head>` 區段中的第一個腳本，讓它可以監視所有相依性的任何潛在問題。 如果您使用 Blazor 伺服器應用程式，請在檔案頂端的 `<head>` 區段中新增程式碼片段 `_Host.cshtml`。

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>將遙測傳送至 Azure 入口網站

根據預設，Application Insights JavaScript SDK 會 autocollects 一些遙測專案，這有助於判斷應用程式的健康情況和基礎使用者體驗。 其中包括：

- 應用程式中未攔截到的**例外**狀況，包括的相關資訊
    - 堆疊追蹤
    - 例外狀況詳細資料和伴隨錯誤的訊息
    - 行 & 錯誤的欄數
    - 引發錯誤的 URL
- 您的應用程式**XHR**和**提取**所提出的網路相依性要求（預設會停用提取集合）要求，包括的**相關**資訊
    - 相依性來源的 Url
    - 命令 & 用來要求相依性的方法
    - 要求的持續時間
    - 要求的結果碼和成功狀態
    - 提出要求之使用者的識別碼（如果有的話）
    - 提出要求的相互關聯內容（如果有的話）
- **使用者資訊**（例如，位置、網路、IP）
- **裝置資訊**（例如瀏覽器、OS、版本、語言、解析度、模型）
- **會話資訊**

### <a name="telemetry-initializers"></a>遙測初始化運算式
遙測初始化運算式是用來在從使用者的瀏覽器傳送之前，修改所收集遙測的內容。 它們也可以用來防止傳送特定遙測，方法是傳回 `false`。 您可以將多個遙測初始化運算式新增至您的 Application Insights 實例，並依加入它們的循序執行。

`addTelemetryInitializer` 的輸入引數是回呼，其接受[`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer)做為引數，並傳回 `boolean` 或 `void`。 如果傳回 `false`，則不會傳送遙測專案，否則會繼續進行下一個遙測初始化運算式（如果有的話），或傳送至遙測集合端點。

使用遙測初始化運算式的範例：
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```
## <a name="configuration"></a>組態
大部分設定欄位的名稱都是，讓它們可以預設為 false。 除了 `instrumentationKey`以外，所有欄位都是選擇性的。

| 名稱 | 預設 | 描述 |
|------|---------|-------------|
| instrumentationKey | null | **必要**<br>從 Azure 入口網站取得的檢測金鑰。 |
| accountId | null | 選擇性的帳戶識別碼，如果您的應用程式將使用者群組為帳戶。 不含空格、逗號、分號、等於或分隔號 |
| sessionRenewalMs | 1800000 | 如果使用者處於非使用中狀態的時間（以毫秒為單位），則會記錄會話。 預設值是 30 分鐘 |
| sessionExpirationMs | 86400000 | 如果會話已繼續此時間量（以毫秒為單位），則會記錄會話。 預設值為24小時 |
| maxBatchSizeInBytes | 10000 | 遙測批次的大小上限。 如果批次超過此限制，則會立即傳送並啟動新的批次 |
| maxBatchInterval | 15000 | 在傳送之前，批次遙測的時間長度（毫秒） |
| disableExceptionTracking | false | 若為 true，則不會實驗自動收集例外狀況。 預設值為 false。 |
| disableTelemetry | false | 若為 true，則不會收集或傳送遙測。 預設值為 false。 |
| enableDebug | false | 若為 true，則不論 SDK 記錄設定為何，**內部**的偵錯工具資料都會擲回為例外狀況，**而不**是記錄。 預設值為 false。 <br>***注意：*** 啟用此設定將會在發生內部錯誤時產生已捨棄的遙測。 這有助於快速找出您的設定或使用 SDK 的問題。 如果您不想在進行調試時遺失遙測，請考慮使用 `consoleLoggingLevel` 或 `telemetryLoggingLevel`，而不是 `enableDebug`。 |
| loggingLevelConsole | 0 | 將**內部**Application Insights 錯誤記錄到主控台。 <br>0：關閉， <br>1：只有嚴重錯誤， <br>2：所有專案（錯誤 & 警告） |
| loggingLevelTelemetry | 1 | 將**內部**Application Insights 錯誤當做遙測傳送。 <br>0：關閉， <br>1：只有嚴重錯誤， <br>2：所有專案（錯誤 & 警告） |
| diagnosticLogInterval | 10000 | 內部內部記錄佇列的輪詢間隔（以毫秒為單位） |
| samplingPercentage | 100 | 將傳送的事件百分比。 預設值為100，表示傳送所有事件。 如果您想要保留大型應用程式的資料上限，請設定此設定。 |
| autoTrackPageVisitTime | false | 若為 true，則在 pageview 上，會追蹤先前檢測的頁面的視圖時間並以遙測的形式傳送，並針對目前的 pageview 啟動新的計時器。 預設值為 false。 |
| disableAjaxTracking | false | 若為 true，則不會實驗自動收集 Ajax 呼叫。 預設值為 false。 |
| disableFetchTracking | true | 若為 true，則不會實驗自動收集提取要求。 預設值為 true |
| overridePageViewDuration | false | 若為 true，則在呼叫 trackPageView 時，trackPageView 的預設行為會變更為 [記錄] 網頁檢視持續時間間隔的結尾。 如果為 false，而且沒有提供任何自訂持續時間來 trackPageView，則會使用導覽計時 API 來計算網頁檢視效能。 預設值為 false。 |
| maxAjaxCallsPerView | 500 | 預設值 500-控制每個網頁檢視要監視的 Ajax 呼叫數目。 設定為-1 可監視頁面上所有（無限制的） Ajax 呼叫。 |
| disableDataLossAnalysis | true | 若為 false，則會在啟動時檢查尚未傳送的內部遙測寄件者緩衝區。 |
| disableCorrelationHeaders | false | 若為 false，SDK 會將兩個標頭（「要求識別碼」和「要求-內容」）新增至所有相依性要求，以便將它們與伺服器端上的對應要求相互關聯。 預設值為 false。 |
| correlationHeaderExcludedDomains |  | 停用特定網域的相互關聯標頭 |
| correlationHeaderDomains |  | 啟用特定網域的相互關聯標頭 |
| disableFlushOnBeforeUnload | false | 預設值為 false。 若為 true，onBeforeUnload 事件觸發程式時將不會呼叫 flush 方法 |
| enableSessionStorageBuffer | true | 預設值為 true。 若為 true，則包含所有未傳送遙測的緩衝區都會儲存在會話儲存體中。 在頁面載入時還原緩衝區 |
| isCookieUseDisabled | false | 預設值為 false。 若為 true，則 SDK 不會儲存或讀取 cookie 中的任何資料。|
| cookieDomain | null | 自訂 cookie 網域。 如果您想要跨子域共用 Application Insights 的 cookie，這會很有説明。 |
| isRetryDisabled | false | 預設值為 false。 若為 false，則重試206（部分成功）、408（超時）、429（太多要求）、500（內部伺服器錯誤）、503（服務無法使用）和0（只有在偵測到時才會離線） |
| isStorageUseDisabled | false | 若為 true，則 SDK 不會儲存或讀取本機和會話儲存體中的任何資料。 預設值為 false。 |
| isBeaconApiDisabled | true | 若為 false，SDK 會使用指標[API](https://www.w3.org/TR/beacon)傳送所有遙測 |
| onunloadDisableBeacon | false | 預設值為 false。 當索引標籤關閉時，SDK 會使用指標[API](https://www.w3.org/TR/beacon)傳送所有剩餘的遙測 |
| sdkExtension | null | 設定 sdk 延伸模組名稱。 只允許字母字元。 擴充功能名稱會新增為 ' sdkVersion ' 標記的前置詞（例如 ' ext_javascript： 2.0.0 '）。 預設值為 null。 |
| isBrowserLinkTrackingEnabled | false | 預設值為 false。 若為 true，SDK 將會追蹤所有[瀏覽器連結](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink)要求。 |
| appId | null | AppId 會用於用戶端上發生的 AJAX 相依性與伺服器端要求之間的相互關聯。 啟用「指標 API」時，無法自動使用它，但可在設定中手動設定。 預設值為 null |
| enableCorsCorrelation | false | 若為 true，SDK 會將兩個標頭（「要求識別碼」和「要求-內容」）新增至所有 CORS 要求，以將外寄 AJAX 相依性與伺服器端上的對應要求相互關聯。 預設值為 false。 |
| namePrefix | 未定義 | 選擇性值，將會作為 localStorage 和 cookie 名稱的名稱後置詞使用。
| enableAutoRouteTracking | false | 自動追蹤單一頁面應用程式（SPA）中的路由變更。 若為 true，則每個路由變更都會將新的 Pageview 傳送至 Application Insights。 雜湊路由變更（`example.com/foo#bar`）也會記錄為新的頁面流覽。
| enableRequestHeaderTracking | false | 若為 true，則會追蹤 AJAX & 提取要求標頭，預設值為 false。
| enableResponseHeaderTracking | false | 若為 true，則會追蹤 AJAX & 提取要求的回應標頭，預設值為 false。
| distributedTracingMode | `DistributedTracingModes.AI` | 設定分散式追蹤模式。 如果已設定 AI_AND_W3C 模式或 W3C 模式，則會產生 W3C 追蹤內容標頭（traceparent/tracestate），並將其包含在所有傳出的要求中。 AI_AND_W3C 是針對與任何舊版 Application Insights 檢測服務的回溯相容性而提供的。

## <a name="single-page-applications"></a>單一頁面應用程式

根據預設，此 SDK**不**會處理在單一頁面應用程式中發生的狀態型路由變更。 若要為您的單一頁面應用程式啟用自動路由變更追蹤，您可以將 `enableAutoRouteTracking: true` 新增至您的安裝程式設定。

目前，我們提供了個別的[回應外掛程式](#react-extensions)，您可以使用這個 SDK 來初始化。 它也會為您完成路由變更追蹤，並收集[其他回應特定的遙測](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)。

## <a name="react-extensions"></a>回應延伸模組

| 擴充功能 |
|---------------|
| [React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [React Native](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md) \(英文\)|

## <a name="explore-browserclient-side-data"></a>探索瀏覽器/用戶端資料

瀏覽器/用戶端資料可透過前往 [**計量**] 來查看，並新增您感興趣的個別計量： 

![](./media/javascript/page-view-load-time.png)

您也可以透過入口網站中的瀏覽器體驗，從 JavaScript SDK 來查看您的資料。

選取 [**瀏覽器**]，然後選擇 [**失敗**] 或 [**效能**]。

![](./media/javascript/browser.png)

### <a name="performance"></a>效能 

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>相依性

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>分析 

若要查詢 JavaScript SDK 所收集的遙測資料，請選取 [**記錄（分析）** ] 按鈕中的 [查看]。 藉由新增 `client_Type == "Browser"`的 `where` 語句，您只會看到來自 JavaScript SDK 的資料，而其他 Sdk 所收集的任何伺服器端遙測也會被排除。
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>來源對應支援

您可以在 Azure 入口網站中 unminified 例外狀況遙測的縮減呼叫堆疊。 [例外狀況詳細資料] 面板上的所有現有整合都會使用新 unminified 的呼叫堆疊。 拖放來源對應 unminifying 支援所有現有和未來的 JS Sdk （+ node.js），因此您不需要升級 SDK 版本。 若要查看您的 unminified 呼叫堆疊，
1. 在 Azure 入口網站中選取例外狀況遙測專案，以查看其「端對端交易詳細資料」
2. 識別與此呼叫堆疊對應的來源對應。 來源對應必須符合堆疊框架的原始程式檔，但尾碼為 `.map`
3. 將來源對應拖放到 Azure 入口網站的呼叫堆疊 ![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web 基本

若要取得輕量經驗，您可以改為安裝基本版本的 Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
此版本隨附最少的特性和功能，並視您的需要建立它。 例如，它不會執行任何 autocollection （未攔截的例外狀況、AJAX 等等）。 此版本不會包含傳送特定遙測類型的 Api （例如 `trackTrace`、`trackException`等），因此您必須提供自己的包裝函式。 唯一可用的 API 是 `track`。 [範例](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html)位於此處。

## <a name="examples"></a>範例

如需可執行檔範例，請參閱[Application Insights JAVASCRIPT SDK 範例](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>從舊版本的 Application Insights 升級

SDK V2 版本中的重大變更：
- 為了讓 API 簽章變得更好，某些 API 呼叫（例如 trackPageView）已更新。 不支援在 IE8 或較低版本的瀏覽器中執行。
- 遙測信封因數據架構更新而有欄位名稱和結構變更。
- 已將 `context.operation` 移至 `context.telemetryTrace`。 某些欄位也已變更（`operation.id` --> `telemetryTrace.traceID`）
  - 如果您想要手動重新整理目前的 pageview 識別碼（例如，在 SPA 應用程式中），可以使用 `appInsights.properties.context.telemetryTrace.traceID = Util.newId()` 來完成此作業

如果您使用目前的 application insights 生產 SDK （1.0.20），而且想要查看新的 SDK 是否在執行時間中運作，請根據您目前的 SDK 載入案例更新 URL。

- 透過 CDN 案例下載：更新您目前用來指向下列 URL 的程式碼片段：
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- npm 案例：呼叫 `downloadAndSetup` 以從 CDN 下載完整的 ApplicationInsights 腳本，並使用檢測金鑰將它初始化：

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

在內部環境中測試，以確認監視遙測是否如預期般運作。 如果一切正常，請將您的 API 簽章適當地更新為 SDK V2 版本，並在生產環境中部署。

## <a name="sdk-performanceoverhead"></a>SDK 效能/額外負荷

在只有 25 KB 的 gzipped，而且只需要約15毫秒的時間初始化，Application Insights 在您的網站中增加 loadtime 的數量。 藉由使用程式碼片段，會快速載入程式庫的最小元件。 在此同時，完整的腳本會在背景下載。

從 CDN 下載腳本時，會將頁面的所有追蹤排入佇列。 下載的腳本完成非同步初始化之後，所有已排入佇列的事件都會被追蹤。 因此，您不會在頁面的整個生命週期期間遺失任何遙測。 這個安裝程式會為您的頁面提供無縫分析系統，讓您的使用者看不到。

> 摘要：
> - **25 KB** gzipped
> - **15 毫秒**的整體初始化時間
> - 在頁面生命週期期間遺漏了**零**追蹤

## <a name="browser-support"></a>瀏覽器支援

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Chrome 最新✔ |  Firefox 最新✔ | IE 9 + & Edge ✔ | Opera 最新✔ | Safari 最新✔ |

## <a name="open-source-sdk"></a>開放原始碼 SDK

Application Insights JavaScript SDK 是開放原始碼，可供您查看原始程式碼或參與專案，請造訪官方的[GitHub 存放庫](https://github.com/Microsoft/ApplicationInsights-JS)。

## <a name="next"></a> 後續步驟
* [追蹤流量](usage-overview.md)
* [自訂事件和計量](api-custom-events-metrics.md)
* [Build-measure-learn](usage-overview.md)
