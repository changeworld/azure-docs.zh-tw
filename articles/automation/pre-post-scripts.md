---
title: 在 Azure 中管理更新管理部署中的前置指令碼和後置指令碼
description: 本文將說明如何設定及管理更新部署的前置指令碼和後置指令碼。
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 246fcdb27737e99bb677e23216f0305037f54526
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187450"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>管理前指令碼和後指令碼

前置指令碼和後置指令碼是您在更新部署之前 (前置工作) 和之後 (後置工作) 應在您的 Azure 自動化帳戶中執行的 Runbook。 前置指令碼和後置指令碼會在 Azure 環境中執行，而不是在本機執行。 前置指令碼會在更新部署開始時執行。 後置指令碼會在部署結束時及在已設定的任何重新啟動之後執行。

## <a name="pre-script-and-post-script-requirements"></a>前置指令碼和後置指令碼的需求

若要使用 Runbook 作為前置指令碼或後置指令碼，您必須將其匯入您的自動化帳戶中，並[發佈 Runbook](manage-runbooks.md#publish-a-runbook)。

## <a name="pre-script-and-post-script-parameters"></a>前置指令碼和後置指令碼的參數

當您設定前置指令碼和後置指令碼時，可以像排定 Runbook 時一樣傳入參數。 參數會在建立更新部署時定義。 前置指令碼和後置指令碼支援下列類型：

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

前置指令碼和後置指令碼 Runbook 參數不支援布林值、物件或陣列類型。 這些值會導致 Runbook 失敗。 

如果您需要另一種物件類型，您可以在 Runbook 中使用您自己的邏輯將其轉換成另一種類型。

除了標準 Runbook 參數以外，也會提供 `SoftwareUpdateConfigurationRunContext` 參數 (類型為 JSON 字串)。 如果您在前置指令碼或後置指令碼中定義該參數，更新部署就會自動傳入該參數。 此參數包含更新部署的相關資訊，也就是 [SoftwareUpdateconfigurations API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) 傳回的部分資訊。 以下幾節會定義相關聯的屬性。

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext 屬性

|屬性  |描述  |
|---------|---------|
|SoftwareUpdateConfigurationName     | 軟體更新設定的名稱。        |
|SoftwareUpdateConfigurationRunId     | 執行的唯一識別碼。        |
|SoftwareUpdateConfigurationSettings     | 與軟體更新設定相關的屬性集合。         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | 更新部署的目標作業系統。         |
|SoftwareUpdateConfigurationSettings.duration     | 更新部署執行的持續時間上限 (也稱為維護期間)，根據 ISO8601，其格式為 `PT[n]H[n]M[n]S`。          |
|SoftwareUpdateConfigurationSettings.Windows     | 與 Windows 電腦相關的屬性集合。         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | 排除在更新部署外的 KB 清單。        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | 為更新部署選取的更新分類。        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | 更新部署的重新啟動設定。        |
|azureVirtualMachines     | 更新部署中 Azure VM 的資源識別碼清單。        |
|nonAzureComputerNames|更新部署中的非 Azure 電腦 FQDN 清單。|

以下範例是一個傳遞給 **SoftwareUpdateConfigurationRunContext** 參數的 JSON 字串：

```json
"SoftwareUpdateConfigurationRunContext":{
      "SoftwareUpdateConfigurationName":"sampleConfiguration",
      "SoftwareUpdateConfigurationRunId":"00000000-0000-0000-0000-000000000000",
      "SoftwareUpdateConfigurationSettings":{
         "operatingSystem":"Windows",
         "duration":"PT2H0M",
         "windows":{
            "excludedKbNumbers":[
               "168934",
               "168973"
            ],
            "includedUpdateClassifications":"Critical",
            "rebootSetting":"IfRequired"
         },
         "azureVirtualMachines":[
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
         ],
         "nonAzureComputerNames":[
            "box1.contoso.com",
            "box2.contoso.com"
         ]
      }
   }
```

您可以在以下位置找到所有屬性的完整範例：[依名稱取得軟體更新設定](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)。

> [!NOTE]
> `SoftwareUpdateConfigurationRunContext` 物件可包含機器的重複項目。 這可能會導致前置指令碼和後置指令碼在相同機器上執行多次。 此行為的因應措施是使用 `Sort-Object -Unique`，僅選取唯一的 VM 名稱。

> [!NOTE]
> 目前僅支援 PowerShell runbook 做為前置/後置腳本。 其他 runbook 類型（例如 Python、圖形、PowerShell 工作流程、圖形化 PowerShell 工作流程）目前不支援做為前置/後置腳本。

## <a name="use-a-pre-script-or-post-script-in-a-deployment"></a>在部署中使用前置指令碼或後置指令碼

若要在更新部署中使用前置指令碼或後置指令碼，請從建立更新部署開始著手。 選取 [前置指令碼 + 後置指令碼]。 此動作會開啟 [選取更新前 + 更新後指令碼] 頁面。

![選取指令碼](./media/pre-post-scripts/select-scripts.png)

選取您要使用的指令碼。 在此範例中，我們使用 **UpdateManagement-TurnOnVms** Runbook。 選取了 Runbook 後，[設定指令碼] 頁面會隨即開啟。 請選取 [前置指令碼]，然後選取 [確定]。

對 **UpdateManagement-TurnOffVms** 指令碼重複此程序。 但在選擇 [指令碼類型] 時，請選取 [後置指令碼]。

[選取的項目] 區段現在會顯示您選取的兩個指令碼。 一個是前置指令碼，另一個是後置指令碼：

![選取的項目](./media/pre-post-scripts/selected-items.png)

完成更新部署的設定。

更新部署完成後，您可以移至 [更新部署] 來檢視結果。 您可以看到，前置指令碼和後置指令碼的狀態都已提供：

![更新結果](./media/pre-post-scripts/update-results.png)

選取更新部署執行後，您會看到前置指令碼和後置指令碼的其他詳細資料。 其中會顯示該執行進行時的指令碼來源連結。

![部署執行的結果](./media/pre-post-scripts/deployment-run.png)


## <a name="stop-a-deployment"></a>停止部署

如果您想要根據前置指令碼停止部署，您必須[擲回](automation-runbook-execution.md#throw)例外狀況。 若未這麼做，部署和後置指令碼仍將執行。 下列程式碼片段說明如何擲回例外狀況。

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```

## <a name="interact-with-machines"></a>與機器互動

前置指令碼和後置工作會在您的自動化帳戶中以 Runbook 的形式執行，而不是直接在部署中的機器上執行。 前置工作和後置工作也會在 Azure 環境中執行，且無法存取非 Azure 機器。 以下幾節說明如何直接與機器互動，無論是 Azure VM 還是非 Azure 機器。

### <a name="interact-with-azure-machines"></a>與 Azure 機器互動

前置工作和後置工作會以 Runbook 的形式執行，而不會在部署中的 Azure VM 上以原生方式執行。 若要與您的 Azure VM 互動，您必須具有下列項目：

* 執行身分帳戶
* 您想要執行的 Runbook

若要與 Azure 機器互動，您應使用 [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) Cmdlet 與您的 Azure VM 互動。 如需執行此作業的範例，請參閱 Runbook 範例：[更新管理 – 使用 Run 命令執行指令碼](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc)。

### <a name="interact-with-non-azure-machines"></a>與非 Azure 機器互動

前置工作和後置工作會在 Azure 環境中執行，且無法存取非 Azure 機器。 若要與非 Azure 機器互動，您必須具備下列項目：

* 執行身分帳戶
* 已安裝在機器上的混合式 Runbook 背景工作角色
* 您想要在本機上執行的 Runbook
* 父 Runbook

若要與非 Azure 機器互動，父 Runbook 需在 Azure 環境中執行。 此 Runbook 會使用 [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) Cmdlet 來呼叫子 Runbook。 您必須指定 `RunOn` 參數，並提供混合式 Runbook 背景工作角色的名稱，好讓指令碼在其中執行。 請參閱 Runbook 範例：[更新管理 – 在本機執行指令碼](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44)。

## <a name="abort-patch-deployment"></a>中止修補程式部署

如果您的前置指令碼傳回錯誤，您可能會想要中止部署。 若要這麼做，您必須在指令碼中針對任何會導致失敗的邏輯[擲回](/powershell/module/microsoft.powershell.core/about/about_throw)錯誤。

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="samples"></a>範例

如需前置指令碼和後置指令碼的範例，請參閱[指令碼中心資源庫](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell)和 [PowerShell 資源庫](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)，或透過 Azure 入口網站將其匯入。 若要這麼做，請在您自動化帳戶的 [程序自動化] 下方，選取 [Runbook 資源庫]。 使用 [更新管理] 作為篩選條件。

![資源庫清單](./media/pre-post-scripts/runbook-gallery.png)

或者，您可以透過指令碼名稱來搜尋這些範例，如下列清單所示：

* 更新管理 - 開啟 VM
* 更新管理 - 關閉 VM
* 更新管理 - 在本機執行指令碼
* 更新管理 - 前置/後置指令碼的範本
* 更新管理 - 以執行命令來執行指令碼

> [!IMPORTANT]
> 匯入 Runbook 之後，您必須先將其發佈，方能使用。 若要這麼做，請在自動化帳戶中尋找 Runbook，選取 [編輯]，然後選取 [發佈]。

這些範例都會以下列範例中定義的基底範本為基礎。 此範本可用來建立您自己的 Runbook，以便搭配前置指令碼和後置指令碼使用。 其中包含向 Azure 進行驗證及處理 `SoftwareUpdateConfigurationRunContext` 參數的必要邏輯。

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
  It requires a RunAs account.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)
#region BoilerplateAuthentication
#This requires a RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

> [!NOTE]
> 針對非圖形化 PowerShell Runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) 的別名。 您可使用這些 Cmdlet，也可以在您的自動化帳戶中[將您的模組更新](automation-update-azure-modules.md)為最新版本。 即使您才剛建立新的自動化帳戶，可能還是需要更新您的模組。

## <a name="next-steps"></a>後續步驟

* 如需更新管理的詳細資訊，請參閱[管理 Azure VM 的更新和修補程式](automation-tutorial-update-management.md)。
