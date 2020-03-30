---
title: 適用于 JavaScript Web 應用的 Azure 應用程式見解
description: 獲取網頁檢視和會話計數、Web 用戶端資料、單頁應用程式 （SPA） 和跟蹤使用模式。 Detect exceptions and performance issues in JavaScript web pages.
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: 5414a70180a82be8253dace7d800c90c1ae6a9bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276071"
---
# <a name="application-insights-for-web-pages"></a>適用於網頁的 Application Insights

了解您的網頁或應用程式的效能和使用量。 如果將[應用程式見解](app-insights-overview.md)添加到頁面腳本中，您將獲得頁面載入和 AJAX 調用的時間、計數以及瀏覽器異常和 AJAX 失敗的詳細資訊，以及使用者和會話計數。 這些項目可以依據頁面、用戶端作業系統和瀏覽器版本、地區位置和其他維度分割。 您可以對失敗計數或緩慢頁面載入設定警示。 而在 JavaScript 程式碼中插入追蹤呼叫，即可追蹤網頁應用程式的各種功能使用方式。

Application Insights 可以使用於任何網頁 - 您剛剛新增 JavaScript 的簡短片段。 如果您的 Web 服務是[JAVA](java-get-started.md)或[ASP.NET](asp-net.md)，則可以將伺服器端 SDK 與用戶端 JavaScript SDK 結合使用，以便對應用的性能進行端到端瞭解。

## <a name="adding-the-javascript-sdk"></a>添加 JavaScript SDK

