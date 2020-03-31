---
title: 用於搜索流量分析的遙測
titleSuffix: Azure Cognitive Search
description: 啟用 Azure 認知搜索的搜索流量分析，使用應用程式見解收集遙測和使用者啟動的事件，然後分析 Power BI 報告中的調查結果。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: db8c1781061f038cc90310fcd00c220fa6f5d1a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258204"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>收集遙測資料以進行搜索流量分析

搜索流量分析是一種模式，用於收集有關使用者與 Azure 認知搜索應用程式（如使用者啟動的按一下事件和鍵盤輸入）交互的遙測資料。 使用此資訊，您可以確定搜索解決方案的有效性，包括常用搜索字詞、點擊率以及哪些查詢輸入產生零結果。

此模式依賴于[應用程式見解](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)[（Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/)的一項功能）來收集使用者資料。 它要求您向用戶端代碼添加檢測，如本文所述。 最後，您需要一個報告機制來分析資料。 我們建議使用 Power BI，但您可以使用應用程式儀表板或任何連接到應用程式見解的工具。

> [!NOTE]
> 本文中描述的模式適用于高級方案和由添加到用戶端的代碼生成的點選流資料。 相反，服務日誌易於設置，提供一系列指標，可以在門戶中完成，無需任何代碼。 建議在所有方案中啟用診斷日誌記錄。 有關詳細資訊，請參閱[收集和分析日誌資料](search-monitor-logs.md)。

## <a name="identify-relevant-search-data"></a>識別相關的搜尋資料

要有用於搜索流量分析的有用指標，必須記錄來自搜索應用程式使用者的一些信號。 這些信號表示使用者感興趣的內容，並且他們認為相關內容。 對於搜索流量分析，其中包括：

+ 使用者生成的搜索事件：只有使用者啟動的搜索查詢才有趣。 用來填入 Facet、其他內容或任何內部資訊的搜尋要求並不重要，而且還會扭曲和偏差結果。

+ 使用者生成的按一下事件：在搜尋結果頁上，按一下事件通常表示文檔是特定搜索查詢的相關結果。

通過將搜索和按一下事件與相關 ID 連結，您可以更深入地瞭解應用程式的搜索功能的性能。

## <a name="add-search-traffic-analytics"></a>新增搜尋流量分析

