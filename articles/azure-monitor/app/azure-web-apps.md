---
title: 監視 Azure 應用程式服務效能 | Microsoft Docs
description: Azure 應用程式服務的應用程式效能監視。 圖表載入和回應時間、依賴項資訊以及設置性能警報。
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: d2134e059a446c18108e8dd16bcc74504b42b15a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437212"
---
# <a name="monitor-azure-app-service-performance"></a>監視 Azure App Service 效能

現在,在[Azure 應用服務](https://docs.microsoft.com/azure/app-service/)上運行的基於 ASP.NET 和 ASP.NET 基於酷的 Web 應用程式啟用監視比以往任何時候都更容易。 以前需要手動安裝網站擴展,而最新的擴展/代理現在預設內置到應用服務映射中。 本文將指導您完成啟用應用程式見解監視,並為大規模部署的自動化過程提供初步指導。

> [!NOTE]
> 使用開發**工具** > **擴展**手動添加應用程式見解網站擴展。 這種擴展安裝方法取決於每個新版本的手動更新。 延伸的最新穩定版本現在作為套用服務映像的預[先安裝](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)。 這些文件位於其中`d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent`,並隨每個穩定版本自動更新。 如果您按照基於代理的說明啟用下面的監視,它將自動為您刪除已棄用的擴展。

## <a name="enable-application-insights"></a>啟用 Application Insights

有兩種方法可以為 Azure 應用服務託管應用程式啟用應用程式監視:

* **基於代理的應用程式監視**(應用程式見解代理)。  
    * 此方法是最容易啟用的,不需要高級配置。 它通常被稱為"運行時"監視。 對於 Azure 應用服務,我們建議至少啟用此級別的監視,然後根據特定方案,您可以評估是否需要通過手動檢測進行更高級的監視。

* 透過安裝應用程式的 SDK 以**程式碼手動偵測應用程式**。

    * 此方法更具可自訂性,但它需要[新增對應用程式見解 SDK NuGet 套件的相依項 。](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) 此方法還意味著您必須自己管理最新版本的包的更新。

    * 如果需要進行自定義 API 調用來跟蹤預設情況下未透過基於代理的監視捕獲的事件/依賴項,則需要使用此方法。 請查看[API 中的自訂事件和指標文章](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics)以瞭解更多資訊。 這也是目前基於 Linux 的工作負載的唯一受支援的選項。

> [!NOTE]
> 如果檢測到基於代理的監視和基於 SDK 的手動檢測,則僅遵循手動檢測設置。 這是為了防止發送重複數據。 要瞭解有關此詳細資訊,請查看下面的[故障排除部分](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)。

## <a name="enable-agent-based-monitoring"></a>開啟代理的監視器

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> 不支援APPINSIGHTS_JAVASCRIPT_ENABLED和 url 壓縮的組合。 有關詳細資訊,請參閱[故障排除部分](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)中的說明。


1. 在套用服務的 Azure 控制面板中選擇**應用程式見解**。

    ![在 [設定] 之下，選擇 Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * 除非您已經為此應用程式設定 Application Insights 資源，否則請選擇建立新的資源。 

     > [!NOTE]
     > 當您按一下 [確定]**** 來建立新資源時，系統會提示您 [套用監視設定]****。 選取 [繼續]**** 會將新的 Application Insights 資源連結至您的應用程式服務，這麼做也會**觸發應用程式服務的重新啟動**。 

     ![檢測 Web 應用程式](./media/azure-web-apps/create-resource-01.png)

2. 在指定要使用的資源之後，您可以選擇要如何讓 Application Insights 收集每個應用程式平台的資料。 ASP.NET應用監視是預設的,具有兩個不同的集合級別。

    ![選擇每個平台的選項](./media/azure-web-apps/choose-options-new.png)

   * .NET**基本集合**級別提供基本的單實例 APM 功能。

   * .NET **建議收集**層級：
       * 新增 CPU、記憶體和 I/O 使用趨勢。
       * 將不同要求/相依性界限間的微服務相互關聯。
       * 收集使用趨勢，並啟用從可用性結果到交易的相互關聯。
       * 收集主機程序未處理的例外狀況。
       * 在使用取樣時，改善 APM 計量在負載下的精確度。

3. 要配置採樣等設置,以前可以通過應用程式 insights.config 檔控制這些設置,您現在可以通過具有相應前綴的應用程式設置與這些相同的設置進行交互。 

    * 例如,要更改初始採樣百分比,可以創建 應用程式設置`MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage`:`100`和值。

    * 有關支援的自我調整取樣遙測處理器設定的清單,可以查閱[程式碼](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs)[與相關文件](https://docs.microsoft.com/azure/azure-monitor/app/sampling)。

# <a name="net-core"></a>[.NET Core](#tab/netcore)

支援以下版本的 .NET Core: ASP.NET核心 2.0、ASP.NET核心 2.1、ASP.NET核心 2.2、ASP.NET Core 3.0

基於代理/擴展的監視目前**不支援**從 .NET Core、自包含部署和基於 Linux 的應用程式定位完整框架。 (通過代碼[進行手動檢測](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)將在之前的所有方案中工作。

1. 在套用服務的 Azure 控制面板中選擇**應用程式見解**。

    ![在 [設定] 之下，選擇 Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * 除非您已經為此應用程式設定 Application Insights 資源，否則請選擇建立新的資源。 

     > [!NOTE]
     > 當您按一下 [確定]**** 來建立新資源時，系統會提示您 [套用監視設定]****。 選取 [繼續]**** 會將新的 Application Insights 資源連結至您的應用程式服務，這麼做也會**觸發應用程式服務的重新啟動**。 

     ![檢測 Web 應用程式](./media/azure-web-apps/create-resource-01.png)

2. 指定要使用的資源後,您可以選擇希望應用程式見解如何為應用程式為每個平臺收集數據。 .NET Core 為 .NET Core 2.0、2.1、2.2 和 3.0 提供**推薦集合**或**禁用**。

    ![選擇每個平台的選項](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

在 **「設定」** > 下的應用服務 Web 應用中**選擇「啟用應用程式見解** > **Enable**」 。 基於 Node.js 代理的監視當前處於預覽狀態。

# <a name="java"></a>[Java](#tab/java)

基於 Java 應用程式服務的 Web 應用程式目前不支援基於代理/擴展的自動監視。 要啟用 Java 應用程式的監視,您需要[手動偵測應用程式](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started)。

# <a name="python"></a>[Python](#tab/python)

基於 Python 應用服務的 Web 應用程式目前不支援基於代理/擴展的自動監視。 要開啟對 Python 應用程式的監視,您需要[手動偵測應用程式](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)。

---

## <a name="enable-client-side-monitoring"></a>啟用用戶端監視

# <a name="net"></a>[.NET](#tab/net)

用戶端監視是ASP.NET的選擇。 要開啟客戶端監視:

* 選擇**設定**>* [應用程式設定]
   * 在「應用程式設定」 下,新增**新的應用程式設定名稱**與**值**:

     名稱：`APPINSIGHTS_JAVASCRIPT_ENABLED`

     值: `true`

   * **儲存**設定並**重新啟動**您的應用程式。

![應用程式設定 UI 的螢幕擷取](./media/azure-web-apps/appinsights-javascript-enabled.png)

要禁用客戶端監視,請從應用程式設定中刪除關聯的鍵值對,或將該值設置為 false。

# <a name="net-core"></a>[.NET Core](#tab/netcore)

默認情況下,無論應用設置"APPINSIGHTS_JAVASCRIPT_ENABLED"是否存在,都為具有**推薦集合**的 .NET Core 應用**啟用**用戶端監視。

如果由於某種原因,您希望禁用客戶端監視:

* 選擇**設定** > **應用程式設定**
   * 在「應用程式設定」 下,新增**新的應用程式設定名稱**與**值**:

     名稱:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     值: `false`

   * **儲存**設定並**重新啟動**您的應用程式。

![應用程式設定 UI 的螢幕擷取](./media/azure-web-apps/appinsights-javascript-disabled.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

要為 Node.js 應用程式啟用客戶端的[Kde,您需要手動將用戶端 JavaScript SDK 新增到應用程式](https://docs.microsoft.com/azure/azure-monitor/app/javascript)。

# <a name="java"></a>[Java](#tab/java)

要為 Java 應用程式啟用客戶端監視,您需要[手動將用戶端 JavaScript SDK 新增到應用程式](https://docs.microsoft.com/azure/azure-monitor/app/javascript)。

# <a name="python"></a>[Python](#tab/python)

要為 Python 應用程式啟用客戶端監視,您需要[手動將用戶端 JavaScript SDK 新增到應用程式](https://docs.microsoft.com/azure/azure-monitor/app/javascript)。

---

## <a name="automate-monitoring"></a>自動監視

為了啟用具有應用程式見解的遙測集合,只需設置應用程式設置:

   ![套用服務應用程式設定,帶可用的應用程式見解設定](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>應用程式設定定義

|應用程式設定名稱 |  定義 | 值 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | 主擴展,它控制運行時監視。 | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  僅在預設模式下,才啟用基本功能,以確保最佳性能。 | `default` 或 `recommended`。 |
|InstrumentationEngine_EXTENSION_VERSION | 控制二進位重寫引擎`InstrumentationEngine`是否將打開。 此設置會影響性能,並影響冷啟動/啟動時間。 | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | 控制項是否將捕捉 SQL & Azure 表文本以及依賴項調用。 性能警告:應用程式冷啟動時間將受到影響。 此設置需要。 `InstrumentationEngine` | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>使用 Azure 資源管理員的應用程式設定

應用服務的應用程式設置可以使用[Azure 資源管理器範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)進行管理和配置。 在使用 Azure 資源管理器自動化部署新的應用服務資源或修改現有資源的設置時,可以使用此方法。

應用服務的應用程式設定 JSON 的基本結構如下:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

對於為應用程式見解配置了應用程式的設置的 Azure 資源管理器範本的範例,此[樣本](https://github.com/Andrew-MSFT/BasicImageGallery)非常有用,特別是從第[238 行](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)開始的部分。

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>自動創建應用程式見解資源並連結到新創建的應用服務。

要建立設定所有預設應用程式見解設定的 Azure 資源管理員範本,請開始此過程,就像要創建啟用應用程式見解的新 Web 應用一樣。

選擇**自動化選項**

   ![套用服務 Web 應用程式建立選單](./media/azure-web-apps/create-web-app.png)

此選項生成最新的 Azure 資源管理器範本,並配置了所有必需的設置。

  ![套用服務 Web 應用樣本](./media/azure-web-apps/arm-template.png)

下面是一個範例,將的所有`AppMonitoredSite`實體取代為網站名稱:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enabling-through-powershell"></a>透過 PowerShell 實作

為了啟用透過 PowerShell 的應用程式監視,只需更改基礎應用程式設置。 下面是一個示例,它允許在資源組「AppMonitoredRG」中對名為「AppMonitored Site」的網站進行應用程式監視,並將數據配置為發送到「012345678-abcd-ef01-2345-6789abcd」檢測密鑰。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>升級監控延伸/代理

### <a name="upgrading-from-versions-289-and-up"></a>從版本 2.8.9 和上版本升級

從版本 2.8.9 升級會自動進行,無需執行任何其他操作。 新的監視位在後台傳遞到目標應用服務,並在應用程式重新啟動時將選取它們。

要檢查正在執行的副檔的哪個版本,請造訪`http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![網址 路徑的螢幕擷取http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>從版本 1.0.0 - 2.6.5 升級

從版本 2.8.9 開始,將使用預安裝的網站擴展。 如果您是較早的版本,可以通過兩種方式之一進行更新:

* [透過門戶啟用升級](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)。 (即使您安裝了 Azure 應用服務的應用程式見解擴展,UI 也僅顯示**啟用**按鈕。 在幕後,將刪除舊的專用網站擴展。

* [透過 PowerShell 進行升級](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. 設置應用程式設定以啟用預安裝的網站擴展應用程式應用見解代理。 請參考[以電源殼開啟](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)。
    2. 手動刪除名為 Azure 應用服務的應用程式見解擴展的專用網站擴展。

如果升級是從 2.5.1 之前的版本完成的,請檢查應用程式 Insigths dlls 是否從應用程式箱資料夾中刪除[,請參閱故障排除步驟](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)。

## <a name="troubleshooting"></a>疑難排解

以下是我們在 Azure 應用服務上運行的基於 .NET 和 .NET Core 的應用程式的分步故障排除指南。

> [!NOTE]
> Java 應用程式僅通過基於 SDK 的手動檢測在 Azure 應用服務上受支援,因此以下步驟不適用於這些方案。

1. 檢查應用程式是否通過`ApplicationInsightsAgent`進行監視。
    * 檢查應用`ApplicationInsightsAgent_EXTENSION_VERSION`設置是否設置為"+2"值。
2. 確保應用程式滿足要監視的要求。
    * 瀏覽至 `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`。

    ![結果頁面https://yoursitename.scm.azurewebsites/applicationinsights的螢幕擷取](./media/azure-web-apps/app-insights-sdk-status.png)

    * 確認`Application Insights Extension Status``Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * 如果未執行,請按照[啟用的應用程式來解監視說明操作](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * 確認狀態源是否存在,如下所示:`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * 如果不存在類似的值,則表示應用程式當前未運行或不受支援。 為確保應用程式正在運行,請嘗試手動訪問應用程式 URL/應用程式終結點,這將使運行時資訊變得可用。

    * 確認為`IKeyExists``true`
        * 如果是`false`,請`APPINSIGHTS_INSTRUMENTATIONKEY`添加`APPLICATIONINSIGHTS_CONNECTION_STRING`並且使用 ikey guid 添加到應用程式設置中。

    * 確認`AppAlreadyInstrumented`沒有`AppContainsDiagnosticSourceAssembly`的項目`AppContainsAspNetTelemetryCorrelationAssembly`。
        * 如果存在這些項目中的任何一個,請從應用程式中移除以下包:`Microsoft.ApplicationInsights`與`System.Diagnostics.DiagnosticSource` `Microsoft.AspNet.TelemetryCorrelation` 。

下表更詳細地說明了這些值的含義、其根本原因和建議修復:

|問題值|說明|修正
|---- |----|---|
| `AppAlreadyInstrumented:true` | 此值指示擴展檢測到應用程式中已存在 SDK 的某些方面,並將後退。 這可能是由於對`System.Diagnostics.DiagnosticSource`的引用, `Microsoft.AspNet.TelemetryCorrelation``Microsoft.ApplicationInsights`  | 刪除參考。 預設情況下,某些引用是從某些 Visual Studio 樣本添加的,而舊版本的 Visual Studio 可能會添加`Microsoft.ApplicationInsights`對的引用。
|`AppAlreadyInstrumented:true` | 如果應用程式的目標是 .NET Core 2.1 或 2.2,並且引用[Microsoft.AspNetCore.所有](https://www.nuget.org/packages/Microsoft.AspNetCore.All)元包,則它會帶來應用程式見解,擴展將退卻。 | [建議](https://github.com/aspnet/Announcements/issues/287).NET Core 2.1,2.2 上的客戶改用 Microsoft.AspNetCore.App 元包。|
|`AppAlreadyInstrumented:true` | 此值也可能由上一個部署的應用資料夾中存在上述 dlls 引起。 | 清除應用資料夾以確保刪除這些 dll。 檢查本地應用的 bin 目錄和應用服務上的 wwwroot 目錄。 (要檢查應用服務 Web 應用的 wwwroot 目錄:高級工具 (Kudu) >调试控制台> CMD > home_site_wwwroot)。
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | 此值指示擴展檢測到應用程式中的`Microsoft.AspNet.TelemetryCorrelation`引用,並將後退。 | 移除參考。
|`AppContainsDiagnosticSourceAssembly**:true`|此值指示擴展檢測到應用程式中的`System.Diagnostics.DiagnosticSource`引用,並將後退。| 移除參考。
|`IKeyExists:false`|此值指示偵測金鑰不存在於 App`APPINSIGHTS_INSTRUMENTATIONKEY`設定中 。 可能的原因:這些值可能已被意外刪除,忘記在自動化腳本中設置值等。 | 確保該設置存在於應用服務應用程序設置中。

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>不支援APPINSIGHTS_JAVASCRIPT_ENABLED與 url 壓縮

如果在對內容進行編碼的情況下使用 APPINSIGHTS_JAVASCRIPT_ENABLED_true,則可能會收到類似: 

- 500 URL 重寫錯誤
- 500.53 URL 重寫模組錯誤,其中帶有消息出站重寫規則,當對 HTTP 回應的內容進行編碼時("gzip")時,無法應用該規則。 

這是因為APPINSIGHTS_JAVASCRIPT_ENABLED應用程式設置設置為 true,並且同時存在內容編碼。 此方案尚不受支援。 解決方法是從應用程式設置中刪除APPINSIGHTS_JAVASCRIPT_ENABLED。 不幸的是,這意味著如果仍然需要用戶端/瀏覽器端 JavaScript 檢測,則需要您的網頁提供手動 SDK 引用。 請按照 JavaScript SDK 手動檢測[的說明](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup)進行操作。

有關應用程式見解代理/擴充的最新資訊,請檢視[發行說明](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md)。

### <a name="php-and-wordpress-are-not-supported"></a>不支援 PHP 與 WordPress

不支援 PHP 和 WordPress 網站。 目前沒有官方支援的 SDK/代理用於伺服器端監視這些工作負載。 但是,透過使用[JAVAScript SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript)可以手動檢測 PHP 或 WordPress 網站上的用戶端事務,將用戶端 javascript 添加到您的網頁。 

## <a name="next-steps"></a>後續步驟
* [在即時應用程式上執行分析工具](../app/profiler.md)。
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - 使用 Application Insights 監視 Azure Functions
* [能夠讓 Azure 診斷](../platform/diagnostics-extension-to-application-insights.md) 傳送至 Application Insights。
* [監視服務健康狀態計量](../platform/data-platform.md)，確保您的服務可用且回應正常。
* 每當發生作業事件或計量超過臨界值時，[接收警示通知](../platform/alerts-overview.md)。
* 使用 [JavaScript 應用程式和網頁適用的 Application Insights](javascript.md) ，以從造訪網頁的瀏覽器取得用戶端遙測。
* [設定可用性 Web 測試](monitor-web-app-availability.md) ，以在您的網站關閉時發出警示。
