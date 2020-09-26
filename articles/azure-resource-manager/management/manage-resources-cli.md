---
title: 管理資源-Azure CLI
description: 使用 Azure CLI 和 Azure Resource Manager 來管理您的資源。 說明如何部署和刪除資源。
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9111a16a4264c4b225eaf0888fc09bd1bba07ef4
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372114"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>使用 Azure CLI 來管理 Azure 資源

瞭解如何搭配使用 Azure CLI 與 [Azure Resource Manager](overview.md) 來管理您的 Azure 資源。 如需管理資源群組，請參閱 [使用 Azure CLI 管理 Azure 資源群組](manage-resource-groups-cli.md)。

關於管理資源的其他文章：

- [使用 Azure 入口網站來管理 Azure 資源](manage-resources-portal.md)
- [使用 Azure PowerShell 來管理 Azure 資源](manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>將資源部署到現有的資源群組

您可以使用 Azure CLI 直接部署 Azure 資源，或部署 Resource Manager 範本以建立 Azure 資源。

### <a name="deploy-a-resource"></a>部署資源

下列腳本會建立儲存體帳戶。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>部署範本

下列腳本會建立部署快速入門範本來建立儲存體帳戶。 如需詳細資訊，請參閱 [快速入門：使用 Visual Studio Code 建立 Azure Resource Manager 範本](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell)。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

如需詳細資訊，請參閱[使用 Resource Manager 範本與 Azure CLI 來部署資源](../templates/deploy-cli.md)。

## <a name="deploy-a-resource-group-and-resources"></a>部署資源群組和資源

您可以建立資源群組，並將資源部署至群組。 如需詳細資訊，請參閱[建立資源群組並部署資源](../templates/deploy-to-subscription.md#resource-groups)。

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>將資源部署至多個訂用帳戶或資源群組

一般而言，您要將範本中的所有資源部署至單一資源群組。 不過，在某些情況下，您要將一組資源部署在一起，但將它們放在不同的資源群組或訂用帳戶中。 如需詳細資訊，請參閱 [將 Azure 資源部署至多個訂用帳戶或資源群組](../templates/cross-scope-deployment.md)。

## <a name="delete-resources"></a>刪除資源

下列腳本說明如何刪除儲存體帳戶。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

如需 Azure Resource Manager 如何排序資源刪除的詳細資訊，請參閱 [Azure Resource Manager 資源群組刪除](delete-resource-group.md)。

## <a name="move-resources"></a>移動資源

下列腳本示範如何將儲存體帳戶從某個資源群組移至另一個資源群組。

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](move-resource-group-and-subscription.md)。

## <a name="lock-resources"></a>鎖定資源

鎖定可防止您組織中的其他使用者不小心刪除或修改重要資源，例如 Azure 訂用帳戶、資源群組或資源。 

下列腳本會鎖定儲存體帳戶，因此無法刪除帳戶。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

下列腳本會取得儲存體帳戶的所有鎖定：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

下列腳本會刪除儲存體帳戶的鎖定：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

如需詳細資訊，請參閱 [使用 Azure Resource Manager 鎖定資源](lock-resources.md)。

## <a name="tag-resources"></a>標記資源

標記可協助您以邏輯方式組織您的資源群組和資源。 如需詳細資訊，請參閱 [使用標記來組織您的 Azure 資源](tag-resources.md#azure-cli)。

## <a name="manage-access-to-resources"></a>管理資源的存取權

Azure[角色型存取控制 (AZURE RBAC) ](../../role-based-access-control/overview.md)是您管理 azure 中資源存取權的方式。 如需詳細資訊，請參閱 [使用 Azure CLI 新增或移除 Azure 角色指派](../../role-based-access-control/role-assignments-cli.md)。

## <a name="next-steps"></a>後續步驟

- 若要瞭解 Azure Resource Manager，請參閱 [Azure Resource Manager 總覽](overview.md)。
- 若要瞭解 Resource Manager 範本語法，請參閱 [瞭解 Azure Resource Manager 範本的結構和語法](../templates/template-syntax.md)。
- 若要瞭解如何開發範本，請參閱 [逐步教學](../index.yml)課程。
- 若要查看 Azure Resource Manager 範本架構，請參閱 [範本參考](/azure/templates/)。
