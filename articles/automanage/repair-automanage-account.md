---
title: 修復損壞的 Azure Automanage 帳戶
description: 瞭解如何修正中斷的 Automanage 帳戶
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: ad54b37da8a4945162b507232f33083890ec1fff
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557570"
---
# <a name="repair-a-broken-automanage-account"></a>修復中斷的 Automanage 帳戶
[Automanage 帳戶](./automanage-virtual-machines.md#automanage-account)是安全性內容或執行自動化作業所用的身分識別。 如果您最近將包含 Automanage 帳戶的訂用帳戶移至新的租使用者，則必須重新設定您的 Automanage 帳戶。 若要重新設定您的 Automanage 帳戶，您必須重設身分識別類型，並為帳戶指派適當的角色。

## <a name="step-1-reset-automanage-account-identity-type"></a>步驟1：重設 Automanage 帳戶身分識別類型
使用下方的 Azure Resource Manager (ARM) 範本，重設 Automanage 帳戶身分識別類型。 以或類似的方式將檔案儲存在本機 `armdeploy.json` 。 記下您的 Automanage 帳戶名稱和位置，因為這些是 ARM 範本中的必要參數。

1. 使用下列範本建立新的 ARM 部署，並使用 `identityType = None`
    * 您可以使用 Azure CLI 來這麼做 `az deployment sub create` 。 若要深入瞭解此命令，請參閱 `az deployment sub` 。 [here](https://docs.microsoft.com/cli/azure/deployment/sub)
    * 您也可以使用此模組，透過 PowerShell 來完成此動作 `New-AzDeployment` 。 若要深入瞭解 `New AzDeployment` 此[here](https://docs.microsoft.com/powershell/module/az.resources/new-azdeployment)課程模組，請參閱。

1. 再次執行相同的 ARM 範本 `identityType = SystemAssigned`

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
Automanage 帳戶需要訂用帳戶上的「參與者」和「資源原則參與者」角色，該訂用帳戶包含 Automanage 正在管理的 Vm。 您可以使用 Azure 入口網站、ARM 範本或 Azure CLI 來指派這些角色。

如果您使用 ARM 範本或 Azure CLI，則需要主體識別碼 (也稱為 Automanage 帳戶的物件識別碼)  (如果您使用 Azure 入口網站) ，就不需要這麼做。 您可以使用下列方法，找到 Automanage 帳戶 (物件識別碼) 的主體識別碼：

- [Azure CLI](https://docs.microsoft.com/cli/azure/ad/sp)：使用命令 `az ad sp list --display-name <name of your Automanage Account>` 。

- Azure 入口網站：流覽至 **Azure Active Directory** ，並依名稱搜尋您的 Automanage 帳戶。 在 [ **企業應用程式** ] 下，選取顯示的 Automanage 帳戶名稱。

### <a name="azure-portal"></a>Azure 入口網站
1. 在 [ **訂用帳戶** ] 底下，流覽至包含 Automanaged vm 的訂用帳戶。
1. 流覽至 **(IAM) 的存取控制** 。
1. 按一下 [ **新增角色指派** ]。
1. 選取 [ **參與者** ] 角色，然後輸入您的 Automanage 帳戶名稱。
1. 按下 [儲存] 。
1. 重複步驟3-5，這次使用 **資源原則參與者** 角色。

### <a name="arm-template"></a>ARM 範本
執行下列 ARM 範本。 您將需要 Automanage 帳戶的主體識別碼-取得主要識別碼的步驟步驟。 出現提示時，請輸入。

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
az role assignment create --assignee-object-id <your Automanage Account's object id> --role "Contributor" --scope /subscriptions/<your subscription id>

az role assignment create --assignee-object-id <your Automanage Account's object id> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription id>
```

## <a name="next-steps"></a>後續步驟
[在這裡](./automanage-virtual-machines.md)深入瞭解 Azure Automanage。
