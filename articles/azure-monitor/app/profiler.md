---
title: 使用 Application Insights 來分析即時 Azure App Service 應用程式 | Microsoft Docs
description: 使用 Application Insights Profiler 來分析 Azure App Service 上的即時應用程式。
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: b1158a614da9ba32f628aba5dd2ed2cc71b4b455
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947036"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>使用 Application Insights 來分析即時 Azure App Service 應用程式

您可以使用基本服務層級或更高層級，針對在 Azure App Service 上執行的 ASP.NET 和 ASP.NET Core 應用程式執行 Profiler。 目前僅可透過[這個方法](profiler-aspnetcore-linux.md)，在 Linux 上啟用 Profiler。

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> 為應用程式啟用 Profiler
若要為應用程式啟用 Profiler，請依照下列指示進行操作。 如果您正在執行不同類型的 Azure 服務，以下是在其他支援平台上啟用 Profiler 的指示：
* [雲端服務](./profiler-cloudservice.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Service Fabric 應用程式](./profiler-servicefabric.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [虛擬機器](./profiler-vm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

Application Insights Profiler 會預先安裝為應用程式服務執行階段的一部分。 下列步驟將說明如何為您的 App Service 啟用此分析工具。 即使您的應用程式已在建置期間包含 App Insights SDK，但仍需遵循這些步驟。

1. 流覽至您 App Service 的 [Azure 控制台]。
1. 針對您的應用程式服務啟用 "Always On" 設定。 您可以在 [ **設定**] 下的 [設定 **] 頁面 (** 查看下一個步驟) 的螢幕擷取畫面，然後按一下 [ **一般設定** ] 索引標籤，即可找到此設定。
1. 流覽至 [ **設定] > Application Insights** ] 頁面。

   ![在 App Service 入口網站上啟用 Application Insights](./media/profiler/AppInsights-AppServices.png)

1. 依照窗格上的指示來建立新資源，或選取現有的 App Insights 資源來監視您的應用程式。 也請確定 Profiler 為 [開啟]。 如果您 Application Insights 資源的訂用帳戶與您的 App Service 不同，您就無法使用此頁面來設定 Application Insights。 您仍然可以藉由手動建立必要的應用程式設定，以手動方式執行此動作。 [下一節包含手動啟用 Profiler 的指示。](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![新增 App Insights 網站延伸模組][Enablement UI]

1. 現在已使用 App Service 應用程式設定來啟用 Profiler。

    ![Profiler 的應用程式設定][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>以手動方式或使用 Azure Resource Manager 啟用 Profiler
Application Insights Profiler 可以藉由建立 Azure App Service 的應用程式設定來啟用。 具有上面顯示之選項的頁面會為您建立這些應用程式設定。 但是，您可以使用範本或其他方式，自動建立這些設定。 如果您 Application Insights 資源的訂用帳戶與您的 Azure App Service 不同，這些設定也適用。
以下是啟用分析工具所需的設定：

|應用程式設定    | 值    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | 適用於您 Application Insights 資源的 iKey    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


您可以使用 [Azure Resource Manager 範本](./azure-web-apps.md#app-service-application-settings-with-azure-resource-manager)、 [Azure PowerShell](/powershell/module/az.websites/set-azwebapp)  [Azure CLI](/cli/azure/webapp/config/appsettings)來設定這些值。

### <a name="enabling-profiler-for-other-clouds-manually"></a>針對其他雲端手動啟用 Profiler

如果您想要針對其他雲端啟用此分析工具，即可使用下列應用程式設定。

|應用程式設定    | 美國政府值| 中國雲端 |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>停用 Profiler

若要針對個別應用程式的執行個體停止或重新啟動 Profiler，請在 [WebJobs] 底下，停止名為 ApplicationInsightsProfiler3 的 WebJob。 即使已使用 Application Insights 頁面上的參數來停用分析工具 (如上所述)，分析工具處理序仍將執行。 分析工具將進行檢查，以查看其是否已啟用。 若已停用，其將進入睡眠模式一段時間，然後再次進行檢查。 若已停用，則不會進行任何分析。 如果您停用此 WebJob，分析工具處理序將完全不會執行，即使進行檢查以查看其是否已啟用也一樣。

  ![停用 Web 作業的 Profiler][disable-profiler-webjob]

建議您在所有應用程式上都啟用 Profiler，以儘早找出任何效能問題。

使用 WebDeploy 來將變更部署至 Web 應用程式時，可以刪除 Profiler 的檔案。 您可以藉由拒絕在部署期間刪除 App_Data 資料夾來防止刪除。 


## <a name="next-steps"></a>後續步驟

* [在 Visual Studio 中使用 Application Insights](./visual-studio.md)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png

