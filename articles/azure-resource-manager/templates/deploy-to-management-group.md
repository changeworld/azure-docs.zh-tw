---
title: 將資源部署至管理群組
description: 說明如何在 Azure Resource Manager 範本的管理群組範圍中部署資源。
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 0419f3daca6845c6809c9f66e870fdf884a7193f
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117050"
---
# <a name="create-resources-at-the-management-group-level"></a>在管理群組層級建立資源

您通常會將 Azure 資源部署到 Azure 訂用帳戶中的資源群組。 不過，您也可以在管理群組層級建立資源。 您可以使用管理群組層級部署來採取對該層級有意義的動作，例如指派[角色型存取控制](../../role-based-access-control/overview.md)或套用[原則](../../governance/policy/overview.md)。

目前，若要在管理群組層級部署範本，您必須使用 REST API。

## <a name="supported-resources"></a>支援的資源

您可以在管理群組層級部署下列資源類型：

* [部署](/azure/templates/microsoft.resources/deployments)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schema

您用於管理群組部署的架構與資源群組部署的架構不同。

針對範本，請使用：

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

針對參數檔案，請使用：

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentParameters.json#
```

## <a name="deployment-commands"></a>部署命令

管理群組部署的命令與資源群組部署的命令不同。

針對 REST API，請使用 [[部署-在管理群組範圍建立](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)]。

## <a name="deployment-location-and-name"></a>部署位置和名稱

針對管理群組層級部署，您必須提供部署的位置。 部署的位置與您部署的資源位置不同。 部署位置會指定部署資料的儲存位置。

您可以提供部署的名稱，或使用預設的部署名稱。 預設名稱是範本檔案的名稱。 例如，部署名為 **azuredeploy.json** 的範本會建立預設的部署名稱 **azuredeploy**。

針對每個部署名稱，此位置是不可變的。 當不同位置有相同名稱的現有部署時，您無法在一個位置建立部署。 如果您收到錯誤代碼 `InvalidDeploymentLocation`，請使用不同的名稱或與先前該名稱部署相同的位置。

## <a name="use-template-functions"></a>使用範本函式

針對管理群組部署，使用範本函式時有一些重要的考慮：

* [不](template-functions-resource.md#resourcegroup)支援 **resourceGroup()** 函式。
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

* 建立資源群組、原則和原則指派。  請參閱 [這裡](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json)。

## <a name="next-steps"></a>後續步驟

* 若要瞭解如何指派角色，請參閱[使用 RBAC 和 Azure Resource Manager 範本來管理 Azure 資源的存取權](../../role-based-access-control/role-assignments-template.md)。
* 如需針對 Azure 資訊安全中心部署工作區設定的範例，請參閱 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)。
* 若要了解如何建立 Azure 資源管理員範本，請參閱 [撰寫範本](template-syntax.md)。
* 如需在範本中可用函式的清單，請參閱 [範本函式](template-functions.md)。