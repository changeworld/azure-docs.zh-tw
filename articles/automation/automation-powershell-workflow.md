---
title: 了解適用於 Azure 自動化的 PowerShell 工作流程
description: 本文說明 PowerShell 工作流程與 PowerShell 之間的差異，以及適用於自動化 Runbook 的概念。
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: f175e495af8e925c0d5a6c61669a5e2f44f73ae7
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185990"
---
# <a name="learn-powershell-workflow-for-azure-automation"></a>了解適用於 Azure 自動化的 PowerShell 工作流程

Azure 自動化中的 Runbook 會實作為 Windows PowerShell 工作流程，這是使用 Windows Workflow Foundation 的 Windows PowerShell 指令碼。 工作流程是一連串的程式化、連接步驟，執行長時間執行的工作，或是需要跨多個裝置或受控節點協調多個步驟。 

雖然工作流程是使用 Windows PowerShell 語法編寫，並由 Windows PowerShell 啟動，但其是由 Windows Workflow Foundation 來處理。 透過一般指令碼工作流程的好處包括同時對多個裝置執行動作，以及可從失敗自動復原。 

> [!NOTE]
> PowerShell 工作流程指令碼非常類似於 Windows PowerShell 指令碼，但有一些顯著的差異可能會對新使用者造成混淆。 因此，建議您只有在需要使用[檢查點](#use-checkpoints-in-a-workflow)時，才使用 PowerShell 工作流程來撰寫 Runbook。 

如需這篇文章中的主題完整詳細資訊，請參閱[開始使用 Windows PowerShell 工作流程](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134242(v=ws.11))。

## <a name="use-workflow-keyword"></a>使用 Workflow 關鍵字

將 PowerShell 指令碼轉換成 PowerShell 工作流程的第一個步驟，是使用 `Workflow` 關鍵字將其含括。 一種工作流程，以 `Workflow` 關鍵字為開頭，後面接著括在大括弧中的指令碼主體。 工作流程的名稱會遵循 `Workflow` 關鍵字，如下列語法所示：

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

工作流程的名稱必須符合自動化 Runbook 的名稱。 如果正在匯入 Runbook，則檔案名稱必須符合工作流程名稱，且必須以 **.ps1** 結尾。

若要將參數新增至工作流程，請使用 `Param` 關鍵字，如同您會對指令碼執行的動作。

## <a name="learn-differences-between-powershell-workflow-code-and-powershell-script-code"></a>了解 PowerShell 工作流程程式碼與 PowerShell 指令碼程式碼之間的差異

PowerShell 工作流程程式碼看起來幾乎類似於 PowerShell 指令碼，除了少數幾個重大變更。 下列各節說明您必須對 PowerShell 指令碼進行的變更，以讓它在工作流程中執行。

### <a name="activities"></a>活動

活動是工作流程中以序列執行的特定工作。 執行工作流程時，Windows PowerShell 工作流程會自動將許多 Windows PowerShell Cmdlet 轉換為活動。 在 Runbook 中指定其中一個 Cmdlet 時，對應的活動是由 Windows Workflow Foundation 執行。 

如果 Cmdlet 沒有對應的活動，Windows PowerShell 工作流程會自動在 [InlineScript](#use-inlinescript) 活動內執行 Cmdlet。 某些 Cmdlet 會受到排除，除非您明確在 InlineScript 區塊中將其納入，否則無法用在工作流程中。 如需詳細資訊，請參閱[使用指令碼工作流程中的活動](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574194(v=ws.11))。

工作流程活動共用一組通用參數來設定其作業。 請參閱 [about_WorkflowCommonParameters](/powershell/module/psworkflow/about/about_workflowcommonparameters)。

### <a name="positional-parameters"></a>位置參數

您無法對活動和工作流程中的 Cmdlet 使用位置參數。 因此，您必須使用參數名稱。 請考慮會取得所有執行中服務的下列程式碼：

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

如果您嘗試在工作流程中執行此程式碼，則會收到一則訊息，例如 `Parameter set cannot be resolved using the specified named parameters.`。若要針對此問題進行修正，請提供參數名稱，如下列範例所示：

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>已還原序列化的物件

工作流程中的物件會還原序列化，這表示其屬性仍然可以使用，但並非其方法。  例如，請考慮下列 PowerShell 程式碼，其會使用 `Service` 物件的 `Stop` 方法來停止服務。

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

如果您嘗試在工作流程中執行此項目，您會收到錯誤訊息，指出 `Method invocation is not supported in a Windows PowerShell Workflow.`

其中一個選項是將這兩行程式碼包裝在 [InlineScript](#use-inlinescript) 區塊中。 在此情況下，`Service` 代表區塊內的服務物件。

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

另一個選項是使用會與方法具有相同功能的另一個 Cmdlet (若有)。 在我們的範例中，`Stop-Service` Cmdlet 會提供與 `Stop` 方法相同的功能，且您可能會在工作流程中使用下列程式碼。

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="use-inlinescript"></a>使用 InlineScript

當您需要執行一或多個命令做為傳統的 PowerShell 指令碼 (而不是 PowerShell 工作流程) 時，`InlineScript` 活動很實用。  在工作流程中的命令會傳送至 Windows Workflow Foundation 進行處理，而 Windows PowerShell 會處理 InlineScript 區塊中的命令。

InlineScript 使用如下所示的語法。

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

透過將輸出指派給變數，您可以從 InlineScript 傳回輸出。 下列範例會停止服務，然後輸出服務名稱。

```powershell
Workflow Stop-MyService
{
    $Output = InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

您可以將值傳入 InlineScript 區塊，但必須使用 **$Using** 範圍修飾詞。  下列範例與前一個範例相同，不同之處在於服務名稱是由變數所提供。

```powershell
Workflow Stop-MyService
{
    $ServiceName = "MyService"

    $Output = InlineScript {
        $Service = Get-Service -Name $Using:ServiceName
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

雖然 InlineScript 活動在某些工作流程中可能很重要，但它們不支援工作流程結構。 您應該只在必要時才使用它們，原因如下：

* 您不能在 InlineScript 區塊內使用[檢查點](#use-checkpoints-in-a-workflow)。 如果失敗發生在區塊內，其必須從區塊的開頭繼續。
* 您不能在 InlineScriptBlock 區塊內使用[平行執行](#use-parallel-processing)。
* InlineScript 會影響工作流程的延展性，因為它會保留 InlineScript 區塊的整個長度的 Windows PowerShell 工作階段。

如需使用 InlineScript 的詳細資訊，請參閱[在工作流程中執行 Windows PowerShell 命令](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574197(v=ws.11))和 [about_InlineScript](/powershell/module/psworkflow/about/about_inlinescript)。

## <a name="use-parallel-processing"></a>使用平行處理

Windows PowerShell 工作流程的優點之一是可平行執行一組命令，而不是如同一般的指令碼以循序方式執行。

您可以使用 `Parallel` 關鍵字來建立具有多個同時執行之命令的指令碼區塊。 這會使用如下所示的語法。 在此情況下，Activity1 和 Activity2 將同時開始。 只有在 Activity1 和 Activity2 都已完成之後，Activity3 才會開始。

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

例如，考慮下列 PowerShell 命令，它會將多個檔案複製到網路目的地。 這些命令會循序執行，因此一個檔案必須完成複製才能開始複製下一個。

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

下列工作流程會平行執行這些相同的命令，讓它們在相同的時間全部開始複製。  只有在全部複製之後，才會顯示完成訊息。

```powershell
Workflow Copy-Files
{
    Parallel
    {
        Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
    }

    Write-Output "Files copied."
}
```

您可以使用 `ForEach -Parallel` 建構來並行處理集合中每個項目的命令。 會以平行方式處理集合中的項目，而循序執行指令碼區塊中的命令。 此程序會使用如下所示的語法。 在此情況下，Activity1 將與集合中的所有項目同時開始。 針對每個項目，Activity2 會在 Activity1 完成之後開始。 只有在 Activity1 和 Activity2 已完成所有項目之後，Activity3 才會開始。 我們使用 `ThrottleLimit` 參數來限制平行處理原則。 `ThrottleLimit` 過高可能會造成問題。 `ThrottleLimit` 參數的理想值取決於您的環境中的許多因素。 以較小的值開始，並嘗試不同的遞增值，直到找到適合您特定情況的值為止。

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

下列範例類似於先前的平行複製檔案範例。  在此情況下，複製之後會對每個檔案顯示訊息。  只有在全部複製之後，才會顯示最後的完成訊息。

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach -Parallel -ThrottleLimit 10 ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
    }

    Write-Output "All files copied."
}
```

> [!NOTE]
> 我們不建議並行執行子 Runbook，因為這可能會提供不可靠的結果。 有時候子 Runbook 的輸出不會出現，並且一個子 Runbook 中的設定會影響其他平行子 Runbook。 `VerbosePreference`、`WarningPreference` 等變數和其他變數可能不會傳播至子 Runbook。 如果子 Runbook 變更這些值，它們可能無法在引動過程後正確地還原。

## <a name="use-checkpoints-in-a-workflow"></a>在工作流程中使用檢查點

檢查點是工作流程目前狀態的快照，當中包含變數的目前值和在該點產生的任何輸出。 如果工作流程結束時發生錯誤或已暫停，則在下一次執行時，會從其最後一個檢查點開始，而非從開頭開始。 

您可以使用 `Checkpoint-Workflow` 活動來設定工作流程中的檢查點。 Azure 自動化具有名為[公平共用](automation-runbook-execution.md#fair-share)的功能，其中任何執行三小時的 Runbook 都會卸載，以允許其他 Runbook 執行。 最後，會重新載入已卸載的 Runbook。 進行到此時，其會從 Runbook 所取得的最後一個檢查點繼續執行。

若要保證 Runbook 最終會完成，您必須在執行時間不到三小時的間隔中，新增檢查點。 如果在每次執行時都新增檢查點，且如果 Runbook 在三個小時後由於錯誤而收回，則會無限期地繼續 Runbook。

在下列範例中，Activity2 之後發生的例外狀況會造成工作流程結束。 工作流程再次執行時，其會先執行 Activity2，因為這個活動是緊接在設定的最後一個檢查點之後。

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

在活動可能容易發生例外狀況，且不應在工作流程繼續執行之後重複執行時，在工作流程中設定檢查點。 例如，您的工作流程可能會建立虛擬機器。 您可以在建立虛擬機器命令的前後設定檢查點。 如果建立失敗，則再次開始工作流程時，命令可能會重複。 如果建立成功之後工作流程失敗，當工作流程繼續時，將不會再次建立虛擬機器。

下列範例會將多個檔案複製到網路位置，並在每個檔案後設定檢查點。  如果遺失網路位置，工作流程結束時會發生錯誤。  當它再次啟動時，會在最後一個檢查點繼續。 只會略過已複製的檔案。

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
        Checkpoint-Workflow
    }

    Write-Output "All files copied."
}
```

在您呼叫 [Suspend-Workflow](/powershell/module/psworkflow/about/about_suspend-workflow) 活動或最後一個檢查點之後，使用者名稱認證就不會保存下來，因此您必須將認證設定為 Null，然後在呼叫 `Suspend-Workflow` 或檢查點後，再次從資產存放區擷取認證。  否則，您可能會收到下列錯誤訊息︰`The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

下列同一個程式碼會示範如何在 PowerShell 工作流程 Runbook 中處理這種情況。

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzAutomationCredential -Name "MyCredential"
    $null = Connect-AzAccount -Credential $Cred

    $VmsToCreate = Get-AzAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzVM -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzAutomationCredential -Name "MyCredential"
        $null = Connect-AzAccount -Credential $Cred
        }
}
```

> [!NOTE]
> 針對非圖形化 PowerShell Runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) 的別名。 您可使用這些 Cmdlet，也可以在您的自動化帳戶中[將您的模組更新](automation-update-azure-modules.md)為最新版本。 即使您才剛建立新的自動化帳戶，可能還是需要更新您的模組。 如果您使用以服務主體設定的執行身分帳戶進行驗證，則不必使用這些 Cmdlet。

如需有關檢查點的詳細資訊，請參閱 [加入檢查點至指令碼工作流程](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574114(v=ws.11))。

## <a name="next-steps"></a>後續步驟

* 若要了解 PowerShell 工作流程 Runbook，請參閱[教學課程：建立 PowerShell 工作流程 Runbook](learn/automation-tutorial-runbook-textual.md)。
