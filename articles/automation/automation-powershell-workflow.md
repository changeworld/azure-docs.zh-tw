---
title: 了解適用於 Azure 自動化的 PowerShell 工作流程
description: 本文旨在做為熟悉 PowerShell 的作者的快速課程，以了解 PowerShell 和 PowerShell 工作流程的特定差異，以及適用於自動化 Runbook 的概念。
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 1b275239c19584bc11472711a32972aa3ebea1ab
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457530"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>了解適用於自動化 Runbook 的重要 Windows PowerShell 工作流程概念

Azure 自動化中的 Runbook 會實作為 Windows PowerShell 工作流程。  Windows PowerShell 工作流程類似於 Windows PowerShell 指令碼，但有一些顯著的差異可能會對新使用者造成混淆。  雖然本文旨在協助您使用 PowerShell 工作流程撰寫 Runbook，但是除非您需要檢查點，否則建議您使用 PowerShell 來撰寫 Runbook。  在撰寫 PowerShell 工作流程 Runbook 時有許多語法差異，而這些差異需要更多的工作來撰寫有效的工作流程。

工作流程是一連串的程式化、連接步驟，執行長時間執行的工作，或是需要跨多個裝置或受控節點協調多個步驟。 透過標準的指令碼工作流程的好處包括能夠同時對多個裝置執行動作，以及可自動從失敗復原的能力。 Windows PowerShell 工作流程是使用 Windows Workflow Foundation 的 Windows PowerShell 指令碼。 雖然工作流程是使用 Windows PowerShell 語法編寫，並由 Windows PowerShell 啟動，它是由 Windows Workflow Foundation 來處理。

