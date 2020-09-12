---
title: ARM 範本測試控管組
description: 說明如何在您的範本上執行 ARM 範本測試控管組。 此工具組可讓您查看是否已實行建議的作法。
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 73f6db8cbd5e4d7a0670c394f6af338aae8e9e79
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89439555"
---
# <a name="use-arm-template-test-toolkit"></a>使用 ARM 範本測試控管組

[Azure Resource Manager (ARM) 範本測試控管](https://aka.ms/arm-ttk)組會檢查您的範本是否使用建議的做法。 當您的範本不符合建議的做法時，會傳回一份警告清單以及建議的變更。 藉由使用測試控管組，您可以瞭解如何避免在範本開發中遇到常見問題。

測試控管組提供一 [組預設測試](test-cases.md)。 這些測試是建議，但不是需求。 您可以決定哪些測試與您的目標相關，並自訂要執行的測試。

本文描述如何執行測試控管組，以及如何新增或移除測試。 如需預設測試的說明，請參閱 [工具組測試案例](test-cases.md)。

此工具組是一組 PowerShell 腳本，可從 PowerShell 或 CLI 中的命令執行。

## <a name="install-on-windows"></a>在 Windows 上安裝

1. 如果您還沒有 PowerShell，請 [在 Windows 上安裝 powershell](/powershell/scripting/install/installing-powershell-core-on-windows)。

1. 下載測試控管組的[最新 .zip](https://aka.ms/arm-ttk-latest)檔案，並將其解壓縮。

1. 啟動 PowerShell。

1. 流覽至您解壓縮測試控管組的資料夾。 在該資料夾中，流覽至 **arm-ttk** 資料夾。

1. 如果您的 [執行原則](/powershell/module/microsoft.powershell.core/about/about_execution_policies) 會封鎖來自網際網路的腳本，您就必須解除封鎖腳本檔案。 請確定您位於 **arm-ttk** 資料夾中。

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. 匯入模組。

   ```powershell
   Import-Module .\arm-ttk.psd1
   ```

1. 若要執行測試，請使用下列命令：

   ```powershell
   Test-AzTemplate -TemplatePath \path\to\template
   ```

## <a name="install-on-linux"></a>在 Linux 上安裝

1. 如果您還沒有 PowerShell，請 [在 Linux 上安裝 powershell](/powershell/scripting/install/installing-powershell-core-on-linux)。

1. 下載測試控管組的[最新 .zip](https://aka.ms/arm-ttk-latest)檔案，並將其解壓縮。

1. 啟動 PowerShell。

   ```bash
   pwsh
   ```

1. 流覽至您解壓縮測試控管組的資料夾。 在該資料夾中，流覽至 **arm-ttk** 資料夾。

1. 如果您的 [執行原則](/powershell/module/microsoft.powershell.core/about/about_execution_policies) 會封鎖來自網際網路的腳本，您就必須解除封鎖腳本檔案。 請確定您位於 **arm-ttk** 資料夾中。

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. 匯入模組。

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. 若要執行測試，請使用下列命令：

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="install-on-macos"></a>在 macOS 上安裝

1. 如果您還沒有 PowerShell，請 [在 macOS 上安裝 powershell](/powershell/scripting/install/installing-powershell-core-on-macos)。

1. 安裝 `coreutils`：

   ```bash
   brew install coreutils
   ```

1. 下載測試控管組的[最新 .zip](https://aka.ms/arm-ttk-latest)檔案，並將其解壓縮。

1. 啟動 PowerShell。

   ```bash
   pwsh
   ```

1. 流覽至您解壓縮測試控管組的資料夾。 在該資料夾中，流覽至 **arm-ttk** 資料夾。

1. 如果您的 [執行原則](/powershell/module/microsoft.powershell.core/about/about_execution_policies) 會封鎖來自網際網路的腳本，您就必須解除封鎖腳本檔案。 請確定您位於 **arm-ttk** 資料夾中。

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. 匯入模組。

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. 若要執行測試，請使用下列命令：

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="result-format"></a>結果格式

通過的測試會以 **綠色** 顯示，並在前面加上 **[+]**。

失敗的測試會以 **紅色** 顯示，並以 **[-]** 開頭。

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

當您提供 **-TemplatePath** 參數時，工具組會在該資料夾中尋找名為 azuredeploy.js的範本，或 maintemplate.js。 它會先測試這個範本，然後測試檔案夾及其子資料夾中的所有其他範本。 其他範本會測試為連結的範本。 如果您的路徑包含名為 [CreateUiDefinition.js](../managed-applications/create-uidefinition-overview.md)的檔案，則會執行與 UI 定義相關的測試。

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

若要測試該資料夾中的一個檔案，請新增 **-file** 參數。 不過，資料夾仍必須有一個名為 azuredeploy.js的主要範本，或 maintemplate.js。

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

依預設，會執行所有測試。 若要指定要執行的個別測試，請使用 **-Test** 參數。 提供測試的名稱。 如需名稱，請參閱工具組的 [測試案例](test-cases.md)。

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>自訂測試

針對 ARM 範本，此工具組會執行資料夾 **\arm-ttk\testcases\deploymentTemplate**中的所有測試。 如果您想要永久移除測試，請從資料夾中刪除該檔案。

針對 [CreateUiDefinition](../managed-applications/create-uidefinition-overview.md) 檔案，它會執行 **\arm-ttk\testcases\CreateUiDefinition**資料夾中的所有測試。

若要加入您自己的測試，請建立具有命名慣例： **Your-Custom-Test-Name.test.ps1**的檔案。

測試可以取得範本作為物件參數或字串參數。 一般而言，您會使用其中一個，但您可以同時使用兩者。

當您需要取得範本的區段，並逐一查看其屬性時，請使用物件參數。 使用物件參數的測試格式如下：

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

例如，您可以使用取得參數的集合 `$TemplateObject.parameters` 。

當您需要對整個範本執行字串操作時，請使用 string 參數。 使用字串參數的測試格式如下：

```powershell
param(
    [Parameter(Mandatory)]
    [string]
    $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

例如，您可以執行字串參數的正則運算式，以查看是否使用特定的語法。

若要深入瞭解如何執行測試，請查看該資料夾中的其他測試。

## <a name="integrate-with-azure-pipelines"></a>與 Azure Pipelines 整合

您可以將測試控管組新增至 Azure 管線。 透過管線，您可以在每次更新範本時執行測試，或在部署程式中執行測試。

將測試控管組新增至管線的最簡單方式是使用協力廠商延伸模組。 有下列兩個延伸模組可供使用：

* [執行 ARM TTK 測試](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
* [ARM 範本測試器](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

或者，您可以執行自己的工作。 下列範例顯示如何下載測試控管組。

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

## <a name="next-steps"></a>接下來的步驟

若要瞭解預設測試，請參閱 [工具組的測試案例](test-cases.md)。
