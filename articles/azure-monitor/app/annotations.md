---
title: Application Insights 的發行註解 | Microsoft Docs
description: 在 Application Insights 中對計量瀏覽器新增部署或建置標記。
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: e0e2a106b276110e13b3c68889e4d1d349ba73a4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666508"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Application Insights 中度量圖表上的註解

[計量瀏覽器](../../azure-monitor/app/metrics-explorer.md)圖表上的批註會顯示您部署新組建或其他重要事件的位置。 批註可讓您輕鬆查看您的變更是否對應用程式效能有任何影響。 [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/tasks/)組建系統可以自動建立這些元件。 您也可以從 PowerShell 建立註解來標記您想要的任何事件。

> [!NOTE]
> 本文反映已被取代的**傳統計量體驗**。 註解目前僅適用於傳統經驗和 **[活頁簿](../../azure-monitor/app/usage-workbooks.md)** 中。 若要深入瞭解目前的計量體驗，請參閱[Azure 計量瀏覽器的先進功能](../../azure-monitor/platform/metrics-charts.md)。

![批註的範例](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Azure Pipelines 組建的發行批註

發行批註是 Azure DevOps 的雲端式 Azure Pipelines 服務的一項功能。

### <a name="install-the-annotations-extension-one-time"></a>安裝註解擴充功能 (一次)
若要能夠建立發行批註，您必須安裝 Visual Studio Marketplace 中提供的許多 Azure DevOps 擴充功能之一。

1. 登入您的[Azure DevOps](https://azure.microsoft.com/services/devops/)專案。
   
1. 在 [Visual Studio Marketplace[版本注釋延伸](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)模組] 頁面上，選取您的 Azure DevOps 組織，然後選取 [**安裝**]，將擴充功能新增至您的 Azure DevOps 組織。
   
   ![選取 Azure DevOps 組織，然後選取 [安裝]。](./media/annotations/1-install.png)
   
您只需要為您的 Azure DevOps 組織安裝此延伸模組一次。 您現在可以為組織中的任何專案設定發行批註。

### <a name="configure-release-annotations"></a>設定發行註解

為每個 Azure Pipelines 版本範本建立個別的 API 金鑰。

1. 登入[Azure 入口網站](https://portal.azure.com)，然後開啟監視您應用程式的 Application Insights 資源。 或者，如果您沒有，請[建立新的 Application Insights 資源](../../azure-monitor/app/app-insights-overview.md)。
   
1. 開啟 [ **API 存取**] 索引標籤，並複製**Application Insights 識別碼**。
   
   ![在 [API 存取] 底下，複製 [應用程式識別碼]。](./media/annotations/2-app-id.png)

1. 在另一個瀏覽器視窗中，開啟或建立管理 Azure Pipelines 部署的發行範本。
   
1. 選取 [**新增**工作]，然後從功能表中選取 [ **Application Insights 發行注釋**] 工作。
   
   ![選取 [新增工作]，然後選取 [Application Insights 版本注釋]。](./media/annotations/3-add-task.png)

   > [!NOTE]
   > 發行批註工作目前僅支援以 Windows 為基礎的代理程式;它不會在 Linux、macOS 或其他類型的代理程式上執行。
   
1. 在 [**應用程式識別碼**] 底下，貼上您從 [ **API 存取**] 索引標籤複製的 Application Insights 識別碼。
   
   ![貼上 Application Insights 識別碼](./media/annotations/4-paste-app-id.png)
   
1. 回到 [Application Insights **API 存取**] 視窗中，選取 [**建立 API 金鑰**]。 
   
   ![在 [API 存取] 索引標籤中，選取 [建立 API 金鑰]。](./media/annotations/5-create-api-key.png)
   
1. 在 [**建立 API 金鑰**] 視窗中，輸入描述，選取 [**寫入注釋**]，然後選取 [**產生金鑰**]。 複製新的金鑰。
   
   ![在 [建立 API 金鑰] 視窗中，輸入描述，選取 [寫入注釋]，然後選取 [產生金鑰]。](./media/annotations/6-create-api-key.png)
   
1. 在 [發行範本] 視窗的 [**變數**] 索引標籤上 **，選取 [新增]** 以建立新 API 金鑰的變數定義。

1. 在 [**名稱**] 下，輸入 `ApiKey`，然後在 [**值**] 下，貼上您從 [ **api 存取**] 索引標籤複製的 api 金鑰。
   
   ![在 [Azure DevOps 變數] 索引標籤中，選取 [新增]，將變數命名為 ApiKey，並將 API 金鑰貼入 [值] 底下。](./media/annotations/7-paste-api-key.png)
   
1. 在主要發行範本視窗中選取 [**儲存**]，以儲存範本。

## <a name="view-annotations"></a>檢視註釋
現在，每當您使用發行範本來部署新的版本時，就會將批註傳送至 Application Insights。 批註會顯示在**計量瀏覽器**的圖表上。

選取任何註解標記（淺灰色箭號）以開啟有關發行的詳細資料，包括要求者、原始檔控制分支、發行管線和環境。

![選取發行註解標記。](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>從 PowerShell 建立自訂註解
您可以使用來自 GitHub 的[CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell 腳本，從任何您喜歡的進程建立批註，而不需要使用 Azure DevOps。 

1. 建立[CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)的本機複本。
   
1. 使用上述程式中的步驟來取得您的 Application Insights 識別碼，並從您的 Application Insights **Api 存取**索引標籤建立 API 金鑰。
   
1. 使用下列程式碼呼叫 PowerShell 腳本，並以您的值取代以角括弧括住的預留位置。 `-releaseProperties` 是選擇性的。 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

您可以修改腳本，例如建立過去的注釋。

## <a name="next-steps"></a>後續步驟

* [建立工作項目](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [使用 PowerShell 進行自動化](../../azure-monitor/app/powershell.md)
