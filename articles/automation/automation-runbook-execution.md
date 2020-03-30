---
title: Azure 自動化中的 Runbook 執行
description: 描述如何處理 Azure 自動化中的 Runbook 的詳細資料。
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: c8968eb72b29b004d94e25433da65d3262287147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367137"
---
# <a name="runbook-execution-in-azure-automation"></a>Azure 自動化中的 Runbook 執行

Runbook 基於其中定義的邏輯執行。 如果 Runbook 中斷，該 Runbook 會從頭重新啟動。 此行為要求您編寫支援在發生暫時性問題時重新開機的 Runbook。

在 Azure 自動化中啟動 Runbook 將創建作業。 作業是 Runbook 的單個執行實例。 每個作業都可以通過連接到 Azure 訂閱來訪問 Azure 資源。 僅當可從公共雲訪問這些資源時，作業才能訪問資料中心中的資源。

Azure 自動化分配一個輔助角色在 Runbook 執行期間運行每個作業。 雖然背景工作是由多個 Azure 帳戶共用，來自不同自動化帳戶的工作會彼此隔離。 您無法控制工作請求的服務。

在 Azure 門戶中查看 Runbook 清單時，它會顯示每個 Runbook 已啟動的每個作業的狀態。 Azure 自動化最多存儲作業日誌 30 天。 

