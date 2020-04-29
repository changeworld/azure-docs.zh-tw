---
title: Azure 自動化中的子 Runbook
description: 說明在 Azure 自動化中從另一個 Runbook 啟動 Runbook，以及在它們之間共用資訊的不同方法。
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 42362a170f493afd51a5d4ee139620ad25b54e79
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79367358"
---
# <a name="child-runbooks-in-azure-automation"></a>Azure 自動化中的子 Runbook

建議的作法是 Azure 自動化使用其他 runbook 所呼叫的個別函式來撰寫可重複使用的模組化 runbook。 父 Runbook 通常會呼叫一或多個子 Runbook 來執行必要的功能。 有兩種方式可以呼叫子 runbook，而且您應該瞭解哪些差異，才能判斷哪一個最適合您的案例。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="invoking-a-child-runbook-using-inline-execution"></a>使用內嵌執行叫用子 Runbook

若要從另一個 runbook 叫用 runbook 內嵌，請使用 runbook 的名稱並提供其參數值，如同您使用活動或 Cmdlet 一樣。 在相同自動化帳戶中的所有 Runbook 均可以此方式提供所有其他 Runbook 使用。 父 runbook 會先等待子 runbook 完成，然後再移至下一行，而且任何輸出都會直接傳回父系。

當您叫用 Runbook 內嵌時，它會在父 Runbook 所在的相同工作中執行。 子 runbook 的工作歷程記錄中不會有任何指示。 來自子 runbook 的任何例外狀況和任何資料流程輸出都會與父代相關聯。 這種行為會導致較少的作業，並讓它們更容易追蹤和疑難排解。

發佈 Runbook 時，其呼叫的所有子 Runbook 都必須是已發佈的。 原因是 Azure 自動化在編譯 runbook 時，會建立與任何子 runbook 的關聯。 如果尚未發佈子 runbook，父 runbook 會顯示正常發佈，但在啟動時產生例外狀況。 如果發生這種情況，您可以重新發佈父 Runbook 以正確參照子 Runbook。 若已變更任何子 runbook，您就不需要重新發佈父 runbook，因為已建立關聯。

