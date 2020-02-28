---
title: Azure 雲端服務的 Application Insight | Microsoft Docs
description: 使用 Application Insights 有效地監視您的 Web 和背景工作角色
ms.topic: conceptual
ms.date: 09/05/2018
ms.openlocfilehash: ce794a7bd18635fddfa30056ab2d675dc138097d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77655832"
---
# <a name="application-insights-for-azure-cloud-services"></a>Azure 雲端服務的 Application Insights
[Application Insights][start]可以藉由結合來自 Application Insights sdk 的資料與雲端服務的[Azure 診斷](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)資料，來監視[Azure 雲端服務應用程式](https://azure.microsoft.com/services/cloud-services/)的可用性、效能、失敗和使用方式。 當您取得有關應用程式在現實世界的效能和效率的意見反應時，您可以在每個開發生命週期中針對設計方向做出明智的抉擇。

![概觀儀表板](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>必要條件
在開始之前，您需要：

* [Azure](https://azure.com) 訂用帳戶。 使用 Windows、Xbox Live 或其他 Microsoft 雲端服務適用的 Microsoft 帳戶登入。 
* Microsoft Azure 工具 2.9 或更新版本。
* 開發人員分析工具 7.10 或更新版本。

## <a name="get-started-quickly"></a>即刻開始使用
使用 Application Insights 來監視您雲端服務的最快、最簡單方式就是在將服務發佈到 Azure 時選擇該選項。

![診斷設定頁面範例](./media/cloudservices/azure-cloud-application-insights.png)

此選項會在執行階段檢測您的應用程式，為您提供監視您 Web 角色中的要求、例外狀況及相依性時所需的一切遙測。 此外也會監視來自您背景工作角色的效能計數器。 您應用程式產生的所有診斷追蹤資料也會一併傳送給 Application Insights。

如果此選項就是您所需的一切，您就已大功告成。 

接下來的步驟是[從您的應用程式中查看計量](../../azure-monitor/app/metrics-explorer.md)，[流量分析查詢您的資料](../../azure-monitor/app/analytics.md)。 

若要在瀏覽器中監視效能，您可以設定[可用性測試](../../azure-monitor/app/monitor-web-app-availability.md)及[在網頁中新增程式碼](../../azure-monitor/app/javascript.md)。

下列各節將討論下列其他選項：

* 將來自各種元件和組建組態的資料傳送給個別資源。
* 新增來自您應用程式的自訂遙測。

## <a name="sample-app-instrumented-with-application-insights"></a>使用 Application Insights 檢測的範例應用程式
在這個 [範例應用程式](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)中，Application Insights 會新增到雲端服務，搭配兩個背景工作角色託管於 Azure 中。 

在下一節中，您將了解如何透過相同的方式來調整您的雲端服務專案。

## <a name="plan-resources-and-resource-groups"></a>規劃資源和資源群組
來自您應用程式的遙測將會在 Application Insights 類型的 Azure 資源中儲存、分析及顯示。 

每個資源只屬於一個資源群組。 資源群組用來在經過協調的單一交易中管理成本、將存取權授與小組成員，以及部署更新項目。 例如，您可以[撰寫指令碼來部署](../../azure-resource-manager/templates/deploy-powershell.md) Azure 雲端服務及其 Application Insights 監視資源，全部都在一個作業中完成。

### <a name="resources-for-components"></a>元件的資源
我們建議您建立為您應用程式的每個元件建立不同的資源。 也就是為每個 Web 角色和背景工作角色建立資源。 您可以個別分析每個元件，但建立一個[儀表板](../../azure-monitor/app/overview-dashboard.md)來彙總所有元件的重要圖表，讓您能夠在單一檢視中一起比較和監視這些圖表。 

替代方法是將來自多個角色的遙測傳送給同一個資源，但[為每個遙測項目新增一個維度屬性](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)來識別其來源角色。 在此方法中，計量圖表 (例如例外狀況) 通常會顯示來自各種角色之計數的彙總，但您可以視需要依角色識別碼分割該圖表。 您也可以依相同的維度來篩選搜尋。 此替代方案可讓您稍微容易同時檢視每個項目，但也會造成角色之間的一些混淆。

瀏覽器遙測通常包含在與其伺服器端 Web 角色相同的資源中。

請將各種元件的 Application Insights 資源放在一個資源群組中。 此方法讓您能夠輕鬆地一起管理它們。 

### <a name="separate-development-test-and-production"></a>區分開發、測試及生產環境
如果您是在上一個版本正在運作的情況下，為下一個功能開發自訂事件，您就會想要將開發遙測傳送給個別的 Application Insights 資源。 否則，會很難在來自即時網站的所有流量中找出您的測試遙測。

為了避免這種情況，請為您系統的每個組建組態或「戳記」(開發、測試、生產等) 建立個別的資源。 請將每個組建組態的資源放在個別的資源群組中。 

若要將遙測傳送給適當的資源，您可以安裝 Application Insights SDK，讓它根據組建組態挑選不同的檢測金鑰。 

## <a name="create-an-application-insights-resource-for-each-role"></a>為每個角色建立 Application Insights 資源

如果您已決定為每個角色建立個別的資源，或許也為每個組建組態建立一組個別的資源，則最簡單的方式就是全部都在 Application Insights 入口網站中建立。 如果您要建立很多資源，您可以[將程序自動化](../../azure-monitor/app/powershell.md)。

1. 在 [ [Azure 入口網站][portal]中，選取 [**新增** > **開發人員服務**] > **Application Insights**]。  

    ![Application Insights 窗格](./media/cloudservices/01-new.png)

1. 在 [應用程式類型] 下拉式清單中，選取 [ASP.NET Web 應用程式]。

每項資源均會以檢測金鑰來識別。 如果您想要手動設定或確認 SDK 的組態，稍後可能會需要此金鑰。


## <a name="set-up-azure-diagnostics-for-each-role"></a>設定每個角色的 Azure 診斷
設定此選項即可使用 Application Insights 來監視您的應用程式。 針對 Web 角色，此選項提供效能監視、警示、診斷及使用狀況分析。 針對其他角色，您可以搜尋和監視 Azure 診斷，例如重新啟動、效能計數器及對 System.Diagnostics.Trace 的呼叫。 

1. 在 Visual Studio [方案總管] 中，[ **\<YourCloudService>** ]  >  [角色] 下方，開啟每個角色的屬性。

1. 在 [組態] 中選取 [傳送診斷資料至 Application Insights] 核取方塊，然後選取您先前建立的 Application Insights 資源。

如果您已決定針對每個組建組態使用個別的 Application Insights 資源，請先選取組態。

![設定 Application Insights](./media/cloudservices/configure-azure-diagnostics.png)

這會將您的 Application insights 檢測金鑰插入名為 *ServiceConfiguration.\*.cscfg* 的檔案中。 以下為[範例程式碼](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg)。

如果您想要變更傳送到 Application Insights 的診斷資訊層級，[做法是直接編輯 *.cscfg* 檔案](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)。

## <a name="sdk"></a>在每個專案中安裝 SDK
使用此選項，您可以將自訂業務遙測新增至任何角色。 此選項會對您應用程式的使用與執行方式進行更精確的分析。

在 Visual Studio 中，設定每個雲端應用程式專案的 Application Insights SDK。

1. 若要設定 **Web 角色**，請以滑鼠右鍵按一下專案，然後選取 [設定 Application Insights] 或 [新增] > [Application Insights 遙測]。

1. 若要設定**背景工作角色**： 

    a. 以滑鼠右鍵按一下專案，然後選取 [管理 NuGet 套件]。

    b. 新增[適用於 Windows 伺服器的 Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)。

    ![搜尋「Application Insights」](./media/cloudservices/04-ai-nuget.png)

1. 若要設定 SDK 以將資料傳送給 Application Insights 資源：

    a. 在適合的啟動函式中，從 *.cscfg* 檔案中的組態設定設定檢測金鑰：
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    b. 對您應用程式中的每個角色重複「步驟 a」。 請參閱範例：
   
    * [Web 角色](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [背景工作角色](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [對於網頁](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. 將 *ApplicationInsights.config* 檔案設定為一律複製到輸出目錄。

   *.config* 檔案中的訊息會要求您將檢測金鑰放置在此處。 不過針對雲端應用程式，最好是從 *.cscfg* 檔案中設定。 此方法可確保角色會在入口網站中正確識別。

## <a name="set-up-status-monitor-to-collect-full-sql-queries-optional"></a>設定狀態監視器以收集完整的 SQL 查詢（選擇性）

只有當您想要在 .NET Framework 上捕獲完整的 SQL 查詢時，才需要執行此步驟。 

1. 在 `\*.csdef` 檔案中，為每個角色新增[啟動](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)工作，如下所示 

    ```xml
    <Startup>
      <Task commandLine="AppInsightsAgent\InstallAgent.bat" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="ApplicationInsightsAgent.DownloadLink" value="http://go.microsoft.com/fwlink/?LinkID=522371" />
          <Variable name="RoleEnvironment.IsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
    
2. 下載[InstallAgent](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.bat)和[InstallAgent](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.ps1)，將它們放入每個角色專案的 [`AppInsightsAgent`] 資料夾中。 請務必透過 Visual Studio 檔案屬性或組建腳本，將它們複製到輸出目錄。

3. 在所有背景工作角色上，新增環境變數： 

    ```xml
      <Environment>
        <Variable name="COR_ENABLE_PROFILING" value="1" />
        <Variable name="COR_PROFILER" value="{324F817A-7420-4E6D-B3C1-143FBED6D855}" />
        <Variable name="MicrosoftInstrumentationEngine_Host" value="{CA487940-57D2-10BF-11B2-A3AD5A13CBC0}" />
      </Environment>
    ```
    
## <a name="run-and-publish-the-app"></a>執行和發佈應用程式

1. 執行應用程式，並且登入 Azure。 

1. 開啟您建立的 Application Insights 資源。

   個別資料點會顯示在[搜尋][diagnostic]中，彙總的資料則會顯示在[計量瀏覽器](../../azure-monitor/app/metrics-explorer.md)中。

1. 新增更多遙測 (請參閱以下章節)，然後再發佈應用程式以取得即時診斷和使用方式的意見反應。 

如果沒有任何資料，請執行下列作業：

1. 若要查看個別事件，請開啟 [[搜尋][diagnostic]] 磚。
1. 在應用程式中開啟各種頁面，以產生一些遙測。
1. 請稍等片刻，然後按一下 [重新整理]。  

如需詳細資訊，請參閱 [疑難排解][qna]。

## <a name="view-azure-diagnostics-events"></a>檢視 Azure 診斷事件
您可以在 Application Insights 的下列位置中，找到 [Azure 診斷](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)資訊：

* 效能計數器顯示為自訂度量。 
* Windows 事件記錄顯示為追蹤和自訂事件。
* 應用程式記錄、ETW 記錄和任何診斷基礎結構記錄顯示為追蹤。

若要查看效能計數器和事件計數，請開啟[計量瀏覽器](../../azure-monitor/app/metrics-explorer.md) 並新增下列圖表：

![Azure 診斷資料](./media/cloudservices/23-wad.png)

若要搜尋 Azure 診斷所傳送的各種追蹤記錄檔，請使用[搜尋](../../azure-monitor/app/diagnostic-search.md)或[分析查詢](../../azure-monitor/log-query/get-started-portal.md)。 例如，假設您有未處理的例外狀況導致角色損毀並需要回收。 這項資訊會出現在 Windows 事件記錄檔的「應用程式 (Application)」通道。 您可以使用 [搜尋] 來檢視 Windows 事件記錄檔錯誤，並取得例外狀況的完整堆疊追蹤。 這將協助您找出問題的根本原因。

![Azure 診斷搜尋](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>更多遙測
以下各節說明如何取得您應用程式各種層面的其他遙測。

## <a name="track-requests-from-worker-roles"></a>從背景工作角色追蹤要求
在 Web 角色中，要求模組會自動收集有關 HTTP 要求的資料。 如需有關如何覆寫預設收集行為的範例，請參閱 [範例 MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole) \(英文\)。 

您可以藉由與追蹤 HTTP 要求相同的方式追蹤背景工作角色，來擷取背景工作角色呼叫的效能。 在 Application Insights 中，要求遙測類型會測量一個單位的具名伺服器端工作，可以進行計時，而且可以獨立成功或失敗。 雖然 SDK 會自動擷取 HTTP 要求，您可以插入自己的程式碼，來追蹤對背景工作角色的要求。

查看兩個供用於回報要求的範例背景工作角色： 
* [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>例外狀況
如需如何從各種 Web 應用程式類型收集未處理例外狀況的資訊，請參閱[在 Application Insights 中監視例外狀況](../../azure-monitor/app/asp-net-exceptions.md)。

範例 Web 角色具有 MVC5 以及 Web API 2 控制器。 來自這兩者的未處理例外狀況是使用下列處理常式擷取：

* 針對 MVC5 控制器設定的 [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs)[如此範例所示](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* 針對 Web API 2 控制器設定的 [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs)，[如此範例所示](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

針對背景工作角色，有兩種方式可追蹤例外狀況：

* 使用 TrackException(ex)。
* 如果您已新增 Application Insights 追蹤接聽項 NuGet 套件，您可以使用 System.Diagnostics.Trace 來記錄例外狀況，[如此範例所示](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)。

## <a name="performance-counters"></a>效能計數器
根據預設會收集下列計數器：

* \Process （？APP_WIN32_PROC？？）\% 處理器時間
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

若是 Web 角色，也會收集這些計數器︰

* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

您可以藉由編輯*ApplicationInsights*來指定額外的自訂或其他 Windows 效能計數器，[如下列範例所示](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14)。

  ![效能計數器](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>背景工作角色的相互關聯遙測
若您有豐富的診斷經驗，您可以檢視導致失敗或高延遲要求的原因。 透過 Web 角色，SDK 會在關聯的遙測間自動設定相互關聯。 

若要達成此檢視背景工作角色的目的，您可以使用自訂遙測初始設定式，為所有遙測設定一個通用 Operation.Id 內容屬性。 這可讓您一目了然延遲/失敗問題是相依性或程式碼所造成的。 

方法：

* 設定相互關聯識別碼到 CallContext 中，如[此範例所示](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36)。 在此案例中，我們使用「要求 ID」做為相互關聯識別碼。
* 新增自訂的 TelemetryInitializer 實作，其會將 Operation.Id 設為前面所設定的相互關聯識別碼。 例如，請見 [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)。
* 新增自訂的遙測初始設定式。 您可以在 *ApplicationInsights.config* 檔案或程式碼中執行此作業，[如此範例所示](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233)。

## <a name="client-telemetry"></a>用戶端遙測
若要取得以瀏覽器為基礎的遙測資料，例如網頁檢視計數、頁面載入時間或腳本例外狀況，並在頁面腳本中撰寫自訂遙測，請參閱[將 JAVASCRIPT SDK 新增至您的網頁][client]。

## <a name="availability-tests"></a>可用性集合
若要確保您的應用程式保持上線並回應，請[設定 web 測試][availability]。

## <a name="display-everything-together"></a>將所有內容一起顯示
如需您系統的整體情況，您可以將重要的監視圖表在一個[儀表板](../../azure-monitor/app/overview-dashboard.md)上一起顯示。 例如，您可以釘選每個角色的要求和失敗計數。 

如果您的系統使用其他 Azure 服務 (例如「串流分析」)，請將它們的監視圖表一併納入。 

如果您有用戶端行動應用程式，請使用 [App Center](../../azure-monitor/learn/mobile-center-quickstart.md)。 請在[分析](../../azure-monitor/app/analytics.md)中建立查詢以顯示事件計數，並將它們釘選到儀表板。

## <a name="example"></a>範例
[此範例](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) 監視具有 Web 角色和兩個背景工作角色的服務。

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>在 Azure 雲端服務中執行時發生的「找不到方法」例外狀況
您是否已針對 .NET 4.6 組建？ Azure 雲端服務角色不自動支援 .NET 4.6。 [在每個角色上安裝 .NET 4.6](../../cloud-services/cloud-services-dotnet-install-dotnet.md)，再執行應用程式。

## <a name="video"></a>視訊

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>後續步驟
* [設定將 Azure 診斷傳送至 Application Insights](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [自動建立 Application Insights 資源](../../azure-monitor/app/powershell.md)
* [自動執行 Azure 診斷](../../azure-monitor/app/powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[azure]: ../../azure-monitor/app/app-insights-overview.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md 
