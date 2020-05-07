---
title: 針對 Azure 自動化中的共用資源進行疑難排解
description: 瞭解如何疑難排解和解決 Azure 自動化共用資源的問題。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c59e8ec67777a9cfebc12508b197e1237a61df4a
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864193"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>針對 Azure 自動化中的共用資源進行疑難排解

本文將討論當您在 Azure 自動化中使用[共用資源](../automation-intro.md#shared-resources)時可能發生的問題解決方案。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 您仍然可以在目前的時間使用 AzureRM 模組。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="modules"></a>單元

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>案例：匯入期間模組停滯

#### <a name="issue"></a>問題

當您匯入或更新 Azure 自動化模組時，模組會卡在匯*入*狀態。

#### <a name="cause"></a>原因

因為匯入 PowerShell 模組是一個複雜的多步驟程式，所以模組可能無法正確匯入，而且可能會卡在暫時性的狀態。 若要深入瞭解匯入程式，請參閱匯[入 PowerShell 模組](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)。

#### <a name="resolution"></a>解決方案

若要解決此問題，您必須使用[AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) Cmdlet 來移除停滯的模組。 您可以稍後再重試匯入模組。

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>案例：在嘗試更新之後，AzureRM 模組會停滯在匯入期間

#### <a name="issue"></a>問題

嘗試更新您的 AzureRM 模組之後，您的帳戶中會保留下列訊息的橫幅：

```error
Azure modules are being updated
```

#### <a name="cause"></a>原因

在自動化帳戶中更新 AzureRM 模組有已知的問題。 具體而言，如果模組位於以0開頭之數值名稱的資源群組中，就會發生此問題。

#### <a name="resolution"></a>解決方案

若要在您的自動化帳戶中更新 AzureRM 模組，帳戶必須位於具有英數位元名稱的資源群組中。 數位名稱開頭為0的資源群組目前無法更新 AzureRM 模組。

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>案例：無法匯入模組，或無法在匯入之後執行 Cmdlet

#### <a name="issue"></a>問題

模組無法匯入，或匯入成功，但未解壓縮任何 Cmdlet。

#### <a name="cause"></a>原因

模組可能無法成功匯入 Azure 自動化的部分常見原因如下：

* 結構不符合自動化所需的結構。
* 模組相依於另一個尚未部署到您自動化帳戶的模組。
* 模組在資料夾中遺失其相依性。
* [AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0)指令程式是用來上傳模組，但您尚未提供完整的儲存路徑，或是尚未使用可公開存取的 URL 來載入模組。

#### <a name="resolution"></a>解決方案

使用上述任何解決方案來修正問題：

* 請確定模組會遵循下列格式： ModuleName. zip-> ModuleName 或版本號碼-> （ModuleName. .psm1，ModuleName. .psd1）。
* 開啟 **.psd1**檔案，並查看模組是否有任何相依性。 如果有，請將這些模組上傳至自動化帳戶。
* 請確定所有參考的 **.dll**檔案都存在於模組資料夾中。

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>案例： Update-azuremodule.ps1 在更新模組時暫停

#### <a name="issue"></a>問題

當您使用[update-azuremodule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook 來更新 Azure 模組時，模組更新程式會暫停。

#### <a name="cause"></a>原因

針對此 runbook，用來判斷會同時更新多少模組的預設值是10。 同時更新太多模組時，更新程序很容易發生錯誤。

#### <a name="resolution"></a>解決方案

相同的自動化帳戶中不需要所有的 AzureRM 或 Az 模組。 您應該只匯入所需的特定模組。

> [!NOTE]
> 請避免匯入`Az.Automation`整個`AzureRM.Automation`或模組，這會匯入所有包含的模組。

如果更新程式暫停，請將`SimultaneousModuleImportJobCount`參數新增至**Update-AzureModules**腳本，並提供比預設值10還低的值。 如果您執行此邏輯，請嘗試以3或5的值開始。 `SimultaneousModuleImportJobCount`是**更新 update-automationazuremodulesforaccount**系統 runbook 的參數，用來更新 Azure 模組。 如果您進行這項調整，更新程式的執行時間會較長，但有更好的完成機會。 下列範例顯示參數以及要在 Runbook 中放置它的位置：

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

當您嘗試建立或更新執行身分帳戶時，您會收到類似下列的錯誤：

```error
You do not have permissions to create…
```

#### <a name="cause"></a>原因

您沒有建立或更新執行身分帳戶所需的許可權，或資源已在資源群組層級鎖定。

#### <a name="resolution"></a>解決方案

若要建立或更新執行身分帳戶，您必須具有執行身分帳戶所使用之各種資源的適當[許可權](../manage-runas-account.md#permissions)。 

如果問題是因為鎖定所致，請確認可以移除鎖定。 然後移至 Azure 入口網站中鎖定的資源，以滑鼠右鍵按一下鎖定，然後選取 [**刪除**]。

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>案例：當執行 runbook 時，您會收到「找不到 DLL ' iplpapi ' 中名為 ' GetPerAdapterInfo ' 的進入點」錯誤

#### <a name="issue"></a>問題

當您執行 runbook 時，您會收到下列例外狀況：

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>原因

此錯誤很可能是因為[執行身分帳戶](../manage-runas-account.md)設定不正確所造成。

#### <a name="resolution"></a>解決方案

請確定已正確設定您的執行身分帳戶。 然後確認您的 runbook 中有適當的程式碼，可向 Azure 進行驗證。 下列範例顯示使用執行身分帳戶在 runbook 中向 Azure 進行驗證的程式碼片段。

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>後續步驟

如果本文無法解決您的問題，請嘗試下列其中一個通道以取得其他支援：

* 透過[Azure 論壇](https://azure.microsoft.com/support/forums/)取得 azure 專家的解答。
* 連接[@AzureSupport](https://twitter.com/azuresupport)。 這是將 Azure 社區連接到正確資源的官方 Microsoft Azure 帳戶：解答、支援和專家。
* 提出 Azure 支援事件。 移至 [ [Azure 支援] 網站](https://azure.microsoft.com/support/options/)，然後選取 [**取得支援**]。

