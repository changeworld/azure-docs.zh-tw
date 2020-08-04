---
title: JavaScript web 應用程式的 Azure 應用程式 Insights
description: 取得頁面流覽和會話計數、web 用戶端資料、單一頁面應用程式（SPA），以及追蹤使用模式。 Detect exceptions and performance issues in JavaScript web pages.
ms.topic: conceptual
ms.date: 09/20/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: e0545660cbca68d41bc24b7266496b7912d408bc
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87531314"
---
# <a name="application-insights-for-web-pages"></a>適用於網頁的 Application Insights

了解您的網頁或應用程式的效能和使用量。 如果您將[Application Insights](app-insights-overview.md)新增至頁面腳本，您會取得頁面載入和 ajax 呼叫、計數和瀏覽器例外狀況與 ajax 失敗的詳細資料，以及使用者和會話計數的時間。 這些項目可以依據頁面、用戶端作業系統和瀏覽器版本、地區位置和其他維度分割。 您可以對失敗計數或緩慢頁面載入設定警示。 而在 JavaScript 程式碼中插入追蹤呼叫，即可追蹤網頁應用程式的各種功能使用方式。

Application Insights 可以使用於任何網頁 - 您剛剛新增 JavaScript 的簡短片段。 如果您的 web 服務是[JAVA](java-get-started.md)或[ASP.NET](asp-net.md)，您可以使用伺服器端 Sdk 搭配用戶端 JavaScript SDK 來取得應用程式效能的端對端瞭解。

## <a name="adding-the-javascript-sdk"></a>新增 JavaScript SDK

