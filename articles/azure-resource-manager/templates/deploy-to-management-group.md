---
title: 將資源部署至管理群組
description: 說明如何在 Azure Resource Manager 範本的管理群組範圍中部署資源。
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 79cdb35de40501dfc0794155dcf807cced94bfa7
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95798597"
---
# <a name="management-group-deployments-with-arm-templates"></a>使用 ARM 範本進行管理群組部署

隨著您的組織逐漸成熟，您可以 (ARM 範本部署 Azure Resource Manager 範本，) 在管理群組層級建立資源。 例如，您可能需要為管理群組定義和指派 [原則](../../governance/policy/overview.md) 或 [azure 角色型存取控制 (azure RBAC) ](../../role-based-access-control/overview.md) 。 您可以使用管理群組層級範本，以宣告方式套用原則，並在管理群組層級指派角色。

## <a name="supported-resources"></a>支援的資源

並非所有的資源類型都可部署到管理群組層級。 本節將列出支援的資源類型。

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

若要 (azure RBAC) 的 Azure 角色型存取控制，請使用：

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

針對部署至訂用帳戶或資源群組的嵌套範本，請使用：

* [部署](/azure/templates/microsoft.resources/deployments)

若要管理您的資源，請使用：

* [「標記」](/azure/templates/microsoft.resources/tags)

## <a name="schema"></a>結構描述

用於管理群組部署的架構與資源群組部署的架構不同。

針對範本，請使用：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    ...
}
```

所有部署範圍的參數檔案結構描述都相同。 針對參數檔案，請使用：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>部署命令

若要部署至管理群組，請使用管理群組部署命令。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

針對 Azure CLI，請使用 [az deployment mg create](/cli/azure/deployment/mg#az-deployment-mg-create)：

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

針對 Azure PowerShell，請使用 [AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment)。

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

如需部署 ARM 範本的部署命令和選項的詳細資訊，請參閱：

* [使用 ARM 範本和 Azure 入口網站部署資源](deploy-portal.md)
* [使用 ARM 範本與 Azure CLI 來部署資源](deploy-cli.md)
* [使用 ARM 範本與 Azure PowerShell 來部署資源](deploy-powershell.md)
* [使用 ARM 範本部署資源，並 Azure Resource Manager REST API](deploy-rest.md)
* [使用部署按鈕從 GitHub 存放庫部署範本](deploy-to-azure-button.md)
* [從 Cloud Shell 部署 ARM 範本](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>部署位置和名稱

針對管理群組層級部署，您必須提供部署的位置。 部署的位置與您部署的資源位置不同。 部署位置會指定部署資料的儲存位置。 [訂](deploy-to-subscription.md) 用帳戶和 [租](deploy-to-tenant.md) 使用者部署也需要一個位置。 針對 [資源群組](deploy-to-resource-group.md) 部署，資源群組的位置會用來儲存部署資料。

您可以提供部署的名稱，或使用預設的部署名稱。 預設名稱是範本檔案的名稱。 例如，部署名為 **azuredeploy.json** 的範本會建立預設的部署名稱 **azuredeploy**。

對於每個部署名稱而言，此位置是不可變的。 當某個位置已經有名稱相同的現有部署時，您無法在其他位置建立部署。 例如，如果您在 **centralus** 中建立名為 **deployment1** 的管理群組部署，稍後就無法再使用名稱 **deployment1** 建立另一個部署，而是使用 **westus** 的位置建立另一個部署。 如果您收到錯誤代碼 `InvalidDeploymentLocation`，請使用不同的名稱或與先前該名稱部署相同的位置。

## <a name="deployment-scopes"></a>部署範圍

部署至管理群組時，您可以將資源部署至：

* 作業中的目標管理群組
* 租使用者中的另一個管理群組
* 管理群組中的訂用帳戶
* 管理群組中的資源群組
* 資源群組的租使用者
* [擴充功能資源](scope-extension-resources.md) 可以套用至資源

部署範本的使用者必須擁有指定範圍的存取權。

本節說明如何指定不同的範圍。 您可以將這些不同的範圍結合在單一範本中。

### <a name="scope-to-target-management-group"></a>範圍設定為目標管理群組

在範本的資源區段中定義的資源會從部署命令套用至管理群組。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

### <a name="scope-to-another-management-group"></a>將範圍設為另一個管理群組

若要以另一個管理群組為目標，請新增嵌套部署並指定 `scope` 屬性。 將 `scope` 屬性設定為格式的值 `Microsoft.Management/managementGroups/<mg-name>` 。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>訂用帳戶的範圍

您也可以將管理群組中的訂用帳戶設為目標。 部署範本的使用者必須擁有指定範圍的存取權。

若要以管理群組內的訂用帳戶為目標，請使用嵌套部署和 `subscriptionId` 屬性。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>將範圍設為資源群組

您也可以將目標設為管理群組內的資源群組。 部署範本的使用者必須擁有指定範圍的存取權。

若要以管理群組內的資源群組為目標，請使用嵌套部署。 請設定 `subscriptionId` 和 `resourceGroup` 屬性。 請勿設定嵌套部署的位置，因為它是部署在資源群組的位置。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="9,10,18":::

若要使用管理群組部署來建立訂用帳戶內的資源群組，並將儲存體帳戶部署到該資源群組，請參閱 [部署至訂用帳戶和資源群組](#deploy-to-subscription-and-resource-group)。

### <a name="scope-to-tenant"></a>租使用者範圍

您可以將設定為，以在租使用者中建立資源 `scope` `/` 。 部署範本的使用者必須具有必要的 [存取權，才能在租使用者上進行部署](deploy-to-tenant.md#required-access)。

您可以使用和設定的嵌套 `scope` 部署 `location` 。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-to-tenant.json" highlight="9,10,14":::

或者，您可以將範圍設定為 `/` 某些資源類型，例如管理群組。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-create-mg.json" highlight="12,15":::

## <a name="azure-policy"></a>Azure 原則

部署至管理群組的自訂原則定義是管理群組的延伸。 若要取得自訂原則定義的識別碼，請使用 [extensionResourceId ( # B1 ](template-functions-resource.md#extensionresourceid) 函數。 內建原則定義是租使用者層級的資源。 若要取得內建原則定義的識別碼，請使用 [tenantResourceId](template-functions-resource.md#tenantresourceid) 函數。

下列範例顯示如何在管理群組層級 [定義](../../governance/policy/concepts/definition-structure.md) 原則，並加以指派。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
                "policyRule": {
                    "if": {
                        "not": {
                            "field": "location",
                            "in": "[parameters('allowedLocations')]"
                        }
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>部署至訂用帳戶和資源群組

從管理群組層級部署中，您可以將訂用帳戶的目標設為管理群組內的訂用帳戶。 下列範例會在訂用帳戶內建立資源群組，並將儲存體帳戶部署到該資源群組。

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
                            "location": "[parameters('nestedLocation')]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "nestedRG",
            "subscriptionId": "[parameters('nestedSubId')]",
            "resourceGroup": "[parameters('nestedRG')]",
            "dependsOn": [
                "nestedSub"
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
                            "kind": "StorageV2",
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
```

## <a name="next-steps"></a>後續步驟

* 若要瞭解如何指派角色，請參閱 [使用 Azure Resource Manager 範本新增 Azure 角色指派](../../role-based-access-control/role-assignments-template.md)。
* 如需針對 Azure 資訊安全中心部署工作區設定的範例，請參閱 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)。
* 您也可以在訂用帳戶 [層](deploy-to-subscription.md) 級和 [租使用者層級](deploy-to-tenant.md)部署範本。