稱為內嵌之子 runbook 的參數可以是任何資料類型，包括複雜物件。 沒有[JSON 序列化](start-runbooks.md#runbook-parameters)，如同您使用 Azure 入口網站或使用[AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) Cmdlet 來啟動 runbook 時一樣。

### <a name="runbook-types"></a>Runbook 類型

哪些 runbook 類型可以彼此呼叫？

* [Powershell runbook](automation-runbook-types.md#powershell-runbooks)和[圖形化 runbook](automation-runbook-types.md#graphical-runbooks)可以內嵌方式呼叫彼此，因為兩者都是以 PowerShell 為基礎。
* [Powershell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks)和圖形化 PowerShell 工作流程 runbook 可以內嵌方式呼叫彼此，因為兩者都是以 PowerShell 工作流程為基礎。
* PowerShell 類型和 PowerShell 工作流程類型無法以內嵌方式呼叫彼此，而且必須使用`Start-AzAutomationRunbook`。

發行順序是否重要？

Runbook 的發行順序只對 PowerShell 工作流程和圖形化 PowerShell 工作流程 runbook 很重要。

當您的 runbook 使用內嵌執行呼叫圖形化或 PowerShell 工作流程子 runbook 時，它會使用 runbook 的名稱。 名稱的開頭必須是`.\\` ，才能指定腳本位於本機目錄中。

### <a name="example"></a>範例

下列範例會啟動接受複雜物件、整數值和布林值的測試子 runbook。 子 Runbook 的輸出會指派給一個變數。 此案例的子 Runbook 是 PowerShell 工作流程 Runbook。

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

以下是使用 PowerShell runbook 做為子系的相同範例。

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>使用 Cmdlet 啟動子 runbook

> [!IMPORTANT]
> 如果您的 runbook 會使用`Start-AzAutomationRunbook` Cmdlet 搭配`Wait`參數來叫用子 runbook，而子 runbook 會產生物件結果，則作業可能會遇到錯誤。 若要解決此錯誤，請參閱[具有物件輸出的子 runbook](troubleshoot/runbooks.md#child-runbook-object) ，以瞭解如何使用[AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) Cmdlet 來執行邏輯來輪詢結果。

您可以使用`Start-AzAutomationRunbook`啟動 runbook，如[使用 Windows PowerShell 啟動 runbook](start-runbooks.md#start-a-runbook-with-powershell)中所述。 使用這個 Cmdlet 的模式有兩種。 在一個模式中，Cmdlet 會在為子 runbook 建立作業時傳回作業識別碼。 在您的腳本藉由指定*Wait*參數來啟用的另一個模式中，Cmdlet 會等候子作業完成，並傳回子 runbook 的輸出。

使用 Cmdlet 從子 runbook 啟動的工作，會與父 runbook 作業分開執行。 此行為會導致比啟動 runbook 內嵌更多的作業，並使作業更難以追蹤。父系可以非同步啟動一個以上的子 runbook，而不需要等待每個 runbook 完成。 對於呼叫子 runbook 內嵌的平行執行，父 runbook 必須使用[parallel 關鍵字](automation-powershell-workflow.md#parallel-processing)。

子 runbook 輸出因為計時，而無法可靠地傳回至父 runbook。 此外， `$VerbosePreference`、 `$WarningPreference`和等變數可能不會傳播至子 runbook。 若要避免這些問題，您可以使用`Start-AzAutomationRunbook`搭配`Wait`參數，將子 Runbook 當做個別的自動化作業來啟動。 這項技術會封鎖父 runbook，直到子 runbook 完成為止。

如果您不想讓父 runbook 在等待時遭到封鎖，您可以使用`Start-AzAutomationRunbook`而不搭配`Wait`參數來啟動子 runbook。 在此情況下，您的 runbook 必須使用[AzAutomationJob](/powershell/module/az.automation/get-azautomationjob)來等候工作完成。 它也必須使用[AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput)和[AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord)來取得結果。

使用 Cmdlet 啟動之子 runbook 的參數是以雜湊表的形式提供，如[runbook 參數](start-runbooks.md#runbook-parameters)中所述。 只能使用簡單資料類型。 若 Runbook 有複雜資料類型的參數，必須以內嵌方式呼叫。

以個別作業的形式啟動子 Runbook 時，可能會遺失訂用帳戶內容。 若要讓子 runbook 針對特定的 Azure 訂用帳戶執行 Az module Cmdlet，子系必須在父 runbook 以外獨立向此訂用帳戶進行驗證。

如果相同自動化帳戶中的作業使用多個訂用帳戶，則在某個作業中選取訂用帳戶可以變更其他作業目前選取的訂閱內容。 若要避免這種情況`Disable-AzContextAutosave –Scope Process` ，請在每個 runbook 的開頭使用。 此動作只會將內容儲存至該 Runbook 執行。

### <a name="example"></a>範例

下列範例會使用參數啟動子 runbook，然後使用`Start-AzAutomationRunbook` Cmdlet 搭配`Wait`參數來等待它完成。 完成後，此範例會從子 runbook 收集 Cmdlet 輸出。 若要`Start-AzAutomationRunbook`使用，腳本必須向您的 Azure 訂用帳戶進行驗證。

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>子 Runbook 的呼叫方法比較

下表摘要說明從另一個 runbook 呼叫 runbook 的兩種方式之間的差異。

|  | 內嵌 | Cmdlet |
|:--- |:--- |:--- |
| 工作 (Job) |與父代在相同的工作中執行的子 Runbook。 |會為子 Runbook 建立個別的工作。 |
| 執行 |父 Runbook 會等待子 Runbook 完成後再繼續執行。 |父 Runbook 會在子 Runbook 啟動後立刻繼續執行，或 ** 父 Runbook 會等候子作業完成。 |
| 輸出 |父 Runbook 可以直接從子 Runbook 取得輸出。 |父 Runbook 必須擷取子 Runbook 作業的輸出，或 ** 父 Runbook 可以直接從子 Runbook 取得輸出。 |
| 參數 |子 Runbook 參數的值是個別指定，而且可以使用任何資料類型。 |子 Runbook 參數的值必須結合成單一雜湊表。 這個雜湊表只能包含簡單、陣列，以及使用 JSON 序列化的物件資料類型。 |
| 自動化帳戶 |父 runbook 只能在相同的自動化帳戶中使用子 runbook。 |父 runbook 可以使用來自任何自動化帳戶的子 runbook，從相同的 Azure 訂用帳戶，甚至是來自您擁有連線的不同訂用帳戶。 |
| 發佈 |發佈父 Runbook 之前必須先發佈子 Runbook。 |在啟動父 runbook 之前，會隨時發佈子 runbook。 |

## <a name="next-steps"></a>後續步驟

* [在 Azure 自動化中啟動 Runbook](start-runbooks.md)
* [Azure 自動化中的 Runbook 輸出和訊息](automation-runbook-output-and-messages.md)