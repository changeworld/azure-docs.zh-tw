---
title: 在 Azure 自動化中排除分享資源的故障
description: 瞭解如何使用 Azure 自動化共享資源解決問題。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733582"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>在 Azure 自動化中排除分享資源的故障

本文討論在 Azure 自動化中使用[共用資源](../automation-intro.md#shared-resources)時可能會遇到的問題的解決方案。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](../automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="modules"></a>模組

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>方案:模組在匯入過程中卡住

#### <a name="issue"></a>問題

導入或更新 Azure 自動化模組時,模組將卡在「導入」狀態。

#### <a name="cause"></a>原因

由於導入 PowerShell 模組是一個複雜的多步驟過程,因此模組可能無法正確導入,並且可能卡處於瞬態狀態。 要瞭解有關匯入過程的更多資訊,請參閱導入[PowerShell 模組](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)。

#### <a name="resolution"></a>解決方案

要解決此問題,必須使用[「刪除-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) cmdlet」刪除卡在「導入」狀態中的模組。 您可以稍後再重試匯入模組。

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>方案:AzureRM 模組在導入期間在更新嘗試後卡住

#### <a name="issue"></a>問題

嘗試更新 AzureRM 模組後,帳戶中將保留帶有以下消息的橫幅:

```error
Azure modules are being updated
```

#### <a name="cause"></a>原因

更新自動化帳戶中的 AzureRM 模組存在已知問題,該帳戶位於以 0 開頭的具有數位名稱的資源組中。

#### <a name="resolution"></a>解決方案

要更新自動化帳戶中的 AzureRM 模組,該帳戶必須位於具有字母數位名稱的資源組中。 以 0 開頭的具有數位名稱的資源組此時無法更新 AzureRM 模組。

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>案例：無法匯入模組，或無法在匯入之後執行 Cmdlet

#### <a name="issue"></a>問題

模組無法導入或成功導入,但不會提取任何釐米。

#### <a name="cause"></a>原因

模組可能無法成功匯入 Azure 自動化的部分常見原因如下：

* 結構與自動化所需的結構不匹配。
* 模組相依於另一個尚未部署到您自動化帳戶的模組。
* 模組在資料夾中遺失其相依性。
* [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) cmdlet 用於上傳模組,您尚未提供完整的儲存路徑,或者未使用可公開存取的 URL 載入模組。

#### <a name="resolution"></a>解決方案

使用這些解決方案中的任何一個來解決此問題。

* 確保模組遵循以下格式:模組Name.zip->模块名称或版本号 ->(模組名稱.psm1,模組名稱.psd1)。
* 打開 **.psd1**檔,查看模組是否有任何依賴項。 如果有，請將這些模組上傳至自動化帳戶。
* 確保模組資料夾中存在任何引用的 **.dll**檔。

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>方案:更新-AzureModule.ps1 更新模組時掛起

#### <a name="issue"></a>問題

使用[Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook 更新 Azure 模組時,模組更新過程將掛起。

#### <a name="cause"></a>原因

使用**Update-AzureModule.ps1**時,用於確定同時更新的模組數的預設設置為 10。 同時更新太多模組時，更新程序很容易發生錯誤。

#### <a name="resolution"></a>解決方案

同一自動化帳戶中需要所有 AzureRM 或 Az 模組並不常見。 建議僅導入所需的特定模組。

> [!NOTE]
> 避免導入導入`Az.Automation`所有`AzureRM.Automation`包含 模組的整個模組或模組。

如果更新過程掛起,請將`SimultaneousModuleImportJobCount`參數添加到**Update-AzureModule.ps1**腳本,並提供低於預設值 10 的值。 如果實現此邏輯,建議從值 3 或 5 開始。 `SimultaneousModuleImportJobCount`是更新 **-自動化AzureModuleForAccount**系統運行簿的參數,用於更新 Azure 模組。 如果進行此調整,更新過程將運行更長時間,但完成的機會更高。 下列範例顯示參數以及要在 Runbook 中放置它的位置：

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

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>方案:您無法建立或更新「帳戶」

#### <a name="issue"></a>問題

當您嘗試建立或更新執行身分帳戶時，您會收到類似下列錯誤訊息的錯誤：

```error
You do not have permissions to create…
```

#### <a name="cause"></a>原因

您沒有建立或更新執行身分帳戶所需的權限，或資源已在資源群組層級鎖定。

#### <a name="resolution"></a>解決方案

要建立或更新「執行為「帳戶,您必須對「執行為「帳戶使用的各種資源具有適當的[權限](../manage-runas-account.md#permissions)。 

如果問題是由於鎖,請驗證是否可以刪除該鎖。 然後導航到在 Azure 門戶中鎖定的資源,右鍵單擊鎖,然後單擊 **「刪除**」。。

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>方案:在執行 Runbook 時,您會收到錯誤"無法在 DLL "iplpapi.dll"中找到名為"GetPerAdapterinfo"的入口點"

#### <a name="issue"></a>問題

執行 Runbook 時,您會收到以下異常:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>原因

此錯誤很可能是由配置不正確的[「作為帳戶運行」](../manage-runas-account.md)引起的。

#### <a name="resolution"></a>解決方案

確保正確配置了"運行作為"帳戶。 然後驗證 Runbook 中是否具有使用 Azure 進行身份驗證的正確代碼。 下面的範例顯示了使用「運行作為帳戶」在 Runbook 中向 Azure 進行身份驗證的代碼段。

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>後續步驟

如果您在上面看不到問題或無法解決問題,請嘗試以下管道之一以獲取其他支援:

* 通過[Azure 論壇](https://azure.microsoft.com/support/forums/)從 Azure 專家那裡獲得答案。
* 與[@AzureSupport](https://twitter.com/azuresupport)連接 ,官方 Microsoft Azure 帳戶通過將 Azure 社區連接到正確的資源(答案、支援和專家)來改善客戶體驗。
* 提出 Azure 支援事件。 跳到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **「取得支援**」。
