---
title: 管理 Azure 自動化中的 Runbook
description: 此文章說明如何管理 Azure 自動化中的 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 93b34af0baed89fd312948aeffe8ea4ac8ef806c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834691"
---
# <a name="manage-runbooks-in-azure-automation"></a>管理 Azure 自動化中的 Runbook

您可以將 Runbook 新增至 Azure 自動化，方法是建立新的，或是從檔案或 [Runbook 資源庫](automation-runbook-gallery.md)匯入現有的。 此文章提供管理從檔案所匯入 Runbook 的資訊。 您可以在 [Azure 自動化的 Runbook 和模組資源庫](automation-runbook-gallery.md)中找到有關存取社群 Runbook 和模組的所有詳細資料。

## <a name="create-a-runbook"></a>建立 Runbook

使用其 Azure 入口網站或 Windows PowerShell，在 Azure 自動化中建立新的 Runbook。 一旦建立 Runbook 之後，您可以使用下列文章中的資訊加以編輯：

* [在 Azure 自動化中編輯文字式 Runbook](automation-edit-textual-runbook.md) 
* [了解適用於自動化 Runbook 的重要 Windows PowerShell 工作流程概念](automation-powershell-workflow.md)
* [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)
* [管理 Azure 自動化中的 Python 2 套件](python-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>在 Azure 入口網站中建立 Runbook

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 在中樞中，選取 [程序自動化] 底下的 [Runbook]，以開啟 Runbook 清單。
3. 按一下 [建立 Runbook]。
4. 輸入 Runbook 的名稱，然後選取其[類型](automation-runbook-types.md)。 Runbook 名稱必須以字母開頭，可以包含字母、數字、底線和連字號。
5. 按一下 [建立]  來建立 Runbook 並開啟編輯器。

### <a name="create-a-runbook-with-powershell"></a>使用 PowerShell 建立 Runbook

您可以使用 [New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) \(英文\) Cmdlet 來建立空白 Runbook。 使用 `Type` 參數指定針對 `New-AzAutomationRunbook` 所定義四個 Runbook 類型的其中一個。

下列範例示範如何建立新的空白 Runbook。

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>匯入 Runbook

您可以匯入 PowerShell 或 PowerShell 工作流程 (**ps1**) 指令碼、圖形化 Runbook ( **.graphrunbook**) 或 Python 2 指令碼 ( **.py**) 來製作自己的 Runbook。  您必須指定匯入期間建立的 [Runbook 類型](automation-runbook-types.md)，並考量下列事項。

* 您可以將不包含工作流程的 **.ps1** 檔案匯入至 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 或 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 您將其匯入 PowerShell 工作流程 Runbook，其會轉換成工作流程。 在此情況下，Runbook 中會包含註解來描述所做的變更。

* 您只能將包含 PowerShell 工作流程的 **.ps1** 檔案匯入至 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 如果檔案包含多個 PowerShell 工作流程，則匯入會失敗。 您必須將每個工作流程儲存到它們各自的檔案，並且個別匯入。

* 請勿將包含 PowerShell 工作流程的 **.ps1** 檔案匯入 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)，因為 PowerShell 指令碼引擎無法加以辨識。

