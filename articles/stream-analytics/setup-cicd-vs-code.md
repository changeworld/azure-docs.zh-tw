---
title: 使用 CI/CD npm 套件部署 Azure 串流分析作業
description: 本文說明如何使用 Azure 串流分析 CI/CD npm 套件來設定持續整合和部署程式。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: deb6c2439cc84f196b7f42fd9f49d3ebfd057cbb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76962146"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>使用 CI/CD npm 套件部署 Azure 串流分析作業 

您可以使用 Azure 串流分析 CI/CD npm 套件，為您的串流分析作業設定持續整合和部署程式。 本文說明如何在任何 CI/CD 系統中一般使用 npm 套件，以及透過 Azure Pipelines 進行部署的特定指示。

如需使用 Powershell 進行部署的詳細資訊，請參閱[使用 Resource Manager 範本檔案進行部署和 Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)。 您也可以深入瞭解如何[使用物件做為 Resource Manager 範本中的參數](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)。

## <a name="build-the-vs-code-project"></a>建立 VS Code 專案

您可以使用**mslearn-streamanalytics-cicd** npm 套件來啟用 Azure 串流分析作業的持續整合和部署。 Npm 套件提供工具來產生[串流分析 Visual Studio Code 專案](quick-create-vs-code.md)的 Azure Resource Manager 範本。 它可以在 Windows、macOS 和 Linux 上使用，而不需要安裝 Visual Studio Code。

