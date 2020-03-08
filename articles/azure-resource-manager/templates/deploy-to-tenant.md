---
title: 將資源部署到租使用者
description: 說明如何在 Azure Resource Manager 範本的租使用者範圍中部署資源。
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: d63697f3c140b5ad374607f1ecb00dad20e697de
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899135"
---
# <a name="create-resources-at-the-tenant-level"></a>在租使用者層級建立資源

您通常會將 Azure 資源部署到 Azure 訂用帳戶中的資源群組。 不過，您也可以在下列位置建立資源：

* [訂用帳戶層級](deploy-to-subscription.md)
* [管理群組層級](deploy-to-management-group.md)
* 租使用者層級（本文涵蓋）

您可以使用租使用者層級部署來採取對該層級有意義的動作，例如指派[角色型存取控制](../../role-based-access-control/overview.md)或套用[原則](../../governance/policy/overview.md)。

## <a name="supported-resources"></a>支援的資源

您可以在租使用者層級部署下列資源類型：

* [部署](/azure/templates/microsoft.resources/deployments)-適用于部署至管理群組或訂用帳戶的嵌套範本。
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schema

您用於租使用者部署的架構與資源群組部署的架構不同。

針對範本，請使用：

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

針對參數檔案，請使用：

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentParameters.json#
```

## <a name="required-access"></a>必要的存取權

部署範本的主體必須具有在租使用者範圍建立資源的許可權。 主體必須擁有執行部署動作（`Microsoft.Resources/deployments/*`）的許可權，並建立在範本中定義的資源。 例如，若要建立管理群組，主體必須具有租使用者範圍的「參與者」許可權。 若要建立角色指派，主體必須具有 [擁有者] 許可權。

Azure Active Directory 的全域管理員不會自動擁有指派角色的許可權。 若要在租使用者範圍啟用範本部署，全域管理員必須執行下列步驟：

1. 提升帳戶存取權，讓全域管理員可以指派角色。 如需詳細資訊，請參閱提高[存取權以管理所有 Azure 訂用帳戶和管理群組](../../role-based-access-control/elevate-access-global-admin.md)。

1. 將擁有者或參與者指派給需要部署範本的主體。

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

主體現在具有部署範本的必要許可權。

## <a name="deployment-commands"></a>部署命令

租使用者部署的命令與資源群組部署的命令不同。

針對 Azure PowerShell，請使用[AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment)。

```azurepowershell-interactive
New-AzTenantDeployment `
  -Location "West US" `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json
```

針對 REST API，請使用 [[部署-在租使用者範圍建立或更新](/rest/api/resources/deployments/createorupdateattenantscope)]。

## <a name="deployment-location-and-name"></a>部署位置和名稱

針對租使用者層級部署，您必須提供部署的位置。 部署的位置與您部署的資源位置不同。 部署位置會指定部署資料的儲存位置。

您可以提供部署的名稱，或使用預設的部署名稱。 預設名稱是範本檔案的名稱。 例如，部署名為 **azuredeploy.json** 的範本會建立預設的部署名稱 **azuredeploy**。

針對每個部署名稱，此位置是不可變的。 當不同位置有相同名稱的現有部署時，您無法在一個位置建立部署。 如果您收到錯誤代碼 `InvalidDeploymentLocation`，請使用不同的名稱或與先前該名稱部署相同的位置。

## <a name="use-template-functions"></a>使用範本函式

針對租使用者部署，使用範本函式時有一些重要的考慮：

* [不](template-functions-resource.md#resourcegroup)支援 **resourceGroup()** 函式。
* **不**支援[訂閱（）](template-functions-resource.md#subscription)函數。
* 支援 [reference()](template-functions-resource.md#reference) 和 [list()](template-functions-resource.md#list) 函式。
* 使用[tenantResourceId （）](template-functions-resource.md#tenantresourceid)函式來取得在租使用者層級部署之資源的資源識別碼。

  例如，若要取得原則定義的資源識別碼，請使用：
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  傳回的資源識別碼具有下列格式：
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>建立管理群組

[下列範本](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg)會建立管理群組。

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

[下列範本](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment)會在租使用者範圍指派角色。

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

* 若要瞭解如何指派角色，請參閱[使用 RBAC 和 Azure Resource Manager 範本來管理 Azure 資源的存取權](../../role-based-access-control/role-assignments-template.md)。
* 若要了解如何建立 Azure 資源管理員範本，請參閱 [撰寫範本](template-syntax.md)。
* 如需在範本中可用函式的清單，請參閱 [範本函式](template-functions.md)。