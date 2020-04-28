---
title: 在 Azure DevTest Labs 中使用自動化 runbook 啟動機器
description: 瞭解如何使用 Azure 自動化 runbook，在 Azure DevTest Labs 中啟動實驗室中的虛擬機器。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9bb97a73b7ca570ca122323e8e9c5a70c9348b15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76166317"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>使用 Azure 自動化 runbook 來啟動實驗室中的虛擬機器
DevTest Labs 的[自動啟動](devtest-lab-set-lab-policy.md#set-autostart)功能可讓您將 vm 設定為在指定的時間自動啟動。 不過，這項功能不支援以特定順序啟動電腦。 在許多情況下，這種類型的自動化會很有用。  其中一個案例是，實驗室中的 Jumpbox VM 必須先在其他 Vm 之前先啟動，因為 Jumpbox 是用來作為其他 Vm 的存取點。  本文說明如何使用執行腳本的 PowerShell runbook 來設定 Azure 自動化帳戶。 此腳本會在實驗室中的 Vm 上使用標記，讓您控制啟動順序，而不需要變更腳本。

## <a name="setup"></a>安裝程式
在此範例中，實驗室中的 Vm 必須將標記**StartupOrder**新增為適當的值（0、1、2等等）。 指定不需要啟動為-1 的任何電腦。

## <a name="create-an-azure-automation-account"></a>建立 Azure 自動化帳戶
遵循[本文中的](../automation/automation-create-standalone-account.md)指示建立 Azure 自動化帳戶。 建立帳戶時，請選擇 [**執行身分帳戶**] 選項。 建立自動化帳戶之後，請開啟 [**模組**] 頁面，然後選取功能表列上的 [**更新 Azure 模組**]。 預設模組是舊版的幾個版本，而且沒有更新，腳本可能無法運作。

## <a name="add-a-runbook"></a>新增 runbook
現在，若要將 runbook 新增至自動化帳戶，請選取左側功能表上的 [ **runbook** ]。 選取功能表上的 [**新增 runbook** ]，並依照指示來[建立 PowerShell runbook](../automation/automation-first-runbook-textual-powershell.md)。

## <a name="powershell-script"></a>PowerShell 指令碼
下列腳本會採用訂用帳戶名稱、實驗室名稱作為參數。 腳本的流程是取得實驗室中的所有 Vm，然後剖析標記資訊，以建立 VM 名稱的清單及其啟動順序。 此腳本會逐步執行 Vm，並啟動 Vm。 如果有多個 Vm 是以特定的順序編號，則會使用 PowerShell 工作以非同步方式啟動。 對於沒有標記的 Vm，將 [啟動值] 設定為最後一個（10），預設為 [已啟動]。  如果實驗室不想讓 VM 自動安裝，請將 [標籤] 值設定為11，它將會被忽略。

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>建立排程
若要讓此腳本每天執行，請在自動化帳戶中[建立排程](../automation/shared-resources/schedules.md#creating-a-schedule)。 建立排程之後，請將[它連結至 runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook)。 

在具有多個實驗室的多個訂用帳戶的大規模情況下，請將參數資訊儲存在不同實驗室的檔案中，並將檔案傳遞至腳本，而不是個別參數。 腳本需要修改，但核心執行會相同。 雖然此範例會使用 Azure 自動化來執行 PowerShell 腳本，但還有其他選項，例如在組建/發行管線中使用工作。

## <a name="next-steps"></a>後續步驟
請參閱下列文章，以深入瞭解 Azure 自動化： [Azure 自動化簡介](../automation/automation-intro.md)。