下圖顯示了[PowerShell Runbook、](automation-runbook-types.md#powershell-runbooks)[圖形運行簿](automation-runbook-types.md#graphical-runbooks)和[PowerShell 工作流運行簿](automation-runbook-types.md#powershell-workflow-runbooks)的運行簿作業的生命週期。

![工作狀態 -PowerShell 工作流程](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶，可以使用["如何更新 Azure 自動化 中的 Azure PowerShell"模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="where-to-run-your-runbooks"></a>在何處執行您的 Runbook

Azure 自動化中的 Runbook 可以在 Azure 沙箱或[混合 Runbook 輔助角色](automation-hybrid-runbook-worker.md)上運行。 大多數 Runbook 都可以輕鬆地在 Azure 沙箱中運行，這是多個作業可以使用的共用環境。 使用相同沙箱的作業均受限於該沙箱的資源限制。

可以使用混合 Runbook 輔助角色直接在承載角色的電腦上運行 Runbook，並針對環境中的本地資源運行 Runbook。 Azure 自動化存儲和管理 Runbook，然後將它們傳遞到一個或多個分配的電腦。

下表列出了一些 Runbook 執行任務，其中列出了每個任務所列出的建議執行環境。

|Task|最佳選項|注意|
|---|---|---|
|與 Azure 資源整合|Azure 沙箱|託管在 Azure 中，身份驗證更簡單。 如果在 Azure VM 上使用混合 Runbook 工作執行緒，則可以[對 Azure 資源使用託管標識](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)。|
|獲得最佳性能來管理 Azure 資源|Azure 沙箱|腳本在同一環境中運行，延遲較少。|
|將營運成本最小化|Azure 沙箱|沒有計算開銷，也不需要 VM。|
|執行長時間運行的腳本|Hybrid Runbook Worker|Azure 沙箱[對資源有限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。|
|與本機服務交互|Hybrid Runbook Worker|可以直接存取主機。|
|需要協力廠商軟體和可執行檔|Hybrid Runbook Worker|您可以管理作業系統，並可以安裝軟體。|
|使用 Runbook 監視檔案或資料夾|Hybrid Runbook Worker|在混合 Runbook 工作執行緒上使用[觀察程式任務](automation-watchers-tutorial.md)。|
|運行資源密集型腳本|Hybrid Runbook Worker| Azure 沙箱[對資源有限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。|
|使用具有特定要求的模組| Hybrid Runbook Worker|部份範例如下：</br> WinSCP：winscp.exe 上的相依性 </br> IIS 管理 - 依賴啟用 IIS。|
|安裝帶有安裝程式的模組|Hybrid Runbook Worker|沙箱模組必須支援複製。|
|使用需要與 4.7.2 不同的 .NET 框架版本的 Runbook 或模組|Hybrid Runbook Worker|自動化沙箱具有 .NET 框架 4.7.2，無法升級它。|
|運行需要提升的腳本|Hybrid Runbook Worker|沙箱不允許高程。 使用混合 Runbook 輔助角色，您可以在運行需要提升的命令時關閉 UAC 並使用**調用命令**。|
|運行需要訪問 WMI 的腳本|Hybrid Runbook Worker|在雲中的沙箱中運行的作業無法訪問 WMI。 |

## <a name="runbook-behavior"></a>Runbook 行為

### <a name="creating-resources"></a>建立資源

如果 Runbook 創建了資源，則腳本在嘗試創建資源之前應檢查該資源是否已存在。 下面是一個基本示例。

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzureRMVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="supporting-time-dependent-scripts"></a>支援與時間相關的腳本

Runbook 必須健壯，能夠處理可能導致它們重新開機或失敗的瞬態錯誤。 如果 Runbook 失敗，Azure 自動化將重試它。

如果 Runbook 通常在時間限制內運行，請讓腳本實現邏輯來檢查執行時間。 此檢查可確保僅在特定時間運行啟動、關閉或橫向擴展等操作。

> [!NOTE]
> Azure 沙箱進程中的本地時間設置為 UTC。 計算 Runbook 中的日期和時間時必須考慮到這一事實。

### <a name="tracking-progress"></a>追蹤進度

最好將 Runbook 創作為模組化，構建 Runbook 邏輯，以便輕鬆重用和重新開機。 跟蹤 Runbook 中的進度是確保 Runbook 邏輯在出現問題時正確執行的好方法。 可以使用外部源（如存儲帳戶、資料庫或共用檔）跟蹤 Runbook 的進度。 您可以在 Runbook 中創建邏輯，以便首先檢查上次執行的操作的狀態。 然後，根據檢查結果，邏輯可以跳過或繼續 Runbook 中的特定任務。

### <a name="preventing-concurrent-jobs"></a>防止併發作業

如果某些 Runbook 同時跨多個作業運行，則它們運行得很奇怪。 在這種情況下，Runbook 實現邏輯以確定是否存在正在運行的作業非常重要。 下面是一個基本示例。

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

### <a name="working-with-multiple-subscriptions"></a>使用多個訂用帳戶

若要處理多個訂閱，Runbook 必須使用[禁用-AzCoNtextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) Cmdlet，以確保不會從在同一沙箱中運行的另一個 Runbook 檢索身份驗證上下文。 Runbook 還使用`AzContext`Az 模組 Cmdlet 上的參數，並將其傳遞適當的上下文。

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

### <a name="handling-exceptions"></a>處理例外狀況

本節介紹處理 Runbook 中的異常或間歇性問題的一些方法。

#### <a name="erroractionpreference"></a>ErrorActionPreference

[ErrorAction 首選項](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference)變數確定 PowerShell 如何回應非終止錯誤。 終止錯誤始終終止，不受*錯誤操作首選項*的影響。

當 Runbook`ErrorActionPreference`使用 時，通常非終止錯誤（如 Cmdlet`Get-ChildItem`中的**PathNotFound）** 會阻止 Runbook 完成。 下列範例示範 `ErrorActionPreference` 的用法。 當腳本`Write-Output`停止時，最終命令永遠不會執行。

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>最後嘗試捕捉

[最後嘗試捕獲](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally)在 PowerShell 腳本中使用來處理終止錯誤。 腳本可以使用此機制捕獲特定的異常或常規異常。 該`catch`語句應用於跟蹤或嘗試處理錯誤。 下面的示例嘗試下載不存在的檔。 它捕獲異常`System.Net.WebException`並返回任何其他異常的最後一個值。

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

#### <a name="throw"></a>Throw

[THROW](/powershell/module/microsoft.powershell.core/about/about_throw)可用於生成終止錯誤。 在 Runbook 中定義自己的邏輯時，此機制非常有用。 如果腳本滿足應停止它的條件，它可以使用 語句`throw`停止。 下面的示例使用此語句來顯示所需的函數參數。

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>使用可執行檔或呼叫處理序

在 Azure 沙箱中運行的 Runbook 不支援調用進程，例如可執行檔 **（.exe**檔）或子進程。  原因是 Azure 沙箱是在可能無法訪問所有基礎 API 的容器中運行的共用進程。 對於需要協力廠商軟體或調用子進程的方案，建議在混合 Runbook 工作執行緒上執行[Runbook。](automation-hybrid-runbook-worker.md)

### <a name="accessing-device-and-application-characteristics"></a>訪問設備和應用程式特徵

在 Azure 沙箱中運行的 Runbook 作業無法訪問任何設備或應用程式特徵。 用於在 Windows 上查詢性能指標的最常見 API 是 WMI，其中一些常見指標是記憶體和 CPU 使用率。 但是，使用什麼 API 並不重要，因為在雲中運行的作業無法訪問基於 Web 的企業管理 （WBEM） 的 Microsoft 實現。 該平臺基於通用訊息模型 （CIM）構建，為定義設備和應用程式特徵提供了行業標準。

## <a name="handling-errors"></a>處理錯誤

您的 Runbook 必須能夠處理錯誤。 PowerShell 有兩種類型的錯誤，即終止錯誤和非終止錯誤。 終止錯誤發生時將停止運行簿執行。 運行簿停止作業狀態為"失敗"。

非終止錯誤允許腳本在發生腳本後繼續。 非終止錯誤的一個示例是 Runbook 使用具有不存在的路徑的`Get-ChildItem`Cmdlet 時發生的錯誤。 PowerShell 發現路徑不存在，則會擲回錯誤，並繼續下一步資料夾。 在這種情況下，錯誤不會將 Runbook 作業狀態設置為"失敗"，並且該作業甚至可能完成。 若要強制 Runbook 在非終止錯誤時停止，您可以在 Cmdlet 上使用 `-ErrorAction Stop`。

## <a name="handling-jobs"></a>處理作業

您可以為同一自動化帳戶中的作業重用執行環境。 單一 Runbook 一次可以執行許多工作。 同時執行的作業越多，越常會分派到相同的沙箱。

在同一沙箱進程中運行的作業可能會相互影響。 一個例子是運行`Disconnect-AzAccount`Cmdlet。 執行此 Cmdlet 會斷開共用沙箱進程中的每個 Runbook 作業。

PowerShell 作業從在 Azure 沙箱中運行的 Runbook 開始，可能無法在完整語言模式下運行。 要瞭解有關 PowerShell 語言模式的更多，請參閱[PowerShell 語言模式](/powershell/module/microsoft.powershell.core/about/about_language_modes)。 有關在 Azure 自動化中與作業交互的其他詳細資訊，請參閱[使用 PowerShell 檢索作業狀態](#retrieving-job-status-using-powershell)。

### <a name="job-statuses"></a>工作狀態

下表描述了作業可能的狀態。

| 狀態 | 描述 |
|:--- |:--- |
| Completed |工作已成功完成。 |
| 失敗 |圖形或 PowerShell 工作流運行簿編譯失敗。 PowerShell 腳本運行簿無法啟動或作業有異常。 請參閱[Azure 自動化運行簿類型](automation-runbook-types.md)。|
| 處理失敗，正在等候資源 |工作失敗，因為其達到 [公平共用](#fair-share) 的三次上限，且每次從相同的檢查點或啟動 Runbook 開始。 |
| 已排入佇列 |作業正在等待自動化輔助角色上的資源可用，以便可以啟動它。 |
| 啟動中 |此作業已指派給背景工作角色，並且系統正在進行啟動。 |
| 繼續中 |工作暫停後，系統正在繼續工作。 |
| 執行中 |工作正在執行。 |
| 執行中，正在等候資源 |工作已卸載，因為已達到 公平共用 上限。 工作會很快地從其上一個檢查點繼續。 |
| 已停止 |工作完成之前已由使用者停止。 |
| 停止中 |系統正在停止作業。 |
| 暫止 |僅適用于[圖形和 PowerShell 工作流運行簿](automation-runbook-types.md)。 工作已由使用者、系統或 Runbook 中的命令暫停。 如果 Runbook 沒有檢查點，則從一開始就啟動。 如果它有檢查點，則可重新啟動並從其最後一個檢查點繼續。 僅當發生異常時，系統才會掛起 Runbook。 預設情況下，`ErrorActionPreference`變數設置為"繼續"，表示作業在錯誤時繼續運行。 如果首選項變數設置為"停止"，則作業將因錯誤而掛起。  |
| Suspending |僅適用于[圖形和 PowerShell 工作流運行簿](automation-runbook-types.md)。 因使用者要求，系統正在嘗試暫停工作。 Runbook 必須達到其下一個檢查點才能暫停。 如果已通過其最後一個檢查點，則在掛起之前完成。 |

### <a name="viewing-job-status-from-the-azure-portal"></a>從 Azure 入口網站檢視作業狀態

您可以檢視所有 Runbook 作業的摘要狀態，或在 Azure 入口網站中向下切入至特定 Runbook 作業的詳細資訊。 您也可以設定與 Log Analytics 工作區的整合，以轉送 Runbook 作業狀態和作業串流。 有關與 Azure 監視器日誌集成的詳細資訊，請參閱[將作業狀態和作業流從自動化轉發到 Azure 監視器日誌](automation-manage-send-joblogs-log-analytics.md)。

在所選的自動化帳戶的右側，您可以在 **"作業統計資訊"** 磁貼下看到所有 Runbook 作業的摘要。

![[作業統計資料] 圖格](./media/automation-runbook-execution/automation-account-job-status-summary.png)

此磁貼顯示所執行每個作業的作業狀態的計數和圖形表示形式。

按一下圖格，即會顯示 [作業] 頁面，其中包含所有已執行作業的摘要清單。 此頁顯示每個作業的狀態、運行簿名稱、開始時間和完成時間。

![自動化帳戶的 [作業] 頁面](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

您可以通過選擇 **"篩選作業"來**篩選作業清單。 篩選特定 Runbook、作業狀態或下拉清單中的選擇，並提供搜索的時間範圍。

![篩選作業狀態](./media/automation-runbook-execution/automation-account-jobs-filter.png)

或者，您可以通過從自動化帳戶中的 Runbook 頁中選擇該 Runbook，然後選擇"作業"磁貼來查看特定 Runbook**的作業**摘要詳細資訊。 此操作顯示"作業"頁。 在此處，您可以按一下作業記錄以查看其詳細資訊和輸出。

![自動化帳戶的 [作業] 頁面](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>查看作業摘要

通過上述作業摘要，您可以查看為特定 Runbook 創建的所有作業的清單及其最新狀態。 要查看作業的詳細資訊和輸出，請按一下清單中的名稱。 作業的詳細視圖包括已提供給該作業的 Runbook 參數的值。

您可以使用下列步驟來檢視 Runbook 工作。

1. 在 Azure 入口網站中，選取 [自動化]****，然後選取自動化帳戶的名稱。
2. 從集線器中，在 **"過程自動化**"下選擇**Runbook。**
3. 在 Runbook 頁上，從清單中選擇 Runbook。
3. 在已選取 Runbook 的頁面上，按一下 [作業]**** 圖格。
4. 按一下清單中的作業之一，並在 Runbook 作業詳細資訊頁上查看其詳細資訊和輸出。

### <a name="retrieving-job-status-using-powershell"></a>使用 PowerShell 檢索作業狀態

使用`Get-AzAutomationJob`Cmdlet 檢索為 Runbook 創建的作業以及特定作業的詳細資訊。 如果使用 使用 啟動使用 PowerShell`Start-AzAutomationRunbook`的運行簿，它將返回生成的作業。 使用[獲取自動化作業輸出](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0)檢索作業輸出。

下面的示例獲取示例 Runbook 的最後一個作業並顯示其狀態、為 Runbook 參數提供的值和作業輸出。

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

下面的示例檢索特定作業的輸出並返回每個記錄。 如果其中一個記錄存在異常，則腳本將寫入異常而不是值。 此行為很有用，因為異常可以提供在輸出期間可能無法正常記錄的其他資訊。

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

## <a name="getting-details-from-the-activity-log"></a>從活動日誌獲取詳細資訊

可以從自動化帳戶的活動日誌中檢索 Runbook 詳細資訊，例如啟動 Runbook 的人員或帳戶。 以下 PowerShell 示例提供運行指定 Runbook 的最後一個使用者。

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

## <a name="sharing-resources-among-runbooks"></a><a name="fair-share"></a>在 Runbook 之間共用資源

要在雲中的所有 Runbook 之間共用資源，Azure 自動化會臨時卸載或停止運行超過三小時的任何作業。 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)和[Python Runbook](automation-runbook-types.md#python-runbooks)的作業將停止，並且不會重新開機，並且作業狀態將變為"已停止"。

對於長時間運行的任務，建議使用混合 Runbook 輔助角色。 混合式 Runbook 背景工作角色並未受限於公平共用，而且未限制 Runbook 執行時間長度。 其他作業[限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)會套用至 Azure 沙箱和混合式 Runbook 背景工作角色。 雖然混合 Runbook 活頁簿不受 3 小時公平份額限制的限制，但您應該開發 Runbook 以運行支援從意外本地基礎結構問題重新開機的工作人員。

另一個選項是使用子 Runbook 優化 Runbook。 例如，Runbook 可能會逐一查看多個資源上的同一函數，例如多個資料庫上的資料庫操作。 您可以將此函數移動到[子 Runbook，](automation-child-runbooks.md)並讓 Runbook 使用`Start-AzAutomationRunbook`調用 它。 子 Runbook 在單獨的進程中並存執行。

使用子 Runbook 可減少父運行簿完成的總時間量。 如果子 Runbook`Get-AzAutomationJob`在子運行簿完成後仍有要執行的操作，則 Runbook 可以使用 Cmdlet 檢查子 Runbook 的作業狀態。

## <a name="next-steps"></a>後續步驟

* 要瞭解有關可用於在 Azure 自動化中啟動 Runbook 的方法，請參閱[在 Azure 自動化 中啟動 Runbook。](automation-starting-a-runbook.md)
* 有關 PowerShell 的更多資訊（包括語言參考和學習模組），請參閱[PowerShell 文檔](https://docs.microsoft.com/powershell/scripting/overview)。
