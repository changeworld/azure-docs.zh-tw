---
title: 使用 Application Insights 來分析即時 Azure App Service 應用程式 | Microsoft Docs
description: 使用 Application Insights Profiler 來分析 Azure App Service 上的即時應用程式。
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 4d41ece86240a20afea06bff3469b5c02c6e46ff
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121192"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>使用 Application Insights 來分析即時 Azure App Service 應用程式

您可以使用基本服務層級或更高的版本，在 ASP.NET 上執行 Profiler，並 ASP.NET Core 在 Azure App Service 上執行的應用程式。 目前僅可透過[這個方法](profiler-aspnetcore-linux.md)，在 Linux 上啟用 Profiler。

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> 為應用程式啟用 Profiler
若要為應用程式啟用 Profiler，請依照下列指示進行操作。 如果您正在執行不同類型的 Azure 服務，以下是在其他支援的平臺上啟用 Profiler 的指示：
* [雲端服務](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric 應用程式](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [虛擬機器](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler 會預先安裝為應用程式服務執行時間的一部分。 下列步驟將說明如何為您的 App Service 啟用它。 即使您的應用程式在組建階段中包含 App Insights SDK，仍請遵循這些步驟。

1. 針對您的 app service 啟用 [Always On] 設定。 您可以在 App Service [一般設定] 底下的 [設定] 頁面中，更新設定。
1. 移至 Azure 入口網站中的 [App Service]**** 窗格。
1. 瀏覽至 [設定] > [Application Insights]**** 窗格。

   ![在 App Service 入口網站上啟用 Application Insights](./media/profiler/AppInsights-AppServices.png)

1. 依照窗格上的指示來建立新資源，或選取現有的 App Insights 資源來監視您的應用程式。 也請確定 Profiler 為 [開啟]****。 如果您的 Application Insights 資源與您 App Service 的訂用帳戶不同，則無法使用此頁面來設定 Application Insights。 但您仍然可以手動建立必要的應用程式設定來執行此動作。 [下一節包含手動啟用 Profiler 的指示。](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![新增 App Insights 網站延伸模組][Enablement UI]

1. 現在已使用 App Service 應用程式設定來啟用 Profiler。

    ![Profiler 的應用程式設定][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>手動啟用 Profiler 或使用 Azure Resource Manager
Application Insights Profiler 可以藉由建立 Azure App Service 的應用程式設定來啟用。 上面顯示選項的頁面會為您建立這些應用程式設定。 但是，您可以使用範本或其他方式，將這些設定的建立作業自動化。 如果您的 Application Insights 資源與 Azure App Service 不同的訂用帳戶，這些設定也會有效。
以下是啟用 profiler 所需的設定：

|應用程式設定    | 值    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights 資源的 iKey    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~ 3 |


您可以使用[Azure Resource Manager 範本](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager)、 [Azure Powershell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp) [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest)來設定這些值。

### <a name="enabling-profiler-for-other-clouds-manually"></a>手動啟用其他雲端的 Profiler

如果您想要啟用其他雲端的分析工具，您可以使用下列應用程式設定。

|應用程式設定    | 美國政府值| 中國雲端 |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://agent.serviceprofiler.azure.us`    | `https://profiler.applicationinsights.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>停用 Profiler

若要為個別應用程式的執行個體停止或重新啟動 Profiler，請在 [Web 工作]**** 底下，移至該應用程式資源。 若要刪除 Profiler，請移至 [擴充功能]****。

![停用 Web 作業的 Profiler][disable-profiler-webjob]

建議您在所有應用程式上都啟用 Profiler，以儘早找出任何效能問題。

使用 WebDeploy 將變更部署至 web 應用程式時，可以刪除 Profiler 的檔案。 您可以排除在部署期間刪除 App_Data 資料夾，藉此防止刪除。 


## <a name="next-steps"></a>後續步驟

* [在 Visual Studio 中使用 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
