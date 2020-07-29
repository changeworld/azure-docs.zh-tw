---
title: 將資源部署至管理群組
description: 說明如何在 Azure Resource Manager 範本的管理群組範圍中部署資源。
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: a17387aef4d35c042d1fe0b02f1c6fd447e4a918
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321797"
---
# <a name="create-resources-at-the-management-group-level"></a>在管理群組層級建立資源

當您的組織成熟時，您可以部署 Azure Resource Manager 範本（ARM 範本）來建立管理群組層級的資源。 例如，您可能需要為管理群組定義和指派[原則](../../governance/policy/overview.md)或[角色型存取控制](../../role-based-access-control/overview.md)。 您可以使用管理群組層級範本，以宣告方式套用原則，並在管理群組層級指派角色。

## <a name="supported-resources"></a>支援的資源

並非所有的資源類型都可以部署到管理群組層級。 本節列出支援的資源類型。

針對 Azure 藍圖，請使用：

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [版本](/azure/templates/microsoft.blueprint/blueprints/versions)

針對 Azure 原則，請使用：

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

若為角色型存取控制，請使用：

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

針對部署至訂用帳戶或資源群組的嵌套範本，請使用：

* [部署](/azure/templates/microsoft.resources/deployments)

若要管理您的資源，請使用：

* [「標記」](/azure/templates/microsoft.resources/tags)

### <a name="schema"></a>結構描述

您用於管理群組部署的架構與資源群組部署的架構不同。

針對範本，請使用：

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

所有部署範圍的參數檔案結構描述都相同。 針對參數檔案，請使用：

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

對於每個部署名稱而言，此位置是不可變的。 當某個位置已經有名稱相同的現有部署時，您無法在其他位置建立部署。 如果您收到錯誤代碼 `InvalidDeploymentLocation`，請使用不同的名稱或與先前該名稱部署相同的位置。

## <a name="deployment-scopes"></a>部署範圍

部署到管理群組時，您可以將部署命令或租使用者中的其他管理群組中指定的管理群組設為目標。 您也可以將目標設為管理群組內的訂用帳戶或資源群組。 部署範本的使用者必須具有指定範圍的存取權。

在範本的資源區段中定義的資源會套用至部署命令中的管理群組。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        management-group-level-resources
    ],
    "outputs": {}
}
```

若要以另一個管理群組為目標，請新增嵌套部署，並指定 `scope` 屬性。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string"
        }
    },
    "variables": {
        "mgId": "[concat('Microsoft.Management/managementGroups/', parameters('mgName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2019-10-01",
            "name": "nestedDeployment",
            "scope": "[variables('mgId')]",
            "location": "eastus",
            "properties": {
                "mode": "Incremental",
                "template": {
                    nested-template
                }
            }
        }
    ],
    "outputs": {}
}
```

若要將管理群組內的訂用帳戶設為目標，請使用嵌套部署和 `subscriptionId` 屬性。 若要將目標設為該訂用帳戶內的資源群組，請新增另一個嵌套部署和 `resourceGroup` 屬性。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "westus2",
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedRG",
              "resourceGroup": "rg2",
              "properties": {
                "mode": "Incremental",
                "template": {
                  nested-template
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="use-template-functions"></a>使用範本函式

針對管理群組部署，使用範本函式時有一些重要的考慮：

* **不**支援 [resourceGroup()](template-functions-resource.md#resourcegroup) 函式。
* **不**支援 [subscription()](template-functions-resource.md#subscription) 函式。
* 支援 [reference()](template-functions-resource.md#reference) 和 [list()](template-functions-resource.md#list) 函式。
* 支援 [resourceId()](template-functions-resource.md#resourceid) 函式。 使用它來取得用於管理群組層級部署之資源的資源識別碼。 請勿提供資源群組參數的值。

  例如，若要取得原則定義的資源識別碼，使用：
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  傳回的資源識別碼具有下列格式：
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="azure-policy"></a>Azure 原則

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

## <a name="deploy-to-subscription-and-resource-group"></a>部署至訂用帳戶和資源群組

從管理群組層級部署，您可以將目標設為管理群組內的訂用帳戶。 下列範例會在訂用帳戶內建立資源群組，並將儲存體帳戶部署到該資源群組。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nestedsubId": {
      "type": "string"
    },
    "nestedRG": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "nestedLocation": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "[parameters('nestedLocation')]",
      "subscriptionId": "[parameters('nestedSubId')]",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
            {
              "type": "Microsoft.Resources/resourceGroups",
              "apiVersion": "2020-06-01",
              "name": "[parameters('nestedRG')]",
              "location": "[parameters('nestedLocation')]",
            },
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedSubRG",
              "resourceGroup": "[parameters('nestedRG')]",
              "dependsOn": [
                "[parameters('nestedRG')]"
              ],
              "properties": {
                "mode": "Incremental",
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[parameters('storageAccountName')]",
                      "location": "[parameters('nestedLocation')]",
                      "sku": {
                        "name": "Standard_LRS"
                      }
                    }
                  ]
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

* 若要了解如何指派角色，請參閱[使用 RBAC 和 Azure Resource Manager 範本管理對 Azure 資源的存取](../../role-based-access-control/role-assignments-template.md)。
* 如需針對 Azure 資訊安全中心部署工作區設定的範例，請參閱 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)。
* 您也可以部署訂用帳戶[層](deploy-to-subscription.md)級和[租使用者層級](deploy-to-tenant.md)的範本。
