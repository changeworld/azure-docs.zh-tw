---
title: 使用命令列工具來啟動和停止 Vm Azure DevTest Labs
description: 瞭解如何使用命令列工具來啟動和停止 Azure DevTest Labs 中的虛擬機器。
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
ms.openlocfilehash: fd643559a09d5c75aad9be5f35c653994c8488cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76169258"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>使用命令列工具來啟動和停止 Azure DevTest Labs 虛擬機器
本文說明如何使用 Azure PowerShell 或 Azure CLI，在 Azure DevTest Labs 中啟動或停止實驗室中的虛擬機器。 您可以建立 PowerShell/CLI 腳本，將這些作業自動化。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概觀
Azure DevTest Labs 是建立快速、輕鬆且精簡的開發/測試環境的一種方式。 它可讓您管理成本、快速布建 Vm，並將浪費降至最低。  Azure 入口網站中有內建功能，可讓您將實驗室中的 Vm 設定為在特定時間自動啟動和停止。 

不過，在某些情況下，您可能會想要從 PowerShell/CLI 腳本自動啟動和停止 Vm。 它可讓您在任何時間，而不是在特定時間啟動和停止個別機器，而有一些彈性。 以下是使用腳本執行這些工作的一些情況會很有説明。

- 在測試環境中使用3層應用程式時，階層必須以序列啟動。 
- 符合自訂準則以節省成本時，請關閉 VM。 
- 在 CI/CD 工作流程內，使用它做為在流程開始時啟動、使用 Vm 作為組建電腦、測試電腦或基礎結構，然後在程式完成時停止 Vm。 其中一個範例是具有 Azure DevTest Labs 的自訂映射 factory。  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> 下列腳本會使用 Azure PowerShell Az 模組。 

下列 PowerShell 腳本會在實驗室中啟動 VM。 [AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0)是此腳本的主要焦點。 **ResourceId**參數是實驗室中 VM 的完整資源識別碼。 **動作**參數是根據所需的設定，**啟動**或**停止**選項的位置。

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>Azure CLI
[Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)是將 DevTest Labs vm 的啟動和停止自動化的另一種方式。 Azure CLI 可以[安裝](/cli/azure/install-azure-cli?view=azure-cli-latest)在不同的作業系統上。 下列腳本提供在實驗室中啟動和停止 VM 的命令。 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>後續步驟
請參閱下列文章，以瞭解如何使用 Azure 入口網站來執行這些作業：[重新開機 VM](devtest-lab-restart-vm.md)。