* 只將 **.graphrunbook** 檔案匯入新的[圖形化 Runbook](automation-runbook-types.md#graphical-runbooks)。 

### <a name="import-a-runbook-from-the-azure-portal"></a>從 Azure 入口網站匯入 Runbook

您可以使用下列程序，將指令碼檔案匯入到 Azure 自動化。

> [!NOTE]
> 若使用入口網站，您只能將 **.ps1** 檔案匯入 PowerShell 工作流程 Runbook。

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 選取 [程序自動化] 底下的 [Runbook]，以開啟 Runbook 清單。
3. 按一下 [匯入 Runbook]。
4. 按一下 [Runbook 檔案] 並選取要匯入的檔案。
5. 如果 [名稱]  欄位已啟用，則您可以選擇變更 Runbook 名稱。 名稱必須以字母開頭，可以包含字母、數字、底線和連字號。
6. [Runbook 類型](automation-runbook-types.md) 會自動選取，但在考量適用的限制之後，您可以變更類型。
7. 按一下頁面底部的 [新增] 。 新的 Runbook 會出現在自動化帳戶的 Runbook 清單中。
8. 您必須 [發佈 Runbook](#publish-a-runbook) ，才能執行。

> [!NOTE]
> 匯入圖形化 Runbook 之後，您可以將其轉換成另一種類型。 不過，您無法將圖形化 Runbook 轉換成文字式 Runbook。

### <a name="import-a-runbook-with-windows-powershell"></a>使用 Windows PowerShell 匯入 Runbook

使用 [Import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) \(英文\) Cmdlet，來匯入為指令碼檔案以作為草稿 Runbook。 如果 Runbook 已經存在，除非您搭配 Cmdlet 使用 `Force` 參數，否則匯入會失敗。

以下範例示範如何將指令碼檔案匯入 Runbook。

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="handle-resources"></a>處理資源

如果您的 Runbook 會建立[資源](automation-runbook-execution.md#resources)，則指令碼應先檢查以查看資源是否已經存在，再嘗試加以建立。 以下是基本定義。

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

## <a name="retrieve-details-from-activity-log"></a>從活動記錄擷取詳細資料

您可以從自動化帳戶的[活動記錄](automation-runbook-execution.md#activity-logging)擷取 Runbook 詳細資料，例如啟動 Runbook 的人員或帳戶。 下列 PowerShell 範例會提供執行所指定 Runbook 的最後一個使用者。

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>追蹤進度

最佳做法是使用可輕鬆重複使用及重新啟動的邏輯，將您的 Runbook 撰寫為本質上是模組化的。 追蹤 Runbook 中的進度可確保 Runbook 邏輯會在發生問題時正確執行。 

您可以藉由使用儲存體帳戶、資料庫或共用檔案等外部來源來追蹤 Runbook 進度。 在您的 Runbook 中建立邏輯，以先檢查所採取最後一個動作的狀態。 然後，根據檢查結果，邏輯可以略過或繼續 Runbook 中的特定工作。

## <a name="prevent-concurrent-jobs"></a>防止並行作業

某些 Runbook 如果跨多個作業同時執行，其會以奇怪的方式運作。 在此情況下，Runbook 務必要實作判斷是否已經有執行中作業的邏輯。 以下是基本定義。

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>在時間相依指令碼中處理暫時性錯誤

您的 Runbook 必須健全且能夠處理[錯誤](automation-runbook-execution.md#errors)，包括可能導致其重新啟動或失敗的暫時性錯誤。 如果 Runbook 失敗，Azure 自動化會加以重試。

如果您的 Runbook 通常會在有限時間內執行，請讓指令碼實作邏輯來檢查執行時間。 此檢查可確保作業 (例如啟動、關機或擴增) 只會在特定時間執行。

> [!NOTE]
> Azure 沙箱程序的本地時間會設定為 UTC。 您 Runbook 中的日期和時間計算必須考慮這一點。

## <a name="work-with-multiple-subscriptions"></a>使用多個訂用帳戶

您的 Runbook 必須能夠搭配[訂用帳戶](automation-runbook-execution.md#subscriptions)運作。 例如，若要處理多個訂用帳戶，Runbook 會使用 [Disable-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) \(英文\) Cmdlet。 此 Cmdlet 可確保系統不會從在相同沙箱中執行的另一個 Runbook 擷取驗證內容。 Runbook 也會在 Az 模組 Cmdlet 上使用 `AzContext` 參數，並傳遞適當的內容給其。

```powershell
# Ensures that you do not inherit an AzContext in your runbook
Disable-AzContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

## <a name="work-with-a-custom-script"></a>使用自訂指令碼

> [!NOTE]
> 您通常無法在已安裝 Log Analytics 代理程式的主機上執行自訂指令碼和 Runbook。 

使用自訂指令碼：

1. 建立自動化帳戶，並取得[參與者角色](automation-role-based-access-control.md)。
2. [將帳戶連結至 Azure 工作區](../security-center/security-center-enable-data-collection.md)。
3. 啟用[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)、[更新管理](automation-update-management.md)或其他自動化功能。 
4. 如果在 Linux 機器上，您需要高權限。 登入以[關閉簽章檢查](automation-linux-hrw-install.md#turn-off-signature-validation)。

## <a name="test-a-runbook"></a>測試 Runbook

當您測試 Runbook 時， [草稿版本](#publish-a-runbook) 會執行，而且它執行的任何動作都會完成。 不會建立任何工作歷程記錄，但 [測試輸出] 窗格中會顯示[輸出](automation-runbook-output-and-messages.md#use-the-output-stream)與[警告和錯誤](automation-runbook-output-and-messages.md#monitor-message-streams)串流。 只有將 [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) 變數設定為 `Continue`，傳送給[詳細資訊串流](automation-runbook-output-and-messages.md#monitor-message-streams)的訊息才會顯示在 [輸出] 窗格中。

即使執行的是草稿版本，Runbook 仍會正常執行，並對環境中的資源執行任何動作。 因此，您只應在非生產資源中測試 Runbook。

測試每個 [Runbook 類型](automation-runbook-types.md)的程序都相同。 無論是在文字式編輯器或 Azure 入口網站的圖形化編輯器中進行測試，都沒有任何差別。

1. 您可以在[文字編輯器](automation-edit-textual-runbook.md)或[圖形化編輯器](automation-graphical-authoring-intro.md)中開啟 Runbook 的草稿版本。
1. 按一下 [測試] 以開啟 [測試] 頁面。
1. 如果 Runbook 有參數，其會列在左窗格中，您可以在此提供用於測試的值。
1. 如果您想要在[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)上執行測試，請將 [執行設定] 變更為 [混合式背景工作角色]，然後選取目標群組的名稱。  否則，請保留預設值 **Azure**，以便在雲端中執行測試。
1. 按一下 [開始] 以開始測試。
1. 您可以使用 [輸出] 窗格下的按鈕來停止或暫止測試中的 [PowerShell 工作流程](automation-runbook-types.md#powershell-workflow-runbooks)或[圖形化](automation-runbook-types.md#graphical-runbooks) Runbook。 暫停 Runbook 時，它會先完成目前的活動後才暫停。 Runbook 一旦暫停，您可以選擇停止或重新啟動。
1. 您可以在 [輸出] 窗格中檢查 Runbook 的輸出。

## <a name="publish-a-runbook"></a>發行 Runbook

當您建立或匯入新的 Runbook 時，您必須發佈才能執行它。 Azure 自動化中的每個 Runbook 都有草稿版本和已發行版本。 只可執行已發行版本，而且只可編輯草稿版本。 已發行版本不會受到草稿版本的任何變更影響。 草稿版本應該已可供使用時，您會加以發佈，以草稿版本覆寫目前的已發佈版本。

### <a name="publish-a-runbook-in-the-azure-portal"></a>在 Azure 入口網站中發行 Runbook

1. 在 Azure 入口網站中開啟 Runbook。
2. 按一下 **[編輯]** 。
3. 按一下 [發佈]，然後按一下 [是] 來回應驗證訊息。

### <a name="publish-a-runbook-using-powershell"></a>使用 PowerShell 發佈 Runbook

您可以使用 [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) Cmdlet 來發佈 Runbook。 

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>在 Azure 入口網站中排程 Runbook

當您的 Runbook 發佈之後，您可以針對作業加以排程：

1. 在 Azure 入口網站中開啟 Runbook。
2. 在 [資源] 下方選取 [排程]。
3. 選取 [新增排程]。
4. 在 [排程 Runbook] 頁面中，選取 [將排程連結至您的 Runbook]。
5. 在 [排程] 窗格中，選擇 [建立新的排程]。
6. 在 [新增排程] 窗格中，輸入名稱、描述和其他參數。 
7. 建立排程之後，請加以反白，然後按一下 [確定]。 其現在應該已連結至您的 Runbook。
8. 尋找您信箱中通知您 Runbook 狀態的電子郵件。

## <a name="obtain-job-statuses"></a>取得作業狀態

### <a name="view-statuses-in-the-azure-portal"></a>在 Azure 入口網站中檢視狀態

[作業](automation-runbook-execution.md#jobs)中提供 Azure 自動化中工作處理的詳細資料。 當您準備好查看 Runbook 作業時，請使用 Azure 入口網站並存取您的自動化帳戶。 在右邊，您可以在 [作業統計資料] 圖格下方，看到所有 Runbook 作業的摘要。 

![[作業統計資料] 圖格](./media/manage-runbooks/automation-account-job-status-summary.png)

摘要會針對已執行的每個作業顯示作業狀態的計數和圖形表示。

按一下圖格，即會顯示 [作業] 頁面，其中包含所有已執行作業的摘要清單。 此頁面會顯示每個作業的狀態、Runbook 名稱、開始時間和完成時間。

![自動化帳戶的 [作業] 頁面](./media/manage-runbooks/automation-account-jobs-status-blade.png)

您可以藉由選取 [篩選作業] 來篩選作業清單。 篩選特定的 Runbook、作業狀態，或從下拉式清單中選擇，並提供搜尋的時間範圍。

![篩選作業狀態](./media/manage-runbooks/automation-account-jobs-filter.png)

或者，您可以檢視特定 Runbook 的作業摘要詳細資料，方法是在您的自動化帳戶中，從 [Runbook] 頁面中選取該 Runbook，然後選取 [作業]。 此動作會顯示 [作業] 頁面。 您可以從此處按一下作業記錄，以檢視其詳細資料和輸出。

![自動化帳戶的 [作業] 頁面](./media/manage-runbooks/automation-runbook-job-summary-blade.png)

### <a name="retrieve-job-statuses-using-powershell"></a>使用 PowerShell 擷取作業狀態

使用 [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) Cmdlet 擷取針對 Runbook 建立的工作以及特定工作的詳細資料。 如果您使用 `Start-AzAutomationRunbook` 啟動 Runbook，其會傳回產生的作業。 使用 [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) 來擷取作業輸出。

下列範例會針對範例 Runbook 取得上一個作業並顯示其狀態、提供給 Runbook 參數的值，以及作業輸出。

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

下列範例會擷取特定作業的輸出，並傳回每一筆記錄。 如果其中一個記錄有[例外狀況](automation-runbook-execution.md#exceptions)，則指令碼會寫入例外狀況，而不是值。 此行為非常有用，因為例外狀況可以提供通常可能不會在輸出期間記錄的額外資訊。

```azurepowershell-interactive
$output = Get-AzAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>後續步驟

* 若要了解 Runbook 管理的詳細資料，請參閱 [Azure 自動化中的 Runbook 執行](automation-runbook-execution.md)。
* 若要準備 PowerShell Runbook，請參閱[在 Azure 自動化中編輯文字式 Runbook](automation-edit-textual-runbook.md)。
* 如需撰寫 PowerShell 工作流程 Runbook 的說明，請參閱[了解適用於 Azure 自動化的 PowerShell 工作流程](automation-powershell-workflow.md)。
* 如需撰寫圖形化 Runbook 的詳細資訊，請參閱[在 Azure 自動化中製作圖形化 Runbook](automation-graphical-authoring-intro.md)。
* 若要疑難排解 Runbook 執行的問題，請參閱[針對 Runbook 問題進行疑難排解](troubleshoot/runbooks.md)。
