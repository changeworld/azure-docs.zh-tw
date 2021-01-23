---
title: 管理資源群組-Azure PowerShell
description: 使用 Azure PowerShell 透過 Azure Resource Manager 管理您的資源群組。 說明如何建立、列出和刪除資源群組。
author: mumian
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: jgao
ms.openlocfilehash: fb8cd45842e2f9b64048aa939f7b6adc9b0e6e0a
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703677"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>使用 Azure PowerShell 管理 Azure Resource Manager 資源群組

瞭解如何搭配使用 Azure PowerShell 與 [Azure Resource Manager](overview.md) 來管理您的 Azure 資源群組。 若要管理 Azure 資源，請參閱 [使用 Azure PowerShell 管理 azure 資源](manage-resources-powershell.md)。

關於管理資源群組的其他文章：

- [使用 Azure 入口網站來管理 Azure 資源群組](manage-resources-portal.md)
- [使用 Azure CLI 來管理 Azure 資源群組](manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>什麼是資源群組

資源群組是存放 Azure 方案相關資源的容器。 資源群組可以包含方案的所有資源，或只包含您要以群組方式管理的資源。 您可決定如何根據對組織最有利的方式，將資源配置到資源群組。 一般而言，會新增共用相同生命週期的資源到相同資源群組，因此您可以以群組為單位輕鬆地部署、更新、刪除它們。

資源群組會儲存資源相關中繼資料。 因此，當您指定資源群組的位置時，即是指定中繼資料的儲存位置。 基於合規性，您可能需要確保資料存放在特定區域中。

資源群組會儲存資源相關中繼資料。 當您指定資源群組的位置時，您便是指定中繼資料的儲存位置。

## <a name="create-resource-groups"></a>建立資源群組

下列 PowerShell 腳本會建立資源群組。

```azurepowershell-interactive
New-AzResourceGroup -Name demoResourceGroup -Location westus
```

## <a name="list-resource-groups"></a>列出資源群組

下列 PowerShell 腳本會列出您訂用帳戶下的資源群組。

```azurepowershell-interactive
Get-AzResourceGroup
```

若要取得一個資源群組：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>刪除資源群組

下列 PowerShell 腳本會刪除資源群組：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

如需 Azure Resource Manager 如何排序資源刪除的詳細資訊，請參閱 [Azure Resource Manager 資源群組刪除](delete-resource-group.md)。

## <a name="deploy-resources-to-an-existing-resource-group"></a>將資源部署到現有的資源群組

請參閱 [將資源部署到現有的資源群組](manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group)。

若要驗證資源群組部署，請參閱 [>new-azresourcegroupdeployment](/powershell/module/Az.Resources/Test-AzResourceGroupDeployment)。

## <a name="deploy-a-resource-group-and-resources"></a>部署資源群組和資源

您可以使用 Resource Manager 範本來建立資源群組，並將資源部署至群組。 如需詳細資訊，請參閱[建立資源群組並部署資源](../templates/deploy-to-subscription.md#resource-groups)。

## <a name="redeploy-when-deployment-fails"></a>部署失敗時重新部署

這項功能也稱為「 *發生錯誤時復原*」。 如需詳細資訊，請參閱 [部署失敗時重新部署](../templates/rollback-on-error.md)。

## <a name="move-to-another-resource-group-or-subscription"></a>移至另一個資源群組或訂用帳戶

您可以將群組中的資源移至另一個資源群組。 如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](move-resource-group-and-subscription.md)。

## <a name="lock-resource-groups"></a>鎖定資源群組

鎖定可防止您組織中的其他使用者不小心刪除或修改重要資源，例如 Azure 訂用帳戶、資源群組或資源。 

下列腳本會鎖定資源群組，因此無法刪除資源群組。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

下列腳本會取得資源群組的所有鎖定：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

如需詳細資訊，請參閱 [使用 Azure Resource Manager 鎖定資源](lock-resources.md)。

## <a name="tag-resource-groups"></a>標記資源群組

您可以將標籤套用至資源群組和資源，以便以邏輯方式組織您的資產。 如需詳細資訊，請參閱 [使用標記來組織您的 Azure 資源](tag-resources.md#powershell)。

## <a name="export-resource-groups-to-templates"></a>將資源群組匯出至範本

設定資源群組之後，您可以查看資源群組的 Resource Manager 範本。 匯出此範本有兩個優點︰

- 將解決方案的未來部署自動化，因為範本包含完整的基礎結構。
- 藉由查看代表您解決方案的 JavaScript 物件標記法 (JSON) 來學習範本語法。

若要匯出資源群組中的所有資源，請使用 [>new-azresourcegroup](/powershell/module/az.resources/Export-AzResourceGroup) Cmdlet，並提供資源組名。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

它會將範本儲存為本機檔案。

您可以選取要匯出的資源，而不是匯出資源群組中的所有資源。

若要匯出一個資源，請傳遞該資源識別碼。

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

若要匯出一個以上的資源，請傳遞陣列中的資源識別碼。

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

匯出範本時，您可以指定是否要在範本中使用參數。 預設會包含資源名稱的參數，但它們沒有預設值。 您必須在部署期間傳遞該參數值。

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

在資源中，參數是用來做為名稱。

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

如果您在 `-IncludeParameterDefaultValue` 匯出範本時使用參數，則範本參數會包含預設值，此值會設定為目前的值。 您可以使用該預設值，或藉由傳入不同的值來覆寫預設值。

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

如果您在 `-SkipResourceNameParameterization` 匯出範本時使用參數，則範本中不會包含資源名稱的參數。 相反地，資源名稱會直接在資源上設定為其目前的值。 您無法在部署期間自訂名稱。

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

匯出範本功能不支援匯出 Azure Data Factory 資源。 若要瞭解如何匯出 Data Factory 資源，請參閱 [Azure Data Factory 中的複製或複製 Data Factory](../../data-factory/copy-clone-data-factory.md)。

若要匯出透過傳統部署模型建立的資源，您必須將 [它們遷移至 Resource Manager 部署模型](../../virtual-machines/migration-classic-resource-manager-overview.md)。

如需詳細資訊，請參閱 [Azure 入口網站中的單一和多重資源匯出至範本](../templates/export-template-portal.md)。

## <a name="manage-access-to-resource-groups"></a>管理資源群組的存取權

Azure[角色型存取控制 (AZURE RBAC) ](../../role-based-access-control/overview.md)是您管理 azure 中資源存取權的方式。 如需詳細資訊，請參閱 [使用 Azure PowerShell 新增或移除 Azure 角色指派](../../role-based-access-control/role-assignments-powershell.md)。

## <a name="next-steps"></a>後續步驟

- 若要瞭解 Azure Resource Manager，請參閱 [Azure Resource Manager 總覽](overview.md)。
- 若要瞭解 Resource Manager 範本語法，請參閱 [瞭解 Azure Resource Manager 範本的結構和語法](../templates/template-syntax.md)。
- 若要瞭解如何開發範本，請參閱 [逐步教學](../index.yml)課程。
- 若要查看 Azure Resource Manager 範本架構，請參閱 [範本參考](/azure/templates/)。