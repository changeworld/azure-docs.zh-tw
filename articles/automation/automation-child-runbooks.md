---
title: 在 Azure 自動化中建立模組化 Runbook
description: 本文說明如何建立 Runbook 供另一個 Runbook 呼叫。
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: c15ed6e9409bee71a778986d8f38ae1ab126c180
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828639"
---
# <a name="create-modular-runbooks"></a>建立模組化 Runbook

在 Azure 自動化中，建議撰寫可重複使用的模組化 Runbook，且包含一個可供 Runbook 呼叫的離散函式。 父 Runbook 通常會呼叫一或多個子 Runbook 來執行必要的功能。 

有兩種方式可以呼叫子 Runbook，但您應該了解其中一些明顯的差異，才能判斷何者最適合您的情況。 下表摘要說明讓兩個 Runbook 彼此呼叫的兩種作法有何差異。

|  | 內嵌 | Cmdlet |
|:--- |:--- |:--- |
| 工作 (Job) |與父代在相同的工作中執行的子 Runbook。 |會為子 Runbook 建立個別的工作。 |
| 執行 |父 Runbook 會等待子 Runbook 完成後再繼續執行。 |父 Runbook 會在子 Runbook 啟動後立刻繼續執行，或  父 Runbook 會等候子作業完成。 |
| 輸出 |父 Runbook 可以直接從子 Runbook 取得輸出。 |父 Runbook 必須擷取子 Runbook 作業的輸出，或  父 Runbook 可以直接從子 Runbook 取得輸出。 |
| 參數 |子 Runbook 參數的值是個別指定，而且可以使用任何資料類型。 |子 Runbook 參數的值必須結合成單一雜湊表。 這個雜湊表只能包含簡單、陣列，以及使用 JSON 序列化的物件資料類型。 |
| 自動化帳戶 |父 Runbook 只能使用相同自動化帳戶中的子 Runbook。 |父 Runbook 使用的子 Runbook 可以來自任何自動化帳戶、相同的 Azure 訂用帳戶，甚至是您已連線的另一個訂用帳戶。 |
| 發佈 |發佈父 Runbook 之前必須先發佈子 Runbook。 |啟動父 Runbook 之前可隨時發佈子 Runbook。 |

## <a name="invoke-a-child-runbook-using-inline-execution"></a>使用內嵌執行來叫用子 Runbook

若要從其他 Runbook 來內嵌叫用 Runbook，請使用 Runbook 的名稱，並提供參數的值，就像使用活動或 Cmdlet 一樣。 在相同自動化帳戶中的所有 Runbook 均可以此方式提供所有其他 Runbook 使用。 父 Runbook 會等候子 Runbook 完成，才移動到下一行，任何輸出都直接傳回給父代。

當您叫用 Runbook 內嵌時，它會在父 Runbook 所在的相同工作中執行。 但在子 Runbook 的工作歷程記錄中看不出來。 來自子 Runbook 的任何例外狀況和任何資料流輸出，都與父代相關聯。 這種行為可減少作業，也更易於追蹤作業和進行疑難排解。

發佈 Runbook 時，其呼叫的所有子 Runbook 都必須是已發佈的。 因為 Azure 自動化在編譯 Runbook 時，將會與任何子 Runbook 建立關聯。 如果尚未發佈子 Runbook，則父 Runbook 雖然看似正常發佈，但啟動時會產生例外狀況。 如果發生這種情況，您可以重新發佈父 Runbook 以正確參照子 Runbook。 因為已建立關聯，即使任何子 Runbook 有所變動，您也不需要重新發佈父 Runbook。

