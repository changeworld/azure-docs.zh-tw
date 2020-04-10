---
title: Application Insights 的發行註解 | Microsoft Docs
description: 在 Application Insights 中對計量瀏覽器新增部署或建置標記。
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: 0ad773ca6a7102ac718d43dfbbf6a4f834e681a0
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010699"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Application Insights 中度量圖表上的註解

註釋顯示部署新生成或其他重大事件的位置。 註釋便於查看更改是否對應用程式的性能有任何影響。 它們可以由[Azure 管道](https://docs.microsoft.com/azure/devops/pipelines/tasks/)生成系統自動創建。 您也可以從 PowerShell 建立註解來標記您想要的任何事件。

## <a name="release-annotations-with-azure-pipelines-build"></a>使用 Azure 管道產生宣告註記

發佈註釋是 Azure DevOps 基於雲端的 Azure 管道服務的一項功能。

### <a name="install-the-annotations-extension-one-time"></a>安裝註解擴充功能 (一次)

為了能夠創建發佈註釋,您需要安裝可視化工作室應用商店中可用的許多 Azure DevOps 擴展之一。

1. 登錄到 Azure [DevOps](https://azure.microsoft.com/services/devops/)專案。
   
1. 在可視化工作室應用商店[發佈註釋擴展頁上](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations),選擇 Azure DevOps 組織,然後選擇 **「安裝」** 以將擴展添加到 Azure DevOps 組織。
   
   ![選擇 Azure DevOps 組織,然後選擇" 安裝"](./media/annotations/1-install.png)
   
只需為 Azure DevOps 組織安裝一次擴展。 您現在可以為組織中的任何專案配置發佈註釋。

### <a name="configure-release-annotations"></a>設定發行註解

為每個 Azure 管道發布範本創建單獨的 API 金鑰。

1. 登錄到 Azure[門戶](https://portal.azure.com)並打開監視應用程式的應用程式見解資源。 或,如果沒有,[請建立新的應用程式見解資源](../../azure-monitor/app/app-insights-overview.md)。
   
1. 開啟**API 存**取 選項卡並複製**應用程式的解碼**。
   
   ![在 API 存取下,複製應用程式 ID。](./media/annotations/2-app-id.png)

1. 在單獨的瀏覽器視窗中,打開或創建管理 Azure 管道部署的發佈範本。
   
1. 選擇 **"新增任務**",然後從功能表中選擇 **「應用程式見解發佈註釋」** 任務。
   
   ![選擇「添加任務」並選擇「應用程式見解發佈註釋」。](./media/annotations/3-add-task.png)

   > [!NOTE]
   > 發佈註釋任務目前僅支援基於 Windows 的代理;它不會在 Linux、macOS 或其他類型的代理上運行。
   
1. 在 **「應用程式 ID」** 下,貼上從 API**存取**選項卡複製的應用程式見解 ID。
   
   ![貼上應用程式的代碼](./media/annotations/4-paste-app-id.png)
   
1. 傳回應用程式見解**API 存取**視窗,選擇 **"創建 API 金鑰**" 
   
   ![在 API 存取選項卡中,選擇"創建 API 金鑰"](./media/annotations/5-create-api-key.png)
   
1. 在 **"創建 API 鍵'** 視窗中,鍵入說明,選擇 **「寫入批註**」,然後選擇 **「生成鍵**」。 複製新的金鑰。
   
   ![在"創建 API 鍵"視窗中,鍵入說明,選擇"寫入批註",然後選擇"生成鍵"。](./media/annotations/6-create-api-key.png)
   
1. 在發佈樣本視窗中,在 **「變數」** 選項卡上,選擇 **「添加」** 以創建新 API 鍵的變數定義。

1. 在 **「名稱**」`ApiKey`下,輸入和 **「值**」下,貼上從**API Access**選項卡複製的 API 金鑰。
   
   ![在 Azure DevOps 變數選項卡中,選擇「添加」,命名變數 ApiKey,並在「值」下粘貼 API 鍵。](./media/annotations/7-paste-api-key.png)
   
1. 選擇 **「在**主髮佈樣本視窗中儲存」 以保存範本。

## <a name="view-annotations"></a>檢視註解


   > [!NOTE]
   > 發佈註解目前不在「應用程式見解的指標」窗格中提供

現在,每當使用發佈範本部署新版本時,都會向應用程式見解發送註釋。 註解可以在以下位置檢視:

使用窗格,您還可以手動建立發佈註解:

![條形圖的屏幕截圖,顯示使用者訪問量,持續數小時。 發佈註釋在圖表上方顯示為綠色複選標記,指示發佈發生的瞬間](./media/annotations/usage-pane.png)

在任何基於日誌的工作簿查詢中,可視化效果顯示沿 x 軸的時間。

![工作簿窗格的螢幕截圖,顯示註釋,包含基於時間序列紀錄的查詢](./media/annotations/workbooks-annotations.png)

要在工作簿中啟用批註,請轉到 **「高級設置」** 並選擇 **「顯示註解**」。

![帶有單詞的高級設置功能表的螢幕截圖顯示註釋突出顯示,設置旁邊有複選標記以啟用它。](./media/annotations/workbook-show-annotations.png)

選擇任何註釋標記以打開有關發佈的詳細資訊,包括請求器、原始程式碼管理分支、發布管道和環境。

## <a name="create-custom-annotations-from-powershell"></a>從 PowerShell 建立自訂註解
您可以使用 GitHub 的[CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell 文稿從任何你喜歡的行程創建註解,而無需使用 Azure DevOps。 

1. 製作[CreateRelease 的本地複本 ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)。
   
1. 使用上述過程中的步驟獲取應用程式見解 ID 並從應用程式見解**API 訪問**選項卡創建 API 金鑰。
   
1. 使用以下代碼調用 PowerShell 腳本,將角度括弧的占位符替換為值。 是`-releaseProperties`可選的。 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

您可以修改文稿,例如為過去創建註釋。

## <a name="next-steps"></a>後續步驟

* [建立工作項目](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [使用 PowerShell 進行自動化](../../azure-monitor/app/powershell.md)
