---
title: 將資源部署到租戶
description: 介紹如何在 Azure 資源管理器範本中的租戶作用域中部署資源。
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: fcdfc5b1c4333a0d7eeec80a09ad85579a1f8b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460257"
---
# <a name="create-resources-at-the-tenant-level"></a>在租戶級別創建資源

隨著組織的成熟，您可能需要跨 Azure AD 租戶定義和分配[策略](../../governance/policy/overview.md)或[基於角色的訪問控制項](../../role-based-access-control/overview.md)。 使用租戶級別範本，您可以聲明性地應用策略並在全域級別分配角色。

## <a name="supported-resources"></a>支援的資源

您可以在租戶級別部署以下資源類型：

* [部署](/azure/templates/microsoft.resources/deployments)- 用於部署到管理組或訂閱的嵌套範本。
* [策略分配](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [角色指派](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>結構描述

用於租戶部署的架構與資源組部署的架構不同。

對於範本，請使用：

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

對於所有部署作用域，參數檔的架構都相同。 對於參數檔，請使用：

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>必需的訪問

部署範本的主體必須具有在租戶作用域中創建資源的許可權。 主體必須具有執行部署操作 （`Microsoft.Resources/deployments/*`） 和創建範本中定義的資源的許可權。 例如，要創建管理組，主體必須在租戶作用域中具有"參與者"許可權。 要創建角色指派，主體必須具有擁有者許可權。

Azure 活動目錄的全域管理員不自動具有分配角色的許可權。 要在租戶範圍內啟用範本部署，全域管理員必須執行以下步驟：

1. 提升帳戶存取權限，以便全域管理員可以分配角色。 有關詳細資訊，請參閱[提升管理所有 Azure 訂閱和管理組的訪問](../../role-based-access-control/elevate-access-global-admin.md)。

1. 將擁有者或參與者分配給需要部署範本的主體。

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

主體現在具有部署範本所需的許可權。

## <a name="deployment-commands"></a>部署命令

租戶部署的命令與資源組部署的命令不同。

對於 Azure CLI，使用[az 部署租戶創建](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)：

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

對於 Azure 電源外殼，請使用[新租戶部署](/powershell/module/az.resources/new-aztenantdeployment)。

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

對於 REST API，請使用[部署 - 在租戶作用域創建或更新](/rest/api/resources/deployments/createorupdateattenantscope)。

## <a name="deployment-location-and-name"></a>部署位置和名稱

對於租戶級別部署，必須為部署提供位置。 部署的位置與部署的資源的位置是分開的。 部署位置指定存儲部署資料的位置。

您可以為部署提供名稱，或使用預設部署名稱。 預設名稱是範本檔的名稱。 例如，部署名為 **azuredeploy.json** 的範本會建立預設的部署名稱 **azuredeploy**。

對於每個部署名稱，位置是不可變的。 當其他位置存在同名的現有部署時，無法在一個位置創建部署。 如果您收到錯誤代碼 `InvalidDeploymentLocation`，請使用不同的名稱或與先前該名稱部署相同的位置。

## <a name="use-template-functions"></a>使用範本函式

對於租戶部署，使用範本函數時需要考慮一些重要事項：

* **不**支援 [resourceGroup()](template-functions-resource.md#resourcegroup) 函式。
* 不支援[訂閱（）](template-functions-resource.md#subscription)功能。 **not**
* 支援 [reference()](template-functions-resource.md#reference) 和 [list()](template-functions-resource.md#list) 函式。
* 使用[租戶 ResourceId（）](template-functions-resource.md#tenantresourceid)函數獲取在租戶級別部署的資源的資源識別碼。

  例如，要獲取策略定義的資源識別碼，請使用：
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  返回的資源識別碼 具有以下格式：
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>建立管理群組

[以下範本](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg)創建管理組。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2019-11-01",
      "name": "[parameters('mgName')]",
      "properties": {
      }
    }
  ]
}
```

## <a name="assign-role"></a>指派角色

[以下範本](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment)在租戶作用域中分配角色。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "principalId if the user that will be given contributor access to the resourceGroup"
      }
    },
    "roleDefinitionId": {
      "type": "string",
      "defaultValue": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
      "metadata": {
        "description": "roleDefinition for the assignment - default is owner"
      }
    }
  },
  "variables": {
    // This creates an idempotent guid for the role assignment
    "roleAssignmentName": "[guid('/', parameters('principalId'), parameters('roleDefinitionId'))]"
  },
  "resources": [
    {
      "name": "[variables('roleAssignmentName')]",
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2019-04-01-preview",
      "properties": {
        "roleDefinitionId": "[tenantResourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "/"
      }
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

* 要瞭解如何分配角色，請參閱使用[RBAC 和 Azure 資源管理器範本管理對 Azure 資源的訪問](../../role-based-access-control/role-assignments-template.md)。
* 您還可以在[訂閱級別](deploy-to-subscription.md)或[管理組級別](deploy-to-management-group.md)部署範本。
