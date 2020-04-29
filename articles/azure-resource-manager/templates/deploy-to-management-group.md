---
title: 將資源部署至管理群組
description: 說明如何在 Azure Resource Manager 範本的管理群組範圍中部署資源。
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 863d1330412fa238b820eb0f1f05351fc723de6f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79460308"
---
# <a name="create-resources-at-the-management-group-level"></a>在管理群組層級建立資源

當您的組織成熟時，您可能需要為管理群組定義和指派[原則](../../governance/policy/overview.md)或[角色型存取控制](../../role-based-access-control/overview.md)。 您可以使用管理群組層級範本，以宣告方式套用原則，並在管理群組層級指派角色。

## <a name="supported-resources"></a>支援的資源

您可以在管理群組層級部署下列資源類型：

* [部署](/azure/templates/microsoft.resources/deployments)-適用于部署至訂用帳戶或資源群組的嵌套範本。
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>結構描述

您用於管理群組部署的架構與資源群組部署的架構不同。

針對範本，請使用：

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

所有部署範圍的參數檔案架構都相同。 針對參數檔案，請使用：

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>部署命令

管理群組部署的命令與資源群組部署的命令不同。

針對 Azure CLI，請使用[az deployment mg create](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create)：

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

針對 Azure PowerShell，請使用[AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment)。

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

針對 REST API，請使用 [[部署-在管理群組範圍建立](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)]。

## <a name="deployment-location-and-name"></a>部署位置和名稱

針對管理群組層級部署，您必須提供部署的位置。 部署的位置與您部署的資源位置不同。 部署位置會指定部署資料的儲存位置。

您可以提供部署的名稱，或使用預設的部署名稱。 預設名稱是範本檔案的名稱。 例如，部署名為 **azuredeploy.json** 的範本會建立預設的部署名稱 **azuredeploy**。

針對每個部署名稱，此位置是不可變的。 當不同位置有相同名稱的現有部署時，您無法在一個位置建立部署。 如果您收到錯誤代碼 `InvalidDeploymentLocation`，請使用不同的名稱或與先前該名稱部署相同的位置。

## <a name="use-template-functions"></a>使用範本函式

針對管理群組部署，使用範本函式時有一些重要的考慮：

* **不**支援 [resourceGroup()](template-functions-resource.md#resourcegroup) 函式。
* **不**支援[訂閱（）](template-functions-resource.md#subscription)函數。
* 支援 [reference()](template-functions-resource.md#reference) 和 [list()](template-functions-resource.md#list) 函式。
* 支援 [resourceId()](template-functions-resource.md#resourceid) 函式。 使用它來取得用於管理群組層級部署之資源的資源識別碼。 請勿提供資源群組參數的值。

  例如，若要取得原則定義的資源識別碼，請使用：
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  傳回的資源識別碼具有下列格式：
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-policies"></a>建立原則

### <a name="define-policy"></a>定義原則

下列範例顯示如何在管理群組層級[定義](../../governance/policy/concepts/definition-structure.md)原則。

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

下列範例會將現有的原則定義指派給管理群組。 如果此原則採用參數，請以物件形式提供參數。 如果此原則不採用參數，請使用預設空白物件。

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

## <a name="template-sample"></a>範本範例

* [建立資源群組、原則和原則指派](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json)。

## <a name="next-steps"></a>後續步驟

* 若要瞭解如何指派角色，請參閱[使用 RBAC 和 Azure Resource Manager 範本來管理 Azure 資源的存取權](../../role-based-access-control/role-assignments-template.md)。
* 如需針對 Azure 資訊安全中心部署工作區設定的範例，請參閱 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)。
* 您也可以部署訂用帳戶[層](deploy-to-subscription.md)級和[租使用者層級](deploy-to-tenant.md)的範本。