在 Azure 認知搜索服務的[門戶](https://portal.azure.com)頁中，"搜索流量分析"頁包含一個用於遵循此遙測模式的備忘單。 從此頁面中，您可以選擇或創建應用程式見解資源、獲取檢測金鑰、複製可適應解決方案的程式碼片段，以及下載通過模式中反映的架構構建的 Power BI 報表。

![在門戶中搜索流量分析頁面](media/search-traffic-analytics/azuresearch-trafficanalytics.png "在門戶中搜索流量分析頁面")

## <a name="1---set-up-application-insights"></a>1 - 設置應用程式見解

選擇現有的應用程式見解資源，或者創建[一個](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)，如果您還沒有一個。 如果使用"搜索流量分析"頁，則可以複製應用程式連接到應用程式見解所需的檢測金鑰。

獲得應用程式見解資源後，您可以按照[受支援的語言和平臺的說明](https://docs.microsoft.com/azure/azure-monitor/app/platforms)註冊應用。 註冊只是將應用程式見解中的檢測金鑰添加到代碼中，從而設置關聯。 您可以選擇現有資源時，您可以在門戶或"搜索流量分析"頁中找到金鑰。

適用于某些 Visual Studio 專案類型的快捷方式將反映在以下步驟中。 它創建一個資源，只需按一下幾下即可註冊應用。

1. 對於視覺化工作室和ASP.NET開發，請打開解決方案並選擇 **"專案** > **添加應用程式見解遙測**"。

1. 按一下 [開始使用]****。

1. 通過提供 Microsoft 帳戶、Azure 訂閱和應用程式見解資源（預設為新資源）註冊應用。 按一下 [註冊]****

此時，應用程式已設置為應用程式監視，這意味著使用預設指標跟蹤所有頁面載入。 有關上述步驟的詳細資訊，請參閱[啟用應用程式見解伺服器端遙測](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio)。

## <a name="2---add-instrumentation"></a>2 - 新增檢測

此步驟是使用上面步驟中創建的應用程式見解資源來檢測您自己的搜索應用程式的位置。 此過程有四個步驟，從創建遙測用戶端開始。

### <a name="step-1-create-a-telemetry-client"></a>第 1 步：創建遙測用戶端

創建將事件發送到應用程式見解的物件。 您可以將檢測添加到在瀏覽器中運行的伺服器端應用程式代碼或用戶端代碼，此處以 C# 和 JavaScript 變體表示（對於其他語言，請參閱[支援的平臺和框架](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)的完整清單）。 選擇為您提供所需資訊深度的方法。

伺服器端遙測捕獲應用程式層的指標，例如，在雲中作為 Web 服務運行的應用程式或作為公司網路上的本地應用運行的應用程式中。 伺服器端遙測捕獲搜索和按一下事件、文檔在結果中的位置和查詢資訊，但資料收集的範圍將限定為該層上可用的任何資訊。

在用戶端上，您可能還有其他代碼來操作查詢輸入、添加導航或包括上下文（例如，從主頁引發的查詢與產品頁）。 如果這描述您的解決方案，您可以選擇用戶端檢測，以便遙測反映其他詳細資訊。 如何收集此附加詳細資訊超出了此模式的範圍，但您可以查看[網頁的應用程式見解](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data)以瞭解更多方向。 

**使用 C#**

對於 C#，在應用程式佈建中找到**檢測鍵**，例如，如果專案ASP.NET，則為 appsettings.json。 如果您不確定關鍵位置，請返回註冊說明。

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
    {
        _telemetryClient = telemetry;
    }
```

**使用 JavaScript**

```javascript
<script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
({
instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
});
window.appInsights=appInsights;
</script>
```

### <a name="step-2-request-a-search-id-for-correlation"></a>第 2 步：請求搜索 ID 以查找相關

要將搜索請求與按一下關聯，必須具有與這兩個不同的事件相關的關聯 ID。 Azure 認知搜索在使用 HTTP 標頭請求時為您提供搜索 ID。

具有搜索 ID 允許將 Azure 認知搜索為請求本身發出的指標與在應用程式見解中記錄的自訂指標關聯。  

**使用 C#**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues)){
    searchId = headerValues.FirstOrDefault();
}
```

**使用 JavaScript（調用 REST API）**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>第 3 步：記錄搜索事件

每次由使用者發出搜索請求時，都應在應用程式見解自訂事件中使用以下架構將該請求記錄為搜索事件。 請記住僅記錄使用者生成的搜索查詢。

+ **搜索服務名稱**：（字串） 搜索服務名稱
+ **搜索 Id**：（guid） 搜索查詢的唯一識別碼（在搜索回應中提供）
+ **索引名稱**：（字串）搜索服務索引要查詢
+ **查詢術語**：（字串） 搜索字詞由使用者輸入
+ **結果計數**：（int） 返回的文檔數（來自搜索回應中）
+ **評分設定檔**：（字串）名稱的評分設定檔使用，如果有的話

> [!NOTE]
> 通過將$count_true添加到搜索查詢，請求使用者生成的查詢計數。 有關詳細資訊，請參閱[搜索文檔 （REST）](/rest/api/searchservice/search-documents#counttrue--false)。
>

**使用 C#**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
_telemetryClient.TrackEvent("Search", properties);
```

**使用 JavaScript**

```javascript
appInsights.trackEvent("Search", {
SearchServiceName: <service name>,
SearchId: <search id>,
IndexName: <index name>,
QueryTerms: <search terms>,
ResultCount: <results count>,
ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>第 4 步：記錄按一下事件

每當使用者點選文件時，必須記錄這個訊號才可進行搜尋分析。 使用 Application Insights 自訂事件，記錄包含下列結構描述的這些事件：

+ **服務名稱**： （字串） 搜索服務名稱
+ **搜索 Id**：（guid） 相關搜索查詢的唯一識別碼
+ **文檔 Id**：（字串）文檔識別碼
+ **在**搜尋結果頁中放置 ：（int） 文檔的排名

> [!NOTE]
> 位置是指您應用程式中的基本順序。 您可以自由地設定這個數字 (只要它一律相同) 以便進行比較。
>

**使用 C#**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
_telemetryClient.TrackEvent("Click", properties);
```

**使用 JavaScript**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3 - 在 Power BI 中分析

檢測應用並驗證應用程式正確連接到應用程式見解後，將下載預定義的報表範本以分析 Power BI 桌面中的資料。 該報告包含預定義的圖表和表，可用於分析為搜索流量分析捕獲的其他資料。

1. 在 Azure 認知搜索儀表板左功能窗格中，在 **"設置"** 下，按一下 **"搜索流量分析**"。

1. 在 [搜尋流量分析]**** 頁面上的步驟 3 中，按一下 [取得 Power BI Desktop]**** 以安裝 Power BI。

   ![獲取電源 BI 報告](./media/search-traffic-analytics/get-use-power-bi.png "獲取電源 BI 報告")

1. 在同一頁上，按一下 **"下載電源 BI 報告**"。

1. 報告將在 Power BI 桌面中打開，系統將提示您連接到應用程式見解並提供憑據。 您可以在應用程式見解資源的 Azure 門戶頁中找到連接資訊。 對於憑據，請提供及閘戶登錄相同的使用者名和密碼。

   ![連線至 Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "連線至 Application Insights")

1. 按一下 **"載入**"。

此報表包含圖表和資料表，可幫助您做出更明智的決策，以改善您的搜尋效能和相關性。

計量包含下列項目：

+ 搜索量和最流行的術語文檔對：導致按一下同一文檔的術語，通過按一下排序。
+ 無點選搜尋︰註冊無點選的熱門查詢詞彙

以下螢幕截圖顯示了如果您使用了所有架構元素，則內置報表的外觀。

![為 Azure 認知搜索提供 BI 電源](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "為 Azure 認知搜索提供 BI 電源")

## <a name="next-steps"></a>後續步驟

檢測您的搜尋應用程式，取得搜尋服務的強大且詳細相關資料。

您可以在 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 上找到詳細資訊，以及瀏覽[價格頁面](https://azure.microsoft.com/pricing/details/application-insights/)頁面來深入了解其不同的服務層級。

深入了解如何建立令人讚嘆的報告。 有關詳細資訊[，請參閱使用 Power BI 桌面入門](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)。