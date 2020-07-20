---
title: 快速入門：使用 Azure 監視器 Application Insights 監視網站
description: 在本快速入門中，您將了解如何設定使用 Azure 監視器 Application Insights 的用戶端/瀏覽器端網站監視。
ms.topic: quickstart
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: b47f3ce1ebed12d14dffd68e87dd013bb86218ea
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801632"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>快速入門：使用 Azure 監視器 Application Insights 開始監視您的網站

在本快速入門中，您將了解如何將開放原始碼 Application Insights JavaScript SDK 新增至您的網站。 您也將學習如何進一步了解網站訪客的用戶端/瀏覽器端體驗。

Azure Monitor Application Insights 可讓您輕鬆監視網站的可用性、效能和使用情形。 還可讓您快速識別並診斷應用程式的錯誤，不必等使用者回報。 Application Insights 提供伺服器端監視功能，以及用戶端/瀏覽器端監視功能。

## <a name="prerequisites"></a>Prerequisites

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* 可以新增 Application Insights JavaScript SDK 的網站。

## <a name="enable-application-insights"></a>啟用 Application Insights

Application Insights 可以從任何連上網際網路的應用程式 (不論是在內部部署環境或雲端執行) 收集遙測資料。 請使用下列步驟來檢視此資料：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取 [建立資源]   > [管理工具]   > [Application Insights]  。

   > [!NOTE]
   >如果這是您第一次建立 Application Insights 資源，請參閱[建立 Application Insights 資源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)。
1. 當設定方塊出現時，請使用下表來完成輸入欄位：

    | 設定        | 值           | 描述  |
   | ------------- |:-------------|:-----|
   | **名稱**      | 通用唯一值 | 用來識別所監視應用程式的名稱。 |
   | **資源群組**     | myResourceGroup      | 用來裝載 App Insights 資料的新資源群組名稱。 您可以建立新的資源群組，或使用現有的資源群組。 |
   | **位置** | 美國東部 | 選擇您附近或接近應用程式裝載位置的地點。 |
1. 選取 [建立]  。

## <a name="create-an-html-file"></a>建立 HTML 檔案

1. 在本機電腦上，建立名為 ``hello_world.html`` 的檔案。 在此範例中，檔案會建立在磁碟機 C 的根目錄上，因此看起來會類似 ``C:\hello_world.html``。
1. 請將下列指令碼複製並貼到 ``hello_world.html``：

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations, visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>設定 Application Insights SDK

1. 選取 [概觀]   > [基本資訊]  ，然後複製應用程式的 [檢測金鑰]  。

   ![新增 Application Insights 資源表單](media/website-monitoring/instrumentation-key-001.png)

1. 將下列指令碼新增到 ``hello_world.html`` 檔案中的 ``</head>`` 結束標記之前：

   ```javascript
   <script type="text/javascript">
      var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
      {
         instrumentationKey:"INSTRUMENTATION_KEY"
      }
      );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
   </script>
   ```

1. 編輯 ``hello_world.html`` 檔案並新增檢測金鑰。

1. 在本機瀏覽器工作階段中開啟 ``hello_world.html``。 此動作會建立單一頁面檢視。 您可以重新整理您的瀏覽器，以產生多個測試頁面檢視。

## <a name="monitor-your-website-in-the-azure-portal"></a>在 Azure 入口網站中監視您的網站

1. 在 Azure 入口網站中重新開啟 Application Insights 的 [概觀]  頁面，以檢視目前執行中應用程式的詳細資料。 [概觀]  頁面是您擷取檢測金鑰的位置。

   概觀頁面上的四個預設圖表都只限於伺服器端應用程式資料。 因為我們會檢測用戶端/瀏覽器端與 JavaScript SDK 的互動，所以此特定檢視就不適用，除非我們也安裝了用戶端 SDK。

1. 選取 [分析]  ![應用程式對應圖示](media/website-monitoring/006.png)。  此動作會開啟 **Analytics** 而提供豐富的查詢語言，用以分析 Application Insights 收集的所有資料。 若要檢視用戶端瀏覽器要求相關資料，請執行下列查詢：

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   ![經過一段時間的使用者要求分析圖表](./media/website-monitoring/analytics-query.png)

1. 請返回 [概觀]  頁面。 在 [調查]  標頭底下，選取 [瀏覽器]  ，然後選取 [效能]  。  與您網站效能相關的計量會隨即顯示。 其中有對應的檢視，可用來分析網站中的失敗和例外狀況。 您可以選取**範例**來存取[端對端交易詳細資料](../../azure-monitor/app/transaction-diagnostics.md)。

   ![伺服器計量圖表](./media/website-monitoring/browser-performance.png)

1. 若要開始探索[使用者行為分析工具](../../azure-monitor/app/usage-overview.md)，請從主要 Application Insights 功能表中，選取 [使用量]  標頭下的[**使用者**](../../azure-monitor/app/usage-segmentation.md)。 因為我們會從單一機器進行測試，所以只會看到一位使用者的資料。 對於即時網站，使用者的分布情形可能如下所示：

     ![使用者圖形](./media/website-monitoring/usage-users.png)

1. 針對更複雜的多頁面網站，您可以使用[**使用者流程**](../../azure-monitor/app/usage-flows.md)工具來追蹤造訪者瀏覽您網站各部分的路徑。

   ![使用者流程視覺效果](./media/website-monitoring/user-flows.png)

若要深入了解用於監視網站的進階組態，請參閱 [JavaScript SDK API 參考](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)。

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續進行其他快速入門或教學課程，請勿清除在此快速入門中建立的資源。 否則，請使用下列步驟，在 Azure 入口網站中刪除本快速入門所建立的所有資源。

> [!NOTE]
> 如果您使用現有的資源群組，下列指示將不適用。 相反地，您可以只刪除個別的 Application Insights 資源。 請記住，當您刪除資源群組時，屬於該群組的所有基礎資源也會一併刪除。

1. 在 Azure 入口網站的左側功能表中，選取 [資源群組]  ，然後選取 [myResourceGroup]  或暫存資源群組的名稱。
1. 在資源群組頁面上，選取 [刪除]  ，在文字方塊中輸入 **myResourceGroup**，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [尋找並診斷效能問題](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
