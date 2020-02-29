---
title: Azure 自動化中的 Runbook 執行
description: 描述如何處理 Azure 自動化中的 Runbook 的詳細資料。
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: 4070b004ee791a433b5aeb9e3e0cdd9662fb0429
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191141"
---
# <a name="runbook-execution-in-azure-automation"></a>Azure 自動化中的 Runbook 執行

Runbook 會根據其內定義的邏輯來執行。 如果 Runbook 中斷，該 Runbook 會從頭重新啟動。 若發生暫時性問題，您需要撰寫支援重新開機的 runbook。

在 Azure 自動化中啟動 runbook 會建立作業。 作業是 runbook 的單一執行實例。 每個作業都可透過連線到您的 Azure 訂用帳戶，來存取 Azure 資源。 如果這些資源可從公用雲端存取，則作業只能存取您資料中心內的資源。

Azure 自動化指派背景工作角色，以在 runbook 執行期間執行每個作業。 雖然背景工作是由多個 Azure 帳戶共用，來自不同自動化帳戶的工作會彼此隔離。 您無法控制哪些背景工作角色服務您的作業要求。

當您查看 Azure 入口網站中的 runbook 清單時，它會顯示每個 runbook 已啟動之每個工作的狀態。 Azure 自動化儲存最多30天的作業記錄。 

