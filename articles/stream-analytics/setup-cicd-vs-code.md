---
title: 使用 CI/CD npm 包部署 Azure 流分析作業
description: 本文介紹如何使用 Azure 流分析 CI/CD npm 包來設置持續的集成和部署過程。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: deb6c2439cc84f196b7f42fd9f49d3ebfd057cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76962146"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>使用 CI/CD npm 包部署 Azure 流分析作業 

您可以使用 Azure 流分析 CI/CD npm 包為流分析作業設置連續集成和部署過程。 本文介紹如何使用 npm 包與任何 CI/CD 系統，以及使用 Azure 管道進行部署的特定說明。

有關使用 Powershell 進行部署的詳細資訊，請參閱[使用資源管理器範本檔和 Azure PowerShell 進行部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)。 您還可以瞭解有關如何在[資源管理器範本中使用物件作為參數](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)的更多資訊。

## <a name="build-the-vs-code-project"></a>構建 VS 代碼專案

您可以使用**asa 流分析 cicd** npm 包為 Azure 流分析作業啟用持續集成和部署。 npm 包提供了生成[流分析視覺化工作室代碼專案的](quick-create-vs-code.md)Azure 資源管理器範本的工具。 它可以在 Windows、macOS 和 Linux 上使用，而無需安裝視覺化工作室代碼。

