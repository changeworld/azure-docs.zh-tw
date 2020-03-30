---
title: Application Insights 的發行註解 | Microsoft Docs
description: 在 Application Insights 中對計量瀏覽器新增部署或建置標記。
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: e0e2a106b276110e13b3c68889e4d1d349ba73a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666508"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Application Insights 中度量圖表上的註解

[指標資源管理器](../../azure-monitor/app/metrics-explorer.md)圖表上的注釋顯示部署新生成或其他重大事件的位置。 注釋便於查看更改是否對應用程式的性能有任何影響。 它們可以由[Azure 管道](https://docs.microsoft.com/azure/devops/pipelines/tasks/)生成系統自動創建。 您也可以從 PowerShell 建立註解來標記您想要的任何事件。

> [!NOTE]
> 本文反映已被取代的**傳統計量體驗**。 註解目前僅適用於傳統經驗和**[活頁簿](../../azure-monitor/app/usage-workbooks.md)** 中。 要瞭解有關當前指標體驗的更多，請參閱 Azure[指標資源管理器的高級功能](../../azure-monitor/platform/metrics-charts.md)。

![注釋示例](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>使用 Azure 管道生成發佈批註

發佈注釋是 Azure DevOps 基於雲的 Azure 管道服務的一項功能。

### <a name="install-the-annotations-extension-one-time"></a>安裝註解擴充功能 (一次)
為了能夠創建發佈注釋，您需要安裝視覺化工作室應用商店中可用的許多 Azure DevOps 擴展之一。

1. 登錄到 Azure [DevOps](https://azure.microsoft.com/services/devops/)專案。
   
1. 在視覺化工作室應用商店[發佈注釋擴展頁上](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)，選擇 Azure DevOps 組織，然後選擇 **"安裝"** 以將擴展添加到 Azure DevOps 組織。
   
   ![選擇 Azure DevOps 組織，然後選擇"安裝"。](./media/annotations/1-install.png)
   
只需為 Azure DevOps 組織安裝一次擴展。 您現在可以為組織中的任何專案配置發佈注釋。

### <a name="configure-release-annotations"></a>設定發行註解

為每個 Azure 管道發佈範本創建單獨的 API 金鑰。

1. 登錄到 Azure[門戶](https://portal.azure.com)並打開監視應用程式的應用程式見解資源。 或者，如果沒有，[請創建新的應用程式見解資源](../../azure-monitor/app/app-insights-overview.md)。
   
1. 打開**API 訪問**選項卡並複製**應用程式見解 ID**。
   
   ![在 API 訪問下，複製應用程式 ID。](./media/annotations/2-app-id.png)

1. 在單獨的瀏覽器視窗中，打開或創建管理 Azure 管道部署的發佈範本。
   
1. 選擇 **"添加任務**"，然後從功能表中選擇 **"應用程式見解發佈注釋"** 任務。
   
   ![選擇"添加任務"並選擇"應用程式見解發佈注釋"。](./media/annotations/3-add-task.png)

   > [!NOTE]
   > 發佈注釋任務當前僅支援基於 Windows 的代理;它不會在 Linux、macOS 或其他類型的代理上運行。
   
1. 在 **"應用程式 ID"** 下，粘貼從 API**訪問**選項卡複製的應用程式見解 ID。
   
   ![粘貼應用程式見解 ID](./media/annotations/4-paste-app-id.png)
   
1. 返回應用程式見解**API 訪問**視窗，選擇 **"創建 API 金鑰**"。 
   
   ![在 API 訪問選項卡中，選擇"創建 API 金鑰"。](./media/annotations/5-create-api-key.png)
   
1. 在 **"創建 API 鍵"** 視窗中，鍵入說明，選擇 **"寫入批註**"，然後選擇 **"生成鍵**"。 複製新的金鑰。
   
   ![在"創建 API 鍵"視窗中，鍵入說明，選擇"寫入批註"，然後選擇"生成鍵"。](./media/annotations/6-create-api-key.png)
   
1. 在發佈範本視窗中，在 **"變數"** 選項卡上，選擇 **"添加"** 以創建新 API 鍵的變數定義。

1. 在 **"名稱**"`ApiKey`下，輸入 和 "**值**"下，粘貼從**API Access**選項卡複製的 API 金鑰。
   
   ![在 Azure DevOps 變數選項卡中，選擇"添加"，命名變數 ApiKey，並在"值"下粘貼 API 鍵。](./media/annotations/7-paste-api-key.png)
   
1. 選擇 **"在**主發佈範本視窗中保存"以保存範本。

## <a name="view-annotations"></a>檢視註解
現在，每當使用發佈範本部署新版本時，都會向應用程式見解發送注釋。 注釋顯示在**指標資源管理器**中的圖表上。

選擇任何注釋標記（淺灰色箭頭）以打開有關發佈的詳細資訊，包括請求器、原始程式碼管理分支、發佈管道和環境。

![選擇發佈注釋標記。](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>從 PowerShell 建立自訂註解
您可以使用 GitHub 的[CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell 腳本從任何你喜歡的進程創建批註，而無需使用 Azure DevOps。 

1. 製作[CreateRelease的本機複本.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)。
   
1. 使用上述過程中的步驟獲取應用程式見解 ID 並從應用程式見解**API 訪問**選項卡創建 API 金鑰。
   
1. 使用以下代碼調用 PowerShell 腳本，將角度括弧的預留位置替換為值。 是`-releaseProperties`可選的。 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

您可以修改腳本，例如為過去創建注釋。

## <a name="next-steps"></a>後續步驟

* [建立工作項目](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [使用 PowerShell 進行自動化](../../azure-monitor/app/powershell.md)
