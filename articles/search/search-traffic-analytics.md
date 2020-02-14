---
title: 搜尋流量分析資料的報告
titleSuffix: Azure Cognitive Search
description: 啟用 Azure 認知搜尋的搜尋流量分析、收集遙測和使用者起始的事件（使用 Application Insights），然後分析 Power BI 報告中的結果。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 84e60b0a942bad94d8e36eb20b5be8e3f55af80a
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190949"
---
# <a name="implement-search-traffic-analytics-in-azure-cognitive-search"></a>在 Azure 認知搜尋中執行搜尋流量分析

搜尋流量分析是一種模式，可用來實作搜尋服務的意見反應管道。 目標是要收集使用者起始的 click 事件和鍵盤輸入的遙測。 您可以使用這項資訊來判斷搜尋解決方案的有效性，包括熱門搜尋詞彙、點選連結率，以及哪些查詢輸入產生零結果。

此模式會相依于[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) （ [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/)的一項功能）來收集使用者資料。 您也必須將檢測新增至您的用戶端程式代碼，如本文所述。 最後，您將需要一個報告機制來分析資料。 我們建議 Power BI，但是您可以使用任何連接至 Application Insights 的工具。

> [!NOTE]
> 本文中所述的模式適用于用戶端所產生的先進案例和點選流資料。 或者，您可以報告搜尋服務所產生的記錄資訊。 如需服務記錄檔報告的詳細資訊，請參閱[監視資源耗用量和查詢活動](search-monitor-usage.md)。

## <a name="identify-relevant-search-data"></a>識別相關的搜尋資料

如需取得實用的搜尋計量，就必須從搜尋應用程式的使用者記錄一些訊號。 這些訊號代表使用者感興趣且認為與其需求相關的內容。

搜尋流量分析需要兩個訊號︰

+ 使用者產生的搜尋事件：只有使用者所起始的搜尋查詢才有趣。 用來填入 Facet、其他內容或任何內部資訊的搜尋要求並不重要，而且還會扭曲和偏差結果。

+ 使用者產生的 click 事件：藉由按一下此檔，我們會參考使用者選取搜尋查詢所傳回的特定搜尋結果。 點選通常表示文件為特定搜尋查詢的相關結果。

藉由連結搜尋並按一下具有相互關聯識別碼的事件，您就可以分析應用程式上使用者的行為。 僅使用搜尋流量記錄並無法取得這些搜尋深入分析。

## <a name="add-search-traffic-analytics"></a>新增搜尋流量分析

必須從搜尋應用程式收集前一節所述的訊號，因為使用者會與其進行互動。 Application Insights 是可延伸的監視解決方案，適用於多個平台，並具有彈性的檢測選項。 使用 Application Insights 可讓您利用 Azure 認知搜尋所建立的 Power BI 搜尋報告，讓資料的分析變得更容易。

在 Azure 認知搜尋服務的[入口網站](https://portal.azure.com)頁面中，[搜尋流量分析] 頁面包含遵循此遙測模式的功能提要表。 您也可以選取或建立 Application Insights 資源，並查看必要的資料，這些全都放在同一個位置。

![搜尋流量分析指示][1]

## <a name="1---select-a-resource"></a>1 - 選取資源

如果您還沒有 Application Insights 資源，則必須選取 Application Insights 資源後，才可以加以使用或建立。 您可以使用已在使用中的資源，才可記錄必要的自訂事件。

在建立新的 Application Insights 資源時，所有應用程式類型都適用於這種情況。 選取最適合您所使用平台的 Application Insights 資源。

您需要檢測金鑰來建立您應用程式的遙測用戶端。 您可以從 Application Insights 入口網站儀表板取得，或可以在搜尋流量分析頁面上，選取您想要使用的執行個體來加以取得。

## <a name="2---add-instrumentation"></a>2 - 新增檢測

在此步驟中，您會使用您在上述步驟中建立的 Application Insights 資源來檢測自己的搜尋應用程式。 這個程序有四個步驟︰

**步驟1：建立遙測用戶端**

這是將事件傳送至 Application Insights 資源的物件。

*C#*

    private TelemetryClient telemetryClient = new TelemetryClient();
    telemetryClient.InstrumentationKey = "<YOUR INSTRUMENTATION KEY>";

*JavaScript*

    <script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
    ({
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
    });
    window.appInsights=appInsights;
    </script>

如需了解其他語言和平台，請參閱完整的[清單](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)。

**步驟2：要求相互關聯的搜尋識別碼**

若要讓搜尋要求與按一下相互關聯，您必須具有相互關聯識別碼，以便與這兩個不同的事件相關聯。 當您使用標頭要求時，Azure 認知搜尋會為您提供搜尋識別碼：

*C#*

    // This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
    var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
    var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
    IEnumerable<string> headerValues;
    string searchId = string.Empty;
    if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out headerValues)){
     searchId = headerValues.FirstOrDefault();
    }

