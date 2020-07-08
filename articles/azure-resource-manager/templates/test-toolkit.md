---
title: ARM 範本測試控管組
description: 說明如何在您的範本上執行 ARM 範本測試控管組。 此工具組可讓您查看是否已實施建議的作法。
ms.topic: conceptual
ms.date: 06/19/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 7b88096dfdd1c7fb3e2671a369132e75a8885b8d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255736"
---
# <a name="use-arm-template-test-toolkit"></a>使用 ARM 範本測試控管組

[ARM 範本測試控管](https://aka.ms/arm-ttk)組會檢查您的範本是否使用建議的作法。 當您的範本不符合建議的做法時，它會傳回包含建議變更的警告清單。 藉由使用測試控管組，您可以瞭解如何避免範本開發中的常見問題。

測試控管組提供一[組預設的測試](test-cases.md)。 這些測試是建議事項，但不是需求。 您可以決定哪些測試與您的目標相關，並自訂要執行的測試。

本文說明如何執行測試控管組，以及如何加入或移除測試。 如需預設測試的說明，請參閱[工具組測試案例](test-cases.md)。

此工具組是一組 PowerShell 腳本，可以從 PowerShell 或 CLI 中的命令執行。

## <a name="download-test-toolkit"></a>下載測試控管組

若要使用測試控管組，您可以分叉和複製包含腳本的存放庫，或[下載最新的 .zip](https://aka.ms/arm-ttk-latest)[檔案](https://aka.ms/arm-ttk)。

根據執行腳本之電腦的執行原則而定，您可能會收到有關從網際網路執行腳本的錯誤。 您必須變更[執行原則](/powershell/module/microsoft.powershell.core/about/about_execution_policies)或[解除封鎖腳本](/powershell/module/microsoft.powershell.utility/unblock-file)檔案。

## <a name="run-on-powershell"></a>在 PowerShell 上執行

在執行測試之前，請先匯入模組。

```powershell
Import-Module .\arm-ttk.psd1 # from the same directory as .\arm-ttk.psd1
```

若要在**PowerShell**中執行測試，請使用下列命令：

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

## <a name="run-on-linux"></a>在 Linux 上執行

執行測試之前，請先安裝[PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux)。

若要在 Bash 中的**Linux**上執行測試，請使用下列命令：

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

您也可以在 pwsh.exe 上執行測試。

## <a name="run-on-macos"></a>在 macOS 上執行

執行測試之前，請先安裝[PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-macos)。 

安裝 `coreutils`：

```bash
brew install coreutils
```

若要在**macOS**上執行測試，請使用下列命令：

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

## <a name="result-format"></a>結果格式

通過的測試會以**綠色**顯示，並在前面加上 **[+]**。

失敗的測試會以**紅色**顯示，並在前面加上 **[-]**。

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="查看測試結果":::

文字結果如下：

```powershell
[+] adminUsername Should Not Be A Literal (24 ms)
[+] apiVersions Should Be Recent (18 ms)
[+] artifacts parameter (16 ms)
[+] DeploymentTemplate Schema Is Correct (17 ms)
[+] IDs Should Be Derived From ResourceIDs (15 ms)
[-] Location Should Not Be Hardcoded (41 ms)
     azuredeploy.json must use the location parameter, not resourceGroup().location (except when used as a default value in the main template)
```

## <a name="test-parameters"></a>測試參數

當您提供 **-TemplatePath**參數時，工具組會在該資料夾中尋找名為 azuredeploy.js或 maintemplate.js上的範本。 它會先測試此範本，然後再測試檔案夾及其子資料夾中的所有其他範本。 其他範本會測試為連結的範本。 如果您的路徑包含名為[CreateUiDefinition.js](../managed-applications/create-uidefinition-overview.md)的檔案，則會執行與 UI 定義相關的測試。

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

若要在該資料夾中測試一個檔案，請新增 **-file**參數。 不過，資料夾仍然必須有一個名為 azuredeploy.js或 maintemplate.js上的主要範本。

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

預設會執行所有測試。 若要指定要執行的個別測試，請使用 **-Test**參數。 提供測試的名稱。 如需相關名稱，請參閱工具組的[測試案例](test-cases.md)。

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>自訂測試

針對 ARM 範本，此工具組會執行資料夾**\arm-ttk\testcases\deploymentTemplate**中的所有測試。 如果您想要永久移除測試，請從資料夾中刪除該檔案。

針對[CreateUiDefinition](../managed-applications/create-uidefinition-overview.md)檔案，它會執行資料夾**\arm-ttk\testcases\CreateUiDefinition**中的所有測試。

若要新增您自己的測試，請使用命名慣例建立檔案： **Your-Custom-Test-Name.test.ps1**。

測試可以取得範本作為物件參數或字串參數。 一般來說，您可以使用其中一種，但同時使用兩者。

當您需要取得範本的區段並逐一查看其屬性時，請使用物件參數。 使用物件參數的測試具有下列格式：

```powershell
param(
    [Parameter(Mandatory=$true,Position=0)]
    [PSObject]
    $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

範本物件具有下列屬性：

* $schema
* contentVersion
* 參數
* variables
* resources
* 輸出

例如，您可以使用來取得參數的集合 `$TemplateObject.parameters` 。

當您需要對整個範本執行字串作業時，請使用 string 參數。 使用字串參數的測試具有下列格式：

```powershell
param(
    [Parameter(Mandatory)]
    [string]
    $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

例如，您可以執行字串參數的正則運算式，以查看是否使用了特定的語法。

若要深入瞭解如何執行測試，請查看該資料夾中的其他測試。

## <a name="integrate-with-azure-pipelines"></a>與 Azure Pipelines 整合

您可以將測試控管組新增至您的 Azure 管線。 使用管線時，您可以在每次更新範本時執行測試，或在部署程式中加以執行。

將測試控管組新增至管線的最簡單方式是使用協力廠商延伸模組。 提供下列兩種擴充功能：

* [執行 ARM TTK 測試](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
* [ARM 範本測試器](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

或者，您也可以執行自己的工作。 下列範例顯示如何下載測試控管組。

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": false,
    "alwaysRun": false,
    "displayName": "Download TTK",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
        "errorActionPreference": "stop",
        "failOnStderr": "false",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

下一個範例顯示如何執行測試。

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": true,
    "alwaysRun": false,
    "displayName": "Run Best Practices Tests",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
        "errorActionPreference": "continue",
        "failOnStderr": "true",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

## <a name="next-steps"></a>後續步驟

若要瞭解預設測試，請參閱[工具組的測試案例](test-cases.md)。
