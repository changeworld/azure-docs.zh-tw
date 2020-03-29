---
title: 使用命令列工具啟動和停止 VM Azure 開發人員測試實驗室
description: 瞭解如何使用命令列工具在 Azure 開發人員測試實驗室中啟動和停止虛擬機器。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169258"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>使用命令列工具啟動和停止 Azure 開發人員測試實驗室虛擬機器
本文介紹如何使用 Azure PowerShell 或 Azure CLI 在 Azure DevTest 實驗室的實驗室中啟動或停止虛擬機器。 您可以創建 PowerShell/CLI 腳本來自動執行這些操作。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>總覽
Azure 開發人員測試實驗室是創建快速、簡單和精益開發/測試環境的一種方式。 它允許您管理成本、快速預配 VM 和儘量減少浪費。  Azure 門戶中有一些內置功能，允許您在實驗室中配置 VM 以在特定時間自動啟動和停止 VM。 

但是，在某些情況下，您可能希望自動從 PowerShell/CLI 腳本啟動和停止 VM。 它為您提供了一些靈活性，可隨時啟動和停止單個機器，而不是在特定時間。 下面是使用腳本運行這些任務的一些情況。

- 將 3 層應用程式用作測試環境的一部分時，需要按順序啟動層。 
- 滿足自訂條件以節省資金時關閉 VM。 
- 將其用作 CI/CD 工作流中的任務，從流的開始開始，將 VM 用作生成電腦、測試電腦或基礎結構，然後在該過程完成後停止 VM。 例如，使用 Azure 開發人員測試實驗室的自訂映射工廠。  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> 以下腳本使用 Azure PowerShell Az 模組。 

以下 PowerShell 腳本在實驗室中啟動 VM。 [調用-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0)是此腳本的主要焦點。 **ResourceId**參數是實驗室中 VM 完全限定的資源識別碼。 **"操作"** 參數是根據所需內容設置 **"開始**"或 **"停止"** 選項的位置。

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
[Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)是自動啟動和停止 DevTest 實驗室 VM 的另一種方法。 Azure CLI 可以[安裝在](/cli/azure/install-azure-cli?view=azure-cli-latest)不同的作業系統上。 以下腳本提供了在實驗室中啟動和停止 VM 的命令。 

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
有關使用 Azure 門戶執行這些操作，請參閱以下文章：[重新開機 VM](devtest-lab-restart-vm.md)。
