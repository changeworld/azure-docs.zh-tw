---
title: 使用 Application Insights 來分析即時 Azure App Service 應用程式 | Microsoft Docs
description: 使用 Application Insights Profiler 來分析 Azure App Service 上的即時應用程式。
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ba9a2aca73dbdb8de298b68670fd6ab16f810a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275772"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>使用 Application Insights 來分析即時 Azure App Service 應用程式

您可以在ASP.NET上運行探測器，ASP.NET使用基本服務層或更高級別的 Azure 應用服務上運行的 Core 應用。 目前僅可透過[這個方法](profiler-aspnetcore-linux.md)，在 Linux 上啟用 Profiler。

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> 為應用程式啟用 Profiler
若要為應用程式啟用 Profiler，請依照下列指示進行操作。 如果您正在運行不同類型的 Azure 服務，以下是在其他受支援平臺上啟用探測器的說明：
* [雲端服務](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric 應用程式](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [虛擬機器](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

應用程式見解探測器作為應用服務運行時的一部分預先安裝。 以下步驟將向您展示如何為應用服務啟用它。 即使您在生成時在應用程式中包含應用見解 SDK，也請執行以下步驟。

1. 為應用服務啟用"始終打開"設置。 您可以在"常規設置"下更新應用服務"配置"頁的設置。
1. 移至 Azure 入口網站中的 [App Service]**** 窗格。
1. 瀏覽至 [設定] > [Application Insights]**** 窗格。

   ![在 App Service 入口網站上啟用 Application Insights](./media/profiler/AppInsights-AppServices.png)

1. 依照窗格上的指示來建立新資源，或選取現有的 App Insights 資源來監視您的應用程式。 也請確定 Profiler 為 [開啟]****。 如果應用程式見解資源與應用服務處於不同的訂閱中，則不能使用此頁面配置應用程式見解。 您仍可以通過手動創建必要的應用設置手動執行此操作。 [下一節包含手動啟用探測器的說明。](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![新增 App Insights 網站延伸模組][Enablement UI]

1. 現在已使用 App Service 應用程式設定來啟用 Profiler。

    ![Profiler 的應用程式設定][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>手動啟用探測器或與 Azure 資源管理器一起
可以通過為 Azure 應用服務創建應用設置來啟用應用程式見解探測器。 具有上述選項的頁面為您創建這些應用設置。 但是，您可以使用範本或其他方法自動創建這些設置。 如果應用程式見解資源與 Azure 應用服務處於不同的訂閱中，則這些設置也將起作用。
以下是啟用探測器所需的設置：

|應用程式設定    | 值    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | 應用程式見解資源的 iKey    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | #3 |


您可以使用[Azure 資源管理器範本](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager)[、Azure 電源外殼](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp)[、Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest)來設置這些值。

### <a name="enabling-profiler-for-other-clouds-manually"></a>手動為其他雲啟用探測器

如果要為其他雲啟用探測器，可以使用以下應用設置。

|應用程式設定    | 美國政府價值觀| 雲彩 |   
|---------------|---------------------|-------------|
|應用程式見解設定檔端點         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|應用程式洞察端點 | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>停用 Profiler

若要為個別應用程式的執行個體停止或重新啟動 Profiler，請在 [Web 工作]**** 底下，移至該應用程式資源。 若要刪除 Profiler，請移至 [擴充功能]****。

![停用 Web 作業的 Profiler][disable-profiler-webjob]

建議您在所有應用程式上都啟用 Profiler，以儘早找出任何效能問題。

使用 WebDeploy 將更改部署到 Web 應用程式時，可以刪除探測器的檔。 您可以通過在部署期間排除App_Data資料夾來防止刪除。 


## <a name="next-steps"></a>後續步驟

* [在 Visual Studio 中使用 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
