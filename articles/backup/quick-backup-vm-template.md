---
title: 快速入門 - Resource Manager 範本 VM 備份
description: 了解如何使用 Azure Resource Manager 範本來備份虛擬機器
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: 7aef0e7e48bfd3f56bd1c3718b1a547bea66cbe8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87054484"
---
# <a name="quickstart-back-up-a-virtual-machine-in-azure-with-an-arm-template"></a>快速入門：使用 ARM 範本在 Azure 中備份虛擬機器

[Azure 備份](backup-overview.md)會備份內部部署機器與應用程式，以及 Azure VM。 本文說明如何使用 Azure Resource Manager 範本 (ARM 範本) 和 Azure PowerShell 來備份 Azure VM。 本快速入門著重於部署 ARM 範本來建立「復原服務」保存庫的程序。 如需開發 ARM 範本的詳細資訊，請參閱 [Azure Resource Manager 文件](../azure-resource-manager/index.yml)和[範本參考](/azure/templates/microsoft.recoveryservices/allversions)。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

[復原服務保存庫](backup-azure-recovery-services-vault-overview.md)是一個邏輯容器，可儲存受保護資源 (例如 Azure VM) 的備份資料。 當備份作業執行時，它會在復原服務保存庫內建立復原點。 然後您可以使用其中一個復原點，將資料還原到指定的時間點。 此外，您可以使用 [Azure PowerShell](./quick-backup-vm-powershell.md)、[Azure CLI](quick-backup-vm-cli.md) 或在 [Azure 入口網站](quick-backup-vm-portal.md)中備份 VM。

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-create-vm-and-configure-backup%2Fazuredeploy.json)

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/) 此範本可讓您部署簡單的 Windows VM 和復原服務保存庫，並且設定 DefaultPolicy 來進行保護。

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json" range="1-247" highlight="221-245":::

範本中定義的資源為：

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.RecoveryServices/vaults**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [**Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems)

## <a name="deploy-the-template"></a>部署範本

若要部署範本，請選取 [試試看] 以開啟 Azure Cloud Shell，然後將下列 PowerShell 指令碼貼到 Shell 視窗中。 若要貼上程式碼，請以滑鼠右鍵按一下 Shell 視窗，然後選取 [貼上]。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

在本快速入門中，您會使用 Azure PowerShell 來部署 ARM 範本。 您也可以使用 [Azure 入口網站](../azure-resource-manager/templates/deploy-portal.md)、[Azure CLI](../azure-resource-manager/templates/deploy-cli.md) 和 [Rest API](../azure-resource-manager/templates/deploy-rest.md) 來部署範本。

## <a name="validate-the-deployment"></a>驗證部署

### <a name="start-a-backup-job"></a>開始備份作業

此範本會建立 VM 並在 VM 上啟用備份。 部署範本之後，您需要啟動備份作業。 如需詳細資訊，請參閱[啟動備份作業](./quick-backup-vm-powershell.md#start-a-backup-job)。

### <a name="monitor-the-backup-job"></a>監視備份作業

若要監視備份作業，請參閱[監視備份作業](./quick-backup-vm-powershell.md#monitor-the-backup-job)。

## <a name="clean-up-resources"></a>清除資源

如果您不再需要備份 VM，便可加以清除。

- 如果您想要嘗試還原 VM，請略過清除程序。
- 如果您使用現有的 VM，可以略過最後的 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) Cmdlet，讓資源群組和 VM 留在原處。

停用保護，移除還原點和保存庫。 然後，刪除資源群組和相關聯的 VM 資源，如下所示：

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立復原服務保存庫、啟用 VM 的保護功能，並建立初始復原點。

- [了解如何](tutorial-backup-vm-at-scale.md)在 Azure 入口網站中備份 VM。
- [了解如何](tutorial-restore-disk.md)快速還原的 VM
- [了解如何](../azure-resource-manager/templates/template-tutorial-create-first-template.md)建立 ARM 範本。
