---
title: 管理 Azure 自動化中的 Runbook
description: 本文描述如何管理 Azure 自動化中的 Runbook。 其中涵蓋基本作業並新增一些最佳作法。
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 687579825c652888112ff8ddff7401b3305e3a8e
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871192"
---
# <a name="manage-runbooks-in-azure-automation"></a>管理 Azure 自動化中的 Runbook

您可以透過建立新的 runbook，或從檔案或[runbook 資源庫](automation-runbook-gallery.md)匯入現有的 runbook，來將它新增至 Azure 自動化。 本文提供的資訊可用於管理從檔案匯入的 runbook。 您可以在[Azure 自動化的 Runbook 和模組資源庫](automation-runbook-gallery.md)中找到存取社區 runbook 和模組的所有詳細資料。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="create-a-runbook"></a>建立 Runbook

使用 Azure 入口網站或 Windows PowerShell，在 Azure 自動化中建立新的 runbook。 建立 runbook 之後，您可以使用中的資訊進行編輯：

* [在 Azure 自動化中編輯文字 runbook](automation-edit-textual-runbook.md) 
* [瞭解自動化 runbook 的重要 Windows PowerShell 工作流程概念](automation-powershell-workflow.md)
* [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)
* [管理 Azure 自動化中的 Python 2 封裝](python-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>在 Azure 入口網站中建立 Runbook

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 從中樞選取 [程式**自動化**] 底下的 [ **runbook** ] 以開啟 runbook 清單。
3. 按一下 [**建立 runbook**]。
4. 輸入 runbook 的 [名稱]，然後選取其 [[類型](automation-runbook-types.md)]。 Runbook 名稱必須以字母開頭，而且可以包含字母、數位、底線和連字號。
5. 按一下 [建立] **** 來建立 Runbook 並開啟編輯器。

### <a name="create-a-runbook-with-powershell"></a>使用 PowerShell 建立 Runbook

使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) Cmdlet 來建立空白 runbook。 使用`Type`參數來指定為`New-AzAutomationRunbook`定義的其中一個 runbook 類型。

下列範例顯示如何建立新的空白 runbook。

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>匯入 Runbook

您可以匯入 PowerShell 或 PowerShell 工作流程（**ps1**）腳本、圖形化 runbook （**. .Graphrunbook**）或 Python 2 腳本（**. .py**）來建立自己的 runbook。  您必須指定匯入期間建立的 [Runbook 類型](automation-runbook-types.md)，並考量下列事項。