1. 首先，您需要 Application Insights 資源。 如果您還沒有資源和檢測金鑰，請遵循[建立新的資源指示](create-new-resource.md)。
2. 針對您想要在其中傳送 JavaScript 遙測的資源（來自步驟1），複製_檢測金鑰_（也稱為 "iKey"）。您會將它新增至 `instrumentationKey` Application Insights JAVASCRIPT SDK 的設定。
3. 透過下列兩個選項的其中一個，將 Application Insights JavaScript SDK 新增至您的網頁或應用程式：
    * [npm 設定](#npm-based-setup)
    * [JavaScript 程式碼片段](#snippet-based-setup)

> [!IMPORTANT]
> 僅使用一種方法將 JavaScript SDK 新增至您的應用程式。 如果您使用 NPM 安裝程式，請不要使用程式碼片段，反之亦然。

> [!NOTE]
> NPM 安裝程式會安裝 JavaScript SDK 作為您專案的相依性，啟用 IntelliSense，而程式碼片段則會在執行時間提取 SDK。 兩者都支援相同的功能。 不過，想要有更多自訂事件和設定的開發人員通常會選擇 NPM 安裝，而使用者需要快速啟用現成的 web 分析來選擇程式碼片段。

### <a name="npm-based-setup"></a>以 npm 為基礎的設定

透過 NPM 安裝。

```sh
npm i --save @microsoft/applicationinsights-web
```

> [!Note]
> **Typings 隨附于此封裝**，因此您**不**需要安裝個別的 Typings 套件。
    
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

如果您的應用程式不使用 npm，您可以在每個頁面的頂端貼上此程式碼片段，直接使用 Application Insights 檢測您的網頁。 最好是區段中的第一個腳本， `<head>` 如此一來，它就可以監視所有相依性的潛在問題，並選擇性地監控任何 JavaScript 錯誤。 如果您使用 Blazor 伺服器應用程式，請在區段中的檔案頂端新增程式碼片段 `_Host.cshtml` `<head>` 。

為了協助追蹤您應用程式所使用的程式碼片段版本，從版本2.5.5 開始，網頁檢視事件會包含新的標記 "ai. internal. 程式碼片段"，其中將包含已識別的程式碼片段版本。

目前的程式碼片段（如下所列）將識別為版本 "3"。

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,u="script",k="instrumentationKey",D="ingestionendpoint",C="disableExceptionTracking",E="ai.device.",I="toLowerCase",b="crossOrigin",w="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"4",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[I](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,p,l,u;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][I]()]=i[1])}if(!e[D]){var r=e.endpointsuffix,o=r?e.location:null;e[D]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[k]||d[k]||"",p=s[D],l=p?p+"/v2/track":config.endpointUrl,(u=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=l,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),u.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,l)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:w,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(w,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(u,l))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(u);n.src=h;var e=y[b];return!e&&""!==e||"undefined"==n[b]||(n[b]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(u)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[C]&&!0!==s[C]){method="onerror",t(["_"+method]);var c=T[method];T[method]=function(e,t,n,a,i){var r=c&&c(e,t,n,a,i);return!0!==r&&m["_"+method]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);(T[t]=n).queue&&0===n.queue.length&&n.trackPageView({})}(window,document,{
src: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js", // The SDK URL Source
//name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
//ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
//useXhr: 1, // Use XHR instead of fetch to report failures (if available),
//crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag 
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    /* ...Other Configuration Options... */
}});
</script>
```

> [!NOTE]
> 為了方便閱讀並減少可能的 JavaScript 錯誤，所有可能的設定選項都會列在上述程式碼片段中的新行上，如果您不想要變更批註行的值，可以將它移除。


#### <a name="reporting-script-load-failures"></a>報告腳本載入失敗

此版本的程式碼片段會偵測並報告失敗，從 CDN 載入 SDK 作為 Azure 監視器入口網站的例外狀況（在 [失敗] &gt; 例外狀況 &gt; 瀏覽器底下），此例外狀況會提供此類型失敗的可見度，讓您知道應用程式不會如預期般報告遙測（或其他例外狀況）。 此信號是瞭解您已遺失遙測的重要度量，因為 SDK 並未載入或初始化，而這可能會導致：
- 在報告中，使用者如何使用（或嘗試使用）您的網站;
- 您的終端使用者如何使用您的網站缺少遙測;
- 缺少 JavaScript 錯誤，可能會封鎖您的終端使用者，使其無法成功使用您的網站。

如需此例外狀況的詳細資訊，請參閱[SDK 載入失敗](javascript-sdk-load-failure.md)疑難排解頁面。

將此錯誤報表為入口網站的例外狀況，並不會使用 application insights 設定中的設定選項 ```disableExceptionTracking``` ，因此如果發生此失敗，它一律會由程式碼片段報告，即使已停用 windows onerror 支援也一樣。

SDK 載入失敗的報告在 IE 8 （或更少）上特別不受支援。 這有助於減少程式碼片段的縮減大小，方法是假設大部分的環境不是專門的 IE 8 或更少。 如果您有此需求，而且想要收到這些例外狀況，您將需要包含提取 poly 填滿或建立使用的程式碼片段版本（ ```XDomainRequest``` 而不是 ```XMLHttpRequest``` ），建議您使用[提供的程式碼片段原始程式碼](https://github.com/microsoft/ApplicationInsights-JS/blob/master/AISKU/snippet/snippet.js)做為起點。

> [!NOTE]
> 如果您使用的是舊版程式碼片段，則強烈建議您將更新為最新版本，以便收到先前未報告的問題。

#### <a name="snippet-configuration-options"></a>程式碼片段設定選項

所有設定選項現在都已移至腳本結尾，以協助避免不小心引進不會導致 SDK 無法載入的 JavaScript 錯誤，同時也會停用失敗的報告。

如果您不想要覆寫列為 [選用] 之專案的預設值，則每個設定選項都會顯示在上面，而您可以移除該行，將所傳回頁面的產生大小降到最低。

可用的設定選項為 

| 名稱 | 類型 | 說明
|------|------|----------------
| src | 字串 **[必要]** | 要從中載入 SDK 的完整 URL。 這個值會用於動態加入之腳本/標記的 "src" 屬性 &lt; &gt; 。 您可以使用公用 CDN 位置，或您自己的私人託管。
| NAME | 字串 *[選擇性]* | 已初始化 SDK 的全域名稱，預設為 appInsights。 因此， ```window.appInsights``` 會參考初始化的實例。 注意：如果您提供名稱值或先前的實例會被指派（透過全域名稱 appInsightsSDK），則此名稱值也會在全域命名空間中定義為 ```window.appInsightsSDK=<name value>``` ，這是 SDK 初始化程式碼所需的，以確保它會初始化並更新正確的程式碼片段基本架構和 proxy 方法。
| 個 | 以毫秒為單位的數位 *[選擇性]* | 定義要在嘗試載入 SDK 之前等待的載入延遲。 預設值為0毫秒，任何負值會立即將腳本標記新增至頁面的 &lt; 前端 &gt; 區域，接著會封鎖頁面載入事件直到載入腳本（或失敗）。
| useXhr | 布林值 *[選擇性]* | 此設定僅用於報告 SDK 載入失敗。 報告會先嘗試使用 fetch （）（如果有的話），然後回到 XHR，將此值設定為 true 只會略過提取檢查。 只有當您的應用程式在提取無法傳送失敗事件的環境中使用時，才需要使用此值。
| crossOrigin | 字串 *[選擇性]* | 藉由包含此設定，新增來下載 SDK 的腳本標記會包含具有此字串值的 crossOrigin 屬性。 如果未定義（預設值），則不會加入任何 crossOrigin 屬性。 建議的值未定義（預設值）;"";或「匿名」（針對所有有效值，請參閱[HTML 屬性： crossorigin](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin)檔）
| cfg | 物件 **[必要]** | 初始化期間傳遞至 Application Insights SDK 的設定。

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
- **裝置資訊**（例如，瀏覽器、OS、版本、語言、型號）
- **會話資訊**

### <a name="telemetry-initializers"></a>遙測初始化運算式
遙測初始化運算式是用來在從使用者的瀏覽器傳送之前，修改所收集遙測的內容。 您也可以藉由傳回，使用它們來防止傳送特定遙測 `false` 。 您可以將多個遙測初始化運算式新增至您的 Application Insights 實例，並依加入它們的循序執行。

的輸入引數 `addTelemetryInitializer` 是回呼，其接受 [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) 做為引數，並傳回 `boolean` 或 `void` 。 如果傳回 `false` ，則不會傳送遙測專案，否則會繼續進行下一個遙測初始化運算式（如果有的話），或傳送至遙測集合端點。

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
大部分設定欄位的名稱都是，讓它們可以預設為 false。 除了以外，所有欄位都是選擇性的 `instrumentationKey` 。

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
| enableDebug | false | 若為 true，則不論 SDK 記錄設定為何，**內部**的偵錯工具資料都會擲回為例外狀況，**而不**是記錄。 預設值為 false。 <br>***注意：*** 啟用此設定將會在發生內部錯誤時產生已捨棄的遙測。 這有助於快速找出您的設定或使用 SDK 的問題。 如果您不想在進行調試時遺失遙測，請考慮使用 `consoleLoggingLevel` 或， `telemetryLoggingLevel` 而不是 `enableDebug` 。 |
| loggingLevelConsole | 0 | 將**內部**Application Insights 錯誤記錄到主控台。 <br>0：關閉， <br>1：只有嚴重錯誤， <br>2：所有專案（錯誤 & 警告） |
| loggingLevelTelemetry | 1 | 將**內部**Application Insights 錯誤當做遙測傳送。 <br>0：關閉， <br>1：只有嚴重錯誤， <br>2：所有專案（錯誤 & 警告） |
| diagnosticLogInterval | 10000 | 內部內部記錄佇列的輪詢間隔（以毫秒為單位） |
| samplingPercentage | 100 | 將傳送的事件百分比。 預設值為100，表示傳送所有事件。 如果您想要保留大型應用程式的資料上限，請設定此設定。 |
| autoTrackPageVisitTime | false | 若為 true，則在 pageview 上，會追蹤先前檢測的頁面的視圖時間並以遙測的形式傳送，並針對目前的 pageview 啟動新的計時器。 預設值為 false。 |
| disableAjaxTracking | false | 若為 true，則不會實驗自動收集 Ajax 呼叫。 預設值為 false。 |
| disableFetchTracking | true | 若為 true，則不會實驗自動收集提取要求。 預設值為 true。 |
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
| sdkExtension | null | 設定 sdk 延伸模組名稱。 只允許字母字元。 擴充功能名稱會新增為 ' sdkVersion ' 標記的前置詞（例如 ' ext_javascript： 2.0.0 '）。 預設為 Null。 |
| isBrowserLinkTrackingEnabled | false | 預設值為 false。 若為 true，SDK 將會追蹤所有[瀏覽器連結](/aspnet/core/client-side/using-browserlink)要求。 |
| appId | null | AppId 會用於用戶端上發生的 AJAX 相依性與伺服器端要求之間的相互關聯。 啟用「指標 API」時，無法自動使用它，但可在設定中手動設定。 預設值為 null |
| enableCorsCorrelation | false | 若為 true，SDK 會將兩個標頭（「要求識別碼」和「要求-內容」）新增至所有 CORS 要求，以將外寄 AJAX 相依性與伺服器端上的對應要求相互關聯。 預設值為 false。 |
| namePrefix | 未定義 | 選擇性值，將會作為 localStorage 和 cookie 名稱的名稱後置詞使用。
| enableAutoRouteTracking | false | 自動追蹤單一頁面應用程式（SPA）中的路由變更。 若為 true，則每個路由變更都會將新的 Pageview 傳送至 Application Insights。 雜湊路由變更（ `example.com/foo#bar` ）也會記錄為新的頁面流覽。
| enableRequestHeaderTracking | false | 若為 true，則會追蹤 AJAX & 提取要求標頭，預設值為 false。
| enableResponseHeaderTracking | false | 若為 true，則會追蹤 AJAX & 提取要求的回應標頭，預設值為 false。
| distributedTracingMode | `DistributedTracingModes.AI` | 設定分散式追蹤模式。 如果已設定 AI_AND_W3C 模式或 W3C 模式，則會產生 W3C 追蹤內容標頭（traceparent/tracestate），並將其包含在所有傳出的要求中。 AI_AND_W3C 是針對與任何舊版 Application Insights 檢測服務的回溯相容性而提供的。 請參閱[這裡](./correlation.md#enable-w3c-distributed-tracing-support-for-web-apps)的範例。
| enableAjaxErrorStatusText | false | 預設值為 false。 若為 true，則在失敗的 AJAX 要求上包含相依性事件中的回應錯誤資料文字。
| enableAjaxPerfTracking | false | 預設值為 false。 旗標，可讓您查閱和包含額外的瀏覽器視窗。報告的 ajax （XHR 和 fetch）報告的計量中的效能計時。
| maxAjaxPerfLookupAttempts | 3 | 預設值為3。 要尋找視窗的最大次數。效能時間（如果有的話），因為並非所有瀏覽器都填入視窗，所以這是必要的。在報告 XHR 要求的結尾和提取要求之後，就會在其完成後新增這項功能。
| ajaxPerfLookupDelay | 25 | 預設為25毫秒。 重新嘗試尋找 windows 之前等待的時間量。 ajax 要求的效能時間（以毫秒為單位），並會直接傳遞到 setTimeout （）。
| enableUnhandledPromiseRejectionTracking | false | 若為 true，未處理的承諾拒絕將會實驗自動收集，並回報為 JavaScript 錯誤。 當 disableExceptionTracking 為 true （不追蹤例外狀況）時，將會忽略設定值，而且不會回報未處理的承諾拒絕。

## <a name="single-page-applications"></a>單一頁面應用程式

根據預設，此 SDK**不**會處理在單一頁面應用程式中發生的狀態型路由變更。 若要為您的單一頁面應用程式啟用自動路由變更追蹤，您可以將新增 `enableAutoRouteTracking: true` 到您的安裝程式設定。

目前，我們提供了個別的[回應外掛程式](#react-extensions)，您可以使用這個 SDK 來初始化。 它也會為您完成路由變更追蹤，並收集[其他回應特定的遙測](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)。

> [!NOTE]
> `enableAutoRouteTracking: true`只有在您**未**使用「回應」外掛程式時，才使用。 這兩者都能夠在路由變更時傳送新的 PageViews。 如果兩者都已啟用，可能會傳送重複的 PageViews。

## <a name="configuration-autotrackpagevisittime"></a>設定： autoTrackPageVisitTime

藉由設定 `autoTrackPageVisitTime: true` ，會追蹤使用者在每個頁面上花費的時間。 在每個新的 PageView 上，使用者花費在*上一頁*的持續時間會當做名為的[自訂](../platform/metrics-custom-overview.md)計量傳送 `PageVisitTime` 。 此自訂計量可在[計量瀏覽器](../platform/metrics-getting-started.md)中看到為「記錄式計量」。

## <a name="react-extensions"></a>回應延伸模組

| 延伸模組 |
|---------------|
| [React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [React Native](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md) \(英文\)|

## <a name="explore-browserclient-side-data"></a>探索瀏覽器/用戶端資料

瀏覽器/用戶端資料可透過前往 [**計量**] 來查看，並新增您感興趣的個別計量：

![Application Insights 中 [計量] 頁面的螢幕擷取畫面，其中顯示 web 應用程式的度量資料圖形顯示。](./media/javascript/page-view-load-time.png)

您也可以透過入口網站中的瀏覽器體驗，從 JavaScript SDK 來查看您的資料。

選取 [**瀏覽器**]，然後選擇 [**失敗**] 或 [**效能**]。

![[瀏覽器] 頁面的螢幕擷取畫面，其中 Application Insights 顯示如何將瀏覽器失敗或瀏覽器效能新增至您可以為 web 應用程式查看的度量。](./media/javascript/browser.png)

### <a name="performance"></a>效能

![Application Insights 中 [效能] 頁面的螢幕擷取畫面，其中顯示 web 應用程式之作業計量的圖形顯示。](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>相依性

![Application Insights 中 [效能] 頁面的螢幕擷取畫面，其中顯示 web 應用程式之相依性計量的圖形顯示。](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>分析

若要查詢 JavaScript SDK 所收集的遙測資料，請選取 [**記錄（分析）** ] 按鈕中的 [查看]。 藉由新增的 `where` 語句 `client_Type == "Browser"` ，您只會看到來自 JavaScript SDK 的資料，而其他 sdk 所收集的任何伺服器端遙測也會被排除。
 
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

您可以在 Azure 入口網站中 unminified 例外狀況遙測的縮減呼叫堆疊。 [例外狀況詳細資料] 面板上的所有現有整合都會使用新 unminified 的呼叫堆疊。

#### <a name="link-to-blob-storage-account"></a>連結至 Blob 儲存體帳戶

您可以將 Application Insights 資源連結至您自己的 Azure Blob 儲存體容器，以自動 unminify 呼叫堆疊。 若要開始使用，請參閱[自動來源對應支援](./source-map-support.md)。

### <a name="drag-and-drop"></a>拖放

1. 在 [Azure 入口網站中選取例外狀況遙測專案，以查看其「端對端交易詳細資料」
2. 識別與此呼叫堆疊對應的來源對應。 來源對應必須符合堆疊框架的原始程式檔，但尾碼為`.map`
3. 將來源對應拖放到 Azure 入口網站動畫影像中的呼叫堆疊，示範 ![ 如何從組建資料夾將來源對應檔案拖放到 Azure 入口網站的 [呼叫堆疊] 視窗中。](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web 基本

若要取得輕量經驗，您可以改為安裝基本版本的 Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
此版本隨附最少的特性和功能，並視您的需要建立它。 例如，它不會執行任何 autocollection （未攔截的例外狀況、AJAX 等等）。 此版本不包含傳送特定遙測類型的 Api，例如 `trackTrace` 、 `trackException` 等等，因此您必須提供自己的包裝函式。 唯一可用的 API 是 `track` 。 [範例](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html)位於此處。

## <a name="examples"></a>範例

如需可執行檔範例，請參閱[Application Insights JAVASCRIPT SDK 範例](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>從舊版本的 Application Insights 升級

SDK V2 版本中的重大變更：
- 為了允許更好的 API 簽章，某些 API 呼叫（例如 trackPageView 和 trackException）已更新。 不支援在 Internet Explorer 8 和舊版的瀏覽器中執行。
- 遙測信封因數據架構更新而有欄位名稱和結構變更。
- 已移 `context.operation` 至 `context.telemetryTrace` 。 有些欄位也已變更（ `operation.id`  -->  `telemetryTrace.traceID` ）。
  - 若要手動重新整理目前的 pageview 識別碼（例如，在 SPA 應用程式中），請使用 `appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()` 。
    > [!NOTE]
    > 若要保留唯一的追蹤識別碼（您先前使用的位置 `Util.newId()` ），現在請使用 `Util.generateW3CId()` 。 最後最後是作業識別碼。

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

在只有 36 KB 的 gzipped，而且只需要約15毫秒的時間初始化，Application Insights 在您的網站中增加了可忽略的 loadtime 數量。 藉由使用程式碼片段，會快速載入程式庫的最小元件。 在此同時，完整的腳本會在背景下載。

從 CDN 下載腳本時，會將頁面的所有追蹤排入佇列。 下載的腳本完成非同步初始化之後，所有已排入佇列的事件都會被追蹤。 因此，您不會在頁面的整個生命週期期間遺失任何遙測。 這個安裝程式會為您的頁面提供無縫分析系統，讓您的使用者看不到。

> 摘要：
> - ![npm 版本](https://badge.fury.io/js/%40microsoft%2Fapplicationinsights-web.svg)
> - ![gzip 壓縮大小](https://img.badgesize.io/https://js.monitor.azure.com/scripts/b/ai.2.min.js.svg?compression=gzip)
> - **15 毫秒**的整體初始化時間
> - 在頁面生命週期期間遺漏了**零**追蹤

## <a name="browser-support"></a>瀏覽器支援

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Chrome 最新✔ |  Firefox 最新✔ | IE 9 + & Edge ✔<br>IE 8 相容 | Opera 最新✔ | Safari 最新✔ |

## <a name="es3ie8-compatibility"></a>以及 ES3/IE8 相容性

身為 SDK，有許多使用者無法控制其客戶所使用的瀏覽器。 因此，我們需要確保此 SDK 會繼續「工作」，而且不會在舊版瀏覽器載入時中斷 JS 的執行。 雖然不支援 IE8 和較舊的層代（以及 ES3）瀏覽器，但有許多大型客戶/使用者會繼續要求頁面「工作」，而且會注意到他們可能會或無法控制使用者選擇使用的瀏覽器。

這並不表示我們只會支援最常見的功能集，只是我們需要維護以及 ES3 的程式碼相容性，而且加入新功能時，必須以不會中斷以及 ES3 JavaScript 剖析並新增為選擇性功能的方式新增。

[如需 IE8 支援的完整詳細資料，請參閱 GitHub](https://github.com/Microsoft/ApplicationInsights-JS#es3ie8-compatibility)

## <a name="open-source-sdk"></a>開放原始碼 SDK

Application Insights JavaScript SDK 是開放原始碼，可供您查看原始程式碼或參與專案，請造訪官方的[GitHub 存放庫](https://github.com/Microsoft/ApplicationInsights-JS)。 

如需最新的更新和錯誤修正，[請參閱版本](./release-notes.md)資訊。

## <a name="next-steps"></a><a name="next"></a> 後續步驟
* [追蹤流量](usage-overview.md)
* [自訂事件和計量](api-custom-events-metrics.md)
* [Build-measure-learn](usage-overview.md)
* [針對 SDK 載入失敗進行疑難排解](javascript-sdk-load-failure.md)