1. 首先，您需要一個應用程式見解資源。 如果還沒有資源和檢測金鑰，請按照[創建新的資源說明。](create-new-resource.md)
2. 從希望發送 JavaScript 遙測的資源中複製檢測金鑰。
3. 通過以下兩個選項之一將應用程式見解 JavaScript SDK 添加到您的網頁或應用中：
    * [npm 設置](#npm-based-setup)
    * [JavaScript 程式碼片段](#snippet-based-setup)

> [!IMPORTANT]
> 僅使用一種方法將 JavaScript SDK 添加到應用程式中。 如果使用 NPM 安裝程式，請不要使用程式碼片段，反之亦然。

> [!NOTE]
> NPM 安裝程式將 JavaScript SDK 作為專案的依賴項安裝，啟用 IntelliSense，而程式碼片段在運行時獲取 SDK。 兩者都支援相同的功能。 但是，希望更多自訂事件和配置的開發人員通常選擇 NPM 安裝程式，而希望快速啟用開箱即用 Web 分析的使用者選擇程式碼片段。

### <a name="npm-based-setup"></a>基於 npm 的設置

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>基於程式碼片段的設置

如果應用不使用 npm，則可以通過在每個頁面頂部粘貼此程式碼片段，直接使用應用程式見解來檢測網頁。 最好是，它應該是節中`<head>`的第一個腳本，以便它可以監視所有依賴項的任何潛在問題。 如果使用 Blazor 伺服器應用，則在`_Host.cshtml``<head>`部分中的檔頂部添加程式碼片段。

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>向 Azure 門戶發送遙測

預設情況下，應用程式見解 JavaScript SDK 自動收集許多有助於確定應用程式運行狀況和基礎使用者體驗的遙測項。 其中包括：

- 應用中**未捕獲的異常**，包括有關
    - 堆疊追蹤
    - 異常詳細資訊和錯誤附帶的消息
    - 行&列錯誤數
    - 引發錯誤的 URL
- 應用**XHR**和**Fetch（** 預設情況下禁用提取集合）請求發出**的網路依賴項請求**，包括有關
    - 依賴項源的 URL
    - 用於請求依賴項的命令&方法
    - 請求的持續時間
    - 結果代碼和請求的成功狀態
    - 發出請求的使用者的 ID（如果有）
    - 發出請求的相關上下文（如果有）
- **使用者資訊**（例如，位置、網路、IP）
- **設備資訊**（例如，瀏覽器、作業系統、版本、語言、解析度、型號）
- **會話資訊**

### <a name="telemetry-initializers"></a>遙測初始化器
遙測初始化程式用於在從使用者的瀏覽器發送之前修改收集的遙測內容。 它們還可用於通過返回`false`來防止發送某些遙測資料。 可以將多個遙測初始化程式添加到應用程式見解實例中，並且按添加它們的循序執行它們。

的`addTelemetryInitializer`輸入參數是一個回檔，它將[`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer)作為 參數並返回`boolean`或`void`。 如果返回`false`，遙測項不會發送，否則它將繼續到下一個遙測初始化器（如果有）或發送到遙測集合終結點。

使用遙測初始化程式的示例：
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
大多數配置欄位都命名，以便它們可以預設為 false。 除 之外，所有欄位`instrumentationKey`都是可選的。

| 名稱 | 預設 | 描述 |
|------|---------|-------------|
| instrumentationKey | null | **必要**<br>從 Azure 門戶獲取的檢測金鑰。 |
| accountId | null | 如果應用將使用者分組到帳戶中，則為可選帳戶 ID。 無空格、逗號、分號、等於或分隔號 |
| 會話更新 | 1800000 | 如果使用者在以下時間內處於非活動狀態，則記錄會話（以毫秒為單位）。 預設值是 30 分鐘 |
| 會話過期 M | 86400000 | 如果會話在以毫秒為單位持續此時間，則記錄該會話。 預設值為 24 小時 |
| 最大批次大小位元組 | 10000 | 遙測批次處理的最大大小。 如果批次處理超過此限制，將立即發送該批次並啟動新批次處理 |
| 最大批次間隔 | 15000 | 發送前對遙測的批次處理時間（毫秒） |
| 禁用異常跟蹤 | false | 如果為 true，則異常不會自動收集。 預設值為 false。 |
| 禁用遙測 | false | 如果為 true，則不會收集或發送遙測資料。 預設值為 false。 |
| 啟用調試 | false | 如果為 true，則無論 SDK 日誌記錄設置如何，**內部**調試資料都將作為例外引發 **，而不是**被記錄。 預設值為 false。 <br>***注：*** 啟用此設置將導致每當發生內部錯誤時遙測資料丟棄。 這對於快速識別 SDK 的配置或使用問題非常有用。 如果不想在調試時丟失遙測，請考慮使用`consoleLoggingLevel`或`telemetryLoggingLevel`而不是`enableDebug`。 |
| 日誌記錄 Level 主控台 | 0 | 將**內部**應用程式見解錯誤記錄到主控台。 <br>0： 關閉， <br>1： 僅嚴重錯誤， <br>2： 一切（錯誤&警告） |
| 日誌記錄級別遙測 | 1 | 將**內部**應用程式見解錯誤作為遙測資料發送。 <br>0： 關閉， <br>1： 僅嚴重錯誤， <br>2： 一切（錯誤&警告） |
| 診斷日誌間隔 | 10000 | （內部）內部日誌記錄佇列的輪詢間隔（以毫秒為單位） |
| 採樣百分比 | 100 | 要發送的事件的百分比。 預設值為 100，這意味著將發送所有事件。 如果要為大型應用程式保留資料上限，請設置此選項。 |
| 自動跟蹤頁面存取時間 | false | 如果為 true，則在網頁檢視中，將跟蹤上一個檢測頁面的視圖時間並將其作為遙測資料發送，並且為當前網頁檢視啟動一個新的計時器。 預設值為 false。 |
| 禁用Ajax跟蹤 | false | 如果為 true，則不會自動收集 Ajax 調用。 預設值為 false。 |
| 禁用提取跟蹤 | true | 如果為 true，則不會自動收集"提取"請求。 預設值為 true。 |
| 覆蓋頁面查看持續時間 | false | 如果為 true，則在調用 trackPageView 時，將更改 trackPageView 的預設行為以記錄網頁檢視持續時間間隔的末尾。 如果為 trackPageView 提供 false 且未提供自訂持續時間，則使用導航計時 API 計算網頁檢視性能。 預設值為 false。 |
| 最大Ajax調用PerView | 500 | 預設 500 - 控制每個網頁檢視將監視多少 Ajax 調用。 設置為 -1 以監視頁面上的所有（無限制）Ajax 調用。 |
| 禁用資料丟失分析 | true | 如果為 false，將在啟動時檢查內部遙測發送方緩衝區是否未發送的專案。 |
| 禁用相關標題 | false | 如果為 false，SDK 將向所有依賴項請求添加兩個標頭（"請求 Id"和"請求-上下文"），以將它們與伺服器端的相應請求相關聯。 預設值為 false。 |
| 關聯標題排除域 |  | 禁用特定域的相關標頭 |
| 相關標題域 |  | 為特定域啟用相關標頭 |
| 在卸載之前禁用 Flushon | false | 預設為 false。 如果為 true，則在打開"未載入"事件觸發時不會調用刷新方法 |
| 啟用會話存儲緩衝區 | true | 預設值為 true。 如果為 true，則具有所有未發送遙測的緩衝區將存儲在會話存儲中。 緩衝區在頁面載入時還原 |
| 是曲奇因斯殘疾 | false | 預設為 false。 如果為 true，SDK 將不會存儲或讀取 Cookie 中的任何資料。|
| 曲奇域 | null | 自訂 Cookie 域。 如果您希望跨子域共用應用程式見解 Cookie，這非常有用。 |
| 是重新嘗試禁用 | false | 預設為 false。 如果為 false，請重試 206（部分成功）、408（超時）、429（請求過多）、500（內部伺服器錯誤）、503（服務不可用）和 0（僅在檢測到時離線） |
| 是存儲Use 禁用 | false | 如果為 true，SDK 將不會存儲或讀取本地和會話存儲中的任何資料。 預設值為 false。 |
| 是信標A禁用 | true | 如果為 false，SDK 將使用[信標 API](https://www.w3.org/TR/beacon)發送所有遙測資料 |
| onunload 禁用信標 | false | 預設為 false。 關閉選項卡時，SDK 將使用[信標 API](https://www.w3.org/TR/beacon)發送所有剩餘的遙測資料 |
| sdk 擴展 | null | 設置 sdk 副檔名。 只允許字母字元。 副檔名作為首碼添加到"ai.內部.sdkVersion"標記（例如，ext_javascript：2.0.0'）。 預設值為 null。 |
| 是瀏覽器連結跟蹤啟用 | false | 預設值為 false。 如果為 true，SDK 將跟蹤所有[瀏覽器連結](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink)請求。 |
| appId | null | AppId 用於在用戶端上發生的 AJAX 依賴項與伺服器端請求之間的關聯。 啟用 Beacon API 後，無法自動使用它，但可以在配置中手動設置。 預設值為空 |
| 啟用參數相關 | false | 如果為 true，SDK 將向所有 CORS 請求添加兩個標頭（"請求 Id"和"請求上下文"），以將傳出的 AJAX 依賴項與伺服器端的相應請求相關聯。 預設值為 false。 |
| 名稱首碼 | 未定義 | 將用作本機存放區和 Cookie 名稱的名稱尾碼的可選值。
| 啟用自動路由跟蹤 | false | 自動跟蹤單頁應用程式 （SPA） 中的路由更改。 如果為 true，則每次路由更改都將向應用程式見解發送新的網頁檢視。 雜湊路由更改 （`example.com/foo#bar`） 也會記錄為新網頁檢視。
| 啟用請求標題跟蹤 | false | 如果為 true，則跟蹤 AJAX &提取請求標頭，則預設值為 false。
| 啟用回應標題跟蹤 | false | 如果為 true，則跟蹤 AJAX &提取請求的回應標頭，則預設值為 false。
| 分散式跟蹤模式 | `DistributedTracingModes.AI` | 設置分散式跟蹤模式。 如果設置了AI_AND_W3C模式或 W3C 模式，則 W3C 跟蹤上下文標頭（跟蹤父/跟蹤狀態）將生成並包含在所有傳出請求中。 提供AI_AND_W3C可與任何繼承應用程式見解檢測服務進行背相容性。

## <a name="single-page-applications"></a>單頁應用程式

預設情況下，此 SDK**不會**處理在單頁應用程式中發生的基於狀態的路由更改。 要為單頁應用程式啟用自動路由更改跟蹤，可以添加到`enableAutoRouteTracking: true`設置配置。

目前，我們提供單獨的[React 外掛程式](#react-extensions)，您可以使用此 SDK 初始化。 它還將為您完成路由更改跟蹤，以及收集[其他 React 特定遙測](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)資料。

## <a name="react-extensions"></a>回應擴展

| 延伸模組 |
|---------------|
| [React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [React Native](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md) \(英文\)|

## <a name="explore-browserclient-side-data"></a>流覽瀏覽器/用戶端資料

可以通過訪問**指標**並添加您感興趣的單個指標來查看瀏覽器/用戶端資料：

![](./media/javascript/page-view-load-time.png)

您還可以通過門戶中的瀏覽器體驗從 JavaScript SDK 查看資料。

選擇**瀏覽器**，然後選擇**失敗**或**性能**。

![](./media/javascript/browser.png)

### <a name="performance"></a>效能

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>相依性

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>分析

要查詢 JavaScript SDK 收集的遙測資料，請選擇 **"日誌中視圖（分析）"** 按鈕。 通過添加`where`的`client_Type == "Browser"`語句，您只能看到來自 JavaScript SDK 的資料，其他 SDK 收集的任何伺服器端遙測將排除。
 
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

### <a name="source-map-support"></a>源地圖支援

異常遙測的簡化呼叫堆疊可以在 Azure 門戶中取消分明。 "例外詳細資訊"面板上的所有現有集成都將使用新未分明的呼叫堆疊。

#### <a name="link-to-blob-storage-account"></a>連結到 Blob 存儲帳戶

您可以將應用程式見解資源連結到自己的 Azure Blob 存儲容器，以自動取消呼叫堆疊。 要開始，請參閱[自動源映射支援](./source-map-support.md)。

### <a name="drag-and-drop"></a>拖放

1. 在 Azure 門戶中選擇異常遙測項以查看其"端到端事務詳細資訊"
2. 標識對應于此呼叫堆疊的源映射。 源映射必須與堆疊幀的原始檔案匹配，但尾碼與`.map`
3. 在 Azure 門戶中將源映射拖放到呼叫堆疊上![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>應用程式見解 Web 基本

對於羽量級體驗，您可以安裝應用程式見解的基本版本
```
npm i --save @microsoft/applicationinsights-web-basic
```
此版本附帶最少的功能和功能，並依靠您構建它，你認為適合。 例如，它不執行自動收集（未捕獲的異常、AJAX 等）。 發送某些遙測類型的 API（如`trackTrace`、`trackException`等）不包括在此版本中，因此您需要提供您自己的包裝器。 唯一可用的 API 是`track`。 [示例](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html)位於此處。

## <a name="examples"></a>範例

有關可運行的示例，請參閱[應用程式見解 JavaScript SDK 示例](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>從舊版本的應用程式見解進行升級

SDK V2 版本中的突發更改：
- 為了提供更好的 API 簽名，某些 API 呼叫（如 trackPageView 和 trackException）已更新。 不支援在 Internet 資源管理器 8 中運行以及瀏覽器的早期版本。
- 遙測信封具有由於資料架構更新而導致的欄位名稱和結構更改。
- 移動到`context.operation``context.telemetryTrace`。 某些欄位也已更改 （`operation.id` --> `telemetryTrace.traceID`。
  - 要手動刷新當前網頁檢視 ID（例如，在 SPA 應用中），請使用`appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()`。
    > [!NOTE]
    > 要保持跟蹤 ID 的唯一性（以前`Util.newId()`使用的位置），`Util.generateW3CId()`現在使用 。 兩者最終都是操作 ID。

如果您使用的是當前應用程式見解生產 SDK （1.0.20），並且想要查看新 SDK 是否在運行時工作，請根據當前的 SDK 載入方案更新 URL。

- 通過 CDN 方案下載：更新您當前用於指向以下 URL 的程式碼片段：
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- npm 方案：`downloadAndSetup`調用從 CDN 下載完整的應用程式 Insights 腳本，然後使用檢測金鑰初始化它：

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

在內部環境中進行測試，以驗證監視遙測是否按預期工作。 如果所有功能都有效，請將 API 簽名適當地更新到 SDK V2 版本，並在生產環境中部署。

## <a name="sdk-performanceoverhead"></a>SDK 性能/開銷

應用程式見解只需 25 KB 的壓縮，只需 15 毫秒進行初始化，就為您的網站增加了可忽略不計的載入時間。 通過使用程式碼片段，庫的最小元件將快速載入。 同時，完整腳本在後臺下載。

當腳本從 CDN 下載時，對頁面的所有跟蹤都已排隊。 下載的腳本完成非同步初始化後，將跟蹤所有排隊的事件。 因此，在頁面的整個生命週期內，您不會丟失任何遙測資料。 此設置過程為您的頁面提供無縫的分析系統，而使用者對此不可見。

> 摘要：
> - **25 KB** gzipped
> - **15 ms**整體初始化時間
> - 頁面生命週期中未命中**零**跟蹤

## <a name="browser-support"></a>瀏覽器支援

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
鉻最新✔ |  火狐最新✔ | IE 9+ &邊緣✔ | 歌劇最新✔ | 野生動物園最新✔ |

## <a name="open-source-sdk"></a>開放原始碼 SDK

應用程式見解 JavaScript SDK 是開源的，用於查看原始程式碼或參與專案訪問[官方 GitHub 存儲庫](https://github.com/Microsoft/ApplicationInsights-JS)。

## <a name="next-steps"></a><a name="next"></a> 後續步驟
* [追蹤流量](usage-overview.md)
* [自訂事件和計量](api-custom-events-metrics.md)
* [Build-measure-learn](usage-overview.md)
