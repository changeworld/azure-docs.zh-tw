---
title: JavaScript web 應用程式的 Azure 應用程式見解
description: 取得頁面流覽和會話計數、web 用戶端資料、單一頁面應用程式 (SPA) ，以及追蹤使用模式。 Detect exceptions and performance issues in JavaScript web pages.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js
ms.openlocfilehash: 6678c662c4646a8181b1617ccddf9b8718c957bf
ms.sourcegitcommit: 89c0482c16bfec316a79caa3667c256ee40b163f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97858547"
---
# <a name="application-insights-for-web-pages"></a>適用於網頁的 Application Insights

了解您的網頁或應用程式的效能和使用量。 如果您將 [Application Insights](app-insights-overview.md) 新增至頁面腳本，您會取得頁面載入和 ajax 呼叫、計數和瀏覽器例外狀況和 ajax 失敗的詳細資料，以及使用者和會話計數的時間。 這些項目可以依據頁面、用戶端作業系統和瀏覽器版本、地區位置和其他維度分割。 您可以對失敗計數或緩慢頁面載入設定警示。 而在 JavaScript 程式碼中插入追蹤呼叫，即可追蹤網頁應用程式的各種功能使用方式。

Application Insights 可以使用於任何網頁 - 您剛剛新增 JavaScript 的簡短片段。 如果您的 web 服務是 [JAVA](java-get-started.md) 或 [ASP.NET](asp-net.md)，您可以使用伺服器端 Sdk 搭配用戶端 JavaScript SDK，以端對端瞭解應用程式的效能。

## <a name="adding-the-javascript-sdk"></a>新增 JavaScript SDK

> [!IMPORTANT]
> 新的 Azure 區域 **需要** 使用連接字串，而不是檢測金鑰。 [連接字串](./sdk-connection-string.md?tabs=js) 會識別您想要與遙測資料相關聯的資源。 它也可讓您修改您的資源將用來做為遙測目的地的端點。 您必須複製連接字串，並將它加入應用程式的程式碼或加入環境變數。

