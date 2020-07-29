---
title: 將資源部署至訂用帳戶
description: 描述如何在 Azure Resource Manager 範本中建立資源群組。 此外也會說明如何將資源部署到 Azure 訂用帳戶範圍。
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: a4e21f29762a30baec8d5cf6e3914da2b5faadeb
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321763"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>在訂用帳戶層級建立資源群組和資源

若要簡化資源的管理，您可以使用 Azure Resource Manager 範本（ARM 範本），在您的 Azure 訂用帳戶層級部署資源。 例如，您可以將[原則](../../governance/policy/overview.md)和以[角色為基礎的存取控制](../../role-based-access-control/overview.md)部署至您的訂用帳戶，這會在您的訂用帳戶中加以套用。 您也可以在訂用帳戶內建立資源群組，並將資源部署至訂用帳戶中的資源群組。

> [!NOTE]
> 您可以在訂用帳戶層級部署中部署最多 800 個不同的資源群組。

若要在訂用帳戶層級部署範本，請使用 Azure CLI、PowerShell、REST API 或入口網站。

## <a name="supported-resources"></a>支援的資源

並非所有的資源類型都可以部署至訂用帳戶層級。 本節列出支援的資源類型。

針對 Azure 藍圖，請使用：

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [版本（藍圖）](/azure/templates/microsoft.blueprint/blueprints/versions)

針對 Azure 原則，請使用：

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

若為角色型存取控制，請使用：

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

針對部署至資源群組的嵌套範本，請使用：

* [部署](/azure/templates/microsoft.resources/deployments)

若要建立新的資源群組，請使用：

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

若要管理您的訂用帳戶，請使用：

* [budgets](/azure/templates/microsoft.consumption/budgets)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [「標記」](/azure/templates/microsoft.resources/tags)

其他支援的類型包括：

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

### <a name="schema"></a>結構描述

您用於訂用帳戶層級部署的結構描述與用於資源群組部署的結構描述不同。

針對範本，請使用：

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

所有部署範圍的參數檔案結構描述都相同。 針對參數檔案，請使用：

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>部署命令

用於訂用帳戶層級部署的命令與用於資源群組部署的命令不同。

針對 Azure CLI，請使用 [az deployment sub create](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create)。 下列範例會部署範本來建立資源群組：

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

針對 PowerShell 部署命令，請使用 [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) 或 **New-AzSubscriptionDeployment**。 下列範例會部署範本來建立資源群組：

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

針對 REST API，請使用[部署 - 在訂用帳戶範圍上建立](/rest/api/resources/deployments/createorupdateatsubscriptionscope)。

## <a name="deployment-location-and-name"></a>部署位置和名稱

針對訂用帳戶層級部署，您必須提供部署的位置。 部署的位置與您部署的資源位置不同。 部署位置會指定部署資料的儲存位置。

您可以提供部署的名稱，或使用預設的部署名稱。 預設名稱是範本檔案的名稱。 例如，部署名為 **azuredeploy.json** 的範本會建立預設的部署名稱 **azuredeploy**。

對於每個部署名稱而言，此位置是不可變的。 當某個位置已經有名稱相同的現有部署時，您無法在其他位置建立部署。 如果您收到錯誤代碼 `InvalidDeploymentLocation`，請使用不同的名稱或與先前該名稱部署相同的位置。

## <a name="deployment-scopes"></a>部署範圍

部署至訂用帳戶時，您可以將訂用帳戶或訂用帳戶內的任何資源群組設為目標。 部署範本的使用者必須具有指定範圍的存取權。

在範本的資源區段中定義的資源會套用至訂用帳戶。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        subscription-level-resources
    ],
    "outputs": {}
}
```

若要將訂用帳戶中的資源群組設為目標，請新增嵌套部署並包含 `resourceGroup` 屬性。 在下列範例中，嵌套部署的目標是名為的資源群組 `rg2` 。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "nestedDeployment",
            "resourceGroup": "rg2",
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

## <a name="use-template-functions"></a>使用範本函式

針對訂用帳戶層級部署，使用範本函式時有一些重要考量：

* **不**支援 [resourceGroup()](template-functions-resource.md#resourcegroup) 函式。
* 支援 [reference()](template-functions-resource.md#reference) 和 [list()](template-functions-resource.md#list) 函式。
* 使用 [subscriptionResourceId()](template-functions-resource.md#subscriptionresourceid) 函式取得在訂用帳戶層級部署之資源的資源識別碼。

  例如，若要取得原則定義的資源識別碼，使用：

  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```

  傳回的資源識別碼具有下列格式：

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="resource-groups"></a>資源群組

### <a name="create-resource-groups"></a>建立資源群組

若要在 ARM 範本中建立資源群組，請使用資源群組的名稱和位置定義[Microsoft .resources/resourceGroups](/azure/templates/microsoft.resources/allversions)資源。

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
      "apiVersion": "2020-06-01",
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
      "apiVersion": "2020-06-01",
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

如需資源反覆項目的相關資訊，請參閱[在 Azure Resource Manager 範本中部署資源的多個執行個體](./copy-resources.md)和[教學課程：使用 Resource Manager 範本建立多個資源執行個體](./template-tutorial-create-multiple-instances.md)。

### <a name="create-resource-group-and-resources"></a>建立資源群組和資源

若要建立資源群組並對它部署資源，請使用巢狀範本。 巢狀範本能定義要部署至該資源群組的資源。 將巢狀範本設定為資源群組的相依項目，以確保該資源群組在部署資源之前確實存在。 您最多可以部署 800 個資源群組。

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
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-06-01",
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

## <a name="azure-policy"></a>Azure 原則

### <a name="assign-policy-definition"></a>指派原則定義

下列範例會將現有原則定義指派給訂用帳戶。 如果此原則定義採用參數，請以物件形式提供參數。 如果此原則定義不採用參數，請使用預設空白物件。

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
# Built-in policy definition that accepts parameters
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

### <a name="create-and-assign-policy-definitions"></a>建立及指派原則定義

您可以在相同的範本中[定義](../../governance/policy/concepts/definition-structure.md)和指派原則定義。

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

若要在訂用帳戶中建立原則定義，並將其指派至訂用帳戶，請使用下列 CLI 命令：

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

## <a name="azure-blueprints"></a>Azure 藍圖

### <a name="create-blueprint-definition"></a>建立藍圖定義

您可以從範本[建立](../../governance/blueprints/tutorials/create-from-sample.md)藍圖定義。

:::code language="json" source="~/quickstart-templates/subscription-deployments/blueprints-new-blueprint/azuredeploy.json":::

若要在您的訂用帳戶中建立藍圖定義，請使用下列 CLI 命令：

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

若要使用 PowerShell 部署此範本，請使用：

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="access-control"></a>存取控制

若要了解如何指派角色，請參閱[使用 RBAC 和 Azure Resource Manager 範本管理對 Azure 資源的存取](../../role-based-access-control/role-assignments-template.md)。

下列範例會建立資源群組、對其套用鎖定，並將角色指派給主體。

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-rg-lock-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>後續步驟

* 如需針對 Azure 資訊安全中心部署工作區設定的範例，請參閱 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)。
* 您可於 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments) 找到範本範例。
* 您也可以在[管理群組層級](deploy-to-management-group.md)和[租用戶層級](deploy-to-tenant.md)部署範本。
