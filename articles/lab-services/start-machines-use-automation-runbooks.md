---
title: 使用 Azure 開發人員測試實驗室中的自動化運行簿啟動電腦
description: 瞭解如何使用 Azure 自動化運行簿在 Azure 開發人員測試實驗室的實驗室中啟動虛擬機器。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166317"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>使用 Azure 自動化運行簿在實驗室中按順序啟動虛擬機器
DevTest Labs 的[自動啟動](devtest-lab-set-lab-policy.md#set-autostart)功能允許您配置 VM 在指定時間自動啟動。 但是，此功能不支援按特定順序啟動的電腦。 在幾種方案中，這種類型的自動化將很有用。  一種方案是，在實驗室中需要先啟動一個跳轉盒 VM，然後再在其他 VM 之前啟動，因為跳轉盒用作對其他 VM 的存取點。  本文介紹如何使用執行腳本的 PowerShell Runbook 設置 Azure 自動化帳戶。 該腳本使用實驗室中 VM 上的標記來控制啟動順序，而無需更改腳本。

## <a name="setup"></a>安裝程式
在此示例中，實驗室中的 VM 需要添加帶有相應值（0，1，2 等）的標記**啟動訂單**。 指定不需要啟動的任何電腦為 -1。

## <a name="create-an-azure-automation-account"></a>建立 Azure 自動化帳戶
通過按照[本文](../automation/automation-create-standalone-account.md)中的說明創建 Azure 自動化帳戶。 創建帳戶時選擇"**以帳戶身份運行**"選項。 創建自動化帳戶後，打開 **"模組"** 頁，然後選擇功能表列上的 **"更新 Azure 模組**"。 預設模組是多個舊版本，如果沒有更新，腳本可能無法正常工作。

## <a name="add-a-runbook"></a>添加運行簿
現在，要向自動化帳戶添加 Runbook，請在左側功能表上選擇**Runbook。** 選擇**在功能表上添加運行簿**，然後按照說明[創建 PowerShell Runbook](../automation/automation-first-runbook-textual-powershell.md)。

## <a name="powershell-script"></a>PowerShell 指令碼
以下腳本採用訂閱名稱，實驗室名稱作為參數。 腳本的流是獲取實驗室中的所有 VM，然後解析標記資訊以創建 VM 名稱及其啟動順序的清單。 腳本按順序遍穿 VM 並啟動 VM。 如果特定訂單號中有多個 VM，則使用 PowerShell 作業非同步啟動它們。 對於沒有標記的 VM，將啟動值設置為最後一個 （10），預設情況下，它們將最後啟動。  如果實驗室不希望自動啟動 VM，則將標記值設置為 11，並將忽略該標記值。

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

## <a name="create-a-schedule"></a>創建計畫
要讓此腳本每天執行，請在自動化帳戶中[創建計畫](../automation/shared-resources/schedules.md#creating-a-schedule)。 創建計畫後，[將其連結到 Runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook)。 

在具有多個實驗室的多個訂閱的大規模情況下，將參數資訊存儲在不同實驗室的檔中，並將檔傳遞給腳本而不是單個參數。 腳本需要修改，但核心執行將是相同的。 雖然此示例使用 Azure 自動化執行 PowerShell 腳本，但還有其他選項，如在生成/發佈管道中使用任務。

## <a name="next-steps"></a>後續步驟
請參閱以下文章以瞭解有關 Azure 自動化的更多詳細資訊[：Azure 自動化簡介](../automation/automation-intro.md)。
