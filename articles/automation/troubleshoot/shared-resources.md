---
title: 針對 Azure 自動化共用資源的錯誤進行疑難排解
description: 瞭解如何針對支援 runbook 的 Azure 自動化共用資源進行疑難排解及解決問題。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 4cea558b11d7ee7bbe838cecbd061cd487b536d2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372296"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>針對共用資源的錯誤進行疑難排解

本文討論在 Azure 自動化中使用共用資源時，您可以跨資源解決問題的解決方案。

## <a name="modules"></a>模組

### <a name="module-stuck-importing"></a>案例：模組在匯入時卡住

#### <a name="issue"></a>問題

當您在 Azure 自動化中匯入或更新模組時，模組會卡在 [匯入中] 狀態。

#### <a name="cause"></a>原因

匯入 PowerShell 模組是一種複雜的多步驟程序。 此程序可能會發生模組未正確匯入的問題。 如果發生這種情況，您所匯入的模組可能會卡在暫時性的狀態。 若要深入了解此程序，請參閱[匯入 PowerShell 模組](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)。

#### <a name="resolution"></a>解決方案

若要解決此問題，您必須使用 **Remove-AzureRmAutomationModule** Cmdlet 移除卡在[匯入](/powershell/module/azurerm.automation/remove-azurermautomationmodule)狀態的模組。 您可以稍後再重試匯入模組。

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="update-azure-modules-importing"></a>案例： AzureRM 模組在嘗試更新之後會停滯在匯入

#### <a name="issue"></a>問題

嘗試更新您的 AzureRM 模組之後，您的帳戶中會保留下列訊息的橫幅：

```error
Azure modules are being updated
```

#### <a name="cause"></a>原因

有一個已知問題，就是在資源群組中的自動化帳戶中更新 AzureRM 模組，其數值名稱開頭為0。

#### <a name="resolution"></a>解決方案

若要在您的自動化帳戶中更新 Azure 模組，它必須位於具有英數位元名稱的資源群組中。 數位名稱開頭為0的資源群組目前無法更新 AzureRM 模組。

### <a name="module-fails-to-import"></a>案例：無法匯入模組，或無法在匯入之後執行 Cmdlet

#### <a name="issue"></a>問題

模組無法匯入或匯入成功，但沒有擷取到任何 Cmdlet。

#### <a name="cause"></a>原因

模組可能無法成功匯入 Azure 自動化的部分常見原因如下：

* 結構不符合「自動化」所需的結構。
* 模組相依於另一個尚未部署到您自動化帳戶的模組。
* 模組在資料夾中遺失其相依性。
* 您使用 `New-AzureRmAutomationModule` Cmdlet 來上傳模組，但您尚未提供完整的儲存路徑，或是尚未使用可公開存取的 URL 來載入模組。

#### <a name="resolution"></a>解決方案

下列任何一個解決方案都可以修正此問題：

* 確定模組遵循下列格式：ModuleName.Zip **->** 模組名稱或版本號碼 **->** (ModuleName.psm1、ModuleName.psd1)
* 開啟 .psd1 檔案，並且查看該模組是否有任何相依性。 如果有，請將這些模組上傳至自動化帳戶。
* 請確定任何參考的 .dll 會出現在模組資料夾。

### <a name="all-modules-suspended"></a>案例： Update-azuremodule.ps1 在更新模組時暫停

#### <a name="issue"></a>問題

使用 [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook 來更新 Azure 模組時，模組更新的更新程序會暫止。

#### <a name="cause"></a>原因

使用 `Update-AzureModule.ps1` 指令碼時，用來判斷要同時更新多少個模組的預設設定為 10。 同時更新太多模組時，更新程序很容易發生錯誤。

#### <a name="resolution"></a>解決方案

同一個自動化帳戶中需要所有 AzureRM 模組的情況並不常見。 建議僅匯入您需要的 AzureRM 模組。

> [!NOTE]
> 避免匯入 **AzureRM** 模組。 匯入 **AzureRM** 模組會導致匯入所有 **AzureRM.\*** 模組，不建議這樣做。

如果更新程序暫止，您需要將 `SimultaneousModuleImportJobCount` 參數新增至 `Update-AzureModules.ps1` 指令碼，並提供低於預設值 10 的值。 如果您實作此邏輯，建議您從 3 或 5 的值開始。 `SimultaneousModuleImportJobCount` 是 `Update-AutomationAzureModulesForAccount` 系統 Runbook 的參數，可用來更新 Azure 模組。 此變更會讓程序執行時間變得更長，但更有可能完成。 下列範例顯示參數以及要在 Runbook 中放置它的位置：

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

### <a name="unable-create-update"></a>案例：您無法建立或更新執行身分帳戶

#### <a name="issue"></a>問題

當您嘗試建立或更新執行身分帳戶時，您會收到類似下列錯誤訊息的錯誤：

```error
You do not have permissions to create…
```

#### <a name="cause"></a>原因

您沒有建立或更新執行身分帳戶所需的權限，或資源已在資源群組層級鎖定。

#### <a name="resolution"></a>解決方案

若要建立或更新執行身分帳戶，您必須具備執行身分帳戶所用資源的適當權限。 若要深入了解建立或更新執行身分帳戶所需的權限，請參閱[執行身分帳戶權限](../manage-runas-account.md#permissions)。

如果問題是因為鎖定而造成的，請確認鎖定可以移除。 然後，瀏覽至已鎖定的資源、以滑鼠右鍵按一下鎖定，然後選擇 [刪除] 以移除鎖定。

### <a name="iphelper"></a>案例：當執行 runbook 時，您會收到「找不到 DLL ' iplpapi ' 中名為 ' GetPerAdapterInfo ' 的進入點」錯誤。

#### <a name="issue"></a>問題

執行 runbook 時，您會收到下列例外狀況：

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>原因

此錯誤很可能是因為[執行身分帳戶](../manage-runas-account.md)設定不正確所造成。

#### <a name="resolution"></a>解決方案

請確定已正確設定您的[執行身分帳戶](../manage-runas-account.md)。 正確設定之後，請確定您的 runbook 中有適當的程式碼可向 Azure 進行驗證。 下列範例顯示使用執行身分帳戶在 runbook 中向 Azure 進行驗證的程式碼片段。

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答
* 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫－專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
* 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
