---
title: 使用 CI/CD 工具將 Azure 串流分析作業的組建、測試和部署自動化
description: 本文說明如何使用 Azure 串流分析 CI/CD 工具來自動建立、測試及部署 Azure 串流分析專案。
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: e772701396f172eaab906f99463bd9019728b531
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934021"
---
# <a name="automate-builds-tests-and-deployments-of-an-azure-stream-analytics-job-using-cicd-tools"></a>使用 CI/CD 工具將 Azure 串流分析作業的組建、測試和部署自動化

您可以使用 Azure 串流分析 CI/CD npm 套件，自動建立、測試及部署您的 Azure 串流分析 Visual Studio Code 或 Visual Studio 專案。 您可以使用開發工具來建立專案，也可以從現有的串流分析作業中匯出專案。 本文說明如何搭配任何 CI/CD 系統使用 npm 套件。 如需 Azure Pipelines 的部署，請參閱 [使用 Azure DevOps 來建立串流分析作業的 CI/CD 管線](set-up-cicd-pipeline.md)。

## <a name="installation"></a>安裝

您可以直接 [下載套件](https://www.npmjs.com/package/azure-streamanalytics-cicd) ，或使用命令 [全域](https://docs.npmjs.com/downloading-and-installing-packages-globally) 安裝 `npm install -g azure-streamanalytics-cicd` 。 建議您使用命令，此命令也可以在 **Azure Pipelines**中的組建管線 PowerShell 或 Azure CLI 腳本工作中使用。

## <a name="build-the-project"></a>建置專案

**>mslearn-streamanalytics-cicd** npm 套件提供的工具可產生串流分析[Visual Studio Code 專案](quick-create-vs-code.md)或[Visual Studio 專案](stream-analytics-quick-create-vs.md)的 Azure Resource Manager 範本。 您也可以在 Windows、macOS 和 Linux 上使用 npm 套件，而不需要安裝 Visual Studio Code 或 Visual Studio。

安裝套件之後，請使用下列命令來建立您的串流分析專案。

```powershell
azure-streamanalytics-cicd build -project <projectFullPath> [-outputPath <outputPath>]
```

*Build*命令會執行關鍵字語法檢查，並輸出 Azure Resource Manager 範本。

| 參數 | 描述 |
|---|---|
| `-project` | Visual Studio Code 專案的 **asaproj.js** 檔案的絕對路徑，或 Visual Studio 專案的 **[專案名稱]. >asaproj.json** 。 |
| `-outputPath` | Azure Resource Manager 範本的輸出檔案夾路徑。 如果未指定，則會將範本放在目前的目錄中。 |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/samplejob.asaproj"
```

--- 

當串流分析專案建立成功時，它會在輸出檔案夾下產生下列兩個檔案：

* Azure Resource Manager 範本檔案

   `[ProjectName].JobTemplate.json`

* Azure Resource Manager 參數檔

   `[ProjectName].JobTemplate.parameters.json`

parameters.json 檔案中的預設參數是來自 Visual Studio Code 或 Visual Studio 專案中的設定。 如果您想要部署到其他環境，請據以取代參數。

所有認證的預設值都是 **null**。 您必須在部署至 Azure 之前設定這些值。

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

若要使用 Azure Data Lake 存放區 Gen1 的受控識別作為輸出接收，您必須在部署至 Azure 之前，使用 PowerShell 提供服務主體的存取權。 深入了解如何[使用 Resource Manager 範本部署具有受控識別的 ADLS Gen1](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)。

## <a name="local-run"></a>本機執行

如果您的專案已指定本機輸入檔案，您可以使用命令在本機執行串流分析腳本 `localrun` 。

```powershell
azure-streamanalytics-cicd localrun -project <projectFullPath> [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| 參數 | 描述 |
|---|---|
| `-project` | Visual Studio Code 專案的 **asaproj.js** 檔案路徑，或 Visual Studio 專案的 **[專案名稱]. >asaproj.json** 。 |
| `-outputPath` | 輸出檔案夾的路徑。 如果未指定，輸出結果檔案將會放在目前的目錄中。 |
| `-customCodeZipFilePath` | C # 自訂程式碼的 zip 檔案路徑，例如 UDF 或還原序列化程式（如果使用的話）。 將 Dll 封裝成 zip 檔案，並指定此路徑。 |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```

---

> [!Note] 
> JavaScript UDF 只適用于 Windows。

## <a name="automated-test"></a>自動化測試

您可以使用 CI/CD npm 套件來設定及執行串流分析腳本的自動化測試。

### <a name="add-a-test-case"></a>新增測試案例

測試案例會在測試組態檔中說明。 若要開始使用，請使用 `addtestcase` 命令將測試案例範本加入測試組態檔中。 如果測試組態檔不存在，預設會建立一個。

```powershell
azure-streamanalytics-cicd addtestcase -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>]
```

| 參數 | 描述 |
|---|---|
| `-project` | Visual Studio Code 專案的 **asaproj.js** 檔案路徑，或 Visual Studio 專案的 **[專案名稱]. >asaproj.json** 。 |
| `-testConfigPath` | 測試組態檔案的路徑。 如果未指定，則會在 **\test** 的目前 **asaproj.js** 目錄下的檔案中搜尋檔案，預設檔案名為 **testConfig.js**。 如果不存在，則會建立新的檔案。 |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```
---

如果測試組態檔案是空的，則會將下列內容寫入檔案中。 否則，會將測試案例加入至 **testcases.txt**陣列。 必要的輸入設定會根據輸入設定檔（如果有的話）自動填入。 否則，會設定預設值。 執行測試之前，必須指定每個輸入的**FilePath**和預期的輸出。 您可以手動修改設定。

如果您想要讓測試驗證忽略特定的輸出，請將預期輸出的 **必要** 欄位設定為 **false**。

```json
{
  "Script": "",
  "TestCases": [
    {
      "Name": "Case 1",
      "Inputs": [
        {
          "InputAlias": [Input alias string],
          "Type": "Data Stream",
          "Format": "JSON",
          "FilePath": [Required],
          "ScriptType": "InputMock"
        }
      ],
      "ExpectedOutputs": [
        {
          "OutputAlias": [Output alias string],
          "FilePath": "Required",
          "Required": true
        }
      ]
    }
  ]
}
```

### <a name="run-a-unit-test"></a>執行單元測試

您可以使用下列命令來執行專案的多個測試案例。 輸出檔案夾中會產生測試結果的摘要。 所有通過的測試的程式碼為 **0** 結束;如果發生例外狀況，則為 **-1** ; **-2** 表示測試失敗。

```powershell
azure-streamanalytics-cicd test -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>] [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| 參數 | 描述 |
|---|---|
| `-project` | Visual Studio Code 專案的 **asaproj.js** 檔案路徑，或 Visual Studio 專案的 **[專案名稱]. >asaproj.json** 。 |
| `-testConfigPath` | 測試組態檔案的路徑。 如果未指定，則會在 **\test** 的目前 **asaproj.js** 目錄下的檔案中搜尋檔案，預設檔案名為 **testConfig.js**。
| `-outputPath` | 測試結果輸出檔案夾的路徑。 如果未指定，輸出結果檔案將會放在目前的目錄中。 |
| `-customCodeZipFilePath` | 自訂程式碼（例如 UDF 或還原序列化程式）的 zip 檔案路徑（如果使用的話）。 |

當所有測試完成時，輸出檔案夾中會產生 JSON 格式的測試結果摘要。 摘要檔案的名稱為 **testResultSummary.json**。

```json
{
  "Total": (integer) total_number_of_test_cases,
  "Passed": (integer) number_of_passed_test_cases,
  "Failed": (integer) number_of_failed_test_cases,
  "Script": (string) absolute_path_to_asaql_file,
  "Results": [ (array) detailed_results_of_test_cases
    {
      "Name": (string) name_of_test_case,
      "Status": (integer) 0(passed)_or_1(failed),
      "Time": (string) time_span_of_running_test_case,
      "OutputMatched": [ (array) records_of_actual_outputs_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputNotEqual": [ (array) records_of_actual_outputs_not_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputMissing": [ (array) records_of_actual_outputs_missing
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": ""
        }
      ],
      "OutputUnexpected": [ (array) records_of_actual_outputs_unexpected
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": "",
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputUnrequired": [ (array) records_of_actual_outputs_unrequired_to_be_checked
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ]
    }
  ],
  "Time": (string) time_span_of_running_all_test_cases,
}
```

## <a name="deploy-to-azure"></a>部署至 Azure

您可以使用從 Build 產生的 Azure Resource Manager 範本和參數檔案，將 [您的作業部署到 Azure](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md?tabs=azure-powershell#deploy-template)。

## <a name="next-steps"></a>後續步驟

* [Azure 串流分析的持續整合與持續部署](cicd-overview.md)
* [使用 Azure Pipelines 為串流分析作業設定 CI/CD 管線](set-up-cicd-pipeline.md)
