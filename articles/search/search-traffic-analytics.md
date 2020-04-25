---
title: 搜尋流量分析的遙測
titleSuffix: Azure Cognitive Search
description: 啟用 Azure 認知搜尋的搜尋流量分析、收集遙測和使用者起始的事件（使用 Application Insights），然後分析 Power BI 報告中的結果。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 7c843b45b5a398aaaa1aab66f80961560477cf18
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82128094"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>收集搜尋流量分析的遙測資料

搜尋流量分析是一種模式，用來收集有關使用者與 Azure 認知搜尋應用程式互動的遙測資料，例如使用者起始的 click 事件和鍵盤輸入。 您可以使用這項資訊來判斷搜尋解決方案的有效性，包括熱門搜尋詞彙、點選連結率，以及哪些查詢輸入產生零結果。

此模式會相依于[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) （ [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/)的一項功能）來收集使用者資料。 您必須將檢測加入至您的用戶端程式代碼，如本文所述。 最後，您將需要一個報告機制來分析資料。 我們建議 Power BI，但您可以使用應用程式儀表板或任何連接至 Application Insights 的工具。

> [!NOTE]
> 本文中所述的模式適用于您新增至用戶端之程式碼所產生的先進案例和點選流資料。 相反地，服務記錄很容易設定、提供一系列的計量，而且可以在入口網站中完成，而不需要程式碼。 建議在所有案例中啟用記錄。 如需詳細資訊，請參閱[收集和分析記錄資料](search-monitor-logs.md)。

## <a name="identify-relevant-search-data"></a>識別相關的搜尋資料

若要讓搜尋流量分析具有有用的計量，必須從搜尋應用程式的使用者記錄一些信號。 這些信號代表使用者感興趣的內容，而且會考慮相關事項。 針對搜尋流量分析，其中包括：

+ 使用者產生的搜尋事件：只有使用者所起始的搜尋查詢才有趣。 用來填入 Facet、其他內容或任何內部資訊的搜尋要求並不重要，而且還會扭曲和偏差結果。

+ 使用者產生的 click 事件：在搜尋結果頁面上，click 事件通常表示檔是特定搜尋查詢的相關結果。

藉由連結搜尋並按一下相互關聯識別碼的事件，您可以更深入瞭解應用程式的搜尋功能的執行程度。

## <a name="add-search-traffic-analytics"></a>新增搜尋流量分析

