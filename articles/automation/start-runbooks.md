---
title: 在 Azure 自動化中啟動 Runbook
description: 本文說明如何啟動 Azure 自動化中的 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: b5c5166785ad8c82c114fb7193cd49716536b408
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896591"
---
# <a name="start-a-runbook-in-azure-automation"></a>在 Azure 自動化中啟動 Runbook

下表可協助您判斷在 Azure 自動化中啟動 Runbook 的方法，最適合您的特定案例。 這篇文章包含有關使用 Azure 入口網站和 Windows PowerShell 啟動 Runbook 的詳細資料。 其他方法的詳細資訊在其他文件中提供，您可以從下列連結來存取。

| **方法** | **特性** |
| --- | --- |
| [Azure 入口網站](#start-a-runbook-with-the-azure-portal) |<li>互動式使用者介面的最簡單方法。<br> <li>提供簡單參數值的表單。<br> <li>輕鬆追蹤工作狀態。<br> <li>使用 Azure 登入資訊驗證存取。 |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>使用 Windows PowerShell Cmdlet 從命令列呼叫。<br> <li>可以包含在具有多個步驟的自動化功能中。<br> <li>使用憑證或 OAuth 使用者主體/服務主體來驗證要求。<br> <li>提供簡單和複雜的參數值。<br> <li>追蹤工作狀態。<br> <li>支援 PowerShell Cmdlet 所需的用戶端。 |
| [Azure 自動化 API](/rest/api/automation/) |<li>最有彈性的方法，但也最複雜。<br> <li>從任何可提出 HTTP 要求的自訂程式碼呼叫。<br> <li>使用憑證或 OAuth 使用者主體/服務主體來驗證要求。<br> <li>提供簡單和複雜的參數值。 如果您使用 API 呼叫 Python Runbook，則必須將 JSON 承載序列化。<br> <li>追蹤工作狀態。 |
| [Webhook](automation-webhooks.md) |<li>從單一 HTTP 要求啟動 Runbook。<br> <li>在 URL 中使用安全性權杖進行驗證。<br> <li>用戶端無法覆寫在建立 Webhook 時指定的參數值。 Runbook 可以定義填入了 HTTP 要求詳細資料的單一參數。<br> <li>無法透過 Webhook URL 追蹤工作狀態。 |
| [回應 Azure 警示](../azure-monitor/platform/alerts-overview.md) |<li>啟動 Runbook 以回應 Azure 警示。<br> <li>設定 Runbook 的 Webhook 以及警示的連結。<br> <li>在 URL 中使用安全性權杖進行驗證。 |
| [[排程]](./shared-resources/schedules.md) |<li>每小時、每天、每週或每月排程，自動啟動 Runbook。<br> <li>透過 Azure 入口網站、PowerShell Cmdlet 或 Azure API 操縱排程。<br> <li>提供參數值來搭配排程使用。 |
| [另一個 Runbook](automation-child-runbooks.md) |<li>在另一個 Runbook 中使用 Runbook 作為活動。<br> <li>對多個 Runbook 使用的功能很有用。<br> <li>提供參數值給子 Runbook，並在父 Runbook 中使用輸出。 |

下圖說明 Runbook 的生命週期的詳細逐步程序。 它包含了 Runbook 在 Azure 自動化中啟動的不同方式、Hybrid Runbook Worker 執行 Azure 自動化 Runbook 所需的元件，以及不同元件之間的互動。 若要了解如何在您的資料中心執行自動化 Runbook，請參閱 [混合式 Runbook 背景工作](automation-hybrid-runbook-worker.md)

![Runbook 架構](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="work-with-runbook-parameters"></a>使用 Runbook 參數

從 Azure 入口網站或 Windows PowerShell 啟動 Runbook 時，指示會透過 Azure 自動化 Web 服務傳送。 此服務不支援具有複雜資料類型的參數。 如 [Azure 自動化中的子 Runbook](automation-child-runbooks.md)中所述，如果您需要提供複雜參數的值，您必須從另一個 Runbook 呼叫它內嵌。

Azure 自動化 Web 服務會為特定資料型別的參數提供特殊功能，如下列各節所述：

### <a name="named-values"></a>具名值

如果參數是資料類型 [object]，則您可以使用下列 JSON 格式，對它傳送具名值清單：{Name1:'Value1', Name2:'Value2', Name3:'Value3'} 。 這些值必須是簡單型別。 Runbook 會接收參數作為 [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject)，其具有每個具名值的對應屬性。

請考慮可接受稱為 user 的參數的下列測試 Runbook。

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

下列文字可用於 user 參數。

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

這會導致下列輸出：

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>陣列

如果參數是陣列，例如 [array] 或 [string[]]，則您可以使用下列 JSON 格式對其傳送值清單：[Value1, Value2, Value3]。 這些值必須是簡單型別。

請考慮可接受稱為 *user* 的參數的下列測試 Runbook。

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

下列文字可用於 user 參數。

```input
["Joe","Smith",2,true]
```

這會導致下列輸出：

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>認證

如果參數是資料型別 `PSCredential`，則可以提供 Azure 自動化[認證資產](./shared-resources/credentials.md)的名稱。 Runbook 會使用您所指定的名稱擷取認證。 下列測試 Runbook 可接受稱為 `credential` 的參數。

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

假設有稱為 `My Credential` 的認證資產，則下列文字可用於 user 參數。

```input
My Credential
```

假設認證中的使用者名稱是 `jsmith`，則會顯示下列輸出。

```output
jsmith
```

## <a name="start-a-runbook-with-the-azure-portal"></a>使用 Azure 入口網站啟動 Runbook

1. 在 Azure 入口網站中，選取 [自動化]，然後按一下自動化帳戶的名稱。
2. 在 [中樞] 功能表中，選取 [Runbook]。
3. 在 Runbook 頁面中，選取 Runbook，然後按一下 [啟動]。
4. 如果 Runbook 有參數，系統會針對每個參數，以文字方塊提示您提供值。 如需參數的詳細資訊，請參閱以下的 [Runbook 參數](#work-with-runbook-parameters)。
5. 在作業窗格中，您可以檢視 Runbook 作業的狀態。

## <a name="start-a-runbook-with-powershell"></a>使用 PowerShell 啟動 Runbook

您可以使用 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook)，利用 Windows PowerShell 來啟動 Runbook。 下列範例程式碼會啟動名稱為 **Test-Runbook** 的 Runbook。

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

`Start-AzAutomationRunbook` 會傳回工作物件，一旦啟動 Runbook，您即可用來追蹤狀態。 然後，您可以搭配使用此工作物件與 [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) 來判斷工作的狀態，以及使用 [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) 擷取其輸出。 下列範例會啟動名稱為 **Test-Runbook** 的 Runbook，等到它完成，然後顯示其輸出。

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

如果 Runbook 需要參數，則您必須以[雜湊表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)的形式提供它們。 雜湊表的索引鍵必須符合參數名稱，而值是參數值。 下列範例顯示如何啟動 Runbook 具有名為 FirstName 和 LastName 的兩個參數、名為 RepeatCount 的整數，和名為 Show 的布林值參數。 如需參數的詳細資訊，請參閱以下的 [Runbook 參數](#work-with-runbook-parameters)。

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="next-steps"></a>後續步驟

* 若要了解 Runbook 管理的詳細資料，請參閱 [在 Azure 自動化中管理 Runbook](manage-runbooks.md)。
* 如需 PowerShell 的詳細資料，請參閱 [PowerShell 文件](/powershell/scripting/overview)。
* 若要疑難排解 Runbook 執行的問題，請參閱[為 Runbook 問題疑難排解](troubleshoot/runbooks.md)。
