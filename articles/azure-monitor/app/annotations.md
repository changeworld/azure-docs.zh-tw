---
title: Application Insights 的發行註解 | Microsoft Docs
description: 在 Application Insights 中對計量瀏覽器新增部署或建置標記。
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 58f6603687838713fafbf4cd5cc3f100e22b7401
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758178"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Application Insights 中度量圖表上的註解

批註會顯示您部署新組建或其他重要事件的位置。 批註可讓您輕鬆查看您的變更是否對應用程式的效能產生任何影響。 [Azure Pipelines](/azure/devops/pipelines/tasks/)組建系統可以自動建立它們。 您也可以從 PowerShell 建立註解來標記您想要的任何事件。

## <a name="release-annotations-with-azure-pipelines-build"></a>Azure Pipelines 組建的發行批註

版本注釋是 Azure DevOps 雲端式 Azure Pipelines 服務的一項功能。

### <a name="install-the-annotations-extension-one-time"></a>安裝註解擴充功能 (一次)

若要能夠建立發行批註，您必須安裝 Visual Studio Marketplace 中許多可用 Azure DevOps 擴充功能的其中一個。

1. 登入您的 [Azure DevOps](https://azure.microsoft.com/services/devops/) 專案。
   
1. 在 [Visual Studio Marketplace [版本注釋延伸](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) 模組] 頁面上，選取您的 Azure DevOps 組織，然後選取 [ **安裝** ]，將延伸模組新增至您的 Azure DevOps 組織。
   
   ![選取 Azure DevOps 的組織，然後選取 [安裝]。](./media/annotations/1-install.png)
   
您只需要為您的 Azure DevOps 組織安裝一次延伸模組。 您現在可以為組織中的任何專案設定發行批註。

### <a name="configure-release-annotations"></a>設定發行註解

針對每個 Azure Pipelines 發行範本建立個別的 API 金鑰。

1. 登入 [Azure 入口網站](https://portal.azure.com) ，然後開啟監視您應用程式的 Application Insights 資源。 如果您沒有帳戶，請 [建立新的 Application Insights 資源](./app-insights-overview.md)。
   
1. 開啟 [ **API 存取** ] 索引標籤，並複製 **Application Insights 識別碼**。
   
   ![在 [API 存取] 下，複製應用程式識別碼。](./media/annotations/2-app-id.png)

1. 在另一個瀏覽器視窗中，開啟或建立管理您的 Azure Pipelines 部署的發行範本。
   
1. 選取 [ **新增**工作]，然後從功能表中選取 [ **Application Insights 發行注釋** ] 工作。
   
   ![選取 [新增工作]，然後選取 Application Insights 版本注釋。](./media/annotations/3-add-task.png)

   > [!NOTE]
   > [發行注釋] 工作目前僅支援以 Windows 為基礎的代理程式;它不會在 Linux、macOS 或其他類型的代理程式上執行。
   
1. 在 [ **應用程式識別碼**] 下，貼上您從 [ **API 存取** ] 索引標籤複製的 Application Insights 識別碼。
   
   ![貼上 Application Insights 識別碼](./media/annotations/4-paste-app-id.png)
   
1. 回到 [Application Insights **Api 存取** ] 視窗中，選取 [ **建立 API 金鑰**]。 
   
   ![在 [API 存取] 索引標籤中，選取 [建立 API 金鑰]。](./media/annotations/5-create-api-key.png)
   
1. 在 [ **建立 API 金鑰** ] 視窗中，輸入描述、選取 [ **寫入附注**]，然後選取 [ **產生金鑰**]。 複製新的金鑰。
   
   ![在 [建立 API 金鑰] 視窗中，輸入描述、選取 [寫入附注]，然後選取 [產生金鑰]。](./media/annotations/6-create-api-key.png)
   
1. 在 [發行範本] 視窗的 [ **變數** ] 索引標籤上 **，選取 [新增]** 以建立新 API 金鑰的變數定義。

1. 在 [ **名稱**] 底下，輸入 `ApiKey` ，並在 [ **值**] 底下，貼上您從 [ **API 存取** ] 索引標籤複製的 api 金鑰。
   
   ![在 [Azure DevOps 變數] 索引標籤中，選取 [新增]、為變數命名 ApiKey，然後在 [值] 底下貼上 API 金鑰。](./media/annotations/7-paste-api-key.png)
   
1. 選取 [主要發行範本] 視窗中的 [ **儲存** ]，以儲存範本。


   > [!NOTE]
   > API 金鑰的限制如 [REST API 速率限制檔](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits)中所述。

## <a name="view-annotations"></a>檢視註解


   > [!NOTE]
   > 版本注釋目前無法在 [計量] 窗格中使用 Application Insights

現在，每當您使用發行範本來部署新版本時，就會將批註傳送至 Application Insights。 批註可以在下列位置中查看：

您也可以手動建立發行批註的 [使用方式] 窗格：

![橫條圖的螢幕擷取畫面，其中顯示在一段時間內顯示的使用者造訪次數。 版本批註會顯示為圖表上方的綠色核取記號，表示發生發行的時間點](./media/annotations/usage-pane.png)

在任何以記錄為基礎的活頁簿查詢中，視覺效果沿著 X 軸顯示時間。

![螢幕擷取畫面：有顯示批註的時間序列記錄式查詢的活頁簿窗格](./media/annotations/workbooks-annotations.png)

若要啟用活頁簿中的批註，請移至 [ **Advanced Settings** ]，然後選取 [ **顯示批註**]

![[Advanced Settings] 功能表的螢幕擷取畫面，其中的單字顯示批註醒目提示，並在設定旁邊顯示核取記號，以啟用它。](./media/annotations/workbook-show-annotations.png)

選取任何註解標記以開啟發行的詳細資料，包括要求者、原始檔控制分支、發行管線和環境。

## <a name="create-custom-annotations-from-powershell"></a>從 PowerShell 建立自訂註解
您可以使用 GitHub 中的 [CreateReleaseAnnotation](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell 腳本，從任何您喜歡的進程建立批註，而不需要使用 Azure DevOps。 

1. 製作 [CreateReleaseAnnotation.ps1](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)的本機複本。
   
1. 使用上述程式中的步驟，從您的 Application Insights **API 存取** 索引標籤取得 Application Insights 識別碼和建立 API 金鑰。
   
1. 使用下列程式碼來呼叫 PowerShell 腳本，並將以角括弧括住的預留位置取代為您的值。 `-releaseProperties`是選擇性的。 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

您可以修改腳本，例如建立過去的批註。

## <a name="next-steps"></a>後續步驟

* [建立工作項目](./diagnostic-search.md#create-work-item)
* [使用 PowerShell 進行自動化](./powershell.md)