您可以直接[下載包](https://www.npmjs.com/package/azure-streamanalytics-cicd)，或通過 命令`npm install -g azure-streamanalytics-cicd`[全域](https://docs.npmjs.com/downloading-and-installing-packages-globally)安裝它。 這是推薦的方法，也可以在**Azure 管道**中生成管道的 PowerShell 或 Azure CLI 腳本任務中使用。

安裝包後，請使用以下命令輸出 Azure 資源管理器範本。 **腳本路徑**參數是專案中**asaql**檔的絕對路徑。 確保 asaproj.json 和 JobConfig.json 檔與指令檔位於同一資料夾中。 如果未指定**輸出路徑**，範本將放置在專案的**bin**資料夾下的 **"部署"** 資料夾中。

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
示例（macOS 上）
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

當流分析視覺化工作室代碼專案成功生成時，它將在**bin/[調試/零售]/部署**資料夾下生成以下兩個 Azure 資源管理器範本檔： 

*  Resource Manager 範本檔案

       [ProjectName].JobTemplate.json 

*  Resource Manager 參數檔案

       [ProjectName].JobTemplate.parameters.json   

參數.json 檔中的預設參數來自視覺化工作室代碼專案中的設置。 如果您想要部署到其他環境，請據以取代參數。

> [!NOTE]
> 對於所有認證，預設值都會設為 null。 部署至雲端之前，「必須」**** 設定這些值。

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>使用 Azure Pipelines 部署

本節詳細介紹了如何使用 npm 創建 Azure 管道[生成](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav)和[發佈](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)管道。

打開 Web 瀏覽器並導航到 Azure 流分析視覺化工作室代碼專案。

1. 在左側導航功能表中的**管道**下，選擇 **"生成**"。 然後選擇 **"新建管道"**

   ![創建新的 Azure 管道](./media/setup-cicd-vs-code/new-pipeline.png)

2. 選擇"**使用經典編輯器**創建沒有 YAML 的管道。

3. 選擇源類型、團隊專案和存儲庫。 然後選擇 **"繼續**"。

   ![選擇 Azure 流分析專案](./media/setup-cicd-vs-code/select-repo.png)

4. 在 **"選擇範本**"頁上，選擇 **"空作業**"。

### <a name="add-npm-task"></a>添加 npm 任務

1. 在 **"任務"** 頁上，選擇**代理作業 1**旁邊的加號。 在任務搜索中輸入"npm"，然後選擇**npm**。

   ![選擇 npm 任務](./media/setup-cicd-vs-code/search-npm.png)

2. 為任務指定**顯示名稱**。 將**命令**選項更改為*自訂*，並在**命令和參數**中輸入以下命令。 保留剩餘的預設選項。

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![輸入 npm 任務的配置](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>添加命令列任務

1. 在 **"任務"** 頁上，選擇**代理作業 1**旁邊的加號。 搜索**命令列**。

2. 為任務指定**顯示名稱**並輸入以下腳本。 使用存儲庫名稱和專案名稱修改腳本。

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![輸入命令列任務的配置](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>添加複製檔任務

1. 在 **"任務"** 頁上，選擇**代理作業 1**旁邊的加號。 搜索**複製檔**。 然後輸入以下配置。

   |參數|輸入|
   |-|-|
   |顯示名稱|將檔案複製到：$（生成.工件目錄）|
   |來源資料夾|`$(system.defaultworkingdirectory)`| 
   |內容| `**\Deploy\**` |
   |目的檔案夾| `$(build.artifactstagingdirectory)`|

   ![輸入複製任務的配置](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>添加發佈生成專案工作

1. 在 **"任務"** 頁上，選擇**代理作業 1**旁邊的加號。 搜索 **"發佈生成專案"，** 然後選擇帶有黑色箭頭圖示的選項。 

2. 不要更改任何預設配置。

### <a name="save-and-run"></a>儲存並執行

完成 npm、命令列、複製檔和發佈生成專案工作後，選擇 **"保存&佇列**"。 系統提示您時，輸入"保存注釋"並選擇 **"保存"並運行**。

## <a name="release-with-azure-pipelines"></a>使用 Azure 管道發佈

打開 Web 瀏覽器並導航到 Azure 流分析視覺化工作室代碼專案。

1. 在左側導航功能表中的**管道**下，選擇 **"釋放**"。 然後選擇 **"新建管道**"。

2. 選擇**以空作業開頭**。

3. 在 **"工件"** 框中，選擇 **"添加專案**"。 在 **"源"** 下，選擇剛剛創建的生成管道，然後選擇 **"添加**"。

   ![輸入生成管道專案](./media/setup-cicd-vs-code/build-artifact.png)

4. 將**階段 1**的名稱更改為 **"將作業部署到測試環境**"。

5. 添加新階段並將其命名為 **"將作業部署到生產環境**"。

### <a name="add-tasks"></a>新增工作

1. 從任務下拉清單中，選擇 **"將作業部署到測試環境**"。 

2. 選擇**+****"代理"作業**的下一個，然後搜索*Azure 資源組部署*。 輸入下列參數：

   |設定|值|
   |-|-|
   |顯示名稱| *部署 myASA作業*|
   |Azure 訂用帳戶| 選擇您的訂用帳戶。|
   |動作| *建立或更新資源群組*|
   |資源群組| 為包含流分析作業的測試資源組選擇名稱。|
   |Location|選擇測試資源組的位置。|
   |範本位置| *連結專案*|
   |[範本]| $（生成.artifactstagingDirectory）\刪除\myASAJob.JobTemplate.json |
   |範本參數|（$（生成.工件臨時目錄）\刪除\myASAJob.JobTemplate.參數.json|
   |覆寫範本參數|-Input_IoTHub1_iotHubNamespace $（test_eventhubname）|
   |部署模式|累加|

3. 從任務下拉清單中，選擇 **"將作業部署到生產環境**"。

4. 選擇**+****"代理"作業**的下一個，然後搜索*Azure 資源組部署*。 輸入下列參數：

   |設定|值|
   |-|-|
   |顯示名稱| *部署 myASA作業*|
   |Azure 訂用帳戶| 選擇您的訂用帳戶。|
   |動作| *建立或更新資源群組*|
   |資源群組| 為包含流分析作業的生產資源組選擇名稱。|
   |Location|選擇生產資源組的位置。|
   |範本位置| *連結專案*|
   |[範本]| $（生成.artifactstagingDirectory）\刪除\myASAJob.JobTemplate.json |
   |範本參數|（$（生成.工件臨時目錄）\刪除\myASAJob.JobTemplate.參數.json|
   |覆寫範本參數|-Input_IoTHub1_iotHubNamespace $（事件名）|
   |部署模式|累加|

### <a name="create-release"></a>創建發佈

要創建版本，請選擇右上角的 **"創建版本**"。

![使用 Azure 管道創建發佈](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>其他資源

若要使用 Azure Data Lake Store Gen1 的受控識別作為輸出接收端，您必須在部署至 Azure 之前，使用 PowerShell 提供服務主體的存取權。 深入了解如何[使用 Resource Manager 範本部署具有受控識別的 ADLS Gen1](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)。


## <a name="next-steps"></a>後續步驟

* [快速入門：在視覺化工作室代碼（預覽）中創建 Azure 流分析雲作業](quick-create-vs-code.md)
* [使用視覺化工作室代碼（預覽）在本地測試流分析查詢](visual-studio-code-local-run.md)
* [使用視覺化工作室代碼流覽 Azure 流分析（預覽版）](visual-studio-code-explore-jobs.md)
