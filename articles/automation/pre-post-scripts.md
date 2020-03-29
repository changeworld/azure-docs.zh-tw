---
title: 在 Azure 中的更新管理部署上配置預腳本和後腳本
description: 本文介紹如何配置和管理更新部署的預腳本和後腳本。
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 35fba966fcdb6d1c5cd7c531bb22c9c78ae16ff3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75417791"
---
# <a name="manage-pre-and-post-scripts"></a>管理預腳本和後腳本

通過預腳本和後腳本，您可以在更新部署之前（任務前）和更新部署之後（任務後）在 Azure 自動化帳戶中運行 PowerShell Runbook。 在 Azure 上下文中運行前腳本和後腳本，而不是本地運行。 預腳本在更新部署開始時運行。 後腳本在部署結束時和配置的任何重新開機後運行。

## <a name="runbook-requirements"></a>Runbook 需求

要將 Runbook 用作預腳本或後腳本，必須將 Runbook 導入您的自動化帳戶併發布。 要瞭解有關此過程的更多內容，請參閱[發佈 Runbook](manage-runbooks.md#publish-a-runbook)。

## <a name="using-a-pre-script-or-post-script"></a>使用預腳本或後腳本

要在更新部署中使用預腳本或後腳本，請從創建更新部署開始。 選擇**預腳本 + 後腳本**。 此動作會開啟 [選取更新前 + 更新後指令碼]**** 頁面。

![選取指令碼](./media/pre-post-scripts/select-scripts.png)

選擇要使用的腳本。 在此示例中，我們使用**更新管理-TurnOnVms**運行簿。 選擇 Runbook 時，將打開 **"配置腳本"** 頁。 選擇 **"腳本前**"，然後選擇 **"確定**"。

對 **UpdateManagement-TurnOffVms** 指令碼重複此程序。 但是，當您選擇**腳本類型**時，請選擇 **"後腳本**"。

"**選定專案**"部分現在顯示所選腳本。 一個是預腳本，另一個是後腳本：

![選取的項目](./media/pre-post-scripts/selected-items.png)

完成配置更新部署。

更新部署完成後，可以轉到 **"更新部署"** 以查看結果。 如您所見，為預腳本和後腳本提供了狀態：

![更新結果](./media/pre-post-scripts/update-results.png)

通過選擇更新部署運行，將顯示前腳本和腳本後的其他詳細資訊。 其中會顯示該執行進行時的指令碼來源連結。

![部署執行的結果](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>傳遞參數

配置預腳本和後腳本時，可以傳遞參數，就像安排 Runbook 一樣。 參數會在建立更新部署時定義。 腳本前和腳本後支援以下類型：

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

如果您需要另一種物件類型，您可以在 Runbook 中使用您自己的邏輯將其轉換成另一種類型。

除了標準 Runbook 參數外，還提供另一個參數：**軟體更新配置RunCoNtext**

此參數是 JSON 字串，如果您在腳本前或腳本後定義參數，則更新部署會自動傳入該參數。 該參數包含有關更新部署的資訊，更新部署是[軟體更新配置 API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration)返回的資訊的子集。 

下表顯示了變數中提供的屬性。

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext 屬性

|屬性  |描述  |
|---------|---------|
|SoftwareUpdateConfigurationName     | 軟體更新配置的名稱。        |
|SoftwareUpdateConfigurationRunId     | 運行的唯一 ID。        |
|SoftwareUpdateConfigurationSettings     | 與軟體更新配置相關的屬性的集合。         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | 更新部署的目標作業系統。         |
|SoftwareUpdateConfigurationSettings.duration     | 更新部署的最大持續時間按`PT[n]H[n]M[n]S`ISO8601 運行;也稱為*維護視窗*。          |
|SoftwareUpdateConfigurationSettings.Windows     | 與 Windows 電腦相關的屬性的集合。         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | 從更新部署中排除的 QB 的清單。        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | 更新為更新部署選擇的分類。        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | 重新開機更新部署的設置。        |
|azureVirtualMachines     | 更新部署中 Azure VM 的資源識別碼 清單。        |
|nonAzureComputerNames|更新部署中非 Azure 電腦 FQDN 的清單。|

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

具有所有屬性的完整示例，請于：[按名稱獲取軟體更新配置](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)。

> [!NOTE]
> 該`SoftwareUpdateConfigurationRunContext`物件可以包含電腦的重複條目。 這可能會導致腳本前和腳本後在同一台電腦上多次運行。 要解決此問題，請使用 僅在`Sort-Object -Unique`腳本中選擇唯一的 VM 名稱。


## <a name="stopping-a-deployment"></a>停止部署

如果要停止基於預腳本的部署，則必須[引發](automation-runbook-execution.md#throw)異常。 如果沒有，部署和後腳本仍將運行。 以下程式碼片段演示如何引發異常。

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

## <a name="samples"></a>範例

在[腳本中心庫](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell)和[PowerShell 庫中](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)可以找到預腳本和後腳本的示例，也可以通過 Azure 門戶導入它們。 為此，在您的自動化帳戶中，在 **"過程自動化**"下，選擇**Runbook 庫**。 使用 [更新管理]**** 作為篩選條件。

![資源庫清單](./media/pre-post-scripts/runbook-gallery.png)

或者，您可以按腳本名稱搜索它們，如以下清單所示：

* 更新管理 - 開啟 VM
* 更新管理 - 關閉 VM
* 更新管理 - 在本機執行指令碼
* 更新管理 - 前置/後置指令碼的範本
* 更新管理 - 以執行命令來執行指令碼

> [!IMPORTANT]
> 導入 Runbook 後，必須先發佈它們，然後才能使用它們。 為此，請在自動化帳戶中找到 Runbook，選擇 **"編輯**"，然後選擇 **"發佈**"。

這些示例都基於以下示例中定義的基本範本。 此範本可用於創建自己的 Runbook，以便與預腳本和後腳本一起使用。 包括使用 Azure 進行身份驗證和處理`SoftwareUpdateConfigurationRunContext`參數的必要邏輯。

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

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
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
New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

## <a name="interacting-with-machines"></a>與機器交互

任務前和任務後作為自動化帳戶中的 Runbook 運行，而不是直接在部署中的電腦上運行。 任務前和任務後也會在 Azure 上下文中運行，並且無法訪問非 Azure 電腦。 以下各節演示如何與電腦直接交互，無論是 Azure VM 還是非 Azure 電腦。

### <a name="interacting-with-azure-machines"></a>與 Azure 電腦交互

預任務和任務後任務作為 Runbook 運行，並且不會本機在部署中的 Azure VM 上運行。 要與 Azure VM 進行交互，必須具有以下專案：

* 執行身分帳戶
* 要運行的 Runbook

要與 Azure 電腦進行交互，應使用[調用 AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) Cmdlet 與 Azure VM 進行交互。 有關如何執行此操作的示例，請參閱 Runbook 示例["更新管理 — 使用 Run 命令運行腳本](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc)"。

### <a name="interacting-with-non-azure-machines"></a>與非 Azure 電腦交互

任務前和任務後在 Azure 上下文中運行，並且無法訪問非 Azure 電腦。 要與非 Azure 電腦進行交互，必須具有以下項：

* 執行身分帳戶
* 已安裝在機器上的混合式 Runbook 背景工作角色
* 您想要在本機上執行的 Runbook
* 父運行簿

要與非 Azure 電腦進行交互，在 Azure 上下文中運行父 Runbook。 此 Runbook 會使用 [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) Cmdlet 來呼叫子 Runbook。 您必須指定 `-RunOn` 參數，並提供混合式 Runbook 背景工作角色的名稱，好讓指令碼在其中執行。 有關詳細資訊，請參閱 runbook 示例["更新管理 " 在本地運行腳本](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44)。

## <a name="abort-patch-deployment"></a>中止修補程式部署

如果預腳本返回錯誤，則可能需要中止部署。 為此，您必須在腳本中[為](/powershell/module/microsoft.powershell.core/about/about_throw)任何構成失敗的邏輯引發錯誤。

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>已知問題

* 使用預腳本和後腳本時，不能將布林、物件或陣列傳遞給參數。 如果這樣做，則 Runbook 將失敗。 有關受支援類型的完整清單，請參閱[傳遞參數](#passing-parameters)。

## <a name="next-steps"></a>後續步驟

繼續學習以下教程，瞭解如何管理 Windows 虛擬機器的更新：

> [!div class="nextstepaction"]
> [管理 Azure Windows VM 的更新和修補程式](automation-tutorial-update-management.md)

