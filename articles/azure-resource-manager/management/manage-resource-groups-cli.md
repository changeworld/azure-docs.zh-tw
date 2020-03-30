---
title: 管理資源組 - Azure CLI
description: 使用 Azure CLI 通過 Azure 資源管理器管理資源組。 演示如何創建、列出和刪除資源組。
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 7face572f545153ea92efbdb345bbaabda5dd126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248329"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>使用 Azure CLI 管理 Azure 資源管理器資源管理器資源組

瞭解如何將 Azure CLI 與[Azure 資源管理器](overview.md)一起管理 Azure 資源組。 有關管理 Azure 資源，請參閱[使用 Azure CLI 管理 Azure 資源](manage-resources-cli.md)。

有關管理資源組的其他文章：

- [使用 Azure 門戶管理 Azure 資源組](manage-resources-portal.md)
- [使用 Azure PowerShell 管理 Azure 資源組](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>什麼是資源組

資源群組是存放 Azure 方案相關資源的容器。 資源群組可以包含方案的所有資源，或只包含您要以群組方式管理的資源。 您可決定如何根據對組織最有利的方式，將資源配置到資源群組。 一般而言，會新增共用相同生命週期的資源到相同資源群組，因此您可以以群組為單位輕鬆地部署、更新、刪除它們。

資源群組會儲存資源相關中繼資料。 因此，當您指定資源群組的位置時，您便是指定中繼資料的儲存位置。 基於相容性理由，您可能需要確保您的資料存放在特定區域中。

資源群組會儲存資源相關中繼資料。 當您指定資源群組的位置時，您便是指定中繼資料的儲存位置。

## <a name="create-resource-groups"></a>建立資源群組

以下 CLI 腳本創建資源組，然後顯示資源組。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>列出資源群組

以下 CLI 腳本列出了訂閱下的資源組。

```azurecli-interactive
az group list
```

要獲取一個資源組，可以：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>刪除資源群組

以下 CLI 腳本將刪除資源組：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

有關 Azure 資源管理器如何命令刪除資源的詳細資訊，請參閱 Azure[資源管理器資源組刪除](delete-resource-group.md)。

## <a name="deploy-resources-to-an-existing-resource-group"></a>將資源部署到現有資源組

請參閱[將資源部署到現有資源組](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group)。

## <a name="deploy-a-resource-group-and-resources"></a>部署資源組和資源

可以使用資源管理器範本創建資源組並將資源部署到該組。 如需詳細資訊，請參閱[建立資源群組並部署資源](../templates/deploy-to-subscription.md#resource-group-and-resources)。

## <a name="redeploy-when-deployment-fails"></a>部署失敗時重新部署

此功能也稱為*錯誤回滾*。 有關詳細資訊，請參閱[部署失敗時重新部署](../templates/rollback-on-error.md)。

## <a name="move-to-another-resource-group-or-subscription"></a>移動到其他資源組或訂閱

您可以將組中的資源移動到其他資源組。 有關詳細資訊，請參閱[移動資源](manage-resources-cli.md#move-resources)。

## <a name="lock-resource-groups"></a>鎖定資源組

鎖定可防止組織中的其他使用者意外刪除或修改關鍵資源，如 Azure 訂閱、資源組或資源。 

以下腳本鎖定資源組，以便無法刪除資源組。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

以下腳本獲取資源組的所有鎖：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

以下腳本刪除鎖：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

有關詳細資訊，請參閱使用[Azure 資源管理器鎖定資源](lock-resources.md)。

## <a name="tag-resource-groups"></a>標記資源組

您可以將標籤套用至資源群組和資源，以便以邏輯方式組織您的資產。 有關詳細資訊，請參閱[使用標記來組織 Azure 資源](tag-resources.md#azure-cli)。

## <a name="export-resource-groups-to-templates"></a>將資源組匯出到範本

成功設置資源組後，可能需要查看資源組的資源管理器範本。 匯出此範本有兩個優點︰

- 自動執行解決方案的未來部署，因為範本包含所有完整的基礎結構。
- 通過查看表示解決方案的 JavaScript 物件標記法 （JSON） 來瞭解範本語法。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

腳本在主控台上顯示範本。  複製 JSON，並儲存為檔案。

匯出範本功能不支援匯出 Azure 資料工廠資源。 要瞭解如何匯出資料工廠資源，請參閱在 Azure[資料工廠中複製或克隆資料工廠](https://aka.ms/exportTemplateViaAdf)。

要匯出通過經典部署模型創建的資源，必須[將它們遷移到資源管理器部署模型](https://aka.ms/migrateclassicresourcetoarm)。

有關詳細資訊，請參閱[Azure 門戶中的單資源和多資源匯出到範本](../templates/export-template-portal.md)。

## <a name="manage-access-to-resource-groups"></a>管理對資源組的訪問

[基於角色的存取控制 （RBAC）](../../role-based-access-control/overview.md)是管理對 Azure 中資源的訪問的方式。 有關詳細資訊，請參閱使用[RBAC 和 Azure CLI 管理訪問](../../role-based-access-control/role-assignments-cli.md)。

## <a name="next-steps"></a>後續步驟

- 要瞭解 Azure 資源管理器，請參閱[Azure 資源管理器概述](overview.md)。
- 要瞭解資源管理器範本語法，請參閱瞭解[Azure 資源管理器範本的結構和語法](../templates/template-syntax.md)。
- 要瞭解如何開發範本，請參閱分[步教程](/azure/azure-resource-manager/)。
- 要查看 Azure 資源管理器範本架構，請參閱[範本引用](/azure/templates/)。