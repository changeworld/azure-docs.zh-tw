---
title: 管理資源群組-Azure CLI
description: 使用 Azure CLI 透過 Azure Resource Manager 管理您的資源群組。 說明如何建立、列出和刪除資源群組。
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: d8cf93d8945d6597f3c7276e83df0b837490c57d
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326846"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>使用 Azure CLI 管理 Azure Resource Manager 資源群組

瞭解如何搭配[Azure Resource Manager](overview.md)使用 Azure CLI 來管理您的 Azure 資源群組。 若要管理 Azure 資源，請參閱[使用 Azure CLI 來管理 azure 資源](manage-resources-cli.md)。

關於管理資源群組的其他文章：

- [使用 Azure 入口網站來管理 Azure 資源群組](manage-resources-portal.md)
- [使用 Azure PowerShell 來管理 Azure 資源群組](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>什麼是資源群組

資源群組是存放 Azure 方案相關資源的容器。 資源群組可以包含方案的所有資源，或只包含您要以群組方式管理的資源。 您可決定如何根據對組織最有利的方式，將資源配置到資源群組。 一般而言，會新增共用相同生命週期的資源到相同資源群組，因此您可以以群組為單位輕鬆地部署、更新、刪除它們。

資源群組會儲存資源相關中繼資料。 因此，當您指定資源群組的位置時，您便是指定中繼資料的儲存位置。 基於相容性理由，您可能需要確保您的資料存放在特定區域中。

資源群組會儲存資源相關中繼資料。 當您指定資源群組的位置時，您便是指定中繼資料的儲存位置。

## <a name="create-resource-groups"></a>建立資源群組

下列 CLI 腳本會建立資源群組，然後顯示資源群組。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>列出資源群組

下列 CLI 腳本會列出您訂用帳戶下的資源群組。

```azurecli-interactive
az group list
```

若要取得一個資源群組：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>刪除資源群組

下列 CLI 腳本會刪除資源群組：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

如需 Azure Resource Manager 如何排序資源刪除的詳細資訊，請參閱[Azure Resource Manager 資源群組刪除](delete-resource-group.md)。

## <a name="deploy-resources-to-an-existing-resource-group"></a>將資源部署至現有的資源群組

請參閱[將資源部署至現有的資源群組](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group)。

## <a name="deploy-a-resource-group-and-resources"></a>部署資源群組和資源

您可以使用 Resource Manager 範本來建立資源群組，並將資源部署到群組。 如需詳細資訊，請參閱[建立資源群組並部署資源](../templates/deploy-to-subscription.md#resource-groups)。

## <a name="redeploy-when-deployment-fails"></a>部署失敗時重新部署

這項功能也稱為「*發生錯誤時復原*」。 如需詳細資訊，請參閱[部署失敗時重新部署](../templates/rollback-on-error.md)。

## <a name="move-to-another-resource-group-or-subscription"></a>移至另一個資源群組或訂用帳戶

您可以將群組中的資源移至另一個資源群組。 如需詳細資訊，請參閱[移動資源](manage-resources-cli.md#move-resources)。

## <a name="lock-resource-groups"></a>鎖定資源群組

鎖定可防止您組織中的其他使用者不小心刪除或修改重要資源，例如 Azure 訂用帳戶、資源群組或資源。 

下列腳本會鎖定資源群組，因此無法刪除資源群組。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

下列腳本會取得資源群組的所有鎖定：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

下列腳本會刪除鎖定：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

如需詳細資訊，請參閱[使用 Azure Resource Manager 鎖定資源](lock-resources.md)。

## <a name="tag-resource-groups"></a>標記資源群組

您可以將標籤套用至資源群組和資源，以便以邏輯方式組織您的資產。 如需相關資訊，請參閱[使用標記來組織您的 Azure 資源](tag-resources.md#azure-cli)。

## <a name="export-resource-groups-to-templates"></a>將資源群組匯出至範本

成功設定資源群組之後，您可能會想要查看資源群組的 Resource Manager 範本。 匯出此範本有兩個優點︰

- 將解決方案的未來部署自動化，因為範本包含所有完整的基礎結構。
- 查看代表您的解決方案的 JavaScript 物件標記法（JSON）來瞭解範本語法。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

腳本會在主控台上顯示範本。  複製 JSON，並儲存為檔案。

「匯出範本」功能不支援匯出 Azure Data Factory 資源。 若要瞭解如何匯出 Data Factory 資源，請參閱[在 Azure Data Factory 中複製或複製資料](https://aka.ms/exportTemplateViaAdf)處理站。

若要匯出透過傳統部署模型建立的資源，您必須將[它們遷移至 Resource Manager 部署模型](https://aka.ms/migrateclassicresourcetoarm)。

如需詳細資訊，請參閱[Azure 入口網站中的單一和多資源匯出至範本](../templates/export-template-portal.md)。

## <a name="manage-access-to-resource-groups"></a>管理資源群組的存取權

[角色型存取控制（RBAC）](../../role-based-access-control/overview.md)是您在 Azure 中管理資源存取權的方式。 如需詳細資訊，請參閱[使用 RBAC 和 Azure CLI 來管理存取權](../../role-based-access-control/role-assignments-cli.md)。

## <a name="next-steps"></a>後續步驟

- 若要瞭解 Azure Resource Manager，請參閱[Azure Resource Manager 總覽](overview.md)。
- 若要瞭解 Resource Manager 範本語法，請參閱[瞭解 Azure Resource Manager 範本的結構和語法](../templates/template-syntax.md)。
- 若要瞭解如何開發範本，請參閱[逐步教學](../index.yml)課程。
- 若要查看 Azure Resource Manager 範本架構，請參閱[範本參考](/azure/templates/)。