下圖顯示[powershell runbook](automation-runbook-types.md#powershell-runbooks)、[圖形化 Runbook](automation-runbook-types.md#graphical-runbooks)和[powershell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks)之 runbook 作業的生命週期。

![工作狀態 -PowerShell 工作流程](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需混合式 Runbook 背景工作角色上的 Az module 安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="where-to-run-your-runbooks"></a>在何處執行您的 Runbook

Azure 自動化中的 runbook 可以在 Azure 沙箱或[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)上執行。 大部分的 runbook 可以輕鬆地在 Azure 沙箱中執行，這是多個工作可以使用的共用環境。 使用相同沙箱的作業均受限於該沙箱的資源限制。

您可以使用混合式 Runbook 背景工作角色，直接在裝載角色的電腦上，以及針對環境中的本機資源執行 runbook。 Azure 自動化會儲存並管理 runbook，然後將其傳遞至一或多個指派的電腦。

下表列出一些 runbook 執行工作，其中列出每個的建議執行環境。

|工作|最佳選項|注意事項|
|---|---|---|
|與 Azure 資源整合|Azure 沙箱|在 Azure 中託管，驗證會比較簡單。 如果您在 Azure VM 上使用混合式 Runbook 背景工作角色，您可以使用[azure 資源的受控](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)識別。|
|取得最佳效能來管理 Azure 資源|Azure 沙箱|腳本會在相同的環境中執行，但延遲較少。|
|將營運成本最小化|Azure 沙箱|沒有任何計算額外負荷，也不需要 VM。|
|執行長時間執行的腳本|Hybrid Runbook Worker|Azure 沙箱具有[資源的限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。|
|與本機服務互動|Hybrid Runbook Worker|可以直接存取主機電腦。|
|需要協力廠商軟體和可執行檔|Hybrid Runbook Worker|您可以管理作業系統並安裝軟體。|
|使用 Runbook 監視檔案或資料夾|Hybrid Runbook Worker|在混合式 Runbook 背景工作角色上使用監看[員工作。](automation-watchers-tutorial.md)|
|執行耗用大量資源的腳本|Hybrid Runbook Worker| Azure 沙箱具有[資源的限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。|
|使用具有特定需求的模組| Hybrid Runbook Worker|部份範例如下：</br> WinSCP-WinSCP 上的相依性 </br> IISAdministration-啟用 IIS 的相依性。|
|使用安裝程式安裝模組|Hybrid Runbook Worker|沙箱的模組必須支援複製。|
|使用需要不同于4.7.2 之 .NET Framework 版本的 runbook 或模組|Hybrid Runbook Worker|自動化沙箱有 .NET Framework 4.7.2，而且沒有任何方法可以升級。|
|執行需要提高許可權的腳本|Hybrid Runbook Worker|沙箱不允許提高許可權。 使用混合式 Runbook 背景工作角色時，您可以關閉 UAC，並在執行需要提高許可權的命令時使用**Invoke 命令**。|
|執行需要存取 WMI 的腳本|Hybrid Runbook Worker|在雲端沙箱中執行的作業無法存取 WMI。 |

## <a name="runbook-behavior"></a>Runbook 行為

### <a name="creating-resources"></a>建立資源

如果您的 runbook 會建立資源，腳本應該檢查該資源是否已存在，然後再嘗試建立它。 以下是基本的範例。

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

### <a name="supporting-time-dependent-scripts"></a>支援時間相依的腳本

您的 runbook 必須是健全且能夠處理暫時性錯誤，使其無法重新開機或失敗。 如果 runbook 失敗，Azure 自動化重試。

如果您的 runbook 通常會在時間條件約束中執行，請讓腳本執行邏輯來檢查執行時間。 這項檢查可確保在特定時間執行的作業（例如啟動、關機或相應放大）。

> [!NOTE]
> Azure 沙箱進程的當地時間會設定為 UTC。 您的 runbook 中的日期和時間計算必須考慮這一點。

### <a name="tracking-progress"></a>追蹤進度

最佳做法是將您的 runbook 撰寫為模組化，並將 runbook 邏輯組成結構，讓它可以輕鬆地重複使用並重新啟動。 在 runbook 中追蹤進度是確保 runbook 邏輯在發生問題時正確執行的好方法。 您可以使用外部來源（例如儲存體帳戶、資料庫或共用檔案）來追蹤 runbook 的進度。 您可以在 runbook 中建立邏輯，先檢查所採取的最後一個動作的狀態。 然後，根據檢查的結果，邏輯可以略過或繼續 runbook 中的特定工作。

### <a name="preventing-concurrent-jobs"></a>防止並行作業

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

### <a name="working-with-multiple-subscriptions"></a>使用多個訂用帳戶

若要處理多個訂用帳戶，您的 runbook 必須使用[AzCoNtextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) Cmdlet，以確保不會從相同沙箱中執行的另一個 runbook 抓取驗證內容。 Runbook 也會在 Az module Cmdlet 上使用*set-azcoNtext*參數，並傳遞適當的內容給它。

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

本節說明在您的 runbook 中處理例外狀況或間歇性問題的一些方式。

#### <a name="erroractionpreference"></a>$ErrorActionPreference

[$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference)變數會決定 PowerShell 如何回應非終止錯誤。 終止錯誤一律會終止，而且不會受到 *$ErrorActionPreference*的影響。

當 runbook 使用 *$ErrorActionPreference*時，通常不會終止的錯誤（例如從**get-childitem** Cmdlet **PathNotFound** ）會阻止 runbook 完成。 下列範例示範如何使用 *$ErrorActionPreference*。 當腳本停止時，最後的**寫入輸出**命令永遠不會執行。

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>嘗試 Catch Finally

[Try Catch 最後](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally)會在 PowerShell 腳本中用來處理終止錯誤。 腳本可以使用此機制來攔截特定例外狀況或一般例外狀況。 **Catch**語句應該用來追蹤或嘗試處理錯誤。 下列範例會嘗試下載不存在的檔案。 它會攔截 WebException 例外狀況，並傳回任何其他例外狀況的最後一個值。

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

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw)可以用來產生終止錯誤。 在 runbook 中定義您自己的邏輯時，這項機制會很有用。 如果腳本符合應停止它的準則，它可以使用**throw**語句來停止。 下列範例會使用這個語句來顯示必要的函式參數。

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>使用可執行檔或呼叫處理序

在 Azure 沙箱中執行的 runbook 不支援呼叫進程，例如可執行檔（ **.exe**檔）或子流程。  這是因為 Azure 沙箱是在容器中執行的共用進程，可能無法存取所有基礎 Api。 對於需要協力廠商軟體或呼叫子流程的案例，建議您在[混合式 runbook 背景工作角色](automation-hybrid-runbook-worker.md)上執行 runbook。

### <a name="accessing-device-and-application-characteristics"></a>存取裝置和應用程式特性

在 Azure 沙箱中執行的 Runbook 作業無法存取任何裝置或應用程式特性。 最常用來查詢 Windows 效能計量的 API 是 WMI，其中一些常見的計量是記憶體和 CPU 使用量。 不過，使用什麼 API 並不重要，因為在雲端中執行的工作無法存取 Microsoft 的 Web 架構企業管理（WBEM）。 此平臺是以通用訊息模型（CIM）為基礎，提供定義裝置和應用程式特性的業界標準。

## <a name="handling-errors"></a>處理錯誤

您的 runbook 必須能夠處理錯誤。 PowerShell 有兩種類型的錯誤，終止和非終止。 終止錯誤發生時，會停止 runbook 執行。 Runbook 停止，作業狀態為 [**失敗**]。

非終止錯誤可讓腳本在執行後仍繼續。 非終止錯誤的範例之一，就是當 runbook 使用**get-childitem** Cmdlet 的路徑不存在時，就會發生這種錯誤。 PowerShell 發現路徑不存在，則會擲回錯誤，並繼續下一步資料夾。 此案例中的錯誤不會將 runbook 作業狀態狀態設定為 [**失敗**]，而且作業甚至可能會完成。 若要強制 Runbook 在非終止錯誤時停止，您可以在 Cmdlet 上使用 `-ErrorAction Stop`。

## <a name="handling-jobs"></a>處理作業

您可以針對相同自動化帳戶中的作業重複使用執行環境。 單一 Runbook 一次可以執行許多工作。 同時執行的作業越多，越常會分派到相同的沙箱。

在相同沙箱進程中執行的作業可能會互相影響。 其中一個範例是執行**Disconnect-disconnect-azaccount** Cmdlet。 執行此 Cmdlet 會中斷共用沙箱進程中的每個 runbook 工作。

從在 Azure 沙箱中執行的 runbook 啟動的 PowerShell 工作可能無法在完整語言模式中執行。 若要深入瞭解 PowerShell 語言模式，請參閱[powershell 語言模式](/powershell/module/microsoft.powershell.core/about/about_language_modes)。 如需與 Azure 自動化中的作業互動的詳細資訊，請參閱[使用 PowerShell 來抓取作業狀態](#retrieving-job-status-using-powershell)。

### <a name="job-statuses"></a>工作狀態

下表描述作業可能的狀態。

| 狀態 | 描述 |
|:--- |:--- |
| 已完成 |工作已成功完成。 |
| 失敗 |無法編譯圖形化或 PowerShell 工作流程 runbook。 PowerShell 腳本 runbook 無法啟動，或作業發生例外狀況。 請參閱[Azure 自動化的 runbook 類型](automation-runbook-types.md)。|
| 處理失敗，正在等候資源 |工作失敗，因為其達到 [公平共用](#fair-share) 的三次上限，且每次從相同的檢查點或啟動 Runbook 開始。 |
| 已排入佇列 |作業正在等候自動化背景工作角色上的資源變成可用，以便可以啟動它。 |
| 啟動中 |此作業已指派給背景工作角色，並且系統正在進行啟動。 |
| 繼續中 |工作暫停後，系統正在繼續工作。 |
| 正在執行 |工作正在執行。 |
| 執行中，正在等候資源 |作業已卸載，因為已達到公平共用限制。 工作會很快地從其上一個檢查點繼續。 |
| 已停止 |工作完成之前已由使用者停止。 |
| Stopping |系統正在停止作業。 |
| Suspended |僅適用于[圖形化和 PowerShell 工作流程 runbook](automation-runbook-types.md) 。 工作已由使用者、系統或 Runbook 中的命令暫停。 如果 runbook 沒有檢查點，則會從頭開始。 如果它有檢查點，則可重新啟動並從其最後一個檢查點繼續。 只有在發生例外狀況時，系統才會暫停 runbook。 根據預設， *ErrorActionPreference*變數會設定為 Continue，表示作業會在發生錯誤時持續**執行**。 如果喜好設定變數設為 [**停止**]，則作業會在發生錯誤時暫停。  |
| Suspending |僅適用于[圖形化和 PowerShell 工作流程 runbook](automation-runbook-types.md) 。 因使用者要求，系統正在嘗試暫停工作。 Runbook 必須達到其下一個檢查點才能暫停。 如果它已經通過其最後一個檢查點，它就會在暫停之前完成。 |

### <a name="viewing-job-status-from-the-azure-portal"></a>從 Azure 入口網站檢視作業狀態

您可以檢視所有 Runbook 作業的摘要狀態，或在 Azure 入口網站中向下切入至特定 Runbook 作業的詳細資訊。 您也可以設定與 Log Analytics 工作區的整合，以轉送 Runbook 作業狀態和作業串流。 如需與 Azure 監視器記錄整合的詳細資訊，請參閱[從自動化將作業狀態和作業串流轉送到 Azure 監視器記錄](automation-manage-send-joblogs-log-analytics.md)。

在所選自動化帳戶的右邊，您可以在 [**作業統計資料]** 圖格底下看到所有 runbook 作業的摘要。

![[作業統計資料] 圖格](./media/automation-runbook-execution/automation-account-job-status-summary.png)

此磚會針對每個執行的作業顯示作業狀態的計數和圖形表示。

按一下圖格，即會顯示 [作業] 頁面，其中包含所有已執行作業的摘要清單。 此頁面會顯示每個作業的狀態、runbook 名稱、開始時間和完成時間。

![自動化帳戶的 [作業] 頁面](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

您可以選取 [**篩選作業**] 來篩選工作清單。 篩選特定的 runbook、作業狀態，或從下拉式清單中選擇，並提供搜尋的時間範圍。

![篩選作業狀態](./media/automation-runbook-execution/automation-account-jobs-filter.png)

或者，您可以從自動化帳戶中的 [ **runbook** ] 頁面選取該 runbook，然後選取 [**作業**] 圖格，以查看特定 runbook 的作業摘要詳細資料。 此動作會顯示 [**作業**] 頁面。 您可以從這裡按一下作業記錄，以查看其詳細資料和輸出。

![自動化帳戶的 [作業] 頁面](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>查看作業摘要

以上所述的作業摘要可讓您查看已針對特定 runbook 建立的所有作業清單，以及其最新狀態。 若要查看作業的詳細資訊和輸出，請在清單中按一下其名稱。 作業的詳細觀點包括已提供給該工作的 runbook 參數值。

您可以使用下列步驟來檢視 Runbook 工作。

1. 在 Azure 入口網站中，選取 [自動化]，然後選取自動化帳戶的名稱。
2. 從中樞選取 [**進程自動化**] 底下的 [ **runbook** ]。
3. 在 [ **runbook** ] 頁面上，從清單中選取 runbook。
3. 在已選取 Runbook 的頁面上，按一下 [作業] 圖格。
4. 按一下清單中的其中一個工作，並在 [runbook 作業詳細資料] 頁面上查看其詳細資料和輸出。

### <a name="retrieving-job-status-using-powershell"></a>使用 PowerShell 來抓取作業狀態

使用**AzAutomationJob 指令程式**來抓取為 runbook 建立的作業，以及特定作業的詳細資料。 如果您使用**AzAutomationRunbook**以 PowerShell 啟動 runbook，它會傳回產生的作業。 使用[AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0)來取出作業輸出。

下列範例會取得範例 runbook 的最後一個作業，並顯示其狀態、針對 runbook 參數提供的值，以及作業輸出。

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

下列範例會抓取特定作業的輸出，並傳回每一筆記錄。 如果其中一個記錄發生例外狀況，腳本會寫入例外狀況，而不是值。 這種行為很有用，因為例外狀況可以提供輸出期間可能不會正常記錄的其他資訊。

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

## <a name="getting-details-from-the-activity-log"></a>從活動記錄中取得詳細資料

您可以從自動化帳戶的活動記錄中，抓取 runbook 詳細資料（例如啟動 runbook 的人員或帳戶）。 下列 PowerShell 範例會提供最後一個執行指定 runbook 的使用者。

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

## <a name="fair-share"></a>在 runbook 之間共用資源

若要在雲端中的所有 runbook 之間共用資源，Azure 自動化暫時卸載或停止已執行超過三小時的任何工作。 [PowerShell runbook](automation-runbook-types.md#powershell-runbooks)和[Python runbook](automation-runbook-types.md#python-runbooks)的作業會停止且不會重新開機，且作業狀態會變成 [**已停止**]。

針對長時間執行的工作，建議使用混合式 Runbook 背景工作角色。 混合式 Runbook 背景工作角色並未受限於公平共用，而且未限制 Runbook 執行時間長度。 其他作業[限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)會套用至 Azure 沙箱和混合式 Runbook 背景工作角色。 雖然混合式 Runbook 背景工作角色不受限於3小時的公平共用限制，但您仍應開發 runbook，以便在支援從非預期的本機基礎結構問題重新開機的背景工作上執行。

另一個選項是使用子 runbook 將 runbook 優化。 例如，您的 runbook 可能會在數個資源上迴圈執行相同的函式，例如數個資料庫上的資料庫作業。 您可以將此函式移至[子 runbook](automation-child-runbooks.md) ，並讓您的 Runbook 使用**AzAutomationRunbook**呼叫它。 子 runbook 會以平行方式在個別的進程中執行。

使用子 runbook 可減少完成父 runbook 所需的總時間。 您的 runbook 可以使用**AzAutomationJob**指令程式，檢查子 runbook 的作業狀態（如果它仍然具有在子系完成後執行的操作）。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解可在 Azure 自動化中用來啟動 runbook 的方法，請參閱[在 Azure 自動化中啟動 runbook](automation-starting-a-runbook.md)。
* 如需 PowerShell 的詳細資訊（包括語言參考和學習模組），請參閱[powershell](https://docs.microsoft.com/powershell/scripting/overview)檔。