如需這篇文章中的主題的完整詳細資訊，請參閱 [開始使用 Windows PowerShell 工作流程](https://technet.microsoft.com/library/jj134242.aspx)。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="basic-structure-of-a-workflow"></a>工作流程的基本結構

將 PowerShell 文稿轉換為 PowerShell 工作流的第一`Workflow`步是將其與 關鍵字一起包含。  工作流從關鍵字開始,`Workflow`後跟括弧中包含的腳本正文。 工作流的名稱遵循`Workflow`以下文法中的關鍵字:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

工作流程的名稱必須符合自動化 Runbook 的名稱。 如果正在導入 Runbook,則檔名必須與工作流名稱匹配,並且必須以 *.ps1*結尾。

要向工作流添加參數,請使用關鍵字,`Param`就像在腳本中一樣。

## <a name="code-changes"></a>程式碼變更

PowerShell 工作流程程式碼看起來幾乎類似於 PowerShell 指令碼，除了少數幾個重大變更。  下列各節說明您必須對 PowerShell 指令碼進行的變更，以讓它在工作流程中執行。

### <a name="activities"></a>活動

活動是工作流程中的特定工作。 就像指令碼是由一或多個命令所組成，工作流程是由序列中執行的一或多個活動所組成。 執行工作流程時，Windows PowerShell 工作流程會自動將許多 Windows PowerShell Cmdlet 轉換為活動。 在 Runbook 中指定其中一個 Cmdlet 時，對應的活動是由 Windows Workflow Foundation 執行。 針對沒有對應活動的 Cmdlet，Windows PowerShell 工作流程會自動在 [InlineScript](#inlinescript) 活動內執行 Cmdlet。 有一組 Cmdlet 被排除，除非您明確在 InlineScript 區塊中將其納入，否則無法用在工作流程中。 如需這些概念的詳細資訊，請參閱 [在指令碼工作流程中使用活動](https://technet.microsoft.com/library/jj574194.aspx)。

工作流程活動共用一組通用參數來設定其作業。 如需有關工作流程通用參數的詳細資訊，請參閱 [about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx)。

### <a name="positional-parameters"></a>位置參數

您無法對活動和工作流程中的 Cmdlet 使用位置參數。  這都表示您必須使用參數名稱。

例如，請考慮會取得所有執行中服務的下列程式碼。

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

如果您嘗試在工作流程中執行這個相同的程式碼，您會接收到像「無法使用指定的具名參數解析參數集」的訊息。  若要修正這個問題，請提供參數名稱，如下所示。

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>已還原序列化的物件

工作流程中的物件會還原序列化。  這表示其屬性都仍然可用，而不是它們的方法。  例如，請考慮下列 PowerShell 程式碼，它會使用 Service 物件的 Stop 方法來停止服務。

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

如果您嘗試執行此工作流程，您會接收到錯誤，指出「Windows PowerShell 工作流程不支援方法引動過程」。

其中一個選項是將這兩行程式碼中包裝在 [InlineScript](#inlinescript) 區塊中，在此情況下 $Service 會是區塊內的服務物件。

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

另一個選項是使用會與方法執行相同功能的另一個 Cmdlet，如果有的話。  在我們的範例中，Stop-Service Cmdlet 會提供與 Stop 方法相同的功能，並且您可以使用下面工作流程。

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

當您`InlineScript`需要作為傳統的 PowerShell 腳本而不是 PowerShell 工作流運行一個或多個命令時,該活動非常有用。  在工作流程中的命令會傳送至 Windows Workflow Foundation 進行處理，而 Windows PowerShell 會處理 InlineScript 區塊中的命令。

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

雖然 InlineScript 活動在特定工作流程中可能是關鍵，它們不支援工作流程建構，而且應該基於以下原因時使用：

* 您不能在 InlineScript 區塊內使用[檢查點](#checkpoints)。 如果失敗發生在區塊內，它必須從區塊的開頭繼續。
* 您不能在 InlineScriptBlock 內使用[平行執行](#parallel-processing)。
* InlineScript 會影響工作流程的延展性，因為它會保留 InlineScript 區塊的整個長度的 Windows PowerShell 工作階段。

如需使用 InlineScript 的詳細資訊，請參閱[在工作流程中執行 Windows PowerShell 命令](https://technet.microsoft.com/library/jj574197.aspx)和 [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx)。

## <a name="parallel-processing"></a>平行處理

Windows PowerShell 工作流程的優點之一是可平行執行一組命令，而不是如同一般的指令碼以循序方式執行。

可以使用關鍵字`Parallel`創建具有多個同時運行的命令的腳本塊。 這會使用如下所示的語法。 在此情況下，Activity1 和 Activity2 將同時開始。 只有在 Activity1 和 Activity2 都已完成之後，Activity3 才會開始。

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

例如，考慮下列 PowerShell 命令，它會將多個檔案複製到網路目的地。  這些命令會循序執行，因此一個檔案必須完成複製才能開始複製下一個。

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

您可以使用 `ForEach -Parallel` 建構函式同時處理集合中每個項目的命令。 會以平行方式處理集合中的項目，而循序執行指令碼區塊中的命令。 這會使用如下所示的語法。 在此情況下，Activity1 將與集合中的所有項目同時開始。 針對每個項目，Activity2 會在 Activity1 完成之後開始。 只有在 Activity1 和 Activity2 已完成所有項目之後，Activity3 才會開始。 我們使用 `ThrottleLimit` 參數來限制平行處理原則。 `ThrottleLimit` 過高可能會造成問題。 `ThrottleLimit` 參數的理想值取決於您的環境中的許多因素。 您應該嘗試以較小的值開始，並嘗試不同的遞增值，直到找到適合您特定情況的值。

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

下列範例類似於先前的平行複製檔案範例。  在此情況下，複製之後會對每個檔案顯示訊息。  只有在全部完成複製之後，才會顯示最終完成訊息。

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
> 我們不建議並行執行子 Runbook，因為這可能會提供不可靠的結果。 有時候子 Runbook 的輸出不會出現，並且一個子 Runbook 中的設定會影響其他平行子 Runbook。 $VerbosePreference、$WarningPreference 等變數和其他變數可能不會傳播至子 Runbook。 如果子 Runbook 變更這些值，它們可能無法在引動過程後正確地還原。

## <a name="checkpoints"></a>檢查點

*檢查點* 是包含變數的目前值和在該點產生的任何輸出的工作流程的目前狀態的快照。 如果工作流程結束時發生錯誤或是擱置，下次執行時，就會從其最後一個檢查點開始，而不是從工作流程的開頭開始。  您可以使用`Checkpoint-Workflow`活動在工作流中設置檢查點。 Azure 自動化具有一個稱為[公平共用](automation-runbook-execution.md#fair-share)的功能,其中將卸載運行 3 小時的 Runbook 以允許其他 Runbook 運行。 最終,將重新載入已卸載的 Runbook,當它被重新載入時,它將從 Runbook 中獲取的最後一個檢查點恢復執行。 為了保證 Runbook 最終完成,您必須按運行時間少於 3 小時的間隔添加檢查點。 如果在每次運行期間都添加了一個新檢查點,並且 Runbook 在 3 小時後由於錯誤而被逐出,則 Runbook 將無限期恢復。

在下列範例程式碼中，Activity2 之後發生的例外狀況造成工作流程結束。 工作流程再次執行時，它會先執行 Activity2，因為這是緊接在設定的最後一個檢查點之後。

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

在活動可能容易發生例外狀況，且不應在工作流程繼續執行之後重複執行時，您應該在工作流程中設定檢查點。 例如，您的工作流程可能會建立虛擬機器。 您可以在建立虛擬機器命令的前面和後面設定檢查點。 如果建立失敗，若再次開始工作流程，命令可能會重複。 如果建立成功之後工作流程失敗，當工作流程繼續時，將不會再次建立虛擬機器。

下列範例會將多個檔案複製到網路位置，並在每個檔案後設定檢查點。  如果遺失網路位置，工作流程結束時會發生錯誤。  當重新啟動時，它會從最後一個檢查點繼續，表示只會略過已複製的檔案。

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

由於使用者名憑據在調用[掛起工作流](https://technet.microsoft.com/library/jj733586.aspx)活動后或最後一個檢查點之後不會保留,因此您需要將憑據設置為 null,然後在調用或調用`Suspend-Workflow`檢查點後 從資產存儲處再次檢索它們。  否則,您可能會收到以下錯誤訊息:`The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

下列同一個程式碼會示範如何在 PowerShell 工作流程 Runbook 中處理此問題。

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
> 對於非圖形 PowerShell`Add-AzAccount`執行`Add-AzureRMAccount`簿, 並且是[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)的別名。 您可以使用這些 cmdlet,也可以將自動化帳戶中的[模組更新](automation-update-azure-modules.md)到最新版本。 即使您剛剛創建了新的自動化帳戶,您也可能需要更新模組。


如果您使用以服務主體設定的執行身分帳戶進行驗證，則不必這麼做。

如需有關檢查點的詳細資訊，請參閱 [加入檢查點至指令碼工作流程](https://technet.microsoft.com/library/jj574114.aspx)。

## <a name="next-steps"></a>後續步驟

* 要開始使用 PowerShell 工作流執行簿,請參閱[我的第一個 PowerShell 工作流執行簿](automation-first-runbook-textual.md)

