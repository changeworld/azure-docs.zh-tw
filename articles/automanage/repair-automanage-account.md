---
title: 修復損壞的 Azure Automanage 帳戶
description: 如果您最近將包含 Automanage 帳戶的訂用帳戶移至新的租使用者，則需要重新設定它。 在本文中，您將瞭解做法。
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: 4694fa679c7bbff309a0452219ff39bacf2488c4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183697"
---
# <a name="repair-an-automanage-account"></a>修復 Automanage 帳戶
您的 [Azure Automanage 帳戶](./automanage-virtual-machines.md#automanage-account) 是在其下進行自動化作業所用的安全性內容或身分識別。 如果您最近將包含 Automanage 帳戶的訂用帳戶移至新的租使用者，則需要重新設定帳戶。 若要重新設定，您必須重設身分識別類型，並為帳戶指派適當的角色。

## <a name="step-1-reset-the-automanage-account-identity-type"></a>步驟1：重設 Automanage 帳戶身分識別類型
使用下列 Azure Resource Manager (ARM) 範本來重設 Automanage 帳戶身分識別類型。 在本機將檔案儲存為 armdeploy.js或類似的名稱。 請注意您的 Automanage 帳戶名稱和位置，因為它們是 ARM 範本中的必要參數。

1. 使用下列範本建立 Resource Manager 部署。 請使用 `identityType = None`。
    * 您可以使用在 Azure CLI 中建立部署 `az deployment sub create` 。 如需詳細資訊，請參閱 [az deployment sub](/cli/azure/deployment/sub)。
    * 您可以使用模組，在 PowerShell 中建立部署 `New-AzDeployment` 。 如需詳細資訊，請參閱 [test-azdeployment](/powershell/module/az.resources/new-azdeployment)。

1. 使用再次執行相同的 ARM 範本 `identityType = SystemAssigned` 。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>步驟2：為 Automanage 帳戶指派適當的角色
Automanage 帳戶需要訂用帳戶上的「參與者」和「資源原則參與者」角色，且該訂用帳戶包含 Automanage 正在管理的 Vm。 您可以使用 Azure 入口網站、ARM 範本或 Azure CLI 來指派這些角色。

如果您使用 ARM 範本或 Azure CLI，則需要主體識別碼 (也稱為 Automanage 帳戶的物件識別碼) 。  (如果您使用 Azure 入口網站，則不需要識別碼。 ) 您可以使用下列方法來尋找此識別碼：

- [Azure CLI](/cli/azure/ad/sp)：使用命令 `az ad sp list --display-name <name of your Automanage Account>` 。

- Azure 入口網站：移至 **Azure Active Directory** ，並依名稱搜尋您的 Automanage 帳戶。 在 [ **企業應用程式**] 下，選取出現的 Automanage 帳戶名稱。

### <a name="azure-portal"></a>Azure 入口網站
1. 在 [ **訂用帳戶**] 下，移至包含 automanaged vm 的訂用帳戶。
1. 移至 [ **存取控制] (IAM)**。
1. 選取 [ **新增角色指派**]。
1. 選取 [ **參與者** ] 角色，然後輸入您的 Automanage 帳戶名稱。
1. 選取 [儲存]。
1. 重複步驟3到5，這次使用 **資源原則參與者** 角色。

### <a name="arm-template"></a>ARM 範本
執行下列 ARM 範本。 您將需要 Automanage 帳戶的主體識別碼。 取得此步驟的步驟位於本節的開頭。 當系統提示您時，請輸入識別碼。

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
        }
    },
    "variables": {
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>Azure CLI
執行下列命令：

```azurecli
az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Contributor" --scope /subscriptions/<your subscription ID>

az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription ID>
```

## <a name="next-steps"></a>後續步驟
[深入瞭解 Azure Automanage](./automanage-virtual-machines.md)