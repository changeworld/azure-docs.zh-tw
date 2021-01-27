---
title: 針對 Azure 自動化共用資源的問題進行疑難排解
description: 本文說明如何診斷並解決 Azure 自動化共用資源的問題。
services: automation
ms.subservice: ''
ms.date: 03/12/2019
ms.topic: troubleshooting
ms.openlocfilehash: c4ede0bffedc256f4af621d4945ebbbea0f8a4b6
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896286"
---
# <a name="troubleshoot-shared-resource-issues"></a>針對共用資源問題進行疑難排解

本文討論您在 Azure 自動化中使用[共用資源](../automation-intro.md#shared-resources)時可能發生的問題。

## <a name="modules"></a>模組

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>案例：模組在匯入期間停滯

#### <a name="issue"></a>問題

當您匯入或更新 Azure 自動化模組時，模組停滯於 [匯入中] 狀態。

#### <a name="cause"></a>原因

因為匯入 PowerShell 模組是很複雜、多步驟的過程，模組可能無法正確匯入，而停滯於暫時性狀態。 若要深入了解匯入流程，請參閱[匯入 PowerShell 模組](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)。

#### <a name="resolution"></a>解決方案

若要解決此問題，您必須使用 [Remove-AzAutomationModule](/powershell/module/Az.Automation/Remove-AzAutomationModule) Cmdlet 移除停滯的模組。 您可以稍後再重試匯入模組。

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>案例：AzureRM 模組在匯入期間嘗試更新之後停滯

#### <a name="issue"></a>問題

嘗試更新 AzureRM 模組之後，下列訊息的橫幅停留在您的帳戶中：

```error
Azure modules are being updated
```

#### <a name="cause"></a>原因

在自動化帳戶中更新 AzureRM 模組已知有問題。 具體而言，此問題起因於模組所在的資源群組是開頭為 0 的數值名稱。

#### <a name="resolution"></a>解決方案

若要在自動化帳戶中更新 AzureRM 模組，帳戶所在的資源群組必須是英數字元名稱。 目前，如果資源群組是開頭為 0 數值名稱，則無法更新 AzureRM 模組。

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>案例：無法匯入模組，或無法在匯入之後執行 Cmdlet

#### <a name="issue"></a>問題

無法匯入模組，或雖然成功匯入，但未擷取到任何 Cmdlet。

#### <a name="cause"></a>原因

模組可能無法成功匯入 Azure 自動化的部分常見原因如下：

* 結構不符合自動化所需的結構。
* 模組相依於另一個尚未部署到您自動化帳戶的模組。
* 模組在資料夾中遺失其相依性。
* 您使用 [New-AzAutomationModule](/powershell/module/Az.Automation/New-AzAutomationModule) Cmdlet 來上傳模組，但未提供完整的儲存體路徑，或未使用可公開存取的 URL 來載入模組。

#### <a name="resolution"></a>解決方案

使用下列任何解決方案來修正問題：

* 請確定模組遵循下列格式：ModuleName.zip -> ModuleName 或 Version Number -> (ModuleName.psm1, ModuleName.psd1)。
* 開啟 **.psd1** 檔案，查看模組是否有任何相依性。 如果有，請將這些模組上傳至自動化帳戶。
* 請確定任何參考的 **.dll** 檔案存在於模組資料夾中。

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>案例：Update-AzureModule.ps1 會在更新模組時暫止

#### <a name="issue"></a>問題

使用 [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook 來更新 Azure 模組時，模組更新流程暫止。

#### <a name="cause"></a>原因

在此 Runbook 中，用以決定同時更新多少個模組的預設設定為 10。 同時更新太多模組時，更新程序很容易發生錯誤。

#### <a name="resolution"></a>解決方案

同一個自動化帳戶中需要所有 AzureRM 或 Az 模組的情況並不常見。 您應該只匯入需要的特定模組。

> [!NOTE]
> 避免匯入整個 `Az.Automation` 或 `AzureRM.Automation` 模組，這樣匯入所有包含的模組。

如果更新流程暫止，請將 `SimultaneousModuleImportJobCount` 參數新增至 **Update-AzureModules.ps1** 指令碼，並提供低於預設值 10 的值。 如果您實作此邏輯，請嘗試從 3 或 5 的值開始。 `SimultaneousModuleImportJobCount` 是 **Update-AutomationAzureModulesForAccount** 系統 Runbook (用於更新 Azure 模組) 的參數。 如果您進行這項調整，則更新流程會執行較久，但較有機會完成。 下列範例顯示參數以及要在 Runbook 中放置它的位置：

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>執行身分帳戶

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>案例：您無法建立或更新執行身分帳戶

#### <a name="issue"></a>問題

當您嘗試建立或更新「執行身分帳戶」時，您收到類似下列的錯誤：

```error
You do not have permissions to create…
```

#### <a name="cause"></a>原因

您不具有建立或更新「執行身分帳戶」所需的權限，或資源已鎖定在資源群組層級。

#### <a name="resolution"></a>解決方案

若要建立或更新「執行身分帳戶」，對於「執行身分帳戶」使用的各種資源，您必須具備適當的[權限](../manage-runas-account.md#permissions)。 

如果問題起因於鎖定，請確認可以移除鎖定。 然後，在 Azure 入口網站中移至鎖定的資源，以滑鼠右鍵按一下鎖定，並選取 [刪除]。

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>案例：執行 Runbook 時，您收到「在 DLL 'iplpapi.dll' 中找不到名為 ' GetPerAdapterInfo ' 的進入點」錯誤

#### <a name="issue"></a>問題

當您執行 Runbook 時，您收到下列例外狀況：

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>原因

此錯誤很可能起因於[執行身分帳戶](../manage-runas-account.md)設定不正確。

#### <a name="resolution"></a>解決方案

請確定已正確設定「執行身分帳戶」。 然後，確認 Runbook 中有適當的程式碼可向 Azure 進行驗證。 下列範例顯示 Runbook 中使用「執行身分帳戶」向 Azure 進行驗證的程式碼片段。

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>後續步驟

如果本文無法解決您的問題，請嘗試下列其中一個管道以取得其他支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答。
* 聯繫 [@AzureSupport](https://twitter.com/azuresupport)。 這是官方 Microsoft Azure 帳戶，可將 Azure 社群連結到正確的資源：解答、支援和專家。
* 提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。

