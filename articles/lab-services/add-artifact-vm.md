---
title: 向 Azure 開發人員測試實驗室中的 VM 添加專案 |微軟文檔
description: 瞭解如何在 Azure DevTest 實驗室的實驗室中向虛擬機器添加專案
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 27fec279582d845972b87ac635c87c16c239924e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73901317"
---
# <a name="add-an-artifact-to-a-vm"></a>新增 VM 構件
創建 VM 時，可以將現有專案添加到其中。 這些專案可以來自[公共 DevTest Labs Git 存儲庫](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)，也可以來自您自己的 Git 存儲庫。 本文介紹如何在 Azure 門戶中使用 Azure PowerShell 添加專案。 

Azure DevTest Labs「構件」** 可讓您指定會在 VM 佈建時執行的「動作」**，例如執行 Windows PowerShell 指令碼、執行 Bash 命令，以及安裝軟體。 構件「參數」 ** 可讓您自訂適用於特定案例的構件。

要瞭解如何創建自訂專案，請參閱文章：[創建自訂工件](devtest-lab-artifact-author.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>使用 Azure 入口網站 
1. 登錄到 Azure[門戶](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。
1. 從實驗室清單中，選取您想要使用之 VM 所在的實驗室。  
1. 選取 [我的虛擬機器]****。
1. 選取所需的 VM。
1. 選取 [管理構件]****。 
1. 選取 [套用構件]****。
1. 在 [套用構件]**** 窗格中，選取您要新增到 VM 的構件。
1. 在 [新增構件]**** 窗格中，輸入必要的參數值以及任何您所需的選用參數。  
1. 選取 [新增]**** 以新增構件，然後返回 [套用構件]**** 窗格。
1. 視需要繼續為您的 VM 加入構件。
1. 加入構件之後，您可以 [變更構件的執行順序](#change-the-order-in-which-artifacts-are-run)。 您也可以返回來 [檢視或修改構件](#view-or-modify-an-artifact)。
1. 當您新增好構件時，選取 [套用]****

### <a name="change-the-order-in-which-artifacts-are-run"></a>變更構件的執行順序
根據預設，構件的動作是依照它們加入 VM 的順序來執行。 下列步驟說明如何變更構件的執行順序。

1. 在 [套用構件]**** 窗格頂端，選取會指出已新增至 VM 之構件數目的連結。
   
    ![新增至 VM 的構件數目](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. 在 [選取的構件]**** 窗格中，將構件拖放到所需的順序。 如果您在拖曳成品時發生問題，請確定您是從成品左側進行拖曳。 
1. 完成時選取 [確定] **** 。  

### <a name="view-or-modify-an-artifact"></a>檢視或修改構件
下列步驟說明如何檢視或修改構件的參數︰

1. 在 [套用構件]**** 窗格頂端，選取會指出已新增至 VM 之構件數目的連結。
   
    ![新增至 VM 的構件數目](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. 在 [選取的構件]**** 窗格中，選取您想要檢視或編輯的構件。  
1. 在 [新增構件]**** 窗格中，進行任何所需的變更，然後選取 [確定]**** 以關閉 [新增構件]**** 窗格。
1. 選取 [確定]**** 以關閉 [選取的構件]**** 窗格。

## <a name="use-powershell"></a>使用 PowerShell
以下腳本將指定的專案應用於指定的 VM。 [Invoke-AzResourceAction 命令](/powershell/module/az.resources/invoke-azresourceaction)是執行該操作的命令。  

```powershell
#Requires -Module Az.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>後續步驟
請參閱以下有關工件的文章：

- [為實驗室指定必填專案](devtest-lab-mandatory-artifacts.md)
- [建立自訂構件](devtest-lab-artifact-author.md)
- [將成品存放庫新增至實驗室](devtest-lab-artifact-author.md)
- [診斷構件失敗](devtest-lab-troubleshoot-artifact-failure.md)