在 Azure 認知搜尋服務的[入口網站](https://portal.azure.com)頁面中，[搜尋流量分析] 頁面包含遵循此遙測模式的功能提要表。 在此頁面中，您可以選取或建立 Application Insights 資源、取得檢測金鑰、複製可針對解決方案進行調整的程式碼片段，以及下載以模式反映的架構建立的 Power BI 報表。

![入口網站中的搜尋流量分析頁面](media/search-traffic-analytics/azuresearch-trafficanalytics.png "入口網站中的搜尋流量分析頁面")

## <a name="1---set-up-application-insights"></a>1-設定 Application Insights

選取現有的 Application Insights 資源，或[建立一個](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)（如果您還沒有的話）。 如果您使用 [搜尋流量分析] 頁面，您可以複製應用程式連線至 Application Insights 所需的檢測金鑰。

擁有 Application Insights 資源之後，您可以依照[支援的語言和平臺的指示](https://docs.microsoft.com/azure/azure-monitor/app/platforms)來註冊您的應用程式。 註冊只是將檢測金鑰從 Application Insights 新增至您的程式碼，以設定關聯。 當您選取現有的資源時，您可以在入口網站中或從 [搜尋流量分析] 頁面中找到該金鑰。

適用于某些 Visual Studio 專案類型的快捷方式會反映在下列步驟中。 它只需按幾下滑鼠，就能建立資源並註冊您的應用程式。

1. 針對 Visual Studio 和 ASP.NET 開發，開啟您的方案，然後選取 [**專案** > ] [**新增 Application Insights 遙測**]。

1. 按一下 [開始使用]****。

1. 藉由提供 Microsoft 帳戶、Azure 訂用帳戶和 Application Insights 資源（預設為新的資源）來註冊您的應用程式。 按一下 [註冊]  。

此時，您的應用程式已設定為進行應用程式監視，這表示所有頁面載入都會以預設計量進行追蹤。 如需上述步驟的詳細資訊，請參閱[啟用 Application Insights 伺服器端遙測](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio)。

## <a name="2---add-instrumentation"></a>2 - 新增檢測

在此步驟中，您會使用您在上述步驟中建立的 Application Insights 資源來檢測自己的搜尋應用程式。 此程式有四個步驟，從建立遙測用戶端開始。

### <a name="step-1-create-a-telemetry-client"></a>步驟1：建立遙測用戶端

建立物件，以將事件傳送至 Application Insights。 您可以將檢測新增至您的伺服器端應用程式程式碼或在瀏覽器中執行的用戶端程式代碼，以 c # 和 JavaScript 變體（如需其他語言，請參閱[支援的平臺和](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)架構的完整清單）。 選擇提供您所需資訊深度的方法。

伺服器端遙測會在應用層（例如在雲端中以 web 服務的形式執行的應用程式）或公司網路上的內部部署應用程式中，捕獲計量。 伺服器端遙測會捕捉搜尋和按一下事件、結果中的檔位置，以及查詢資訊，但您的資料收集範圍會限定在該圖層提供的任何資訊。

在用戶端上，您可能會有額外的程式碼來操作查詢輸入、加入導覽或包含內容（例如，從首頁和產品頁面起始的查詢）。 如果這描述您的解決方案，您可以選擇用戶端檢測，讓遙測反映出額外的詳細資料。 如何收集這項額外的詳細資料會超出此模式的範圍，但您可以查看[網頁 Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data)以取得更多方向。 

**使用 C#**

在 c # 中， **InstrumentationKey**會在您的應用程式設定中找到，例如，如果您的專案是 ASP.NET，則會在 appsettings 中。 如果您不確定金鑰位置，請回頭參閱註冊指示。

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

### <a name="step-2-request-a-search-id-for-correlation"></a>步驟2：要求相互關聯的搜尋識別碼

若要讓搜尋要求與按一下相互關聯，您必須具有相互關聯識別碼，以便與這兩個不同的事件相關聯。 當您使用 HTTP 標頭要求時，Azure 認知搜尋會為您提供搜尋識別碼。

擁有搜尋識別碼可讓 Azure 認知搜尋所發出的計量與要求本身相互關聯，並以您要登入的自訂計量 Application Insights。  

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

**使用 JavaScript （呼叫 REST Api）**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>步驟3：記錄搜尋事件

每次使用者發出搜尋要求時，您都應該以 Application Insights 自訂事件的下列架構，將其記錄為搜尋事件。 請記得只記錄使用者產生的搜尋查詢。

+ **SearchServiceName**：（字串）搜尋服務名稱
+ **SearchId**：（guid）搜尋查詢的唯一識別碼（位於搜尋回應中）
+ **IndexName**：（字串）搜尋要查詢的服務索引
+ **QueryTerms**：（字串）使用者輸入的搜尋字詞
+ **ResultCount**：（int）已傳回的檔數目（出現在搜尋回應中）
+ **ScoringProfile**：（字串）所使用評分設定檔的名稱（如果有的話）

> [!NOTE]
> 藉由將 $count = true 新增至您的搜尋查詢，來要求使用者產生的查詢計數。 如需詳細資訊，請參閱[搜尋檔（REST）](/rest/api/searchservice/search-documents#counttrue--false)。
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

### <a name="step-4-log-click-events"></a>步驟4：記錄點擊事件

每當使用者點選文件時，必須記錄這個訊號才可進行搜尋分析。 使用 Application Insights 自訂事件，記錄包含下列結構描述的這些事件：

+ **ServiceName**：（字串）搜尋服務名稱
+ **SearchId**：（guid）相關搜尋查詢的唯一識別碼
+ **DocId**：（字串）檔識別碼
+ **Position**：（int） [搜尋結果] 頁面中的檔排名

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

在您檢測應用程式並確認應用程式已正確連接到 Application Insights 之後，您可以下載預先定義的報表範本來分析 Power BI desktop 中的資料。 此報表包含預先定義的圖表和資料表，適用于分析針對搜尋流量分析所捕捉的額外資料。

1. 在 [Azure 認知搜尋] 儀表板的左側流覽窗格中，按一下 [**設定**] 底下的 [**搜尋流量分析**]。

1. 在 [搜尋流量分析]**** 頁面上的步驟 3 中，按一下 [取得 Power BI Desktop]**** 以安裝 Power BI。

   ![取得 Power BI 報表](./media/search-traffic-analytics/get-use-power-bi.png "取得 Power BI 報表")

1. 在相同的頁面上，按一下 [**下載 Power BI 報表**]。

1. 報表會在 Power BI Desktop 中開啟，而且系統會提示您連接到 Application Insights 並提供認證。 您可以在 Application Insights 資源的 [Azure 入口網站] 頁面中找到連接資訊。 針對 [認證]，提供您用來登入入口網站的相同使用者名稱和密碼。

   ![連線至 Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "連線至 Application Insights")

1. 按一下 [**載入**]。

此報表包含圖表和資料表，可幫助您做出更明智的決策，以改善您的搜尋效能和相關性。

計量包含下列項目：

+ 搜尋磁片區和最熱門的詞彙檔組：會導致相同檔被按下的詞彙，然後按一下即可排序。
+ 無點選搜尋︰註冊無點選的熱門查詢詞彙

下列螢幕擷取畫面顯示當您使用了所有的架構元素時，內建報告的外觀。

![適用于 Azure 認知搜尋的 Power BI 儀表板](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "適用于 Azure 認知搜尋的 Power BI 儀表板")

## <a name="next-steps"></a>後續步驟

檢測您的搜尋應用程式，取得搜尋服務的強大且詳細相關資料。

您可以在 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 上找到詳細資訊，以及瀏覽[價格頁面](https://azure.microsoft.com/pricing/details/application-insights/)頁面來深入了解其不同的服務層級。

深入了解如何建立令人讚嘆的報告。 如需詳細資訊，請參閱[開始使用 Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) 。