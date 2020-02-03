---
title: Azure 自動化中的 Runbook 執行
description: 描述如何處理 Azure 自動化中的 Runbook 的詳細資料。
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: c97e10d2785b7dc1a438c95dca9be94fcef82f94
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714836"
---
# <a name="runbook-execution-in-azure-automation"></a>Azure 自動化中的 Runbook 執行

當您在 Azure 自動化中啟動 Runbook 時即會建立一個工作。 工作是 Runbook 的單一執行個體。 會指派 Azure 自動化背景工作，以執行每項工作。 雖然背景工作是由多個 Azure 帳戶共用，來自不同自動化帳戶的工作會彼此隔離。 您無法控制哪個背景工作要為您的作業要求提供服務。 單一 Runbook 一次可以執行許多工作。 來自相同自動化帳戶的作業執行環境可重複使用。 同時執行的作業越多，越常會分派到相同的沙箱。 在相同沙箱處理序中執行的作業可能會影響彼此，範例之一就是執行 `Disconnect-AzureRMAccount` Cmdlet。 執行此 Cmdlet 會中斷連接共用沙箱處理序中的每個 Runbook 作業。 當您在 Azure 入口網站中檢視 Runbook 清單時，它會列出已針對每個 Runbook 啟動之所有作業的狀態。 您可以檢視每一個 Runbook工作的清單，以追蹤每個 Runbook 的狀態。 作業記錄最多儲存 30 天。 如需不同作業狀態的說明，請參閱[作業狀態](#job-statuses)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

下圖顯示[PowerShell runbook](automation-runbook-types.md#powershell-runbooks)、[圖形化 Runbook](automation-runbook-types.md#graphical-runbooks)和[powershell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks)之 runbook 作業的生命週期。

![工作狀態 -PowerShell 工作流程](./media/automation-runbook-execution/job-statuses.png)

藉由連線至您的 Azure 訂用帳戶，您的作業即可存取您的 Azure 資源。 如果可從公用雲端存取這些資源，他們將只可以存取您的資料中心中的資源。

## <a name="where-to-run-your-runbooks"></a>在何處執行您的 Runbook

Azure 自動化中的 Runbook 可以在 Azure 中的沙箱或[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)上執行。 沙箱是 Azure 中可供多個作業使用的共用環境。 使用相同沙箱的作業均受限於該沙箱的資源限制。 「混合式 Runbook 背景工作角色」可以直接在裝載角色的電腦上執行 runbook，並針對環境中的資源來管理這些本機資源。 Runbook 會儲存並在 Azure 自動化中管理，接著傳遞至一或多個指派的電腦。 大部分的 runbook 都可以輕鬆地在 Azure 沙箱中執行。 有一些特定案例可能會建議您在 Azure 沙箱上選擇混合式 Runbook 來執行 Runbook。 請參閱下表，以取得一些範例案例清單：

|Task|最佳選項|注意|
|---|---|---|
|與 Azure 資源整合|Azure 沙箱|裝載於 Azure 中，驗證會比較簡單。 如果您在 Azure VM 上使用混合式 Runbook 背景工作角色，則可改用 [Azure 資源的受控識別](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)|
|管理 Azure 資源的最佳效能|Azure 沙箱|腳本會在相同的環境中執行，因而降低延遲|
|將營運成本最小化|Azure 沙箱|沒有任何計算額外負荷，不需針對 VM 使用|
|長時間執行指令碼|Hybrid Runbook Worker|Azure 沙箱會有[資源限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|與本機服務互動|Hybrid Runbook Worker|可以直接存取主機電腦|
|需要第三方軟體和可執行檔|Hybrid Runbook Worker|您要管理 OS 且可安裝軟體|
|使用 Runbook 監視檔案或資料夾|Hybrid Runbook Worker|在混合式 Runbook 背景工作角色上使用[監看員工作](automation-watchers-tutorial.md)|
|耗用大量資源的指令碼|Hybrid Runbook Worker| Azure 沙箱會有[資源限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|使用具特定需求的模組| Hybrid Runbook Worker|部份範例如下：</br> **WinSCP**：winscp.exe 上的相依性 </br> **IISAdministration**：需要啟用 IIS|
|安裝需要安裝程式的模組|Hybrid Runbook Worker|沙箱模組必須是可|
|使用需要 4.7.2 以外之 .NET Framework 的 Runbook 或模組|Hybrid Runbook Worker|自動化沙箱具備 .NET Framework 4.7.2，而且沒有任何方法可將它升級|
|需要提高許可權的腳本|Hybrid Runbook Worker|沙箱不允許提高許可權。 若要解決此問題，請使用混合式 Runbook 背景工作角色，而您可以在執行需要提高許可權的命令時，關閉 UAC 並使用 `Invoke-Command`|
|需要存取 WMI 的腳本|Hybrid Runbook Worker|在雲端沙箱中執行的作業[無法存取 WMI](#device-and-application-characteristics)|

## <a name="runbook-behavior"></a>Runbook 行為

Runbook 會根據定義於其內部的邏輯來執行。 如果 Runbook 中斷，該 Runbook 會從頭重新啟動。 需要將 Runbook 撰寫為支援在發生暫時性問題時可重新啟動，才能有此行為。

從在 Azure 沙箱中執行的 Runbook 啟動的 PowerShell 工作可能無法在完整語言模式中執行。 若要深入瞭解 PowerShell 語言模式，請參閱[powershell 語言模式](/powershell/module/microsoft.powershell.core/about/about_language_modes)。 如需如何在 Azure 自動化中與作業互動的其他詳細資料，請參閱[使用 PowerShell 來抓取作業狀態](#retrieving-job-status-using-powershell)

### <a name="creating-resources"></a>建立資源

如果您的指令碼會建立資源，您應該檢查以查看資源是否已經存在，然後嘗試重新建立它。 下列範例示範一個基本範例：

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzureRmResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exists, creating"
    New-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="time-dependent-scripts"></a>時間相依腳本

編寫 Runbook 時應該仔細考量。 如先前所述，您需要將 Runbook 編寫為健全的，而且可以處理可能造成 Runbook 重新啟動或失敗的暫時性錯誤。 如果 runbook 失敗，則會重試。 如果 runbook 通常會在時間條件約束中執行，則檢查執行時間的邏輯應該在 runbook 中執行，以確保啟動、關機或相應放大等作業只會在特定時間執行。

> [!NOTE]
> Azure 沙箱進程的當地時間會設定為 UTC 時間。 您的 runbook 中的日期和時間計算需要考慮這一點。

### <a name="tracking-progress"></a>追蹤進度

最佳做法是本質上將 Runbook 編寫為模組。 這意味著在 Runbook 中建構邏輯，使其可輕鬆地重複使用和重新啟動。 在 runbook 中追蹤進度是確保 runbook 中的邏輯在發生問題時正確執行的好方法。 部分可能用來追蹤 Runbook 進度的方式是使用外部來源，例如儲存體帳戶、資料庫或共用檔案。 藉由追蹤外部的狀態，您可以在 runbook 中建立邏輯，以先檢查 runbook 所採取的最後一個動作的狀態。 然後根據結果，略過或繼續 runbook 中的特定工作。

### <a name="prevent-concurrent-jobs"></a>防止並行作業

某些 Runbook 如果跨多個作業同時執行，可能就會以奇怪的方式運作。 在此情況下，請務必實作邏輯進行檢查，以查看 Runbook 是否已經具有執行中的作業。 下列範例是示範如何執行此行為的基本範例：

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzureRmAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzureRmContext $AzureContext

# If then check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>使用多個訂用帳戶

撰寫處理多個訂用帳戶的 runbook 時，您的 runbook 需要使用[enable-azurermcoNtextautosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave)指令程式，以確保您的驗證內容不會從可能在相同沙箱中執行的另一個 runbook 抓取。 接著，您必須在您的 `AzureRM` Cmdlet 上使用 `-AzureRmContext` 參數，並將您的適當內容傳遞給它。

```powershell
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzureRmContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzureRmAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>處理例外狀況

撰寫腳本時，請務必能夠處理例外狀況和潛在的間歇性失敗。 以下是處理 runbook 的例外狀況或間歇性問題的一些不同方式：

#### <a name="erroractionpreference"></a>$ErrorActionPreference

[$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference)喜好設定變數會決定 PowerShell 如何回應非終止錯誤。 終止錯誤不會受到 `$ErrorActionPreference`影響，它們一律會終止。 藉由使用 `$ErrorActionPreference`，通常非終止的錯誤（例如來自 `Get-ChildItem` Cmdlet 的 `PathNotFound`）將會停止 runbook 完成。 下列範例會顯示如何使用 `$ErrorActionPreference`。 最後 `Write-Output` 行永遠不會執行，因為腳本將會停止。

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>嘗試 Catch Finally

在 PowerShell 腳本中使用[Try Catch](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) ，協助您處理終止錯誤。 藉由使用 Try Catch，您可以攔截特定例外狀況或一般例外狀況。 Catch 語句應該用來追蹤錯誤，或用來嘗試處理錯誤。 下列範例會嘗試下載不存在的檔案。 它會攔截 `System.Net.WebException` 的例外狀況，如果有另一個例外狀況，則會傳回最後一個值。

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>擲回

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw)可以用來產生終止錯誤。 在 runbook 中定義您自己的邏輯時，這會很有用。 如果符合應停止腳本的特定準則，您可以使用 `throw` 來停止腳本。 下列範例會顯示使用 `throw`所需的電腦函數參數。

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>使用可執行檔或呼叫處理序

在 Azure 沙箱中執行的 runbook 不支援呼叫進程（例如 .exe 或子進程）。 這是因為 Azure 沙箱是在容器中執行的共用進程，可能無法存取所有基礎 Api。 針對您需要第三方軟體或呼叫子處理序的案例，建議您在[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)上執行 Runbook。

### <a name="device-and-application-characteristics"></a>裝置和應用程式特性

在 Azure 沙箱中執行的 Runbook 作業沒有任何裝置或應用程式特性的存取權。 最常見的 API 是用來查詢 Windows 上的效能度量，也就是 WMI。 其中一些常見的計量是記憶體和 CPU 使用量。 不過，使用什麼 API 並不重要。 在雲端中執行的工作無法存取以 Web 為基礎的企業管理（WBEM）的 Microsoft 執行，這是以通用訊息模型（CIM）為基礎，這是定義裝置和應用程式特性的業界標準。

## <a name="job-statuses"></a>工作狀態

下表描述工作可能會有不同的狀態。 PowerShell 有兩種錯誤類型：終止和非終止錯誤。 如果發生終止錯誤，則會將 Runbook 狀態設為 [失敗]。 非終止錯誤可讓指令碼在錯誤發生後繼續執行。 非終止錯誤的範例會使用 `Get-ChildItem` Cmdlet 搭配不存在的路徑。 PowerShell 發現路徑不存在，則會擲回錯誤，並繼續下一步資料夾。 此錯誤不會將 Runbook 狀態設為 [失敗] 且可能標示為 [已完成]。 若要強制 Runbook 在非終止錯誤時停止，您可以在 Cmdlet 上使用 `-ErrorAction Stop`。

| 狀態 | 描述 |
|:--- |:--- |
| Completed |工作已成功完成。 |
| 失敗 |針對 [圖形化和 PowerShell 工作流程 Runbook](automation-runbook-types.md)，此 Runbook 無法編譯。 針對 [PowerShell 指令碼 Runbook](automation-runbook-types.md)，此 Runbook 無法啟動，或作業發生例外狀況。 |
| 處理失敗，正在等候資源 |工作失敗，因為其達到 [公平共用](#fair-share) 的三次上限，且每次從相同的檢查點或啟動 Runbook 開始。 |
| 已排入佇列 |工作正在等候取得自動化背景工作中的資源，以便可啟動。 |
| 啟動中 |此作業已指派給背景工作角色，並且系統正在進行啟動。 |
| 繼續中 |工作暫停後，系統正在繼續工作。 |
| 執行中 |工作正在執行。 |
| 執行中，正在等候資源 |工作已卸載，因為已達到 [公平共用](#fair-share) 上限。 作業很快會從其上一個檢查點繼續。 |
| 已停止 |工作完成之前已由使用者停止。 |
| 停止中 |系統正在停止作業。 |
| 暫止 |工作已由使用者、系統或 Runbook 中的命令暫停。 如果 Runbook 沒有檢查點，它會從 Runbook 的開頭開始。 如果它有檢查點，則可重新啟動並從其最後一個檢查點繼續。 只有在發生例外狀況時，系統才會暫止 Runbook。 根據預設，ErrorActionPreference 會設定為 [繼續]，代表作業會在發生錯誤時繼續執行。 如果此喜好設定變數設定為 [停止]，作業會在發生錯誤時暫停。 只適用於 [圖形化和 PowerShell 工作流程 Runbook](automation-runbook-types.md) 。 |
| Suspending |因使用者要求，系統正在嘗試暫停工作。 Runbook 必須達到其下一個檢查點才能暫停。 如果它已通過其最後一個檢查點，則可在暫停之前完成。 只適用於 [圖形化和 PowerShell 工作流程 Runbook](automation-runbook-types.md) 。 |

## <a name="viewing-job-status-from-the-azure-portal"></a>從 Azure 入口網站檢視作業狀態

您可以檢視所有 Runbook 作業的摘要狀態，或在 Azure 入口網站中向下切入至特定 Runbook 作業的詳細資訊。 您也可以設定與 Log Analytics 工作區的整合，以轉送 Runbook 作業狀態和作業串流。 如需與 Azure 監視器記錄整合的詳細資訊，請參閱[從自動化將作業狀態和作業串流轉送到 Azure 監視器記錄](automation-manage-send-joblogs-log-analytics.md)。

### <a name="automation-runbook-jobs-summary"></a>自動化 Runbook 作業摘要

在所選自動化帳戶的右邊，您可以在 [作業統計資料] 圖格下方，看到所有 Runbook 作業的摘要。

![[作業統計資料] 圖格](./media/automation-runbook-execution/automation-account-job-status-summary.png)

這個圖格會針對所有已執行的作業顯示作業狀態的計數和圖形表示。

按一下圖格，即會顯示 [作業] 頁面，其中包含所有已執行作業的摘要清單。 此頁面會顯示狀態、開始時間和完成時間。

![自動化帳戶的 [作業] 頁面](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

您可以篩選作業清單，方法是選取 [篩選作業]，然後篩選特定的 Runbook、作業狀態，或從下拉式清單中，選取要在其中進行搜尋的時間範圍。

![篩選作業狀態](./media/automation-runbook-execution/automation-account-jobs-filter.png)

或者，您可以檢視特定 Runbook 的作業摘要詳細資料，方法是在您的自動化帳戶中，從 [Runbook] 頁面中選取該 Runbook，然後選取 [作業] 圖格。 此動作會顯示 [作業] 頁面，而您可以從此處按一下作業記錄，以檢視其詳細資料和輸出。

![自動化帳戶的 [作業] 頁面](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>工作摘要

您可以檢視所有針對特定 Runbook 建立之作業的清單及其最新狀態。 您可以依工作狀態和上次工作變更的日期範圍來篩選此清單。 若要檢視其詳細資訊和輸出，按一下作業的名稱。 工作的詳細檢視包含提供給該工作的 Runbook 參數值。

您可以使用下列步驟來檢視 Runbook 工作。

1. 在 Azure 入口網站中，選取 [自動化]，然後選取自動化帳戶的名稱。
2. 從中樞選取 [Runbook]，然後在 [Runbook] 頁面上選取清單中的 Runbook。
3. 在已選取 Runbook 的頁面上，按一下 [作業] 圖格。
4. 按一下清單中的其中一個作業，然後可以在 [Runbook 作業詳細資料] 頁面上檢視其詳細資料和輸出。

## <a name="retrieving-job-status-using-powershell"></a>使用 PowerShell 來抓取作業狀態

您可以使用 [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob)，來擷取針對 Runbook 建立的作業以及特定作業的詳細資料。 如果您使用[set-azurermautomationrunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook)以 PowerShell 啟動 runbook，則會傳回產生的作業。 使用 [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) 來取得作業的輸出。

下列範例命令會針對範例 Runbook 擷取最後一個作業並顯示其狀態、提供給 Runbook 參數的值，以及來自作業的輸出。

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

下列範例會擷取特定作業的輸出，並傳回每一筆記錄。 在其中一筆記錄含有例外狀況的情況下，會寫出該例外狀況，而不是值。 此行為非常有用，因為例外狀況可以提供通常可能不會在輸出期間記錄的額外資訊。

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
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

## <a name="get-details-from-activity-log"></a>從活動記錄取得詳細資料

您可以從自動化帳戶的活動記錄中擷取其他詳細資料 (例如啟動 Runbook 的人員或帳戶)。 下列 PowerShell 範例提供最後一位執行上述 Runbook 的使用者：

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzureRmLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzureRmResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzureRmAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hash table
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>公平共用

若要在雲端中的所有 runbook 之間共用資源，Azure 自動化暫時卸載或停止已執行超過三小時的任何工作。 [PowerShell 型 Runbook](automation-runbook-types.md#powershell-runbooks) 和 [Python Runbook](automation-runbook-types.md#python-runbooks) 的作業會停止且不會重新啟動，而作業狀態顯示 [已停止]。

對於長時間執行的工作，建議使用[混合式 Runbook 背景工作角色](automation-hrw-run-runbooks.md#job-behavior)。 混合式 Runbook 背景工作角色並未受限於公平共用，而且未限制 Runbook 執行時間長度。 其他作業[限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)會套用至 Azure 沙箱和混合式 Runbook 背景工作角色。 雖然混合式 Runbook 背景工作角色不會受限於3小時的公平共用限制，但在其上執行的 runbook 應進行開發，以支援從非預期的本機基礎結構問題重新開機行為。

另一個選項是使用子 Runbook 將 Runbook 最佳化。 如果您的 Runbook 會在多個資源上重複執行同一個函式，例如在數個資料庫上重複進行某一個資料庫作業，您可以將該函式移到[子 Runbook](automation-child-runbooks.md) 並使用 [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) Cmdlet 呼叫它。 每一個 Runbook 會在個別的處理程序中平行執行。 此行為可減少完成父代 Runbook 的時間總計。 您可以在 runbook 中使用[get-azurermautomationjob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob)指令程式，以檢查子 runbook 完成後執行的作業時，每個子系的工作狀態。

## <a name="next-steps"></a>後續步驟

* 若要深入了解可在 Azure 自動化中用來啟動 Runbook 的不同方法，請參閱[在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md)
* 如需 PowerShell 的詳細資訊（包括語言參考和學習模組），請參閱[powershell](https://docs.microsoft.com/powershell/scripting/overview)檔。
