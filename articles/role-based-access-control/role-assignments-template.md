---
title: 使用 Azure Resource Manager 範本新增 Azure 角色指派 - Azure RBAC
description: 了解如何使用 Azure Resource Manager 範本和 Azure 角色型存取控制 (Azure RBAC)，為使用者、群組、服務主體或受控識別授與 Azure 資源的存取權。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 400f0b1b55136f133c9ad01fd0ba4b5dbc5e6bcb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91612739"
---
# <a name="add-azure-role-assignments-using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本新增 Azure 角色指派

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] 除了使用 Azure PowerShell 或 Azure CLI 外，您也可以使用 [Azure Resource Manager 範本](../azure-resource-manager/templates/template-syntax.md)來指派角色。 如果您需要以一致性和重複的方式來部署資源，範本可以派上用場。 本文說明如何使用範本指派角色。

## <a name="get-object-ids"></a>取得物件識別碼

若要指派角色，您必須指定識別碼給要為其指派角色的使用者、群組或應用程式。 識別碼的格式如下：`11111111-1111-1111-1111-111111111111`。 您可以使用 Azure 入口網站、Azure PowerShell 或 Azure CLI 來取得識別碼。

### <a name="user"></a>User

若要取得使用者的識別碼，您可以使用 [AzADUser](/powershell/module/az.resources/get-azaduser) 或 [az ad user show](/cli/azure/ad/user#az-ad-user-show) 命令。

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>群組

若要取得群組的識別碼，您可以使用 [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) 或 [az ad group show](/cli/azure/ad/group#az-ad-group-show) 命令。

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="managed-identities"></a>受控身分識別

若要取得受控識別的識別碼，您可以使用 [>get-azadserviceprincipal](/powershell/module/az.resources/get-azadserviceprincipal) 或 [az ad sp](/cli/azure/ad/sp) 命令。

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName <Azure resource name>).id
```

```azurecli
objectid=$(az ad sp list --display-name <Azure resource name> --query [].objectId --output tsv)
```

### <a name="application"></a>Application

若要取得服務主體 (應用程式所使用的身分識別) 的識別碼，您可以使用 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) 或 [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) 命令。 對於服務主體，請使用物件識別碼，而**不是**應用程式識別碼。

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="add-a-role-assignment"></a>新增角色指派

在 Azure RBAC 中，若要授與存取權，您可以新增角色指派。

### <a name="resource-group-scope-without-parameters"></a>資源群組範圍 (沒有參數) 

下列範本顯示新增角色指派的基本方式。 某些值是在範本內指定。 下列範本會示範：

-  如何將[讀者](built-in-roles.md#reader)角色指派給資源群組範圍中的使用者、群組或應用程式

若要使用範本，您必須執行下列動作：

- 建立新的 JSON 檔案並複製範本
- 取代為 `<your-principal-id>` 要指派角色的使用者、群組、受控識別或應用程式的識別碼

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

以下是範例 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 和 [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) 命令，示範在名為 ExampleGroup 的資源群組中啟動部署。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

以下顯示在部署範本之後，將讀者角色指派給資源群組使用者的範例。

![資源群組範圍的角色指派](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription-scope"></a>資源群組或訂用帳戶範圍

先前的範本不是很有彈性。 下列範本會使用參數，而且可以在不同的範圍中使用。 下列範本會示範：

- 如何將角色指派給資源群組或訂用帳戶範圍中的使用者、群組或應用程式
- 如何將擁有者、參與者和讀者角色指定為參數

若要使用範本，您必須指定下列輸入：

- 要指派角色的使用者、群組、受控識別或應用程式的識別碼
- 將用於角色指派的唯一識別碼，或者您可以使用預設識別碼

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

> [!NOTE]
> 除非為範本的每個部署提供相同的 `roleNameGuid` 值，否則此範本不具等冪性。 如果未提供任何 `roleNameGuid`，則根據預設會在每個部署上產生新的 GUID，而後續部署將會失敗，並出現 `Conflict: RoleAssignmentExists` 錯誤。

角色指派的範圍是從部署層級決定。 以下是範例 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 和 [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) 命令，示範如何在資源群組範圍中啟動部署。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

以下是範例 [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) 和 [az deployment create](/cli/azure/deployment#az-deployment-create) 命令，示範如何在訂用帳戶範圍啟動部署並指定位置。

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource-scope"></a>資源範圍

如果您需要在資源層級新增角色指派，則角色指派的格式會不同。 您會提供資源提供者的命名空間和資源類型，以指派角色給該資源。 您也可以將資源的名稱包含在角色指派的名稱中。

針對角色指派的類型和名稱，請使用下列格式：

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

下列範本會示範：

- 如何建立新的儲存體帳戶
- 如何將角色指派給儲存體帳戶範圍中的使用者、群組或應用程式
- 如何將擁有者、參與者和讀者角色指定為參數

若要使用範本，您必須指定下列輸入：

- 要指派角色的使用者、群組、受控識別或應用程式的識別碼

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', guid(uniqueString(variables('storageName'))))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

若要部署先前的範本，您可以使用資源群組命令。 以下是範例 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 和 [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) 命令，示範如何在資源範圍中啟動部署。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

以下顯示在部署範本之後，將參與者角色指派給儲存體帳戶使用者的範例。

![資源範圍的角色指派](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>新的服務主體

如果您建立新的服務主體，並立即嘗試將角色指派給該服務主體，在某些情況下，該角色指派可能會失敗。 例如，如果您建立新的受控識別，然後嘗試在相同的 Azure Resource Manager 範本中將角色指派給該服務主體，則角色指派可能會失敗。 此失敗的原因可能是複寫延遲。 服務主體建立在某個區域中；不過，角色指派可能發生在另一個尚未複寫服務主體的區域中。 若要解決此問題，您應該在建立角色指派時，將 `principalType` 屬性設定為 `ServicePrincipal`。

下列範本會示範：

- 如何建立新的受控識別服務主體
- 如何指定 `principalType`
- 如何將參與者角色指派給資源群組範圍中的服務主體

若要使用範本，您必須指定下列輸入：

- 受控識別的基底名稱，或者您可以使用預設字串

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "scope": "[resourceGroup().id]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

以下是範例 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 和 [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) 命令，示範如何在資源群組範圍中啟動部署。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

以下顯示在部署範本之後，將參與者角色指派給新受控識別服務主體的範例。

![將角色指派給新的受控識別服務主體](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="remove-a-role-assignment"></a>移除角色指派

在 Azure RBAC 中，若要移除對 Azure 資源的存取，您可以移除角色指派。 沒有方法可以使用範本來移除角色指派。 若要移除角色指派，您必須使用其他工具，例如：

- [Azure 入口網站](role-assignments-portal.md#remove-a-role-assignment)
- [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)
- [Azure CLI](role-assignments-cli.md#remove-role-assignment)
- [REST API](role-assignments-rest.md#remove-a-role-assignment)

## <a name="next-steps"></a>後續步驟

- [快速入門：使用 Azure 入口網站建立及部署 Azure Resource Manager 範本](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [了解 Azure Resource Manager 範本的結構和語法](../azure-resource-manager/templates/template-syntax.md)
- [在訂用帳戶層級建立資源群組和資源](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?term=rbac)