*JavaScript*

    request.setRequestHeader("x-ms-azs-return-searchid", "true");
    request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
    var searchId = request.getResponseHeader('x-ms-azs-searchid');

**步驟3：記錄搜尋事件**

每當使用者發出搜尋要求時，您應該將其記錄為搜尋事件，並包含下列關於 Application Insights 自訂事件的結構描述︰

**SearchServiceName**：（字串）搜尋服務名稱**SearchId**：（guid）搜尋查詢的唯一識別碼（出現在搜尋回應中） **IndexName**：（字串）搜尋要查詢的服務索引**QueryTerms**：（字串）使用者**ResultCount**：（int）所輸入的檔數目（出現在搜尋回應中） **ScoringProfile**：（字串）所使用評分設定檔的名稱（如果有的話）

> [!NOTE]
> 將 $count=true 新增至搜尋查詢，要求使用者所產生查詢的計數。 請參閱[這裡](/rest/api/searchservice/search-documents#counttrue--false)的詳細資訊。
>

> [!NOTE]
> 請記得，務必只記錄使用者所產生的搜尋查詢。
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
    telemetryClient.TrackEvent("Search", properties);

*JavaScript*

    appInsights.trackEvent("Search", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    IndexName: <index name>,
    QueryTerms: <search terms>,
    ResultCount: <results count>,
    ScoringProfile: <scoring profile used>
    });

**步驟4：記錄點擊事件**

每當使用者點選文件時，必須記錄這個訊號才可進行搜尋分析。 使用 Application Insights 自訂事件，記錄包含下列結構描述的這些事件：

**ServiceName**：(字串) 搜尋服務名稱 **SearchId**：(GUID) 相關搜尋查詢的唯一識別碼 **DocId**：(字串) 文件識別碼 **Position**(int) 文件在搜尋結果頁面中的排名

> [!NOTE]
> 位置是指您應用程式中的基本順序。 您可以自由地設定這個數字 (只要它一律相同) 以便進行比較。
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
    telemetryClient.TrackEvent("Click", properties);

*JavaScript*

    appInsights.trackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

## <a name="3---analyze-in-power-bi"></a>3 - 在 Power BI 中分析

在您檢測應用程式並確認應用程式已正確連接到 Application Insights 之後，您可以下載預先定義的報表範本來分析 Power BI desktop 中的資料。 此報表包含預先定義的圖表和資料表，適用于分析針對搜尋流量分析所捕捉的額外資料。 

1. 在 [Azure 認知搜尋] 儀表板的左側流覽窗格中，按一下 [**設定**] 底下的 [**搜尋流量分析**]。

2. 在 [搜尋流量分析] 頁面上的步驟 3 中，按一下 [取得 Power BI Desktop] 以安裝 Power BI。

   ![取得 Power BI 報表](./media/search-traffic-analytics/get-use-power-bi.png "取得 Power BI 報表")

2. 在相同的頁面上，按一下 [**下載 Power BI 報表**]。

3. 報表會在 Power BI Desktop 中開啟，而且系統會提示您連接到 Application Insights 並提供認證。 您可以在 Application Insights 資源的 [Azure 入口網站] 頁面中找到連接資訊。 針對 [認證]，提供您用來登入入口網站的相同使用者名稱和密碼。

   ![連接到 Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "連接到 Application Insights")

4. 按一下 [載入]。

此報表包含圖表和資料表，可幫助您做出更明智的決策，以改善您的搜尋效能和相關性。

計量包含下列項目：

* 搜尋磁片區和最熱門的詞彙檔組：會導致相同檔被按下的詞彙，然後按一下即可排序。
* 無點選搜尋︰註冊無點選的熱門查詢詞彙

以下螢幕擷取畫面示範用於分析搜尋流量分析的內建報表和圖表。

![適用于 Azure 認知搜尋的 Power BI 儀表板](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "適用于 Azure 認知搜尋的 Power BI 儀表板")

## <a name="next-steps"></a>後續步驟
檢測您的搜尋應用程式，取得搜尋服務的強大且詳細相關資料。

您可以在 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 上找到詳細資訊，以及瀏覽[價格頁面](https://azure.microsoft.com/pricing/details/application-insights/)頁面來深入了解其不同的服務層級。

深入了解如何建立令人讚嘆的報告。 如需詳細資訊，請參閱[開始使用 Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) 。

<!--Image references-->
[1]: ./media/search-traffic-analytics/azuresearch-trafficanalytics.png
[2]: ./media/search-traffic-analytics/azuresearch-appinsightsdata.png
[3]: ./media/search-traffic-analytics/azuresearch-pbitemplate.png
