---
title: 部署可補救的原則
description: 若要透過 Azure Lighthouse 部署使用補救工作的原則，您必須在客戶租使用者中建立受控識別。
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: eb473fe2f589cf719e3944c887d46e75e9e7fdbf
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97670486"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>部署可在委派的訂用帳戶內補救的原則

[Azure Lighthouse](../overview.md) 可讓服務提供者在委派的訂用帳戶內建立和編輯原則定義。 不過，若要部署使用 [補救](../../governance/policy/how-to/remediate-resources.md) 工作 (也就是具有 [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) 或 [modify](../../governance/policy/concepts/effects.md#modify) 效果) 原則的原則，您必須在客戶租使用者中建立 [受控識別](../../active-directory/managed-identities-azure-resources/overview.md) 。 Azure 原則可以使用此受控識別在原則內部署範本。 當您讓客戶上線進行 Azure 委派資源管理，以及當您部署原則本身時，必須執行下列步驟才能啟用此案例。

> [!TIP]
> 雖然我們會在本主題中參考服務提供者和客戶，但 [管理多個](../concepts/enterprise.md) 租使用者的企業可以使用相同的程式。

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>建立可將角色指派給客戶租用戶中受控識別的使用者

當您將客戶上架到 Azure Lighthouse 時，您會使用 [Azure Resource Manager 範本](onboard-customer.md#create-an-azure-resource-manager-template) 和參數檔案，該檔案會定義您管理租使用者中的使用者、使用者群組和服務主體，以便能夠存取客戶租使用者中的委派資源。 在您的 parameters 檔案中，每個使用者 (**principalId**) 都會被指派一個 [內建角色](../../role-based-access-control/built-in-roles.md) (**roleDefinitionId**)，以定義存取層級。

若要允許 **principalId** 在客戶租用戶中建立受控識別，您必須將其 **roleDefinitionId** 設定為 [使用者存取管理員]。 雖然此角色通常不受支援，但可在此特定案例中使用，可讓具有此權限的使用者將一個或多個特定內建角色指派給受控識別。 這些角色都定義於 **delegatedRoleDefinitionIds** 屬性中。 除了「使用者存取管理員」或「擁有者」以外，您可以在此包含任何內建角色。

客戶上線之後，在此授權中建立的 **principalId** 就能夠將這些內建角色指派給客戶租用戶中的受控識別。 不過，他們不會有任何通常與「使用者存取管理員」角色相關聯的其他權限。

以下範例顯示將具有「使用者存取管理員」角色的 **principalId**。 此使用者將能夠將兩個內建角色指派給客戶租使用者中的受控識別：參與者和 Log Analytics 參與者。

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>部署可補救的原則

一旦您建立具有上述必要許可權的使用者，該使用者就可以在委派的客戶訂用帳戶內部署使用補救工作的原則。

例如，假設您想要啟用客戶租使用者中 Azure Key Vault 資源的診斷，如本 [範例](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring)所示。 管理租用戶中具有適當權限的使用者 (如上所述) 會部署 [Azure Resource Manager 範本](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json)以啟用此案例。

請注意，目前必須透過 API (而不是在 Azure 入口網站中) 建立要與委派訂用帳戶搭配使用的原則指派。 當您這麼做時，**apiVersion** 必須設定為 **2019-04-01-preview**，其中包含新的 **delegatedManagedIdentityResourceId** 屬性。 此屬性可讓您在已上線至 Azure Lighthouse) 的訂用帳戶或資源群組中，包含位於客戶租使用者 (的受控識別。

下列範例顯示具有 **delegatedManagedIdentityResourceId** 的角色指派。

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> 有個[類似範例](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag)可用來示範如何部署原則，以在委派的訂用帳戶中新增或移除標籤 (使用修改效果)。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 原則](../../governance/policy/index.yml)。
- 了解[適用於 Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)。
