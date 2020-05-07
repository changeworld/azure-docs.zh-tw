---
title: 在 Azure 應用程式深入解析中分隔遙測
description: 將遙測導向開發、測試和生產戳記的不同資源。
ms.topic: conceptual
ms.date: 04/29/2020
ms.openlocfilehash: 92a1bb6cb0bb73ac67d38eeba5bd3cdafacf8b56
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562146"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>區分開發、測試及生產環境的遙測

當您在開發下一版 Web 應用程式時，您不會想看到新版與已發行版本的 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 遙測混合在一起。 為了避免混淆，請將不同開發階段的遙測，以不同的檢測金鑰 (ikey) 傳送到不同的 Application Insights 資源。 為了能夠在版本移到另一個階段時更輕鬆地變更檢測金鑰，您可以將 ikey 設定在程式碼中 (而不是設定在組態檔中)。 

(如果您的系統是「Azure 雲端服務」，有[另一個設定個別 ikey 的方法](../../azure-monitor/app/cloudservices.md))。

## <a name="about-resources-and-instrumentation-keys"></a>關於資源和檢測金鑰

在為 Web 應用程式設定 Application Insights 監視時，您會在 Microsoft Azure 中建立 Application Insights 資源**。 您可以在 Azure 入口網站開啟此資源，以便查看並分析從應用程式中收集到的遙測資料。 透過「檢測金鑰」**(iKey) 即可識別資源。 當您安裝 Application Insights 套件來監視應用程式時，您必須為它設定檢測金鑰，以便讓它知道要將遙測資料傳送到哪裡。

每個 Application Insights 資源都隨附現成可用的計量。 如果完全不同的元件會回報給相同的 Application Insights 資源，這些計量對的儀表板/警示可能沒有意義。

### <a name="use-a-single-application-insights-resource"></a>使用單一 Application Insights 資源

-   適用于一起部署的應用程式元件。 通常由一組相同的 DevOps/ITOps 使用者所管理的單一小組所開發。
-   如果在預設情況下匯總關鍵效能指標（Kpi）（例如回應持續時間、儀表板中的失敗率等等），則會在所有這些指標（您可以選擇依角色名稱分割計量瀏覽器體驗）。
-   如果不需要在應用程式元件之間以不同的方式管理以角色為基礎的存取控制（RBAC）。
-   如果您不需要不同元件之間的計量警示準則。
-   如果您不需要在元件之間以不同的方式管理連續匯出。
-   如果您不需要在元件之間以不同的方式管理計費/配額。
-   如果可以讓 API 金鑰具有與所有元件中的資料相同的存取權。 而10個 API 金鑰就足以滿足其所有需求。
-   如果可以在所有角色之間擁有相同的智慧偵測和工作專案整合設定。

### <a name="other-things-to-keep-in-mind"></a>要記住的其他事項

-   您可能需要加入自訂程式碼，以確保有意義的值會設定為[Cloud_RoleName](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net#set-cloud-role-name)屬性。 若未針對此屬性設定有意義的值，則不會有*任何*入口網站體驗可供使用。
- 針對 Service Fabric 應用程式和傳統雲端服務，SDK 會自動從 Azure 角色環境讀取並設定這些。 對於所有其他類型的應用程式，您可能需要明確地設定。
-   即時計量體驗不支援依角色名稱分割。

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> 動態檢測金鑰

若要能夠在程式碼歷經不同生產階段時更輕鬆地變更 ikey，請將 ikey 設定在程式碼中 (而不是設定在組態檔中)。

在初始化方法中設定金鑰，例如 ASP.NET 服務中的 global.aspx.cs：

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

在此範例中，不同資源的 ikeys 會放置在不同版本的 Web 組態檔中。 交換 Web 組態檔 (您可以在發行指令碼中進行) 將會交換目標資源。

### <a name="web-pages"></a>網頁
IKey 也會用於您的應用程式網頁中，[從 [快速入門] 窗格所得到的腳本](../../azure-monitor/app/javascript.md)。 不要按其原義編寫至指令碼，請從伺服器狀態產生。 例如，在 ASP.NET 應用程式中：

*Razor 中的 JavaScript*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>建立其他 Application Insights 資源

若要建立 Application Insights 資源，請遵循[資源建立指南](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)。

### <a name="getting-the-instrumentation-key"></a>取得檢測金鑰
檢測金鑰會識別您所建立的資源。

您將需要您的應用程式會將資料傳送至其中的所有資源的檢測金鑰。

## <a name="filter-on-build-number"></a>篩選組建編號
當您發佈新的 App 版本時，希望能夠將不同組建的遙測分開。

您可以設定 [應用程式版本] 屬性，如此便能篩選[搜尋](../../azure-monitor/app/diagnostic-search.md)和[計量總管](../../azure-monitor/platform/metrics-charts.md)的結果。

設定 [應用程式版本] 屬性有幾種不同的方法。

* 直接設定：

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* 在 [遙測初始設定式](../../azure-monitor/app/api-custom-events-metrics.md#defaults) 中將該行換行，以確保會以一致性方式設定所有 TelemetryClient 執行個體。
* [ASP.NET] 在 `BuildInfo.config`中設定版本。 Web 模組將會從 BuildLabel 節點取得版本。 在您的專案中包含此檔案， 而且記得要在 [方案總管] 中設定 [永遠複製] 屬性。

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] 在 MSBuild 中自動產生 BuildInfo.config。 若要這樣做，請先在 `.csproj` 檔案中加入幾行：

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    這會產生名為*yourProjectName*的檔案。Buildinfo.config。發佈程式會將它重新命名為 Buildinfo.config。

    當您使用 Visual Studio 建置時，組建標籤會包含預留位置 (AutoGen_...)。 但是當使用 MSBuild 建立時，則會填入正確的版本號碼。

    若要允許 MSBuild 產生版本號碼，請在 AssemblyReference.cs 中設定類似 `1.0.*` 的版本

## <a name="version-and-release-tracking"></a>版本和版次追蹤
若要追蹤應用程式版本，請確定您的 Microsoft Build Engine 程序已產生 `buildinfo.config`。 在您`.csproj`的檔案中，新增：  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

當它有組建資訊時，Application Insights Web 模組會自動新增 **應用程式版本** ，做為每個遙測項目的屬性。 如此可讓您在執行[診斷搜尋](../../azure-monitor/app/diagnostic-search.md)或在[探索計量](../../azure-monitor/platform/metrics-charts.md)時，依據版本來篩選。

不過，請注意，組建版本號碼只會由 Microsoft Build Engine 產生，而不是由 Visual Studio 的開發人員組建產生。

### <a name="release-annotations"></a>版本註解
如果您使用 Azure DevOps，您可以[取得註解標記](../../azure-monitor/app/annotations.md) (每當發行新版本時，此標記就會新增至您的圖表)。 下圖顯示此標記的顯示方式。

## <a name="next-steps"></a>後續步驟

* [多個角色的共用資源](../../azure-monitor/app/app-map.md)
* [建立遙測初始設定式來區分 A |B 變體](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
