---
title: 使用 Azure 串流分析 CI/CD NuGet 套件
description: 本文說明如何使用 Azure 串流分析 CI/CD NuGet 套件來設定持續整合和部署程式。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/15/2019
ms.openlocfilehash: c05db2d9ba184da89665a236994c851355cc2644
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019849"
---
# <a name="use-the-azure-stream-analytics-cicd-nuget-package-for-integration-and-development"></a>使用 Azure 串流分析 CI/CD NuGet 套件進行整合和開發 
本文說明如何使用 Azure 串流分析 CI/CD NuGet 套件來設定持續整合和部署程式。

使用 2.3.0000.0 版或更新版本的[適用於 Visual Studio 的串流分析工具](./stream-analytics-quick-create-vs.md)來取得 MSBuild 支援。

Nuget 套件可供使用：[Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/)。 它提供 MSBuild、本機執行和部署工具，可支援 [串流分析 Visual Studio 專案](stream-analytics-vs-tools.md)的持續整合和部署程式。 
> [!NOTE]
> NuGet 套件僅能搭配 2.3.0000.0 或更新版本之適用於 Visual Studio 的串流分析工具使用。 如果您有使用舊版 Visual Studio 工具建立的專案，請直接使用 2.3.0000.0 或更新版本加以開啟並儲存。 接著啟用新功能。 

如需詳細資訊，請參閱[適用於 Visual Studio 的串流分析工具](./stream-analytics-quick-create-vs.md)。

## <a name="msbuild"></a>MSBuild
與標準 Visual Studio MSBuild 體驗類似，若要建置專案，您有兩個選項。 您可以使用滑鼠右鍵按一下專案，然後選擇 [建置]。 您也可以從命令列中使用 NuGet 套件中的 **MSBuild**。
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

串流分析 Visual Studio 專案建置成功時，會在 **bin/[Debug/Retail]/Deploy** 資料夾下方產生下列兩個 Azure Resource Manager 範本檔案： 

* Resource Manager 範本檔案

   `[ProjectName].JobTemplate.json`

* Resource Manager 參數檔案
   
   `[ProjectName].JobTemplate.parameters.json`

parameters.json 檔案中的預設參數來自 Visual Studio 專案中的設定。 如果您想要部署到其他環境，請據以取代參數。

> [!NOTE]
> 對於所有認證，預設值都會設為 null。 部署至雲端之前，「必須」設定這些值。

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
深入了解如何[使用 Resource Manager 範本檔案和 Azure PowerShell 部署](../azure-resource-manager/templates/deploy-powershell.md)。 深入了解如何[在 Resource Manager 範本中使用物件作為參數](/azure/architecture/building-blocks/extending-templates/objects-as-parameters)。

若要使用 Azure Data Lake Store Gen1 的受控識別作為輸出接收端，您必須在部署至 Azure 之前，使用 PowerShell 提供服務主體的存取權。 深入了解如何[使用 Resource Manager 範本部署具有受控識別的 ADLS Gen1](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)。


## <a name="command-line-tool"></a>命令列工具

### <a name="build-the-project"></a>建置專案
NuGet 套件有一個稱為 **SA.exe** 的命令列工具。 它支援在任意電腦上進行專案建置和本機測試，讓您可以用於持續整合和持續傳遞程序。 

部署檔案預設會放置在目前的目錄底下。 您可以使用下列 -OutputPath 參數指定輸出路徑：

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>在本機測試指令碼

如果您的專案已經在 Visual Studio 中指定本機輸入檔案，您可以使用 *localrun* 命令來執行自動化指令碼測試。 輸出結果會放在目前的目錄底下。
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>產生可搭配串流分析 PowerShell API 使用的作業定義檔案

*arm* 命令會採用透過組建產生的作業範本和作業範本參數檔案作為輸入。 然後，將它們結合到可搭配串流分析 PowerShell API 使用的作業定義 JSON 檔案中。

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
範例：
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>後續步驟

* [快速入門：在 Visual Studio 中建立 Azure 串流分析雲端作業](stream-analytics-quick-create-vs.md)
* [使用 Visual Studio 在本機測試串流分析查詢](stream-analytics-vs-tools-local-run.md)
* [使用 Visual Studio 探索 Azure 串流分析作業](stream-analytics-vs-tools.md)