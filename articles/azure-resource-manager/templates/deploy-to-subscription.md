---
title: 將資源部署到訂閱
description: 描述如何在 Azure Resource Manager 範本中建立資源群組。 此外也會說明如何將資源部署到 Azure 訂用帳戶範圍。
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 65cc220d32d1e1149b7026fc438f5e34262511dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131965"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>在訂用帳戶層級建立資源群組和資源

為了簡化 Azure 訂閱中資源的管理，可以跨訂閱定義和分配[策略](../../governance/policy/overview.md)或[基於角色的訪問控制項](../../role-based-access-control/overview.md)。 使用訂閱級別範本，您可以聲明性地應用策略並在訂閱中分配角色。 您還可以創建資源組並部署資源。

要在訂閱級別部署範本，請使用 Azure CLI、PowerShell 或 REST API。 Azure 入口網站不支援在訂用帳戶層級進行部署。

## <a name="supported-resources"></a>支援的資源

您可以在訂閱級別部署以下資源類型：

* [預算](/azure/templates/microsoft.consumption/budgets)
* [部署](/azure/templates/microsoft.resources/deployments)- 用於部署到資源組的嵌套範本。
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [對等Asns](/azure/templates/microsoft.peering/peerasns)
* [策略分配](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [補救](/azure/templates/microsoft.policyinsights/remediations)
* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)
* [角色指派](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)
* [範圍分配](/azure/templates/microsoft.managednetwork/scopeassignments)
* [支援計畫類型](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [「標記」](/azure/templates/microsoft.resources/tags)

### <a name="schema"></a>結構描述

用於訂閱級部署的架構與資源組部署的架構不同。

對於範本，請使用：

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

對於所有部署作用域，參數檔的架構都相同。 對於參數檔，請使用：

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>部署命令

訂閱級部署的命令與資源組部署的命令不同。

對於 Azure CLI，使用[az 部署子創建](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create)。 以下示例部署範本以創建資源組：

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

對於 PowerShell 部署命令，請使用["新部署"](/powershell/module/az.resources/new-azdeployment)或 **"新訂閱部署**"。 以下示例部署範本以創建資源組：

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

對於 REST API，請使用[部署 - 在訂閱範圍內創建](/rest/api/resources/deployments/createorupdateatsubscriptionscope)。

## <a name="deployment-location-and-name"></a>部署位置和名稱

對於訂閱級別部署，必須為部署提供位置。 部署的位置與部署的資源的位置是分開的。 部署位置指定存儲部署資料的位置。

您可以為部署提供名稱，或使用預設部署名稱。 預設名稱是範本檔的名稱。 例如，部署名為 **azuredeploy.json** 的範本會建立預設的部署名稱 **azuredeploy**。

對於每個部署名稱，位置是不可變的。 當其他位置存在同名的現有部署時，無法在一個位置創建部署。 如果您收到錯誤代碼 `InvalidDeploymentLocation`，請使用不同的名稱或與先前該名稱部署相同的位置。

## <a name="use-template-functions"></a>使用範本函式

針對訂用帳戶層級部署，使用範本函式時有一些重要考量：

* **不**支援 [resourceGroup()](template-functions-resource.md#resourcegroup) 函式。
* 支援 [reference()](template-functions-resource.md#reference) 和 [list()](template-functions-resource.md#list) 函式。
* 使用[訂閱 ResourceId（）](template-functions-resource.md#subscriptionresourceid)函數獲取在訂閱級別部署的資源的資源識別碼。

  例如，要獲取策略定義的資源識別碼，請使用：
  
  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```
  
  返回的資源識別碼 具有以下格式：

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-resource-groups"></a>建立資源群組

若要在 Azure Resource Manager 範本中建立資源群組，請搭配資源群組的名稱和位置定義 [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) 資源。 您可以建立資源群組，並將資源部署至相同範本中的該資源群組。

下列範本會建立空的資源群組。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

搭配資源群組使用 [copy 元素](copy-resources.md)來建立一個以上的資源群組。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgNamePrefix": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "instanceCount": {
      "type": "int"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "location": "[parameters('rgLocation')]",
      "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
      "copy": {
        "name": "rgCopy",
        "count": "[parameters('instanceCount')]"
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

有關資源反覆運算的資訊，請參閱[在 Azure 資源管理器範本中部署多個資源實例](./copy-resources.md)，以及[教程：使用資源管理器範本創建多個資源實例](./template-tutorial-create-multiple-instances.md)。

## <a name="resource-group-and-resources"></a>資源組和資源

若要建立資源群組並對它部署資源，請使用巢狀範本。 巢狀範本能定義要部署至該資源群組的資源。 將巢狀範本設定為資源群組的相依項目，以確保該資源群組在部署資源之前確實存在。

下列範例會建立資源群組，並將儲存體帳戶部署至該資源群組。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "location": "[parameters('rgLocation')]",
      "name": "[parameters('rgName')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2017-10-01",
              "name": "[variables('storageName')]",
              "location": "[parameters('rgLocation')]",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="create-policies"></a>建立原則

### <a name="assign-policy"></a>指派原則

下列範例會將現有原則定義指派給訂用帳戶。 如果此原則採用參數，請以物件形式提供參數。 如果此原則不採用參數，請使用預設空白物件。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
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
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

若要使用 Azure CLI 部署此範本，請使用：

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

若要使用 PowerShell 部署此範本，請使用：

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>定義及指派原則

您可以在相同的範本[定義](../../governance/policy/concepts/definition-structure.md)和指派原則。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
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
    },
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-05-01",
      "name": "location-lock",
      "dependsOn": [
        "locationpolicy"
      ],
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

若要在訂用帳戶中建立原則定義，並將它套用至訂用帳戶，請使用下列 CLI 命令：

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

若要使用 PowerShell 部署此範本，請使用：

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="template-samples"></a>範本範例

* [創建資源組，將其鎖定，並授予其許可權](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)。
* [創建資源組、策略和策略分配](https://github.com/Azure/azure-docs-json-samples/blob/master/subscription-level-deployment/azuredeploy.json)。

## <a name="next-steps"></a>後續步驟

* 要瞭解如何分配角色，請參閱使用[RBAC 和 Azure 資源管理器範本管理對 Azure 資源的訪問](../../role-based-access-control/role-assignments-template.md)。
* 如需針對 Azure 資訊安全中心部署工作區設定的範例，請參閱 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)。
* 示例範本可在[GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments)找到。
* 您還可以在[管理組級別](deploy-to-management-group.md)和[租戶級別](deploy-to-tenant.md)部署範本。
