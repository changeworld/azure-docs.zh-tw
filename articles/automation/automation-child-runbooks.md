---
title: Azure 自動化中的子 Runbook
description: 說明在 Azure 自動化中從另一個 Runbook 啟動 Runbook，以及在它們之間共用資訊的不同方法。
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 42362a170f493afd51a5d4ee139620ad25b54e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367358"
---
# <a name="child-runbooks-in-azure-automation"></a>Azure 自動化中的子 Runbook

在 Azure 自動化中，建議編寫具有其他 Runbook 調用的離散函數的可重用模組化 Runbook。 父 Runbook 通常會呼叫一或多個子 Runbook 來執行必要的功能。 有兩種方法可以調用子 Runbook，並且您應該瞭解哪些方法最適合您的方案，因此應瞭解這些差異。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶，可以使用["如何更新 Azure 自動化 中的 Azure PowerShell"模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="invoking-a-child-runbook-using-inline-execution"></a>使用內嵌執行叫用子 Runbook

要從另一個 Runbook 調用 Runbook 內聯，請使用 Runbook 的名稱並為其參數提供值，就像使用活動或 Cmdlet 一樣。 在相同自動化帳戶中的所有 Runbook 均可以此方式提供所有其他 Runbook 使用。 父 Runbook 等待子 Runbook 完成，然後再移動到下一行，並且任何輸出都直接返回到父級。

當您叫用 Runbook 內嵌時，它會在父 Runbook 所在的相同工作中執行。 子手冊的作業歷史記錄中沒有任何指示。 任何異常和來自子 Runbook 的任何流輸出都與父級相關聯。 此行為導致作業更少，並使它們更易於跟蹤和疑難排解。

發佈 Runbook 時，其呼叫的所有子 Runbook 都必須是已發佈的。 原因是 Azure 自動化在編譯 Runbook 時與任何子 Runbook 建立關聯。 如果子 Runbook 尚未發佈，則父 Runbook 似乎已正確發佈，但在啟動時將生成異常。 如果發生這種情況，您可以重新發佈父 Runbook 以正確參照子 Runbook。 如果由於關聯已創建而更改了任何子 Runbook，則無需重新發佈父 Runbook。

稱為內聯的子 Runbook 的參數可以是任何資料類型，包括複雜物件。 沒有[JSON 序列化](start-runbooks.md#runbook-parameters)，就像使用 Azure 門戶或使用[開始自動化 Runbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) Cmdlet 啟動 Runbook 時一樣。

### <a name="runbook-types"></a>Runbook 類型

哪些 Runbook 類型可以相互調用？

* [PowerShell 運行簿](automation-runbook-types.md#powershell-runbooks)和[圖形運行簿](automation-runbook-types.md#graphical-runbooks)可以相互調用內聯，因為兩者都基於 PowerShell。
* [PowerShell 工作流運行簿](automation-runbook-types.md#powershell-workflow-runbooks)和圖形 PowerShell 工作流運行簿可以相互調用，因為兩者都基於 PowerShell 工作流。
* PowerShell 類型和 PowerShell 工作流類型不能相互內聯調用，必須使用`Start-AzAutomationRunbook`。

何時發佈訂單很重要？

Runbook 的發佈順序僅適用于 PowerShell 工作流和圖形 PowerShell 工作流運行簿。

當您的 Runbook 使用內聯執行調用圖形或 PowerShell 工作流子 Runbook 時，它使用 Runbook 的名稱。 名稱必須從`.\\`開始指定腳本位於本地目錄中。

### <a name="example"></a>範例

下面的示例啟動一個測試子 Runbook，該運行簿接受複雜物件、整數值和布林值。 子 Runbook 的輸出會指派給一個變數。 此案例的子 Runbook 是 PowerShell 工作流程 Runbook。

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

下面是使用 PowerShell 運行簿與子級相同的示例。

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>使用 Cmdlet 啟動子運行簿

> [!IMPORTANT]
> 如果 Runbook 調用具有`Start-AzAutomationRunbook``Wait`參數的 Cmdlet 的子 Runbook，並且子 Runbook 生成物件結果，則操作可能會遇到錯誤。 要解決該錯誤，請參閱[具有物件輸出的子 Runbook，](troubleshoot/runbooks.md#child-runbook-object)瞭解如何使用[Get-AzAutomationJob輸出記錄](/powershell/module/az.automation/get-azautomationjoboutputrecord)Cmdlet 實現邏輯以輪詢結果。

您可以使用 啟動`Start-AzAutomationRunbook`Runbook，如[使用 Windows PowerShell 啟動 Runbook](start-runbooks.md#start-a-runbook-with-powershell)中所述。 使用這個 Cmdlet 的模式有兩種。 在一種模式下，在為子 Runbook 創建作業時，Cmdlet 返回作業 ID。 在其他模式下，腳本通過指定*Wait*參數啟用該模式，Cmdlet 將等待子作業完成並從子 Runbook 返回輸出。

子 runbook 中的作業從 Cmdlet 開始與父 Runbook 作業分開運行。 此行為產生的作業比啟動 Runbook 內聯作業多，並且使作業更難跟蹤。父級可以非同步啟動多個子 Runbook，而無需等待每個子運行簿完成。 對於此並存執行，調用子 Runbook 內聯，父 Runbook 必須使用[並行關鍵字](automation-powershell-workflow.md#parallel-processing)。

子 Runbook 輸出由於計時而無法可靠地返回到父 Runbook。 此外，變數（如`$VerbosePreference`、`$WarningPreference`等變數）和其他變數可能不會傳播到子 Runbook。 為了避免這些問題，可以使用 參數將子 Runbook 作為單獨的自動化作業`Start-AzAutomationRunbook``Wait`啟動。 此技術阻止父 Runbook，直到子 Runbook 完成。

如果不希望在等待時阻止父 Runbook，則可以使用`Start-AzAutomationRunbook`沒有 參數啟動`Wait`子 Runbook。 在這種情況下，您的 Runbook 必須使用[Get-AzAutomationJobJob](/powershell/module/az.automation/get-azautomationjob)來等待作業完成。 它還必須使用[獲取-Az自動化作業輸出](/powershell/module/az.automation/get-azautomationjoboutput)和[獲取阿茲自動化作業輸出記錄](/powershell/module/az.automation/get-azautomationjoboutputrecord)來檢索結果。

以 Cmdlet 啟動的子 Runbook 的參數作為雜湊表提供，如[Runbook 參數](start-runbooks.md#runbook-parameters)中所述。 只能使用簡單資料類型。 若 Runbook 有複雜資料類型的參數，必須以內嵌方式呼叫。

以個別作業的形式啟動子 Runbook 時，可能會遺失訂用帳戶內容。 子 Runbook 要針對特定的 Azure 訂閱執行 Az 模組 Cmdlet，子級必須獨立于父運行簿對此訂閱進行身份驗證。

如果同一自動化帳戶中的作業使用多個訂閱，則在一個作業中選擇訂閱可以更改當前為其他作業選擇的訂閱上下文。 為了避免這種情況，請在每個`Disable-AzContextAutosave –Scope Process`Runbook 的開頭使用。 此動作只會將內容儲存至該 Runbook 執行。

### <a name="example"></a>範例

下面的示例啟動具有參數的子 Runbook，然後等待它使用帶有 參數`Start-AzAutomationRunbook``Wait`的 Cmdlet 完成。 完成後，該示例將從子 Runbook 收集 Cmdlet 輸出。 要使用`Start-AzAutomationRunbook`，腳本必須對 Azure 訂閱進行身份驗證。

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

下表總結了從另一個 Runbook 調用 Runbook 的兩種方法之間的差異。

|  | 內嵌 | Cmdlet |
|:--- |:--- |:--- |
| 工作 (Job) |與父代在相同的工作中執行的子 Runbook。 |會為子 Runbook 建立個別的工作。 |
| 執行 |父 Runbook 會等待子 Runbook 完成後再繼續執行。 |父 Runbook 會在子 Runbook 啟動後立刻繼續執行，或 ** 父 Runbook 會等候子作業完成。 |
| 輸出 |父 Runbook 可以直接從子 Runbook 取得輸出。 |父 Runbook 必須擷取子 Runbook 作業的輸出，或 ** 父 Runbook 可以直接從子 Runbook 取得輸出。 |
| 參數 |子 Runbook 參數的值是個別指定，而且可以使用任何資料類型。 |子 Runbook 參數的值必須結合成單一雜湊表。 這個雜湊表只能包含簡單、陣列，以及使用 JSON 序列化的物件資料類型。 |
| 自動化帳戶 |父運行簿只能在同一自動化帳戶中使用子 Runbook。 |父 Runbook 可以使用來自任何自動化帳戶、同一 Azure 訂閱的子 Runbook，甚至使用您連接到的不同訂閱的子 Runbook。 |
| 發佈 |發佈父 Runbook 之前必須先發佈子 Runbook。 |子運行簿在啟動父運行簿之前隨時發佈。 |

## <a name="next-steps"></a>後續步驟

* [在 Azure 自動化中啟動 Runbook](start-runbooks.md)
* [Azure 自動化中的 Runbook 輸出和消息](automation-runbook-output-and-messages.md)