您可以直接[下載套件](https://www.npmjs.com/package/azure-streamanalytics-cicd)，或透過`npm install -g azure-streamanalytics-cicd`命令以[全域](https://docs.npmjs.com/downloading-and-installing-packages-globally)方式進行安裝。 這是建議的方法，它也可以在**Azure Pipelines**的組建管線的 PowerShell 或 Azure CLI 腳本工作中使用。

安裝套件之後，請使用下列命令來輸出 Azure Resource Manager 範本。 **ScriptPath**引數是您專案中**script.asaql**檔案的絕對路徑。 請確定 asaproj.json 和 Jobconfig.json 檔案位於與腳本檔案相同的資料夾中。 如果未指定**outputPath** ，範本會放在專案**bin**資料夾下的 [**部署**] 資料夾中。

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
範例（在 macOS 上）
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

當串流分析 Visual Studio Code 專案成功建立時，它會在**bin/[Debug/Retail]/Deploy**資料夾下產生下列兩個 Azure Resource Manager 範本檔案： 

*  Resource Manager 範本檔案

       [ProjectName].JobTemplate.json 

*  Resource Manager 參數檔案

       [ProjectName].JobTemplate.parameters.json   

Parameters. json 檔案中的預設參數是來自 Visual Studio Code 專案中的設定。 如果您想要部署到其他環境，請據以取代參數。

> [!NOTE]
> 對於所有認證，預設值都會設為 null。 部署至雲端之前，「必須」**** 設定這些值。

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>使用 Azure Pipelines 部署

本節詳細說明如何使用 npm 建立 Azure Pipelines[組建](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav)和[發行](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)管線。

開啟網頁瀏覽器，並流覽至您的 Azure 串流分析 Visual Studio Code 專案。

1. 在左側導覽功能表中的 [**管線**] 底下，選取 [**組建**]。 然後選取 [**新增管線**]

   ![建立新的 Azure 管線](./media/setup-cicd-vs-code/new-pipeline.png)

2. 選取 **[使用傳統編輯器**建立管線但不 YAML]。

3. 選取您的 [來源類型]、[team 專案] 和 [存放庫]。 接著，選取 [繼續]  。

   ![選取 Azure 串流分析專案](./media/setup-cicd-vs-code/select-repo.png)

4. 在 [**選擇範本**] 頁面上，選取 [**空白作業**]。

### <a name="add-npm-task"></a>新增 npm 工作

1. **在 [工作**] 頁面上，選取 [**代理程式作業 1**] 旁的加號。 在 [工作搜尋] 中輸入 "npm"，然後選取 [ **npm**]。

   ![選取 npm 工作](./media/setup-cicd-vs-code/search-npm.png)

2. 提供工作的**顯示名稱**。 將 [**命令**] 選項變更為 [*自訂*]，並在**命令和引數**中輸入下列命令。 保留其餘的預設選項。

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![輸入 npm 工作的設定](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>新增命令列工作

1. **在 [工作**] 頁面上，選取 [**代理程式作業 1**] 旁的加號。 搜尋**命令列**。

2. 提供工作的**顯示名稱**，並輸入下列腳本。 使用您的存放庫名稱和專案名稱來修改腳本。

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![輸入命令列工作的設定](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>新增複製檔案工作

1. **在 [工作**] 頁面上，選取 [**代理程式作業 1**] 旁的加號。 搜尋 [**複製**檔案]。 然後輸入下列設定。

   |參數|輸入|
   |-|-|
   |顯示名稱|將檔案複製到： $ （build. artifactstagingdirectory）|
   |來源資料夾|`$(system.defaultworkingdirectory)`| 
   |內容| `**\Deploy\**` |
   |目的檔案夾| `$(build.artifactstagingdirectory)`|

   ![輸入複製工作的設定](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>加入發行組建成品工作

1. **在 [工作**] 頁面上，選取 [**代理程式作業 1**] 旁的加號。 搜尋 [**發行組建**成品]，然後選取具有黑色箭號圖示的選項。 

2. 請勿變更任何預設設定。

### <a name="save-and-run"></a>儲存並執行

當您完成新增 npm、命令列、複製檔案和發行組建成品工作之後，請選取 [**儲存 & 佇列**]。 當系統提示您時，請輸入儲存批註，然後選取 [**儲存並執行**]。

## <a name="release-with-azure-pipelines"></a>發行 Azure Pipelines

開啟網頁瀏覽器，並流覽至您的 Azure 串流分析 Visual Studio Code 專案。

1. 在左側導覽功能表中的 [**管線**] 底下，選取 [**發行**]。 然後選取 [**新增管線**]。

2. 選取 [**從空白作業開始**]。

3. 在 [成品] 方塊中，選取 [ **+ 新增****構件**]。 在 [**來源**] 底下，選取您剛建立的組建管線，然後選取 [**新增**]。

   ![輸入組建管線成品](./media/setup-cicd-vs-code/build-artifact.png)

4. 變更**第1階段**的名稱，以將**作業部署到測試環境**。

5. 新增新的階段，並將其命名**為將作業部署到生產環境**。

### <a name="add-tasks"></a>新增工作

1. 從 [工作] 下拉式清單中，選取 [**將作業部署到測試環境**]。 

2. 選取 [ **+** **代理程式作業**] 旁的，然後搜尋 [ *Azure 資源群組部署*]。 輸入下列參數：

   |設定|值|
   |-|-|
   |顯示名稱| *部署 myASAJob*|
   |Azure 訂用帳戶| 選擇您的訂用帳戶。|
   |動作| *建立或更新資源群組*|
   |資源群組| 選擇將包含您串流分析作業的測試資源群組的名稱。|
   |位置|選擇測試資源群組的位置。|
   |範本位置| *連結的成品*|
   |[範本]| $ （ArtifactStagingDirectory） \drop\myASAJob.JobTemplate.json |
   |範本參數|（$ （Build. ArtifactStagingDirectory） \drop\myASAJob.JobTemplate.parameters.json|
   |覆寫範本參數|-Input_IoTHub1_iotHubNamespace $ （test_eventhubname）|
   |部署模式|累加|

3. 從 [工作] 下拉式清單中，選取 [**將作業部署到生產環境**]。

4. 選取 [ **+** **代理程式作業**] 旁的，然後搜尋 [ *Azure 資源群組部署*]。 輸入下列參數：

   |設定|值|
   |-|-|
   |顯示名稱| *部署 myASAJob*|
   |Azure 訂用帳戶| 選擇您的訂用帳戶。|
   |動作| *建立或更新資源群組*|
   |資源群組| 選擇將包含您串流分析作業的生產資源群組的名稱。|
   |位置|選擇您的生產資源群組的位置。|
   |範本位置| *連結的成品*|
   |[範本]| $ （ArtifactStagingDirectory） \drop\myASAJob.JobTemplate.json |
   |範本參數|（$ （Build. ArtifactStagingDirectory） \drop\myASAJob.JobTemplate.parameters.json|
   |覆寫範本參數|-Input_IoTHub1_iotHubNamespace $ （eventhubname）|
   |部署模式|累加|

### <a name="create-release"></a>建立發行

若要建立發行，請選取右上角的 [**建立發行**]。

![使用 Azure Pipelines 建立發行](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>其他資源

若要使用 Azure Data Lake Store Gen1 的受控識別作為輸出接收端，您必須在部署至 Azure 之前，使用 PowerShell 提供服務主體的存取權。 深入了解如何[使用 Resource Manager 範本部署具有受控識別的 ADLS Gen1](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)。


## <a name="next-steps"></a>後續步驟

* [快速入門：在 Visual Studio Code （預覽）中建立 Azure 串流分析雲端作業](quick-create-vs-code.md)
* [使用 Visual Studio Code （預覽）在本機測試串流分析查詢](visual-studio-code-local-run.md)
* [使用 Visual Studio Code 探索 Azure 串流分析（預覽）](visual-studio-code-explore-jobs.md)
