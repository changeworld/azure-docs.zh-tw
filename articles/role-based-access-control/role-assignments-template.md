---
title: 使用 RBAC 和 Azure 資源管理器範本添加角色指派
description: 瞭解如何使用基於 Azure 角色的存取控制 （RBAC） 和 Azure 資源管理器範本為使用者、組、服務主體或託管標識授予對 Azure 資源的訪問。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9f817880f938f5d03024e3aacd9b84817a5ac721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77138293"
---
# <a name="add-role-assignments-using-azure-rbac-and-azure-resource-manager-templates"></a>使用 Azure RBAC 和 Azure 資源管理器範本添加角色指派

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]除了使用 Azure PowerShell 或 Azure CLI 外，還可以使用[Azure 資源管理器範本](../azure-resource-manager/templates/template-syntax.md)分配角色。 如果您需要以一致性和重複的方式來部署資源，範本可以派上用場。 本文介紹如何使用範本分配角色。

## <a name="get-object-ids"></a>獲取物件指示

要分配角色，您需要指定要將角色指派給的使用者、組或應用程式的 ID。 ID 的格式為： `11111111-1111-1111-1111-111111111111`。 您可以使用 Azure 門戶、Azure PowerShell 或 Azure CLI 獲取 ID。

### <a name="user"></a>User

要獲取使用者的 ID，可以使用[Get-AzADUser](/powershell/module/az.resources/get-azaduser)或 az[廣告使用者顯示](/cli/azure/ad/user#az-ad-user-show)命令。

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>群組

要獲取組的 ID，可以使用[Get-AzADGroup](/powershell/module/az.resources/get-azadgroup)或 az[廣告組顯示](/cli/azure/ad/group#az-ad-group-show)命令。

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="application"></a>Application

要獲取服務主體的 ID（應用程式使用的身份），可以使用[Get-AzADService 委託書](/powershell/module/az.resources/get-azadserviceprincipal)或 az [ad sp 清單](/cli/azure/ad/sp#az-ad-sp-list)命令。 對於服務主體，請使用物件識別碼**而不是應用程式**ID。

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="add-a-role-assignment"></a>新增角色指派

在 RBAC 中，要授予存取權限，您需要添加角色指派。

### <a name="resource-group-without-parameters"></a>資源組（無參數）

以下範本顯示了添加角色指派的基本方法。 某些值在範本中指定。 下列範本會示範：

-  如何將[Reader](built-in-roles.md#reader)角色指派給資源組作用域中的使用者、組或應用程式

要使用範本，必須執行以下操作：

- 創建新的 JSON 檔並複製範本
- 替換為`<your-principal-id>`使用者、組或應用程式的 ID，以將角色指派給

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

下面是["新建-AzResourceGroup 部署"](/powershell/module/az.resources/new-azresourcegroupdeployment)和["az 組部署"創建](/cli/azure/group/deployment#az-group-deployment-create)命令，用於如何在名為示例組的資源組中開始部署。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

下面顯示了部署範本後資源組的使用者的 Reader 角色指派示例。

![資源組作用域中的角色指派](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription"></a>資源組或訂閱

以前的範本不是很靈活。 以下範本使用參數，可在不同的作用域中使用。 下列範本會示範：

- 如何在資源組或訂閱範圍內為使用者、組或應用程式分配角色
- 如何將擁有者、參與者和讀者角色指定為參數

若要使用範本，您必須指定下列輸入：

- 將角色指派給的使用者、組或應用程式的 ID
- 將用於角色指派的唯一 ID，或者您可以使用預設 ID

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
> 除非為每個範本的部署提供與參數相同的`roleNameGuid`值，否則此範本不是冪等的。 如果未`roleNameGuid`提供，則預設情況下在每個部署上生成新的 GUID，後續部署將失敗，並出現`Conflict: RoleAssignmentExists`錯誤。

角色指派的範圍根據部署級別確定。 下面是["新建-AzResourceGroup 部署"](/powershell/module/az.resources/new-azresourcegroupdeployment)和["az 組部署"](/cli/azure/group/deployment#az-group-deployment-create)創建命令，用於瞭解如何在資源組作用域開始部署。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

下面是["新建-AzDeployment"](/powershell/module/az.resources/new-azdeployment)和["az 部署"創建](/cli/azure/deployment#az-deployment-create)命令，用於瞭解如何在訂閱範圍內啟動部署並指定位置。

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource"></a>資源

如果需要在資源級別添加角色指派，角色指派的格式會有所不同。 提供要分配給其角色的資來源提供者命名空間和資源類型。 您還可以在角色指派的名稱中包括資源的名稱。

對於角色指派的類型和名稱，請使用以下格式：

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

下列範本會示範：

- 如何建立新的儲存體帳戶
- 如何在存儲帳戶範圍內為使用者、組或應用程式分配角色
- 如何將擁有者、參與者和讀者角色指定為參數

若要使用範本，您必須指定下列輸入：

- 將角色指派給的使用者、組或應用程式的 ID

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

要部署以前的範本，請使用資源組命令。 下面是["新建-AzResourceGroup 部署"](/powershell/module/az.resources/new-azresourcegroupdeployment)和["az 組部署"](/cli/azure/group/deployment#az-group-deployment-create)創建命令，用於瞭解如何在資源範圍內啟動部署。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

下面顯示了部署範本後向存儲帳戶的使用者分配參與者角色的示例。

![資源範圍內的角色指派](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>新的服務主體

如果創建新的服務主體並立即嘗試為該服務主體分配角色，則在某些情況下，該角色指派可能會失敗。 例如，如果創建新的託管標識，然後嘗試在同一 Azure 資源管理器範本中為該服務主體分配角色，則角色指派可能會失敗。 此失敗的原因可能是複寫延遲。 服務主體在一個區域中創建;服務主體在一個區域中創建。但是，角色指派可能發生在尚未複製服務主體的不同區域中。 要解決此問題，應在創建角色指派`principalType``ServicePrincipal`時將屬性設置為。

下列範本會示範：

- 如何創建新的託管標識服務主體
- 如何指定`principalType`
- 如何在資源組作用域中將參與者角色分配給該服務主體

若要使用範本，您必須指定下列輸入：

- 託管標識的基本名稱，也可以使用預設字串

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

下面是["新建-AzResourceGroup 部署"](/powershell/module/az.resources/new-azresourcegroupdeployment)和["az 組部署"](/cli/azure/group/deployment#az-group-deployment-create)創建命令，用於瞭解如何在資源組作用域開始部署。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

下面顯示了部署範本後，參與者角色分配到新的託管標識服務主體的示例。

![新託管標識服務主體的角色指派](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>後續步驟

- [快速入門：使用 Azure 入口網站建立及部署 Azure Resource Manager 範本](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [瞭解 Azure 資源管理器範本的結構和語法](../azure-resource-manager/templates/template-syntax.md)
- [在訂用帳戶層級建立資源群組和資源](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?term=rbac)
