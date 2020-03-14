---
title: 監視 Azure 應用程式服務效能 | Microsoft Docs
description: Azure 應用程式服務的應用程式效能監視。 圖表載入和回應時間、相依性資訊，以及設定效能警示。
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 03d332af182f8f40ede634fbd563f7b064751f32
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276162"
---
# <a name="monitor-azure-app-service-performance"></a>監視 Azure App Service 效能

在您的 ASP.NET 和[Azure App 服務](https://docs.microsoft.com/azure/app-service/)上執行的 ASP.NET Core web 應用程式上啟用監視，現在比以往更容易。 雖然先前您需要手動安裝網站延伸模組，但根據預設，最新的擴充功能/代理程式已內建到 app service 映射中。 這篇文章將逐步引導您啟用 Application Insights 監視，以及提供將大規模部署程式自動化的初步指導方針。

> [!NOTE]
> 透過**開發工具**手動新增 Application Insights 的網站延伸模組 > **延伸**模組已被取代。 這種擴充功能安裝方法相依于每個新版本的手動更新。 擴充功能的最新穩定版本現在已[預先安裝](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)為 App Service 映射的一部分。 這些檔案位於 `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent`，而且會隨著每個穩定版本自動更新。 如果您遵循以代理程式為基礎的指示來啟用下列監視，它會自動為您移除已淘汰的延伸模組。

## <a name="enable-application-insights"></a>啟用 Application Insights

有兩種方式可針對 Azure App Services 裝載的應用程式啟用應用程式監視：

* 以**代理程式為基礎的應用程式監視**（ApplicationInsightsAgent）。  
    * 這個方法最容易啟用，而且不需要任何 advanced 設定。 這通常稱為「執行時間」監視。 對於 Azure App 服務，我們建議您至少啟用此層級的監視，然後根據您的特定案例，您可以評估是否需要透過手動檢測進行更先進的監視。

* 藉由安裝 Application Insights SDK，**透過程式碼手動檢測應用程式**。

    * 這種方法更容易自訂，但需要[在 APPLICATION INSIGHTS SDK NuGet 套件上新增](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)相依性。 這種方法也表示您必須自行管理最新版本套件的更新。

    * 如果您需要進行自訂 API 呼叫來追蹤預設不是以代理程式為基礎所捕捉的事件/相依性，則必須使用此方法。 若要深入瞭解，請參閱[自訂事件和計量的 API 一文](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics)。

> [!NOTE]
> 如果偵測到以代理程式為基礎的監視和手動以 SDK 為基礎的檢測，則只會接受手動檢測設定。 這是為了防止傳送重複的資料。 若要深入瞭解，請參閱下面的[疑難排解一節](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)。

## <a name="enable-agent-based-monitoring"></a>啟用以代理程式為基礎的監視

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> 不支援 APPINSIGHTS_JAVASCRIPT_ENABLED 和 urlCompression 的組合。 如需詳細資訊，請參閱[疑難排解一節](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)中的說明。


1. 在 Azure 控制台中，選取您應用程式服務的 [Application Insights]。

    ![在 [設定] 之下，選擇 Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * 除非您已經為此應用程式設定 Application Insights 資源，否則請選擇建立新的資源。 

     > [!NOTE]
     > 當您按一下 [確定] 來建立新資源時，系統會提示您 [套用監視設定]。 選取 [繼續] 會將新的 Application Insights 資源連結至您的應用程式服務，這麼做也會**觸發應用程式服務的重新啟動**。 

     ![檢測 Web 應用程式](./media/azure-web-apps/create-resource-01.png)

2. 在指定要使用的資源之後，您可以選擇要如何讓 Application Insights 收集每個應用程式平台的資料。 ASP.NET 應用程式監視預設為使用兩個不同層級的集合。

    ![選擇每個平台的選項](./media/azure-web-apps/choose-options-new.png)

   * .NET **基本收集**層級提供基本的單一執行個體 APM 功能。

   * .NET **建議收集**層級：
       * 新增 CPU、記憶體和 I/O 使用趨勢。
       * 將不同要求/相依性界限間的微服務相互關聯。
       * 收集使用趨勢，並啟用從可用性結果到交易的相互關聯。
       * 收集主機程序未處理的例外狀況。
       * 在使用取樣時，改善 APM 計量在負載下的精確度。

3. 若要進行取樣（您先前可以透過 applicationinsights 來控制）的設定，您現在可以透過具有對應前置詞的應用程式設定，與這些相同的設定進行互動。 

    * 例如，若要變更初始取樣百分比，您可以建立的應用程式設定： `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage`，以及 `100`的值。

    * 如需支援的調適型取樣遙測處理器設定清單，您可以查閱程式[代碼](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs)和[相關聯的檔](https://docs.microsoft.com/azure/azure-monitor/app/sampling)。

# <a name="net-core"></a>[.NET Core](#tab/netcore)

支援下列 .NET Core 版本： ASP.NET Core 2.0、ASP.NET Core 2.1、ASP.NET Core 2.2、ASP.NET Core 3。0

以 .NET Core、獨立式部署和以 Linux 為基礎的應用程式為目標的完整架構，目前**不支援**以代理程式/延伸模組為基礎的監視。 （透過程式碼的[手動檢測](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)功能將在先前的所有案例中使用）。

1. 在 Azure 控制台中，選取您應用程式服務的 [Application Insights]。

    ![在 [設定] 之下，選擇 Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * 除非您已經為此應用程式設定 Application Insights 資源，否則請選擇建立新的資源。 

     > [!NOTE]
     > 當您按一下 [確定] 來建立新資源時，系統會提示您 [套用監視設定]。 選取 [繼續] 會將新的 Application Insights 資源連結至您的應用程式服務，這麼做也會**觸發應用程式服務的重新啟動**。 

     ![檢測 Web 應用程式](./media/azure-web-apps/create-resource-01.png)

2. 在指定要使用的資源之後，您可以選擇您想要 Application Insights 為應用程式的每個平臺收集資料。 .NET Core 針對 .NET Core 2.0、2.1、2.2 和3.0 提供**建議的收集**或**停用**。

    ![選擇每個平台的選項](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

從 App Service web 應用程式內的 [**設定**] 下 > **選取 [Application Insights** > **啟用**]。 以 node.js 代理程式為基礎的監視目前為預覽狀態。

# <a name="java"></a>[Java](#tab/java)

以 JAVA App Service 為基礎的 web 應用程式目前不支援自動代理程式/擴充功能型監視。 若要啟用 JAVA 應用程式的監視，您必須[手動檢測您的應用程式](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started)。

# <a name="python"></a>[Python](#tab/python)

以 Python App Service 為基礎的 web 應用程式目前不支援以自動代理程式/延伸模組為基礎的監視。 若要為您的 Python 應用程式啟用監視，您必須[手動檢測應用程式](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)。

---

## <a name="enable-client-side-monitoring"></a>啟用用戶端監視

# <a name="net"></a>[.NET](#tab/net)

用戶端監視是加入宣告以進行 ASP.NET。 若要啟用用戶端監視：

* 選取 [**設定**] > [應用程式設定] * * * *
   * 在 [應用程式設定] 下，新增**應用程式設定名稱**和**值**：

     名稱：`APPINSIGHTS_JAVASCRIPT_ENABLED`

     值: `true`

   * **儲存**設定並**重新啟動**您的應用程式。

![應用程式設定 UI 的螢幕擷取畫面](./media/azure-web-apps/appinsights-javascript-enabled.png)

若要停用用戶端監視，請從應用程式設定中移除相關聯的機碼值組，或將值設定為 false。

# <a name="net-core"></a>[.NET Core](#tab/netcore)

不論應用程式設定 ' APPINSIGHTS_JAVASCRIPT_ENABLED ' 是否存在，使用建議的**集合**的 .net Core 應用程式**預設會啟用**用戶端監視。

如果基於某些原因，您會想要停用用戶端監視：

* 選取 [**設定**] > [**應用程式設定**]
   * 在 [應用程式設定] 下，新增**應用程式設定名稱**和**值**：

     名稱： `APPINSIGHTS_JAVASCRIPT_ENABLED`

     值: `false`

   * **儲存**設定並**重新啟動**您的應用程式。

![應用程式設定 UI 的螢幕擷取畫面](./media/azure-web-apps/appinsights-javascript-disabled.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

若要為您的 node.js 應用程式啟用用戶端監視，您必須[手動將用戶端 JAVASCRIPT SDK 新增至您的應用程式](https://docs.microsoft.com/azure/azure-monitor/app/javascript)。

# <a name="java"></a>[Java](#tab/java)

若要啟用 JAVA 應用程式的用戶端監視，您必須[手動將用戶端 JAVASCRIPT SDK 新增至您的應用程式](https://docs.microsoft.com/azure/azure-monitor/app/javascript)。

# <a name="python"></a>[Python](#tab/python)

若要為您的 Python 應用程式啟用用戶端監視，您必須[手動將用戶端 JAVASCRIPT SDK 新增至您的應用程式](https://docs.microsoft.com/azure/azure-monitor/app/javascript)。

---

## <a name="automate-monitoring"></a>自動監視

若要使用 Application Insights 啟用遙測收集，只需要設定應用程式設定：

   ![App Service 具有可用 Application Insights 設定的應用程式設定](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>應用程式設定定義

|應用程式設定名稱 |  定義 | 值 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | 主要延伸模組，控制執行時間監視。 | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  只有在預設模式下，才會啟用基本功能，以確保最佳效能。 | `default` 或 `recommended`。 |
|InstrumentationEngine_EXTENSION_VERSION | 控制二進位重寫引擎是否 `InstrumentationEngine` 將會開啟。 此設定會影響效能，並會影響冷啟動/啟動時間。 | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | 控制 SQL & Azure 資料表文字是否會隨著相依性呼叫一起捕捉。 效能警告：應用程式冷啟動時間將會受到影響。 此設定需要 `InstrumentationEngine`。 | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>使用 Azure Resource Manager App Service 應用程式設定

應用程式服務的應用程式設定可以使用[Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)進行管理和設定。 使用 Azure Resource Manager 自動化部署新的 App Service 資源，或修改現有資源的設定時，可以使用這個方法。

App service 的應用程式設定 JSON 基本結構如下：

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

如需 Azure Resource Manager 範本的範例，其中包含為 Application Insights 設定的應用程式設定，此[範本](https://github.com/Andrew-MSFT/BasicImageGallery)可能很有説明，特別是從[第238行](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)開始的章節。

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>自動建立 Application Insights 資源，並連結至新建立的 App Service。

若要建立已設定所有預設 Application Insights 設定的 Azure Resource Manager 範本，請開始進行此程式，就像是在啟用 Application Insights 的情況下建立新的 Web 應用程式一樣。

選取**自動化選項**

   ![App Service web 應用程式建立 功能表](./media/azure-web-apps/create-web-app.png)

此選項會產生最新的 Azure Resource Manager 範本，並設定所有必要設定。

  ![App Service web 應用程式範本](./media/azure-web-apps/arm-template.png)

以下是範例，請將 `AppMonitoredSite` 的所有實例取代為您的網站名稱：

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

### <a name="enabling-through-powershell"></a>透過 PowerShell 啟用

若要透過 PowerShell 啟用應用程式監視，只需要變更基礎應用程式設定。 以下範例會啟用資源群組 "AppMonitoredRG" 中名為 "AppMonitoredSite" 之網站的應用程式監視，並設定要傳送至 "012345678-abcd-ef01-2345-6789abcd" 檢測金鑰的資料。

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

## <a name="upgrade-monitoring-extensionagent"></a>升級監視擴充功能/代理程式

### <a name="upgrading-from-versions-289-and-up"></a>從2.8.9 和更新版本升級

從版本2.8.9 升級會自動進行，而不需要任何其他動作。 新的監視位會在背景中傳遞至目標應用程式服務，並在應用程式重新開機時將其挑選。

若要檢查您正在執行的延伸模組版本，請 `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Url 路徑 http://yoursitename.scm.azurewebsites.net/ApplicationInsights 的螢幕擷取畫面](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>從1.0.0 版升級-2.6。5

從版本2.8.9 開始，會使用預先安裝的網站延伸模組。 如果您是較舊的版本，您可以透過下列兩種方式的其中一種來更新：

* 透過[入口網站啟用來進行升級](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)。 （即使已安裝 Azure App Service 的 Application Insights 延伸模組，UI 只會顯示 [**啟用**] 按鈕。 在幕後，將會移除舊的私用網站延伸模組）。

* [透過 PowerShell 升級](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)：

    1. 設定 [應用程式設定] 以啟用預先安裝的網站擴充功能 ApplicationInsightsAgent。 請參閱[透過 Powershell 啟用](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)。
    2. 手動移除名為 Application Insights extension for Azure App Service 的私人網站延伸模組。

如果是從2.5.1 之前的版本完成升級，請檢查 ApplicationInsigths dll 是否已從應用程式 bin 資料夾中移除，[請參閱疑難排解步驟](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)。

## <a name="troubleshooting"></a>疑難排解

以下是針對在 Azure App 服務上執行的 .NET 和 .NET Core 應用程式，以擴充/代理程式為基礎之監視的逐步疑難排解指南。

> [!NOTE]
> JAVA 應用程式僅支援透過手動 SDK 型檢測 Azure App 服務，因此下列步驟不適用於這些案例。

1. 檢查是否已透過 `ApplicationInsightsAgent`監視應用程式。
    * 檢查 `ApplicationInsightsAgent_EXTENSION_VERSION` 應用程式設定是否設定為 "~ 2" 的值。
2. 請確定應用程式符合要監視的需求。
    * 流覽至 `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![https://yoursitename.scm.azurewebsites/applicationinsights [結果] 頁面的螢幕擷取畫面](./media/azure-web-apps/app-insights-sdk-status.png)

    * 確認 `Application Insights Extension Status` 是 `Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * 如果未執行，請遵循[啟用 Application Insights 監視指示](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * 確認狀態來源存在，而且看起來像這樣： `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * 如果沒有類似的值，則表示應用程式目前不在執行中或不受支援。 若要確保應用程式正在執行，請嘗試手動流覽應用程式 url/應用程式端點，這將允許執行時間資訊變成可用。

    * 確認 `IKeyExists` 為 `true`
        * 如果 `false`，請使用您的 ikey guid 將 `APPINSIGHTS_INSTRUMENTATIONKEY` 和 `APPLICATIONINSIGHTS_CONNECTION_STRING` 新增至您的應用程式設定。

    * 確認沒有 `AppAlreadyInstrumented`、`AppContainsDiagnosticSourceAssembly`和 `AppContainsAspNetTelemetryCorrelationAssembly`的專案。
        * 如果其中有任何專案存在，請從您的應用程式移除下列套件： `Microsoft.ApplicationInsights`、`System.Diagnostics.DiagnosticSource`和 `Microsoft.AspNet.TelemetryCorrelation`。

下表提供這些值的意義、其根本原因和建議的修正的詳細說明：

|問題值|說明|修正
|---- |----|---|
| `AppAlreadyInstrumented:true` | 此值表示延伸模組偵測到應用程式中已有 SDK 的某些層面，而且將會回復。 這可能是因為參考 `System.Diagnostics.DiagnosticSource`、`Microsoft.AspNet.TelemetryCorrelation`或 `Microsoft.ApplicationInsights`  | 移除參考。 根據預設，某些參考會從某些 Visual Studio 範本新增，而舊版的 Visual Studio 可能會將參考新增至 `Microsoft.ApplicationInsights`。
|`AppAlreadyInstrumented:true` | 如果應用程式是以 .NET Core 2.1 或2.2 為目標，並參考[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.All) ，則會帶入 Application Insights，而且延伸模組將會回復。 | [建議您使用](https://github.com/aspnet/Announcements/issues/287).net Core 2.1、2.2 的客戶，改為使用 AspNetCore 應用程式元封裝。|
|`AppAlreadyInstrumented:true` | 這個值也可能是因為先前部署的應用程式資料夾中有上述的 dll。 | 清除應用程式資料夾，以確保這些 dll 已移除。 檢查本機應用程式的 bin 目錄，以及 App Service 上的 wwwroot 目錄。 （若要檢查 App Service web 應用程式的 wwwroot 目錄： Advanced Tools （Kudu） > Debug 主控台 > CMD > home\site\wwwroot）。
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | 此值表示延伸模組偵測到應用程式中 `Microsoft.AspNet.TelemetryCorrelation` 的參考，而且將會回復。 | 移除參考。
|`AppContainsDiagnosticSourceAssembly**:true`|此值表示延伸模組偵測到應用程式中 `System.Diagnostics.DiagnosticSource` 的參考，而且將會回復。| 移除參考。
|`IKeyExists:false`|此值表示 AppSetting 中不存在檢測金鑰，`APPINSIGHTS_INSTRUMENTATIONKEY`。 可能的原因：這些值可能已被意外移除、忘記設定自動化腳本中的值等等。 | 請確定 App Service 的應用程式設定中有該設定。

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>不支援 APPINSIGHTS_JAVASCRIPT_ENABLED 和 urlCompression

如果您在編碼內容的情況下使用 APPINSIGHTS_JAVASCRIPT_ENABLED = true，您可能會收到類似下列的錯誤： 

- 500 URL 重寫錯誤
- 500.53 URL 重寫模組錯誤，而且當 HTTP 回應的內容編碼時，無法套用訊息輸出重寫規則（' gzip '）。 

這是因為 APPINSIGHTS_JAVASCRIPT_ENABLED 應用程式設定設為 true，同時同時出現內容編碼。 尚不支援此案例。 解決方法是從您的應用程式設定中移除 APPINSIGHTS_JAVASCRIPT_ENABLED。 可惜的是，如果仍然需要用戶端/瀏覽器端的 JavaScript 檢測，您的網頁需要手動的 SDK 參考。 請遵循使用 JavaScript SDK 進行手動檢測的[指示](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup)。

如需有關 Application Insights 代理程式/擴充功能的最新資訊，請參閱[版本](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md)資訊。

## <a name="next-steps"></a>後續步驟
* [在即時應用程式上執行分析工具](../app/profiler.md)。
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - 使用 Application Insights 監視 Azure Functions
* [能夠讓 Azure 診斷](../platform/diagnostics-extension-to-application-insights.md) 傳送至 Application Insights。
* [監視服務健康狀態計量](../platform/data-platform.md)，確保您的服務可用且回應正常。
* 每當發生作業事件或計量超過臨界值時，[接收警示通知](../platform/alerts-overview.md)。
* 使用 [JavaScript 應用程式和網頁適用的 Application Insights](javascript.md) ，以從造訪網頁的瀏覽器取得用戶端遙測。
* [設定可用性 Web 測試](monitor-web-app-availability.md) ，以在您的網站關閉時發出警示。