內嵌呼叫的子 Runbook 可以有任何資料類型的參數，包括複雜物件。 沒有 [JSON 序列化](start-runbooks.md#work-with-runbook-parameters)，這不同於使用 Azure 入口網站或 [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) Cmdlet 來啟動 Runbook 的情形。

### <a name="runbook-types"></a>Runbook 類型

哪些 Runbook 類型可以彼此呼叫？

* [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 和[圖形化 Runbook](automation-runbook-types.md#graphical-runbooks) 可以內嵌呼叫彼此，因為兩者都以 PowerShell 為基礎。
* [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 和圖形化 PowerShell 工作流程 Runbook 可以內嵌呼叫彼此，因為兩者都以 PowerShell 工作流程為基礎。
* PowerShell 類型與 PowerShell 工作流程類型無法內嵌呼叫彼此，必須使用 `Start-AzAutomationRunbook`。

發行順序何時重要？

只有在涉及 PowerShell 工作流程和圖形化 PowerShell 工作流程 Runbook 時，Runbook 的發行順序才重要。

當 Runbook 使用內嵌執行來呼叫圖形化或 PowerShell 工作流程子 Runbook 時，將會使用 Runbook 的名稱。 名稱的開頭必須是 `.\\`，以表示指令碼位於本機目錄中。

### <a name="example"></a>範例

下列範例啟動的測試子 Runbook 接受複雜物件、整數值和布林值。 子 Runbook 的輸出會指派給一個變數。 此案例的子 Runbook 是 PowerShell 工作流程 Runbook。

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

以下是同一個範例，但使用 PowerShell Runbook 作為子 Runbook。

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="start-a-child-runbook-using-a-cmdlet"></a>使用 Cmdlet 啟動子 Runbook

> [!IMPORTANT]
> 如果 Runbook 以 `Start-AzAutomationRunbook` Cmdlet 搭配 `Wait` 參數來叫用子 Runbook，而且子 Runbook 產生物件結果，則作業可能會遇到錯誤。 若要解決此錯誤，請參閱[具有物件輸出的子 Runbook](troubleshoot/runbooks.md#child-runbook-object)，了解如何實作邏輯以使用 [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) Cmdlet 來輪詢結果。

您可以使用 `Start-AzAutomationRunbook` 來啟動 Runbook，如[使用 Windows PowerShell 啟動 Runbook](start-runbooks.md#start-a-runbook-with-powershell)中所述。 使用這個 Cmdlet 的模式有兩種。 在第一個模式中，為子 Runbook 建立作業時，Cmdlet 會傳回作業識別碼。 在第二個模式中 (因為指令碼指定 *Wait* 參數而啟用)，Cmdlet 會等候子作業完成，並傳回子 Runbook 的輸出。

使用 Cmdlet 從子 Runbook 啟動的作業，與父 Runbook 作業分開執行。 此行為會產生比內嵌啟動 Runbook 更多的作業，而且更難以追蹤作業。父 Runbook 可以利用非同步方式啟動多個子 Runbook，不需要等候每個子 Runbook 完成。 對於這種內嵌呼叫子 Runbook 內嵌的平行執行，父 Runbook 必須使用 [parallel 關鍵字](automation-powershell-workflow.md#use-parallel-processing)。

因為時間的關係，子 Runbook 的輸出無法可靠地傳回給父 Runbook。 此外，`$VerbosePreference`、`$WarningPreference` 等變數可能不會傳播至子 Runbook。 若要避免這些問題，您可以使用 `Start-AzAutomationRunbook` Cmdlet 搭配 `Wait` 參數，將子 Runbook 當作個別的自動化作業來啟動。 這項技術會封鎖父 Runbook，直到子 Runbook 完成為止。

如果您不希望父 Runbook 遭到封鎖而只能等待，您可以使用 `Start-AzAutomationRunbook` Cmdlet 但不指定 `Wait` 參數，以啟動子 Runbook。 在此情況下，Runbook 必須使用 [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) 來等候作業完成。 還必須使用 [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) 和 [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) 來取出結果。

對於使用 Cmdlet 啟動的子 Runbook，參數是以雜湊表形式提供，如 [Runbook 參數](start-runbooks.md#work-with-runbook-parameters)中所述。 只能使用簡單資料類型。 若 Runbook 有複雜資料類型的參數，必須以內嵌方式呼叫。

以個別作業的形式啟動子 Runbook 時，可能會遺失訂用帳戶內容。 若要讓子 Runbook 對特定的 Azure 訂用帳戶執行 Az 模組Cmdlet，子系必須在父 Runbook 以外單獨向此訂用帳戶進行驗證。

如果相同自動化帳戶中的作業涉及多個訂用帳戶，則在某個作業中選取訂用帳戶時，可能會變更其他作業目前選取的訂用帳戶內容。 若要避免這種情況，請在每個 Runbook 的開頭使用 `Disable-AzContextAutosave –Scope Process`。 此動作只會將內容儲存至該 Runbook 執行。

### <a name="example"></a>範例

下列範例使用參數啟動子 Runbook，然後使用 `Start-AzAutomationRunbook` Cmdlet 搭配 `Wait` 參數，以等待子 Runbook 完成。 完成後，此範例會從子 Runbook 收集 Cmdlet 輸出。 若要使用 `Start-AzAutomationRunbook` ，指令碼必須向 Azure 訂用帳戶進行驗證。

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

## <a name="next-steps"></a>後續步驟

* 若要執行 Runbook，請參閱[在 Azure 自動化中啟動 Runbook](start-runbooks.md)。
* 若要監視 Runbook 作業，請參閱 [Azure 自動化中的 Runbook 輸出與訊息](automation-runbook-output-and-messages.md)。