1. 首先，您需要 Application Insights 資源。 如果您還沒有資源和檢測金鑰，請遵循 [建立新的資源指示](create-new-resource.md)。
2. 將 _檢測金鑰_ （ (也稱為 "iKey"）複製到您要從中傳送 JavaScript 遙測 (之資源的 ) 或 [連接字串](#connection-string-setup) 。 ) 您會將它新增至 `instrumentationKey` `connectionString` Application Insights JavaScript SDK 的或設定。
3. 透過下列兩個選項的其中一種，將 Application Insights JavaScript SDK 新增至您的網頁或應用程式：
    * [npm 設定](#npm-based-setup)
    * [JavaScript 程式碼片段](#snippet-based-setup)

> [!IMPORTANT]
> 只使用一個方法將 JavaScript SDK 新增至您的應用程式。 如果您使用 NPM 安裝程式，請勿使用程式碼片段，反之亦然。

> [!NOTE]
> NPM 安裝程式會將 JavaScript SDK 安裝為您專案的相依性，並啟用 IntelliSense，而程式碼片段會在執行時間提取 SDK。 兩者都支援相同的功能。 不過，想要更多自訂事件和設定的開發人員通常會選擇 NPM 設定，而使用者則需要快速啟用現成的 web 分析，以選擇是否為程式碼片段。

### <a name="npm-based-setup"></a>以 npm 為基礎的安裝程式

透過 NPM 安裝。

```sh
npm i --save @microsoft/applicationinsights-web
```

> [!Note]
> **Typings 隨附于此套件** 中，因此您 **不** 需要安裝個別的 Typings 套件。
    
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

如果您的應用程式不使用 npm，您可以在每個頁面的頂端貼上此程式碼片段，直接使用 Application Insights 檢測您的網頁。 建議您最好是區段中的第一個腳本， `<head>` 讓它可以監視所有相依性的任何潛在問題，並選擇性地監視任何 JavaScript 錯誤。 如果您使用 Blazor Server 應用程式，請在該區段的檔案頂端新增程式碼片段 `_Host.cshtml` `<head>` 。

為了協助追蹤您的應用程式所使用的程式碼片段版本，從版本2.5.5 開始，網頁檢視事件將包含新的標記 ""，其中將包含所識別的程式碼片段版本。

下列)  (的最新程式碼片段會識別為版本 "3"。

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
> 為了方便閱讀，並減少可能的 JavaScript 錯誤，如果您不想變更批註行的值，則可能會在上述程式碼片段的新行中列出所有可能的設定選項。


#### <a name="reporting-script-load-failures"></a>報告腳本載入失敗

這一版的程式碼片段會偵測到將 SDK 從 CDN 載入 Azure 監視器入口網站 (例外狀況瀏覽器) 下的例外狀況 &gt; &gt; ，此例外狀況可讓您知道您的應用程式未如預期般報告遙測 (或其他) 例外狀況。 此信號是瞭解您已遺失遙測的重要度量，因為 SDK 沒有載入或初始化，可能會導致：
- 使用者如何使用 (或嘗試使用您的網站) 的報告;
- 缺少終端使用者如何使用您的網站的遙測;
- 缺少可能封鎖您的終端使用者成功使用您的網站的 JavaScript 錯誤。

如需此例外狀況的詳細資訊，請參閱 [SDK 載入失敗](javascript-sdk-load-failure.md) 疑難排解頁面。

此失敗的回報為入口網站的例外狀況，並不會使用 application insights 設定中的設定選項 ```disableExceptionTracking``` ，因此，如果發生此失敗，則即使在視窗中停用 onerror 支援，也一律會由程式碼片段回報。

IE 8 (或較少的) 不支援報告 SDK 載入失敗。 這有助於減少程式碼片段的縮減大小，方法是假設大部分的環境不是專門的 IE 8 或更少。 如果您有這項需求，而且您想要接收這些例外狀況，您必須包含 fetch poly 填滿或建立使用的程式碼片段版本（ ```XDomainRequest``` 而不是 ```XMLHttpRequest``` ），建議您使用提供的 [程式碼片段原始程式碼](https://github.com/microsoft/ApplicationInsights-JS/blob/master/AISKU/snippet/snippet.js) 做為起始點。

> [!NOTE]
> 如果您使用的是舊版程式碼片段，強烈建議您更新至最新版本，如此您將會收到先前未報告的問題。

#### <a name="snippet-configuration-options"></a>程式碼片段設定選項

所有設定選項現在都已移至腳本的結尾，以協助避免意外引進不會導致 SDK 無法載入的 JavaScript 錯誤，同時也會停用失敗的報告。

每個設定選項都會在新行上顯示，如果您不想要覆寫列為 [選擇性] 的專案預設值，您可以移除該行以將所傳回頁面的結果大小降到最低。

可用的設定選項為

| 名稱 | 類型 | 描述
|------|------|----------------
| src | 字串 **[必要]** | 要從何處載入 SDK 的完整 URL。 此值可用於動態新增之腳本/標記的 "src" 屬性 &lt; &gt; 。 您可以使用公用 CDN 位置或您自己的私人託管位置。
| NAME | 字串 *[選用]* | 已初始化之 SDK 的全域名稱，預設為 `appInsights` 。 因此 ```window.appInsights``` 將會是已初始化之實例的參考。 注意：如果您提供名稱值或先前的實例透過全域名稱 appInsightsSDK 指派 () 則此名稱值也會定義在全域命名空間中 ```window.appInsightsSDK=<name value>``` ，這是 SDK 初始化程式碼所需的值，以確保其初始化和更新正確的程式碼片段基本架構和 proxy 方法。
| Ld | 以毫秒為單位的數位 *[選用]* | 定義嘗試載入 SDK 之前，要等待的載入延遲。 預設值為0毫秒，任何負值都會立即將腳本標記新增至頁面的 &lt; 前端 &gt; 區域，然後會封鎖頁面載入事件，直到載入腳本 (或) 失敗為止。
| useXhr | 布林值 *[選擇性]* | 這項設定僅用於報告 SDK 載入失敗。 報告將會先嘗試使用提取 ( # A1 （如果有的話），然後回到 XHR，將此值設定為 true 只會略過提取檢查。 只有當您的應用程式是在提取會無法傳送失敗事件的環境中使用時，才需要使用此值。
| crossOrigin | 字串 *[選用]* | 藉由包含這項設定，加入至下載 SDK 的腳本標記將會包含 crossOrigin 屬性，此字串值為。 未定義時 (預設) 不會加入任何 crossOrigin 屬性。 建議的值 (預設) 不會定義;"";或「匿名」 (所有有效值的詳細資料，請參閱[HTML 屬性： `crossorigin` ](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin)檔) 
| cfg | 物件 **[必要]** | 初始化期間傳遞至 Application Insights SDK 的設定。

### <a name="connection-string-setup"></a>連接字串設定

針對 NPM 或程式碼片段的設定，您也可以使用連接字串來設定 Application Insights 的實例。 只要 `instrumentationKey` 以欄位取代欄位即可 `connectionString` 。
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'YOUR_CONNECTION_STRING_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

### <a name="sending-telemetry-to-the-azure-portal"></a>將遙測傳送至 Azure 入口網站

根據預設，Application Insights JavaScript SDK 會 autocollects 許多遙測專案，這些專案有助於判斷您的應用程式和基礎使用者體驗的健康情況。 其中包含：

- 您應用程式中未攔截到的 **例外** 狀況，包括有關的資訊
    - 堆疊追蹤
    - 例外狀況詳細資料和伴隨錯誤的訊息
    - 錯誤行 & 錯誤的資料行數目
    - 引發錯誤的 URL
- 依預設會停用您的應用程式 **XHR** 和 **提取** (提取集合所提出的網路相依性 **要求**，) 的要求，包含相關資訊
    - 相依性來源的 Url
    - 用來要求相依性的命令 & 方法
    - 要求的持續時間
    - 要求的結果碼和成功狀態
    - 如果提出要求的使用者有任何) ，則為識別碼 (
    - 如果提出要求的任何) ，相互關聯內容 (
- **使用者資訊** (例如，位置、網路、IP) 
- **裝置資訊** (例如瀏覽器、OS、版本、語言、模型) 
- **會話資訊**

### <a name="telemetry-initializers"></a>遙測初始化運算式
遙測初始化運算式是用來在從使用者的瀏覽器傳送之前，修改所收集之遙測的內容。 您也可以藉由傳回來使用它們來防止傳送某些遙測資料 `false` 。 您可以將多個遙測初始化運算式新增至您的 Application Insights 實例，並依新增它們的順序來執行。

的輸入引數 `addTelemetryInitializer` 是接受 [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) 做為引數並傳回或的回呼 `boolean` `void` 。 如果傳回 `false` ，則不會傳送遙測專案，否則會繼續進行下一個遙測初始化運算式（如果有的話），或傳送至遙測集合端點。

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

## <a name="configuration"></a>設定
大部分的設定欄位都命名為，因此可以預設為 false。 除了以外，所有欄位都是選擇性的 `instrumentationKey` 。

| 名稱 | 預設 | 描述 |
|------|---------|-------------|
| instrumentationKey | null | **必要**<br>您從 Azure 入口網站取得的檢測金鑰。 |
| accountId | null | 如果您的應用程式將使用者群組為帳戶，則為選擇性的帳戶識別碼。 沒有空格、逗號、分號、等於或分隔號 |
| sessionRenewalMs | 1800000 | 如果使用者在這段時間內處於非使用中狀態，則會記錄會話（以毫秒為單位）。 預設值是 30 分鐘 |
| sessionExpirationMs | 86400000 | 如果會話已繼續此時間量（以毫秒為單位），則會記錄會話。 預設值為24小時 |
| maxBatchSizeInBytes | 10000 | 遙測批次的大小上限。 如果批次超過此限制，則會立即傳送並啟動新的批次 |
| maxBatchInterval | 15000 | 傳送 (毫秒之前，批次遙測的時間長度)  |
| disableExceptionTracking | false | 若為 true，則不會實驗自動收集例外狀況。 預設值為 false。 |
| disableTelemetry | false | 若為 true，則不會收集或傳送遙測。 預設值為 false。 |
| enableDebug | false | 若為 true，則不論 SDK 記錄設定為何， **內部** 偵錯工具資料都會擲回為例外狀況， **而不** 是記錄。 預設值為 false。 <br>**_注意：_* _ 啟用這項設定時，將會在發生內部錯誤時，導致中斷的遙測。 這有助於快速找出您的設定或使用 SDK 的問題。 如果您不想在進行偵錯工具時遺失遙測，請考慮使用 `consoleLoggingLevel` 或， `telemetryLoggingLevel` 而不是 `enableDebug` 。 |
| loggingLevelConsole | 0 | 記錄 _ *內部** Application Insights 錯誤至主控台。 <br>0：關閉、 <br>1：僅嚴重錯誤， <br>2：所有 (錯誤 & 警告)  |
| loggingLevelTelemetry | 1 | 以遙測的形式傳送 **內部** Application Insights 錯誤。 <br>0：關閉、 <br>1：僅嚴重錯誤， <br>2：所有 (錯誤 & 警告)  |
| diagnosticLogInterval | 10000 | 內部記錄佇列的 (內部) 輪詢間隔 (毫秒)  |
| samplingPercentage | 100 | 將傳送的事件百分比。 預設值為100，表示傳送所有事件。 如果您想要保留大型應用程式的資料上限，請設定此設定。 |
| autoTrackPageVisitTime | false | 若為 true，則在 pageview 上，會追蹤先前檢測的網頁檢視時間，並將其傳送為遙測，並為目前的 pageview 啟動新的計時器。 預設值為 false。 |
| disableAjaxTracking | false | 若為 true，則不會實驗自動收集 Ajax 呼叫。 預設值為 false。 |
| disableFetchTracking | true | 若為 true，則不會實驗自動收集提取要求。 預設值為 true。 |
| overridePageViewDuration | false | 若為 true，則在呼叫 trackPageView 時，trackPageView 的預設行為會變更為記錄網頁檢視持續時間間隔的結尾。 如果為 false，且未提供任何自訂持續時間給 trackPageView，則會使用流覽計時 API 來計算網頁檢視效能。 預設值為 false。 |
| maxAjaxCallsPerView | 500 | 預設值 500-控制每個網頁檢視將監視多少 Ajax 呼叫。 設定為-1，可監視頁面上所有 (無限制) Ajax 呼叫。 |
| disableDataLossAnalysis | true | 如果為 false，則會在啟動時檢查尚未傳送之專案的內部遙測傳送者緩衝區。 |
| disableCorrelationHeaders | false | 若為 false，SDK 會將 ( ' Request-Id ' 和 ' Request-CoNtext ' 的兩個標頭新增 ) 至所有相依性要求，以將它們與伺服器端上的對應要求相互關聯。 預設值為 false。 |
| correlationHeaderExcludedDomains |  | 停用特定網域的相互關聯標頭 |
| correlationHeaderDomains |  | 啟用特定網域的相互關聯標頭 |
| disableFlushOnBeforeUnload | false | 預設值為 false。 若為 true，onBeforeUnload 事件觸發程式時，將不會呼叫 flush 方法 |
| enableSessionStorageBuffer | true | 預設值為 true。 若為 true，則會將包含所有未傳送遙測的緩衝區儲存在會話儲存體中。 在頁面載入時還原緩衝區 |
| isCookieUseDisabled | false | 預設值為 false。 若為 true，SDK 將不會儲存或讀取 cookie 中的任何資料。|
| cookieDomain | null | 自訂 cookie 網域。 如果您想要在子域之間共用 Application Insights cookie，這會很有説明。 |
| isRetryDisabled | false | 預設值為 false。 若為 false，則在206上重試 (部分成功) 、408 (timeout) 、429 (太多要求) 、500 (內部伺服器錯誤) 、503 (服務無法使用) ，以及 0 (離線，只有在偵測到時)  |
| isStorageUseDisabled | false | 若為 true，SDK 將不會儲存或讀取本機和會話儲存體中的任何資料。 預設值為 false。 |
| isBeaconApiDisabled | true | 若為 false，SDK 會使用指標[API](https://www.w3.org/TR/beacon)傳送所有遙測 |
| onunloadDisableBeacon | false | 預設值為 false。 當索引標籤關閉時，SDK 會使用指標[API](https://www.w3.org/TR/beacon)來傳送所有剩餘的遙測 |
| sdkExtension | null | 設定 sdk 延伸模組名稱。 只允許字母字元。 延伸模組名稱會新增為 ' sdkVersion ' 標記的前置詞 (例如 ' ext_javascript： 2.0.0 ' ) 。 預設為 Null。 |
| isBrowserLinkTrackingEnabled | false | 預設值為 false。 若為 true，SDK 將會追蹤所有 [瀏覽器連結](/aspnet/core/client-side/using-browserlink) 要求。 |
| appId | null | AppId 可用於在用戶端上發生的 AJAX 相依性與伺服器端要求之間的相互關聯。 啟用指標 API 時，它無法自動使用，但可以在設定中手動設定。 預設值為 null |
| enableCorsCorrelation | false | 若為 true，SDK 會將 ( ' Request-Id ' 和 ' Request-CoNtext ' 的兩個標頭新增 ) 至所有 CORS 要求，以將外寄 AJAX 相依性與伺服器端的對應要求相互關聯。 預設值為 false。 |
| namePrefix | 未定義 | 選擇性的值，將用作 localStorage 和 cookie 名稱的名稱後置詞。
| enableAutoRouteTracking | false | 自動追蹤單一頁面應用程式中的路由變更 (SPA) 。 若為 true，每個路由變更都會將新的 Pageview 傳送至 Application Insights。 雜湊路由變更 (`example.com/foo#bar`) 也會記錄為新的頁面流覽。
| enableRequestHeaderTracking | false | 若為 true，則會追蹤 AJAX & Fetch 要求標頭，預設值為 false。
| enableResponseHeaderTracking | false | 若為 true，則會追蹤 AJAX & Fetch 要求的回應標頭，預設值為 false。
| distributedTracingMode | `DistributedTracingModes.AI` | 設定分散式追蹤模式。 如果設定 AI_AND_W3C 模式或 W3C 模式，將會產生 W3C 追蹤內容標頭 (traceparent/tracestate) ，並包含在所有傳出要求中。 AI_AND_W3C 是為了與任何舊版 Application Insights 檢測的服務進行回溯相容。 請參閱 [這裡](./correlation.md#enable-w3c-distributed-tracing-support-for-web-apps)的範例。
| enableAjaxErrorStatusText | false | 預設值為 false。 若為 true，則在失敗 AJAX 要求的相依性事件中包含回應錯誤資料文字。
| enableAjaxPerfTracking | false | 預設值為 false。 旗標，可讓您查閱和包含其他瀏覽器視窗。報告 (中的效能時間 `ajax` XHR 和提取) 回報的度量。
| maxAjaxPerfLookupAttempts | 3 | 預設值為 3。 要尋找視窗的最大次數。效能計時 (（如果有) 的話），因為並非所有的瀏覽器都填入視窗。在報告 XHR 要求結束之前的效能，以及提取要求之後，就會在完成之後加入。
| ajaxPerfLookupDelay | 25 | 預設為25毫秒。 重新嘗試尋找 windows 之前要等候的時間量。要求的效能 `ajax` 時間，以毫秒為單位，並直接傳遞給 setTimeout ( # A1。
| enableUnhandledPromiseRejectionTracking | false | 若為 true，將會實驗自動收集未處理的承諾遭到拒絕，並將其報告為 JavaScript 錯誤。 當 disableExceptionTracking 為 true 時 (不會追蹤) 的例外狀況，將會忽略設定值，而且不會報告未處理的承諾拒絕。

## <a name="enable-time-on-page-tracking"></a>啟用頁面追蹤時間

藉由設定 `autoTrackPageVisitTime: true` ，會追蹤使用者花在每個頁面上的時間。 在每個新的 PageView 上，使用者花費在 *上一頁* 的持續時間會以名為的 [自訂](../platform/metrics-custom-overview.md) 計量來傳送 `PageVisitTime` 。 此自訂計量可在 [計量瀏覽器](../platform/metrics-getting-started.md) 中查看為「記錄式度量」。

## <a name="enable-correlation"></a>啟用相互關聯

相互關聯會產生和傳送資料，以啟用分散式追蹤並提供 [應用程式對應](../app/app-map.md)、 [端對端交易視圖](../app/app-map.md#go-to-details)及其他診斷工具。

下列範例會顯示啟用相互關聯所需的所有可能設定，以及下列案例特定附注：

```javascript
// excerpt of the config section of the JavaScript SDK snippet with correlation
// between client-side AJAX and server requests enabled.
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    disableFetchTracking: false,
    enableCorsCorrelation: true,
    enableRequestHeaderTracking: true,
    enableResponseHeaderTracking: true,
    correlationHeaderExcludedDomains: ['myapp.azurewebsites.net', '*.queue.core.windows.net']
    /* ...Other Configuration Options... */
}});
</script>

``` 

如果與用戶端通訊的任何協力廠商伺服器無法接受 `Request-Id` 和 `Request-Context` 標頭，而且您無法更新其設定，則您必須透過 configuration 屬性將它們放入排除清單中 `correlationHeaderExcludeDomains` 。 這個屬性支援萬用字元。

伺服器端必須能夠接受與這些標頭存在的連接。 視伺服器端的設定而 `Access-Control-Allow-Headers` 定，通常必須以手動方式加入和來延伸伺服器端清單 `Request-Id` `Request-Context` 。

存取控制-允許-標頭： `Request-Id` 、 `Request-Context` 、 `<your header>`

> [!NOTE]
> 如果您使用的是 OpenTelemtry Application Insights 或2020或更新版本中發行的 Sdk，我們建議使用 [WC3 TraceCoNtext](https://www.w3.org/TR/trace-context/)。 請參閱 [這裡](../app/correlation.md#enable-w3c-distributed-tracing-support-for-web-apps)的設定指引。

## <a name="single-page-applications"></a>單一頁面應用程式

根據預設，此 SDK **不** 會處理在單一頁面應用程式中發生的以狀態為基礎的路由變更。 若要為您的單一頁面應用程式啟用自動路由變更追蹤，您可以新增 `enableAutoRouteTracking: true` 至您的安裝程式設定。

目前，我們提供個別的 [回應外掛程式](javascript-react-plugin.md)，您可以使用此 SDK 進行初始化。 它也會為您完成路由變更追蹤，以及收集其他回應特定的遙測。
> [!NOTE]
> `enableAutoRouteTracking: true`只有當您 **未** 使用回應外掛程式時，才使用。 兩者都能夠在路由變更時傳送新的 PageViews。 如果兩者都啟用，可能會傳送重複的 PageViews。

## <a name="extensions"></a>延伸模組

| 延伸模組 |
|---------------|
| [React](javascript-react-plugin.md)|
| [React Native](javascript-react-native-plugin.md) \(英文\)|
| [Angular](javascript-angular-plugin.md) |

## <a name="explore-browserclient-side-data"></a>探索瀏覽器/用戶端資料

瀏覽器/用戶端資料可透過進入 **計量** ，並新增您感興趣的個別計量來查看：

![Application Insights 中 [計量] 頁面的螢幕擷取畫面，其中顯示 web 應用程式的度量資料圖形顯示。](./media/javascript/page-view-load-time.png)

您也可以透過入口網站中的瀏覽器體驗，從 JavaScript SDK 查看您的資料。

選取 [ **瀏覽器** ]，然後選擇 [ **失敗** ] 或 [ **效能**]。

![Application Insights 中瀏覽器頁面的螢幕擷取畫面，其中顯示如何將瀏覽器失敗或瀏覽器效能新增至您可以針對 web 應用程式查看的度量。](./media/javascript/browser.png)

### <a name="performance"></a>效能

![Application Insights 中 [效能] 頁面的螢幕擷取畫面，其中顯示 web 應用程式的作業計量圖形顯示。](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>相依性

![Application Insights 中 [效能] 頁面的螢幕擷取畫面，其中顯示 web 應用程式的相依性計量圖形顯示。](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>分析

若要查詢 JavaScript SDK 所收集的遙測，請選取 [ **在記錄中查看 (分析])** 按鈕。 藉由加入的 `where` 語句 `client_Type == "Browser"` ，您將只會看到來自 JavaScript SDK 的資料，以及其他 sdk 所收集的任何伺服器端遙測都將被排除。
 
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

您可以在 Azure 入口網站中 unminified 例外狀況遙測的縮減呼叫堆疊。 [例外狀況詳細資料] 面板上的所有現有整合都可使用新 unminified 的呼叫堆疊。

#### <a name="link-to-blob-storage-account"></a>連結至 Blob 儲存體帳戶

您可以將 Application Insights 資源連結至自己的 Azure Blob 儲存體容器，以自動美化呼叫堆疊。 若要開始使用，請參閱 [自動來源對應支援](./source-map-support.md)。

### <a name="drag-and-drop"></a>拖放

1. 在 Azure 入口網站中選取例外狀況遙測專案，以查看其「端對端交易詳細資料」
2. 識別與此呼叫堆疊對應的來源對應。 來源對應必須符合堆疊框架的原始程式檔，但尾碼為 `.map`
3. 將 [來源對應] 拖放到 Azure 入口網站 ![ 的動畫影像中，顯示如何從組建資料夾將來源對應檔案拖放到 Azure 入口網站的 [呼叫堆疊] 視窗中。](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web 基本

針對輕量體驗，您可以改為安裝 Application Insights 的基本版本
```
npm i --save @microsoft/applicationinsights-web-basic
```
此版本隨附最少的特性和功能，並視您的需要建立。 例如，它不會執行任何 autocollection (無法攔截的例外狀況、AJAX 等 ) 。 傳送某些遙測類型的 Api （例如 `trackTrace` 、 `trackException` 等等）不會包含在此版本中，因此您必須提供自己的包裝函式。 唯一可用的 API 是 `track` 。 [範例](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html)位於此處。

## <a name="examples"></a>範例

如需可執行檔範例，請參閱 [Application Insights JAVASCRIPT SDK 範例](https://github.com/Azure-Samples?q=applicationinsights-js-demo)。

## <a name="upgrading-from-the-old-version-of-application-insights"></a>從舊版本的 Application Insights 升級

SDK V2 版本的重大變更：
- 為了讓 API 簽章更好，某些 API 呼叫（例如 trackPageView 和 trackException）已更新。 不支援在 Internet Explorer 8 和更早版本的瀏覽器中執行。
- 因為資料架構更新，所以遙測信封具有欄位名稱和結構變更。
- 已移 `context.operation` 至 `context.telemetryTrace` 。 某些欄位也會 (`operation.id`  -->  `telemetryTrace.traceID`) 變更。
  - 若要手動重新整理目前的 pageview 識別碼 (例如，在 SPA 應用程式) 中，請使用 `appInsights.properties.context.telemetryTrace.traceID = Microsoft.ApplicationInsights.Telemetry.Util.generateW3CId()` 。
    > [!NOTE]
    > 若要讓追蹤識別碼成為唯一的，您先前使用的是 `Util.newId()` ，現在請使用 `Util.generateW3CId()` 。 最後最後是作業識別碼。

如果您使用目前的 application insights 生產 SDK (1.0.20) ，而且想要查看新的 SDK 是否可在執行時間中運作，請根據您目前的 SDK 載入案例來更新 URL。

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

在內部環境中測試，以確認監視遙測如預期般運作。 如果一切正常運作，請將您的 API 簽章適當地更新為 SDK V2 版本，並在生產環境中部署。

## <a name="sdk-performanceoverhead"></a>SDK 效能/額外負荷

只有 36 KB 的 gzipped，而且只需要約15毫秒來初始化，Application Insights 在您的網站中新增了可忽略的 loadtime 量。 使用程式碼片段，可快速載入程式庫的基本元件。 在此期間，會在背景下載完整的腳本。

從 CDN 下載腳本時，頁面的所有追蹤都會排入佇列。 一旦下載的腳本以非同步方式完成初始化之後，就會追蹤所有已排入佇列的事件。 因此，您在頁面的整個生命週期中不會遺失任何遙測資料。 此安裝程式會為您的頁面提供順暢的分析系統，讓您的使用者看不到。

> 摘要：
> - ![npm 版本](https://badge.fury.io/js/%40microsoft%2Fapplicationinsights-web.svg)
> - ![gzip 壓縮大小](https://img.badgesize.io/https://js.monitor.azure.com/scripts/b/ai.2.min.js.svg?compression=gzip)
> - **15 毫秒** 整體初始化時間
> - 頁面生命週期期間缺少 **零** 追蹤

## <a name="browser-support"></a>瀏覽器支援

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Chrome 最新✔ |  Firefox 最新✔ | IE 9 + & Edge ✔<br>IE 8 相容 | Opera 最新✔ | Safari 最新✔ |

## <a name="es3ie8-compatibility"></a>ES3/IE8 相容性

SDK 有許多使用者無法控制其客戶所使用的瀏覽器。 因此，我們需要確保此 SDK 會繼續「工作」，且不會在舊版瀏覽器載入時中斷 JS 執行。 雖然不支援 IE8 和較舊版本 (ES3) 瀏覽器，但有許多大型客戶/使用者會繼續要求頁面「工作」，並已注意到他們可能或無法控制使用者選擇使用的瀏覽器。

這並不表示我們只會支援最低的一般功能集，只是我們需要維護 ES3 程式碼的相容性，而且在加入新的功能時，必須以不會中斷 ES3 JavaScript 剖析並新增為選擇性功能的方式來新增它們。

[如需 IE8 支援的完整詳細資訊，請參閱 GitHub](https://github.com/Microsoft/ApplicationInsights-JS#es3ie8-compatibility)

## <a name="open-source-sdk"></a>開放原始碼 SDK

Application Insights JavaScript SDK 是開放原始碼，可供您查看原始程式碼或參與專案，請造訪官方的 [GitHub 存放庫](https://github.com/Microsoft/ApplicationInsights-JS)。 

如需最新的更新和錯誤修正， [請參閱版本](./release-notes.md)資訊。

## <a name="next-steps"></a><a name="next"></a> 後續步驟
* [追蹤流量](usage-overview.md)
* [自訂事件和計量](api-custom-events-metrics.md)
* [Build-measure-learn](usage-overview.md)
* [針對 SDK 載入失敗進行疑難排解](javascript-sdk-load-failure.md)
