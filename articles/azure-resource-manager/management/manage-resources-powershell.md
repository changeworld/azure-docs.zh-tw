---
title: 管理資源 - Azure 電源外殼
description: 使用 Azure PowerShell 和 Azure 資源管理器來管理資源。 演示如何部署和刪除資源。
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: de6f24ea66ef41b5ee4bfdda5948de9639f10a51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485399"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>使用 Azure PowerShell 來管理 Azure 資源

瞭解如何將 Azure PowerShell 與[Azure 資源管理器](overview.md)一起管理 Azure 資源。 有關管理資源組，請參閱[使用 Azure PowerShell 管理 Azure 資源組](manage-resource-groups-powershell.md)。

有關管理資源的其他文章：

- [使用 Azure 門戶管理 Azure 資源](manage-resources-portal.md)
- [使用 Azure CLI 管理 Azure 資源](manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>將資源部署到現有資源組

可以使用 Azure PowerShell 直接部署 Azure 資源，也可以部署資源管理器範本來創建 Azure 資源。

### <a name="deploy-a-resource"></a>部署資源

以下腳本創建存儲帳戶。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>部署範本

以下腳本創建部署快速入門範本以創建存儲帳戶。 有關詳細資訊，請參閱[快速入門：使用 Visual Studio 代碼創建 Azure 資源管理器範本](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell)。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

如需詳細資訊，請參閱[使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../templates/deploy-powershell.md)。

## <a name="deploy-a-resource-group-and-resources"></a>部署資源組和資源

您可以創建資源組並將資源部署到該組。 如需詳細資訊，請參閱[建立資源群組並部署資源](../templates/deploy-to-subscription.md#resource-group-and-resources)。

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>將資源部署到多個訂閱或資源組

一般而言，您要將範本中的所有資源部署至單一資源群組。 不過，在某些情況下，您要將一組資源部署在一起，但將它們放在不同的資源群組或訂用帳戶中。 有關詳細資訊，請參閱將[Azure 資源部署到多個訂閱或資源組](../templates/cross-resource-group-deployment.md)。

## <a name="delete-resources"></a>刪除資源

以下腳本演示如何刪除存儲帳戶。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

有關 Azure 資源管理器如何命令刪除資源的詳細資訊，請參閱 Azure[資源管理器資源組刪除](delete-resource-group.md)。

## <a name="move-resources"></a>移動資源

以下腳本演示如何將存儲帳戶從一個資源組刪除到另一個資源組。

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](move-resource-group-and-subscription.md)。

## <a name="lock-resources"></a>鎖定資源

鎖定可防止組織中的其他使用者意外刪除或修改關鍵資源，如 Azure 訂閱、資源組或資源。 

以下腳本鎖定存儲帳戶，因此無法刪除該帳戶。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

以下腳本獲取存儲帳戶的所有鎖：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

以下腳本刪除存儲帳戶的鎖：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

有關詳細資訊，請參閱使用[Azure 資源管理器鎖定資源](lock-resources.md)。

## <a name="tag-resources"></a>標記資源

標記有助於以邏輯方式組織資源組和資源。 有關詳細資訊，請參閱[使用標記來組織 Azure 資源](tag-resources.md#powershell)。

## <a name="manage-access-to-resources"></a>管理對資源的訪問

[基於角色的存取控制 （RBAC）](../../role-based-access-control/overview.md)是管理對 Azure 中資源的訪問的方式。 有關詳細資訊，請參閱使用[RBAC 和 Azure PowerShell 管理訪問](../../role-based-access-control/role-assignments-powershell.md)。

## <a name="next-steps"></a>後續步驟

- 要瞭解 Azure 資源管理器，請參閱[Azure 資源管理器概述](overview.md)。
- 要瞭解資源管理器範本語法，請參閱瞭解[Azure 資源管理器範本的結構和語法](../templates/template-syntax.md)。
- 要瞭解如何開發範本，請參閱分[步教程](/azure/azure-resource-manager/)。
- 要查看 Azure 資源管理器範本架構，請參閱[範本引用](/azure/templates/)。