* 您可以將未包含工作流程的**ps1**檔案匯入[Powershell Runbook](automation-runbook-types.md#powershell-runbooks)或[powershell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 如果您將它匯入 PowerShell 工作流程 runbook，則會將它轉換成工作流程。 在此情況下，runbook 中會包含批註來描述所做的變更。

* 您只能將包含 PowerShell 工作流程的**ps1**檔案匯入[powershell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 如果檔案包含多個 PowerShell 工作流程，則匯入會失敗。 您必須將每個工作流程儲存到它們各自的檔案，並且個別匯入。

* 請勿將包含 PowerShell 工作流程的**ps1**檔案匯入[powershell Runbook](automation-runbook-types.md#powershell-runbooks)，因為 powershell 腳本引擎無法辨識該檔案。

* 僅將 **.graphrunbook**檔案匯入至新的[圖形化 runbook](automation-runbook-types.md#graphical-runbooks)。 

### <a name="import-a-runbook-from-the-azure-portal"></a>從 Azure 入口網站匯入 runbook

您可以使用下列程序，將指令碼檔案匯入到 Azure 自動化。

> [!NOTE]
> 您只能使用入口網站，將**ps1**檔案匯入 PowerShell 工作流程 runbook。

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 選取 [程序自動化]  底下的 [Runbook]  ，以開啟 Runbook 清單。
3. 按一下 [匯**入 runbook**]。
4. 按一下 [ **Runbook**檔案]，然後選取要匯入的檔案。
5. 如果 [**名稱**] 欄位已啟用，則您可以選擇變更 runbook 名稱。 名稱必須以字母開頭，而且可以包含字母、數位、底線和連字號。
6. [Runbook 類型](automation-runbook-types.md) 會自動選取，但在考量適用的限制之後，您可以變更類型。
7. 按一下 [建立]  。 新的 Runbook 會出現在自動化帳戶的 Runbook 清單中。
8. 您必須 [發佈 Runbook](#publish-a-runbook) ，才能執行。

> [!NOTE]
> 匯入圖形化 runbook 之後，您可以將它轉換成另一種類型。 不過，您無法將圖形化 runbook 轉換成文字 runbook。

### <a name="import-a-runbook-with-windows-powershell"></a>使用 Windows PowerShell 匯入 runbook

使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) Cmdlet 將腳本檔案匯入為草稿 runbook。 如果 runbook 已經存在，除非您將`Force`參數與 Cmdlet 搭配使用，否則匯入將會失敗。

下列範例顯示如何將腳本檔案匯入 runbook。

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

如果您的 runbook 會建立[資源](automation-runbook-execution.md#resources)，腳本應該檢查該資源是否已存在，然後再嘗試建立它。 以下是基本的範例。

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

## <a name="retrieve-details-from-activity-log"></a>從活動記錄中取出詳細資料

您可以從自動化帳戶的[活動記錄](automation-runbook-execution.md#activity-logging)中，取得 runbook 詳細資料，例如啟動 runbook 的人員或帳戶。 下列 PowerShell 範例會提供最後一個執行指定 runbook 的使用者。

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

最好的作法是將您的 runbook 撰寫為模組化，並使用可輕鬆重複使用並重新啟動的邏輯。 在 runbook 中追蹤進度可確保 runbook 邏輯會在發生問題時正確執行。 

您可以使用外部來源（例如儲存體帳戶、資料庫或共用檔案）來追蹤 runbook 的進度。 在您的 runbook 中建立邏輯，以先檢查所採取的最後一個動作的狀態。 然後，根據檢查結果，邏輯可以略過或繼續 runbook 中的特定工作。

## <a name="prevent-concurrent-jobs"></a>防止並行作業

如果某些 runbook 同時在多個作業中執行，則會奇怪其行為。 在此情況下，runbook 必須執行邏輯來判斷是否已有執行中的作業。 以下是基本的範例。

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

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>處理時間相依腳本中的暫時性錯誤

您的 runbook 必須健全且能夠處理[錯誤](automation-runbook-execution.md#errors)，包括可能導致其重新開機或失敗的暫時性錯誤。 如果 runbook 失敗，Azure 自動化重試。

如果您的 runbook 通常會在時間條件約束中執行，請讓腳本執行邏輯來檢查執行時間。 這項檢查可確保在特定時間執行的作業（例如啟動、關機或相應放大）。

> [!NOTE]
> Azure 沙箱進程的當地時間會設定為 UTC。 您的 runbook 中的日期和時間計算必須考慮這一點。

## <a name="work-with-multiple-subscriptions"></a>使用多個訂用帳戶

您的 runbook 必須能夠使用[訂用帳戶](automation-runbook-execution.md#subscriptions)。 例如，若要處理多個訂用帳戶，runbook 會使用[AzCoNtextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) Cmdlet。 此 Cmdlet 可確保不會從相同沙箱中執行的另一個 runbook 抓取驗證內容。 Runbook 也會在 Az`AzContext` module Cmdlet 上使用參數，並傳遞適當的內容給它。

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

## <a name="work-with-a-custom-script"></a>使用自訂腳本

> [!NOTE]
> 您通常無法在已安裝 Log Analytics 代理程式的主機上執行自訂腳本和 runbook。 

若要使用自訂腳本：

1. 建立自動化帳戶並取得[參與者角色](automation-role-based-access-control.md)。
2. 將[帳戶連結至 Azure 工作區](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection.md)。
3. 啟用[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)、[更新管理](automation-update-management.md)或其他自動化功能。 
4. 如果在 Linux 機器上，您需要高許可權。 登入以關閉[簽章檢查](automation-linux-hrw-install.md#turn-off-signature-validation)。

## <a name="test-a-runbook"></a>測試 Runbook

當您測試 Runbook 時， [草稿版本](#publish-a-runbook) 會執行，而且它執行的任何動作都會完成。 不會建立任何工作歷程記錄，但[輸出](automation-runbook-output-and-messages.md#output-stream)和[警告和錯誤](automation-runbook-output-and-messages.md#message-streams)資料流程會顯示在 [測試輸出] 窗格中。 只有在[VerbosePreference](automation-runbook-output-and-messages.md#preference-variables)變數設定為`Continue`時，[輸出] 窗格中才會顯示 [詳細資訊][資料流程](automation-runbook-output-and-messages.md#message-streams)的訊息。

即使執行的是草稿版本，Runbook 仍會正常執行，並對環境中的資源執行任何動作。 因此，您只應在非生產資源中測試 Runbook。

測試每個[runbook 類型](automation-runbook-types.md)的程式都相同。 在文字式編輯器和 Azure 入口網站的圖形化編輯器之間進行測試並沒有差異。

1. 在[文字式編輯器](automation-edit-textual-runbook.md)或[圖形化編輯器](automation-graphical-authoring-intro.md)中開啟 runbook 的草稿版本。
1. 按一下 [**測試**] 以開啟 [測試] 頁面。
1. 如果 runbook 有參數，它們就會列在左窗格中，您可以在其中提供要用於測試的值。
1. 如果您想要在混合式 Runbook 背景[工作角色](automation-hybrid-runbook-worker.md)上執行測試，請將 [**執行設定**] 變更為 [混合式背景**工作角色**]，然後選取目標群組的名稱。  否則，請保留預設值 **Azure**，以便在雲端中執行測試。
1. 按一下 [開始]  以開始測試。
1. 您可以使用 [輸出] 窗格下的按鈕，在測試時停止或暫停[PowerShell 工作流程](automation-runbook-types.md#powershell-workflow-runbooks)或[圖形化](automation-runbook-types.md#graphical-runbooks)runbook。 暫停 Runbook 時，它會先完成目前的活動後才暫停。 Runbook 一旦暫停，您可以選擇停止或重新啟動。
1. 在 [輸出] 窗格中檢查 runbook 的輸出。

## <a name="publish-a-runbook"></a>發行 Runbook

當您建立或匯入新的 Runbook 時，您必須發佈才能執行它。 Azure 自動化中的每個 runbook 都有草稿版本和已發佈版本。 只可執行已發行版本，而且只可編輯草稿版本。 已發行版本不會受到草稿版本的任何變更影響。 當草稿版本應該可供使用時，您會將它發佈，以草稿版本覆寫目前已發行的版本。

### <a name="publish-a-runbook-in-the-azure-portal"></a>在 Azure 入口網站中發佈 runbook

1. 在 Azure 入口網站中開啟 Runbook。
2. 按一下 **[編輯]**。
3. 按一下 [**發佈**]，然後按 **[是]** 以回應驗證訊息。

### <a name="publish-a-runbook-using-powershell"></a>使用 PowerShell 發佈 runbook

使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) Cmdlet 來發佈您的 runbook。 

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>在 Azure 入口網站中排程 runbook

當您的 runbook 發佈之後，您可以針對作業進行排程：

1. 在 Azure 入口網站中開啟 Runbook。
2. 選取 **[** **資源**] 下的 [排程]。
3. 選取 [**新增排程**]。
4. 在 [排程 Runbook] 窗格中，選取 [將**排程連結至您的 runbook**]。
5. 選擇 [排程] 窗格中的 [**建立新的排程**]。
6. 在 [新增排程] 窗格中，輸入 [名稱]、[描述] 和其他參數。 
7. 建立排程之後，請將它反白，然後按一下 **[確定]**。 它現在應該會連結至您的 runbook。
8. 尋找您信箱中的電子郵件，以通知您 runbook 的狀態。

## <a name="obtain-job-statuses"></a>取得作業狀態

### <a name="view-statuses-in-the-azure-portal"></a>查看 Azure 入口網站中的狀態

作業中會提供 Azure 自動化中工作處理的詳細[資料。](automation-runbook-execution.md#jobs) 當您準備好查看 runbook 作業時，請使用 Azure 入口網站並存取您的自動化帳戶。 在右側，您可以在 [**作業統計資料]** 中查看所有 runbook 作業的摘要。 

![[作業統計資料] 圖格](./media/manage-runbooks/automation-account-job-status-summary.png)

[摘要] 會針對每個執行的作業顯示作業狀態的計數和圖形表示。

按一下圖格，即會顯示 [作業] 頁面，其中包含所有已執行作業的摘要清單。 此頁面會顯示每個作業的狀態、runbook 名稱、開始時間和完成時間。

![自動化帳戶的 [作業] 頁面](./media/manage-runbooks/automation-account-jobs-status-blade.png)

您可以選取 [**篩選作業**] 來篩選工作清單。 篩選特定的 runbook、作業狀態，或從下拉式清單中選擇，並提供搜尋的時間範圍。

![篩選作業狀態](./media/manage-runbooks/automation-account-jobs-filter.png)

或者，您可以從自動化帳戶中的 [Runbook] 頁面選取該 runbook，然後選取 [**作業**]，來查看特定 runbook 的作業摘要詳細資料。 此動作會顯示 [作業] 頁面。 您可以從這裡按一下作業記錄，以查看其詳細資料和輸出。

![自動化帳戶的 [作業] 頁面](./media/manage-runbooks/automation-runbook-job-summary-blade.png)

### <a name="retrieve-job-statuses-using-powershell"></a>使用 PowerShell 取出作業狀態

使用[AzAutomationJob 指令程式](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0)來抓取為 runbook 建立的作業，以及特定作業的詳細資料。 如果您使用`Start-AzAutomationRunbook`啟動 runbook，它會傳回產生的作業。 使用[AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0)來取出作業輸出。

下列範例會取得範例 runbook 的最後一個作業，並顯示其狀態、針對 runbook 參數提供的值，以及作業輸出。

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

下列範例會抓取特定作業的輸出，並傳回每一筆記錄。 如果其中一個記錄發生[例外](automation-runbook-execution.md#exceptions)狀況，腳本會寫入例外狀況，而不是值。 這種行為很有用，因為例外狀況可以提供輸出期間可能不會正常記錄的其他資訊。

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

* 若要深入瞭解 runbook 處理，請參閱[Azure 自動化中的 runbook 執行](automation-runbook-execution.md)。
* 若要深入瞭解使用文字編輯器編輯 PowerShell 和 PowerShell 工作流程 runbook，請參閱[在 Azure 自動化中編輯文字 runbook](automation-edit-textual-runbook.md)。
* 若要深入瞭解圖形化 runbook 製作，請參閱[Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)。
