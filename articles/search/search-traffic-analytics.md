---
title: 搜尋流量分析的遙測
titleSuffix: Azure Cognitive Search
description: 啟用 Azure 認知搜尋的搜尋流量分析、使用 Application Insights 收集遙測和使用者起始的事件，然後分析 Power BI 報告中的結果。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 6ab32a2ccb4c7eb79309798c2b53d326723ad6ea
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420068"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>收集搜尋流量分析的遙測資料

搜尋流量分析是一種模式，用來收集有關使用者與 Azure 認知搜尋應用程式互動的遙測資料，例如使用者起始的按一下事件和鍵盤輸入。 您可以使用此資訊來判斷搜尋解決方案的有效性，包括熱門搜尋字詞、點選連結率，以及哪些查詢輸入未產生任何結果。

此模式會相依於 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) ([Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/)的一個功能)，以收集使用者資料。 您必須如此文章所述，在用戶端程式碼中加入檢測設備。 最後，您將需要一個報告機制來分析資料。 我們建議使用 Power BI，但您可以使用應用程式儀表板，或任何連線至 Application Insights 的工具。

> [!NOTE]
> 此文章中描述的模式適用於您新增至用戶端的程式碼所產生的進階案例和點選流資料。 服務記錄相對容易設定，而且可提供一系列的計量，還可以在入口網站中完成，不需要撰寫程式碼。 因此無論是什麼情況，都建議啟用記錄。 如需詳細資訊，請參閱[收集和分析記錄資料](search-monitor-logs.md)。

## <a name="identify-relevant-search-data"></a>識別相關的搜尋資料

若要讓搜尋流量分析產生有用的計量，必須針對搜尋應用程式的使用者記錄一些訊號。 這些訊號代表使用者感興趣且認為與其相關的內容。 針對搜尋流量分析，這些訊號包括：

+ 使用者產生的搜尋事件：只有使用者起始的搜尋查詢才有意義。 用來填入 Facet、其他內容或任何內部資訊的搜尋要求並不重要，而且還會扭曲和偏差結果。

+ 使用者產生的按一下事件：在搜尋結果頁面上，點選事件通常表示文件為特定搜尋查詢的相關結果。

透過相互關聯識別碼來連結搜尋與按一下事件，您就可以更深入了解應用程式搜尋功能的性能。

## <a name="add-search-traffic-analytics"></a>新增搜尋流量分析

