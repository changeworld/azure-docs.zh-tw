---
title: 將資源部署到管理組
description: 介紹如何在 Azure 資源管理器範本中的管理組作用域中部署資源。
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 863d1330412fa238b820eb0f1f05351fc723de6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460308"
---
# <a name="create-resources-at-the-management-group-level"></a>在管理組級別創建資源

隨著組織的成熟，您可能需要為管理組定義和分配[策略](../../governance/policy/overview.md)或[基於角色的存取控制](../../role-based-access-control/overview.md)。 使用管理組級別範本，您可以聲明性地應用策略並在管理組級別分配角色。

## <a name="supported-resources"></a>支援的資源

您可以在管理組級別部署以下資源類型：

* [部署](/azure/templates/microsoft.resources/deployments)- 用於部署到訂閱或資源組的嵌套範本。
* [策略分配](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [角色指派](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>結構描述

用於管理組部署的架構與資源組部署的架構不同。

對於範本，請使用：

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

對於所有部署作用域，參數檔的架構都相同。 對於參數檔，請使用：

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>部署命令

管理組部署的命令與資源組部署的命令不同。

對於 Azure CLI，使用[az 部署 mg 創建](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create)：

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

對於 Azure 電源外殼，請使用[新 Az 管理組部署](/powershell/module/az.resources/new-azmanagementgroupdeployment)。

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

對於 REST API，請使用[部署 - 在管理組作用域創建](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)。

## <a name="deployment-location-and-name"></a>部署位置和名稱

對於管理組級別部署，必須為部署提供位置。 部署的位置與部署的資源的位置是分開的。 部署位置指定存儲部署資料的位置。

您可以為部署提供名稱，或使用預設部署名稱。 預設名稱是範本檔的名稱。 例如，部署名為 **azuredeploy.json** 的範本會建立預設的部署名稱 **azuredeploy**。

對於每個部署名稱，位置是不可變的。 當其他位置存在同名的現有部署時，無法在一個位置創建部署。 如果您收到錯誤代碼 `InvalidDeploymentLocation`，請使用不同的名稱或與先前該名稱部署相同的位置。

## <a name="use-template-functions"></a>使用範本函式

對於管理組部署，使用範本函數時需要考慮一些重要事項：

* **不**支援 [resourceGroup()](template-functions-resource.md#resourcegroup) 函式。
* 不支援[訂閱（）](template-functions-resource.md#subscription)功能。 **not**
* 支援 [reference()](template-functions-resource.md#reference) 和 [list()](template-functions-resource.md#list) 函式。
* 支援 [resourceId()](template-functions-resource.md#resourceid) 函式。 使用它獲取管理組級別部署中使用的資源的資源識別碼。 不為資源組參數提供值。

  例如，要獲取策略定義的資源識別碼，請使用：
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  返回的資源識別碼 具有以下格式：
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-policies"></a>建立原則

### <a name="define-policy"></a>定義策略

下面的示例演示如何在管理組級別[定義](../../governance/policy/concepts/definition-structure.md)策略。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    }
  ]
}
```

### <a name="assign-policy"></a>指派原則

下面的示例將現有策略定義分配給管理組。 如果此原則採用參數，請以物件形式提供參數。 如果此原則不採用參數，請使用預設空白物件。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

## <a name="template-sample"></a>範本示例

* [創建資源組、策略和策略分配](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json)。

## <a name="next-steps"></a>後續步驟

* 要瞭解如何分配角色，請參閱使用[RBAC 和 Azure 資源管理器範本管理對 Azure 資源的訪問](../../role-based-access-control/role-assignments-template.md)。
* 如需針對 Azure 資訊安全中心部署工作區設定的範例，請參閱 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)。
* 您還可以在[訂閱級別](deploy-to-subscription.md)和[租戶級別](deploy-to-tenant.md)部署範本。
