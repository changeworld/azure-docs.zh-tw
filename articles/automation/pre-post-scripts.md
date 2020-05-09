---
title: 在 Azure 中管理更新管理部署的前置腳本和後置腳本
description: 本文說明如何設定及管理更新部署的前置腳本和後置腳本。
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: f55ebb3270fdd97a1fdbbf5a56f9703c08933f9f
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855334"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>管理前置腳本和後置腳本

前置腳本和後置腳本是要在您的 Azure 自動化帳戶中執行的 runbook （預先工作前）和更新部署之後（之後）。 前置腳本和後置腳本會在 Azure 內容中執行，而不是在本機執行。 前置腳本會在更新部署開始時執行。 後置腳本會在部署結束時，以及在任何已設定的重新開機之後執行。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="pre-script-and-post-script-requirements"></a>前置腳本和後置腳本需求

若要使用 runbook 做為前置腳本或後置腳本，您必須將它匯入到您的自動化帳戶並[發佈 runbook](manage-runbooks.md#publish-a-runbook)。

## <a name="pre-script-and-post-script-parameters"></a>前置腳本和後置腳本參數

當您設定前置腳本和後置腳本時，您可以傳入參數，就像排程 runbook 一樣。 參數會在建立更新部署時定義。 前置腳本和後置腳本支援下列類型：

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

前置腳本和後置腳本 runbook 參數不支援布林值、物件或陣列類型。 這些值會導致 runbook 失敗。 

如果您需要另一種物件類型，您可以在 Runbook 中使用您自己的邏輯將其轉換成另一種類型。

除了您的`SoftwareUpdateConfigurationRunContext`標準 runbook 參數之外，也提供參數（類型 JSON 字串）。 如果您在前置腳本或後置腳本 runbook 中定義參數，則更新部署會自動將其傳入。 參數包含更新部署的相關資訊，這是[SOFTWAREUPDATECONFIGURATIONS API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration)傳回的資訊子集。 下列各節會定義相關聯的屬性。

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext 屬性

|屬性  |描述  |
|---------|---------|
|SoftwareUpdateConfigurationName     | 軟體更新設定的名稱。        |
|SoftwareUpdateConfigurationRunId     | 執行的唯一識別碼。        |
|SoftwareUpdateConfigurationSettings     | 與軟體更新設定相關的屬性集合。         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | 更新部署的目標作業系統。         |
|SoftwareUpdateConfigurationSettings.duration     | 更新部署執行`PT[n]H[n]M[n]S`的持續時間上限為每個 ISO8601;也稱為維護視窗。          |
|SoftwareUpdateConfigurationSettings.Windows     | 與 Windows 電腦相關的屬性集合。         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | 從更新部署排除的 Kb 清單。        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | 為更新部署選取的更新分類。        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | 更新部署的重新開機設定。        |
|azureVirtualMachines     | 更新部署中 Azure Vm 的資源識別碼清單。        |
|nonAzureComputerNames|更新部署中的非 Azure 電腦 Fqdn 清單。|

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

您可以在以下位置找到所有屬性的完整範例：[依名稱取得軟體更新](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)設定。

> [!NOTE]
> `SoftwareUpdateConfigurationRunContext`物件可以包含電腦的重複專案。 這可能會導致前置腳本和後置腳本在同一部電腦上執行多次。 若要解決此行為，請`Sort-Object -Unique`使用只選取唯一的 VM 名稱。

## <a name="using-a-pre-script-or-post-script-in-a-deployment"></a>在部署中使用前置腳本或後置腳本

若要在更新部署中使用前置腳本或後置腳本，請先建立更新部署。 選取 [**前置腳本 + 後置腳本**]。 此動作會開啟 [選取更新前 + 更新後指令碼]**** 頁面。

![選取指令碼](./media/pre-post-scripts/select-scripts.png)

選取您要使用的腳本。 在此範例中，我們會使用**UpdateManagement-TurnOnVms** runbook。 當您選取 runbook 時，[**設定腳本**] 頁面隨即開啟。 選取 [**前置腳本**]，然後選取 **[確定]**。

對 **UpdateManagement-TurnOffVms** 指令碼重複此程序。 但當您選擇**腳本類型**時，請選取 [**後置腳本**]。

[**選取的專案**] 區段現在會顯示您選取的腳本。 其中一個是前置腳本，另一個是後置腳本：

![選取的項目](./media/pre-post-scripts/selected-items.png)

完成更新部署的設定。

當更新部署完成時，您可以移至 [**更新部署**] 以查看結果。 如您所見，系統會提供前置腳本和後置腳本的狀態：

![更新結果](./media/pre-post-scripts/update-results.png)

藉由選取 [更新部署執行]，您會看到前置腳本和後置腳本的其他詳細資料。 其中會顯示該執行進行時的指令碼來源連結。

![部署執行的結果](./media/pre-post-scripts/deployment-run.png)

在您的腳本中。

## <a name="stopping-a-deployment"></a>停止部署

如果您想要根據前置腳本停止部署，則必須[擲](automation-runbook-execution.md#throw)回例外狀況。 如果您沒有這麼做，部署和後置腳本仍然會執行。 下列程式碼片段顯示如何擲回例外狀況。

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



## <a name="interacting-with-machines"></a>與機器互動

前置腳本和後置工作會在您的自動化帳戶中做為 runbook 執行，而不是直接在您的部署中的機器上執行。 前置工作和後續工作也會在 Azure 內容中執行，而且不會有非 Azure 機器的存取權。 下列各節顯示如何直接與機器互動，不論它們是 Azure Vm 或非 Azure 機器。

### <a name="interact-with-azure-machines"></a>與 Azure 機器互動

前置工作和後置工作會以 runbook 執行，而且不會在您的部署中的 Azure Vm 上以原生方式執行。 若要與您的 Azure Vm 互動，您必須具有下列專案：

* 執行身分帳戶
* 您想要執行的 runbook

若要與 Azure 機器互動，您應該使用[AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) Cmdlet 來與您的 azure vm 互動。 如需如何執行這項操作的範例，請參閱 runbook 範例[更新管理–使用執行命令執行腳本](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc)。

### <a name="interact-with-non-azure-machines"></a>與非 Azure 機器互動

前置工作和後續工作會在 Azure 內容中執行，且不會有非 Azure 機器的存取權。 若要與非 Azure 機器互動，您必須具有下列專案：

* 執行身分帳戶
* 已安裝在機器上的混合式 Runbook 背景工作角色
* 您想要在本機上執行的 Runbook
* 父 runbook

若要與非 Azure 機器互動，父 runbook 會在 Azure 內容中執行。 此 runbook 會使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) Cmdlet 呼叫子 runbook。 您必須指定 `RunOn` 參數，並提供混合式 Runbook 背景工作角色的名稱，好讓指令碼在其中執行。 請參閱 runbook 範例[更新管理–在本機執行腳本](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44)。

## <a name="aborting-patch-deployment"></a>正在中止修補程式部署

如果您的前置腳本傳回錯誤，您可能會想要中止您的部署。 若要這麼做，您必須在腳本中[擲](/powershell/module/microsoft.powershell.core/about/about_throw)回錯誤，以取得任何會造成失敗的邏輯。

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="samples"></a>範例

您可以在[腳本中心資源庫](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell)和[PowerShell 資源庫](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)中找到前置腳本和後置腳本的範例，也可以透過 Azure 入口網站將它們匯入。 若要這麼做，請在您的自動化帳戶的 [程式**自動化**] 底下，選取 [ **runbook 資源庫**]。 使用 [更新管理]**** 作為篩選條件。

![資源庫清單](./media/pre-post-scripts/runbook-gallery.png)

或者，您也可以依照下列清單所示的腳本名稱來搜尋它們：

* 更新管理 - 開啟 VM
* 更新管理 - 關閉 VM
* 更新管理 - 在本機執行指令碼
* 更新管理 - 前置/後置指令碼的範本
* 更新管理 - 以執行命令來執行指令碼

> [!IMPORTANT]
> 匯入 runbook 之後，您必須先將其發佈，才能使用它們。 若要這麼做，請在您的自動化帳戶中尋找 runbook，選取 [**編輯**]，然後選取 [**發佈**]。

這些範例都是以下列範例中定義的基本範本為基礎。 此範本可用於建立您自己的 runbook，以搭配前置腳本和後置腳本使用。 包含用來向 Azure 進行驗證及處理`SoftwareUpdateConfigurationRunContext`參數的必要邏輯。

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
> 針對非圖形化 PowerShell runbook， `Add-AzAccount`和`Add-AzureRMAccount`是[disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)的別名。 您可使用這些 Cmdlet，也可以在您的自動化帳戶中[將您的模組更新](automation-update-azure-modules.md)為最新版本。 即使您才剛建立新的自動化帳戶，可能還是需要更新您的模組。

## <a name="next-steps"></a>後續步驟

請移至下列教學課程，以瞭解如何管理 Windows 虛擬機器的更新：

> [!div class="nextstepaction"]
> [管理 Azure Windows VM 的更新和修補程式](automation-tutorial-update-management.md)