在 Azure 認知搜尋服務的[入口網站](https://portal.azure.com)頁面中，搜尋流量分析頁面包含了遵循此遙測模式的速查表。 您可以從這個頁面，選取或建立 Application Insights 資源、取得檢測金鑰、複製可針對解決方案調整的程式碼片段，以及下載依據模式中反映的結構描述所建置的 Power BI 報表。

![入口網站中的搜尋流量分析頁面](media/search-traffic-analytics/azuresearch-trafficanalytics.png "入口網站中的搜尋流量分析頁面")

## <a name="1---set-up-application-insights"></a>1 - 設定 Application Insights

請選取現有的 Application Insights 資源，或者如果您還沒有該個資源，請[建立一個](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)。 如果您使用 [搜尋流量分析] 頁面，可以複製應用程式連線 Application Insights 時所需的檢測金鑰。

擁有 Application Insights 資源之後，就可以遵循[支援的語言和平台的相關指示](https://docs.microsoft.com/azure/azure-monitor/app/platforms)來註冊您的應用程式。 註冊方式很簡單，只要將檢測金鑰從 Application Insights 新增到會設定關聯的程式碼中就完成了。 當您選取現有的資源時，可以在入口網站或 [搜尋流量分析] 頁面中找到金鑰。

後續步驟中會反映出某些 Visual Studio 專案類型可使用的快捷方式。 該方式只需按幾下滑鼠，就能建立資源並註冊您的應用程式。

1. 針對 Visual Studio 和 ASP.NET 開發，請開啟您的解決方案並選取 [專案] > [新增 Application Insights 遙測]。

1. 按一下 [開始使用]。

1. 透過提供 Microsoft 帳戶、Azure 訂用帳戶和 Application Insights 資源 (預設為新的資源) 來註冊您的應用程式。 按一下 [註冊] 。

此時，您的應用程式已設定為進行應用程式監視，這表示所有頁面載入作業都會以預設計量進行追蹤。 如需和上述步驟有關的詳細資訊，請參閱[啟用 Application Insights 伺服器端遙測](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio)。

## <a name="2---add-instrumentation"></a>2 - 新增檢測

您可以在這個步驟使用您在上述步驟中建立的 Application Insights 資源，來檢測自己的搜尋應用程式。 此程序從建立遙測用戶端開始，共包含四個步驟。

### <a name="step-1-create-a-telemetry-client"></a>步驟 1:建立遙測用戶端

建立會將事件傳送至 Application Insights 的物件。 您可以將檢測設備新增到伺服器端應用程式程式碼，或新增到在瀏覽器中執行的用戶端程式碼，這裡以 C# 和 JavaScript 變體來表示 (針對其他語言，請參閱[支援的平台和架構](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) \(部分機器翻譯\) 的完整清單)。 請選擇可提供您所需資訊深度的方法。

伺服器端遙測會在應用程式層 (例如，在雲端以 Web 服務形式，或在公司網路中以內部部署應用程式形式執行的應用程式) 擷取計量。 伺服器端遙測會擷取搜尋和按一下事件、文件在結果中的位置，以及查詢資訊，但您的資料收集範圍將會限定在該層所提供的任何資訊。

在用戶端上，您可能會有額外的程式碼，它們或許能夠操作查詢輸入、新增瀏覽，或包含內容 (例如，從首頁和產品頁面起始的查詢)。 如果您的解決方案類似上面所述情況，可以選擇使用用戶端檢測，讓遙測反映出額外的詳細資料。 有關如何收集這個額外詳細資料的問題，已經超出此模式的範圍，但您可以查看[網頁適用的 Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data) 以獲得更多指引。 

**使用 C#**

如果使用 C#，就會在您的應用程式設定中找到 **InstrumentationKey**，例如，如果專案為 ASP.NET，就會找到 appsettings.json。 如果您不確定金鑰位置，請回去參閱註冊指示。

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

### <a name="step-2-request-a-search-id-for-correlation"></a>步驟 2:要求相互關聯的搜尋識別碼

若要透過點選將搜尋要求相互關聯，必須擁有一組與這兩個不同事件相關的相互關聯識別碼。 當您使用 HTTP 標頭要求取得搜尋識別碼時，Azure 認知搜尋會提供您一組搜尋識別碼。

擁有搜尋識別碼可讓 Azure 認知搜尋針對要求發出的計量，與您在 Application Insights 中記錄的自訂計量相互關聯。  

**使用 C#**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**使用 JavaScript (呼叫 REST API)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>步驟 3：記錄搜尋事件

每當使用者發出搜尋要求時，您應該將其記錄為搜尋事件，並包含下列關於 Application Insights 自訂事件的結構描述。 請記得只記錄使用者產生的搜尋查詢。

+ **SearchServiceName**：(字串) 搜尋服務名稱
+ **SearchId**：(guid) 搜尋查詢的唯一識別碼 (出現在搜尋回應中)
+ **IndexName**：(字串) 要查詢的搜尋服務索引
+ **QueryTerms**：(字串) 使用者輸入的搜尋字詞
+ **ResultCount**：(int) 傳回文件的數目 (出現在搜尋回應中)
+ **ScoringProfile**：(字串) 所使用評分設定檔的名稱 (如果有的話)

> [!NOTE]
> 請將 $count=true 新增至搜尋查詢，以要求使用者所產生查詢的計數。 如需詳細資訊，請參閱[搜尋文件 (REST)](/rest/api/searchservice/search-documents#counttrue--false)。
>

**使用 C#**

```csharp
var properties = new Dictionary <string, string> 
{
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

### <a name="step-4-log-click-events"></a>步驟 4：記錄按一下事件

每當使用者點選文件時，必須記錄這個訊號才可進行搜尋分析。 使用 Application Insights 自訂事件，記錄包含下列結構描述的這些事件：

+ **ServiceName**：(字串) 搜尋服務名稱
+ **SearchId**：(guid) 相關搜尋查詢的唯一識別碼
+ **DocId**：(字串) 文件識別碼
+ **Position**：(int) 搜尋結果頁面中文件的順位

> [!NOTE]
> 位置是指您應用程式中的基本順序。 您可以自由地設定這個數字 (只要它一律相同) 以便進行比較。
>

**使用 C#**

```csharp
var properties = new Dictionary <string, string> 
{
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

在您完成您的應用程式檢測，並確認應用程式已正確地連線至 Application Insights 之後，可以下載預先定義的報表範本來分析 Power BI Desktop 中的資料。 此報表包含預先定義的圖表和資料表，可用來分析針對搜尋流量分析擷取的額外資料。

1. 在「Azure 認知搜尋」儀表板左側導覽窗格中的 [設定] 底下，按一下 [搜尋流量分析]。

1. 在 [搜尋流量分析] 頁面上的步驟 3 中，按一下 [取得 Power BI Desktop] 以安裝 Power BI。

   ![取得 Power BI 報表](./media/search-traffic-analytics/get-use-power-bi.png "取得 Power BI 報表")

1. 在同一頁面上，按一下 [下載 Power BI 報表]。

1. 報表會在 Power BI Desktop 中開啟，且系統會提示您連線至 Application Insights 並提供認證。 您可以在 Azure 入口網站頁面中，找到您 Application Insights 資源的連線資訊。 針對認證，請提供您用來登入入口網站的相同使用者名稱與密碼。

   ![連線至 Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "連線至 Application Insights")

1. 按一下 [載入]。

此報表包含圖表和資料表，可幫助您做出更明智的決策，以改善您的搜尋效能和相關性。

計量包含下列項目：

+ 搜尋磁碟區和最熱門的字詞文件組︰依點選排序，造成點選相同文件的字詞。
+ 無點選搜尋︰註冊無點選的熱門查詢詞彙

下列螢幕擷取畫面顯示當您使用了所有的結構描述元素時，內建報表的外觀。

![Azure 認知搜尋的 Power BI 儀表板](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Azure 認知搜尋的 Power BI 儀表板")

## <a name="next-steps"></a>後續步驟

檢測您的搜尋應用程式，取得搜尋服務的強大且詳細相關資料。

您可以在 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 上找到詳細資訊，以及瀏覽[價格頁面](https://azure.microsoft.com/pricing/details/application-insights/)頁面來深入了解其不同的服務層級。

深入了解如何建立令人讚嘆的報告。 如需詳細資訊，請參閱[開始使用 Power BI Desktop](https://docs.microsoft.com/power-bi/fundamentals/desktop-getting-started)。
