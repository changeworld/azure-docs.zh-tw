---
title: 使用 Azure DevOps 建立串流分析作業的 CI/CD 管線
description: 本文說明如何在中設定 Azure 串流分析作業的持續整合和部署 (CI/CD) 管線 Azure DevOps
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: d9b6dfc977aab7d8907b5d3c3851a22f96227d78
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91757753"
---
# <a name="use-azure-devops-to-create-a-cicd-pipeline-for-a-stream-analytics-job"></a>使用 Azure DevOps 建立串流分析作業的 CI/CD 管線

在本文中，您會瞭解如何使用 Azure 串流分析 CI/CD 工具來建立 Azure DevOps [組建](/azure/devops/pipelines/get-started/pipelines-get-started) 和 [發行](/azure/devops/pipelines/release/define-multistage-release-process) 管線。

## <a name="commit-your-stream-analytics-project"></a>認可您的串流分析專案

開始之前，請將您的完整串流分析專案作為來源檔案認可至 [Azure DevOps](/azure/devops/user-guide/source-control) 存放庫。 您可以在 Azure Pipelines 中參考此 [範例存放庫](https://dev.azure.com/wenyzou/azure-streamanalytics-cicd-demo) 和 [串流分析專案原始碼](https://dev.azure.com/wenyzou/_git/azure-streamanalytics-cicd-demo?path=%2FmyASAProject) 。

本文中的步驟會使用串流分析 Visual Studio Code 專案。 如果您是使用 Visual Studio 專案，請遵循 [使用 CI/CD 工具來自動化 Azure 串流分析作業的組建、測試和部署](cicd-tools.md)的步驟。

## <a name="create-a-build-pipeline"></a>建立組建管線

在本節中，您會瞭解如何建立組建管線。 您可以在 Azure DevOps 中參考此範例 [自動組建和測試管線](https://dev.azure.com/wenyzou/_git/azure-streamanalytics-cicd-demo?path=%2FmyASAProject) 。

1. 開啟網頁瀏覽器，並在 Azure DevOps 中流覽至您的專案。  

1. 在左側導覽功能表中的 [ **管線** ] 底下，選取 [ **組建**]。 然後選取 [ **新增管線**]。

   :::image type="content" source="media/set-up-cicd-pipeline/new-pipeline.png" alt-text="建立新的 Azure 管線":::

1. 選取 **[使用傳統編輯器** ] 來建立不含 YAML 的管線。

1. 選取您的來源類型、team 專案和儲存機制。 然後選取 [ **繼續**]。

   :::image type="content" source="media/set-up-cicd-pipeline/select-repo.png" alt-text="建立新的 Azure 管線":::

1. 在 [ **選擇範本** ] 頁面上，選取 [ **空白作業**]。

## <a name="install-npm-package"></a>安裝 npm 套件

1. **在 [工作**] 頁面上，選取 [**代理程式作業 1**] 旁的加號。 在 [工作搜尋] 中輸入 *npm* ，然後選取 [ **npm**]。

   :::image type="content" source="media/set-up-cicd-pipeline/search-npm.png" alt-text="建立新的 Azure 管線":::

2. 提供工作的 **顯示名稱**。 將 **命令** 選項變更為 *自訂* ，並在 **命令和引數**中輸入下列命令。 保留其餘的預設選項。

   ```bash
   install -g azure-streamanalytics-cicd
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/npm-config.png" alt-text="建立新的 Azure 管線":::

## <a name="add-a-build-task"></a>新增組建工作

1. 在 [**變數**] 頁面上，選取 [ **+ 加入****管線變數**]。 加入下列變數。 根據您的喜好設定來設定下列值：

   |變數名稱|值|
   |-|-|
   |projectRootPath|>yourprojectname|
   |outputPath|輸出|
   |deployPath|部署|

2. **在 [工作**] 頁面上，選取 [**代理程式作業 1**] 旁的加號。 搜尋 **命令列**。

3. 提供工作的 **顯示名稱** ，然後輸入下列腳本。 使用您的存放庫名稱和專案名稱來修改腳本。

   ```bash
   azure-streamanalytics-cicd build -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(deployPath)
   ```

   下圖使用串流分析 Visual Studio Code 專案作為範例。

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-build.png" alt-text="建立新的 Azure 管線":::

## <a name="add-a-test-task"></a>新增測試工作

1. 在 [**變數**] 頁面上，選取 [ **+ 加入****管線變數**]。 加入下列變數。 使用您的輸出路徑和存放庫名稱來修改值。

   |變數名稱|值|
   |-|-|
   |testPath|測試|

   :::image type="content" source="media/set-up-cicd-pipeline/pipeline-variables-test.png" alt-text="建立新的 Azure 管線":::

2. **在 [工作**] 頁面上，選取 [**代理程式作業 1**] 旁的加號。 搜尋 **命令列**。

3. 提供工作的 **顯示名稱** ，然後輸入下列腳本。 使用您的專案檔名稱和測試組態檔的路徑來修改腳本。 

   如需如何新增及設定測試案例的詳細資訊，請參閱 [自動化測試指示](cicd-tools.md#automated-test) 。

   ```bash
   azure-streamanalytics-cicd test -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(testPath) -testConfigPath $(projectRootPath)/test/testConfig.json 
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-test.png" alt-text="建立新的 Azure 管線":::

## <a name="add-a-copy-files-task"></a>新增複製檔案工作

您需要新增「複製檔案」工作，以將測試摘要檔和 Azure Resource Manager 範本檔案複製到成品資料夾。 

1. **在 [** 工作] 頁面上，選取 [ **+** **代理程式作業 1**] 旁的。 搜尋 **複製**檔案。 然後輸入下列設定。 藉由指派 `**` 給 **內容**，就會複製測試結果的所有檔案。

   |參數|輸入|
   |-|-|
   |顯示名稱|將檔案複製到： $ (build. artifactstagingdirectory) |
   |來源資料夾|`$(system.defaultworkingdirectory)/$(outputPath)/`|
   |目錄| `**` |
   |目的檔案夾| `$(build.artifactstagingdirectory)`|

2. 展開 [ **控制選項**]。 **除非先前的工作失敗，否則請選取 [執行此工作中的組建已取消**]。 **Run this task**

   :::image type="content" source="media/set-up-cicd-pipeline/copy-config.png" alt-text="建立新的 Azure 管線":::

## <a name="add-a-publish-build-artifacts-task"></a>新增發行組建成品工作

1. **在 [工作**] 頁面上，選取 [**代理程式作業 1**] 旁的加號。 搜尋 [ **發行組建** 成品]，然後選取具有黑色箭號圖示的選項。

2. 展開 [ **控制選項**]。 **除非先前的工作失敗，否則請選取 [執行此工作中的組建已取消**]。 **Run this task**

   :::image type="content" source="media/set-up-cicd-pipeline/publish-config.png" alt-text="建立新的 Azure 管線":::

## <a name="save-and-run"></a>儲存並執行

當您完成新增 npm 封裝、命令列、複製檔案和發佈組建成品工作之後，請選取 [ **儲存 & 佇列**]。 當系統提示您時，請輸入儲存批註，然後選取 [ **儲存並執行**]。 您可以從管線的 [ **摘要** ] 頁面下載測試結果。

## <a name="check-the-build-and-test-results"></a>檢查組建和測試結果

您可以在 **已發行** 的資料夾中找到測試摘要檔案和 Azure Resource Manager 範本檔案。

   :::image type="content" source="media/set-up-cicd-pipeline/check-build-test-result.png" alt-text="建立新的 Azure 管線":::

   :::image type="content" source="media/set-up-cicd-pipeline/check-drop-folder.png" alt-text="建立新的 Azure 管線":::

## <a name="release-with-azure-pipelines"></a>Azure Pipelines 的版本

在本節中，您將瞭解如何建立發行管線。 您可以在 Azure DevOps 中參考此範例 [發行管線](https://dev.azure.com/wenyzou/azure-streamanalytics-cicd-demo/_release?_a=releases&view=mine&definitionId=2&preserve-view=true) 。

開啟網頁瀏覽器，並流覽至您的 Azure 串流分析 Visual Studio Code 專案。

1. 在左側導覽功能表中的 [ **管線** ] 底下，選取 [ **發行**]。 然後選取 [ **新增管線**]。

2. 選取 [ **從空白作業開始**]。

3. **在 [成品**] 方塊中，選取 [ **+ 新增**成品]。 在 [ **來源**] 底下，選取您建立的組建管線，然後選取 [ **新增**]。

   :::image type="content" source="media/set-up-cicd-pipeline/build-artifact.png" alt-text="建立新的 Azure 管線":::

4. 變更 **第1階段** 的名稱，將 **作業部署至測試環境**。

5. 新增階段，並將其 **部署作業命名為生產環境**。

### <a name="add-deploy-tasks"></a>新增部署工作

1. 從 [工作] 下拉式清單中，選取 [ **將作業部署至測試環境**]。

2. 選取 [ **+** **代理程式作業** ] 旁的，然後搜尋 **ARM 範本部署**。 輸入下列參數：

   |參數|值|
   |-|-|
   |顯示名稱| *部署 myASAProject*|
   |Azure 訂用帳戶| 選擇您的訂用帳戶。|
   |動作| *建立或更新資源群組*|
   |資源群組| 選擇將包含您的串流分析作業的測試資源組名。|
   |Location|選擇您的測試資源群組的位置。|
   |範本位置| 連結的成品|
   |範本| $ (System.defaultworkingdirectory) /_azure->mslearn-streamanalytics-cicd-示範-部署/捨棄/myASAProject.JobTemplate.js開啟 |
   |範本參數|$ (System.defaultworkingdirectory) /_azure->mslearn-streamanalytics-cicd-示範-部署/捨棄/myASAProject.JobTemplate.parameters.js開啟 |
   |覆寫範本參數|-<arm_template_parameter> 「您的值」。 您可以使用 **變數**來定義參數。|
   |部署模式|累加|

3. 從 [工作] 下拉式清單中，選取 [ **將作業部署到生產環境**]。

4. 選取 [ **+** **代理程式作業** ] 旁的，然後搜尋 *ARM 範本部署*。 輸入下列參數：

   |參數|值|
   |-|-|
   |顯示名稱| *部署 myASAProject*|
   |Azure 訂用帳戶| 選擇您的訂用帳戶。|
   |動作| *建立或更新資源群組*|
   |資源群組| 選擇將包含您的串流分析作業的生產資源群組的名稱。|
   |Location|選擇生產資源群組的位置。|
   |範本位置| *連結的成品*|
   |範本| $ (System.defaultworkingdirectory) /_azure->mslearn-streamanalytics-cicd-示範-部署/捨棄/myASAProject.JobTemplate.js開啟 |
   |範本參數|$ (System.defaultworkingdirectory) /_azure->mslearn-streamanalytics-cicd-示範-部署/捨棄/myASAProject.JobTemplate.parameters.js開啟 |
   |覆寫範本參數|-<arm_template_parameter> 「您的值」|
   |部署模式|累加|

### <a name="create-a-release"></a>建立發行

若要建立發行，請選取右上角的 [ **建立發行** ]。

:::image type="content" source="media/set-up-cicd-pipeline/create-release.png" alt-text="建立新的 Azure 管線":::

## <a name="next-steps"></a>後續步驟

* [Azure 串流分析的持續整合與持續部署](cicd-overview.md)
* [使用 CI/CD 工具自動建立、測試及部署 Azure 串流分析作業](cicd-tools.md)