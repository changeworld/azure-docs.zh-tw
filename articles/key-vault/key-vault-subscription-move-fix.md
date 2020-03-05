---
title: 在訂用帳戶移動之後，變更金鑰保存庫租用戶識別碼 - Azure Key Vault | Microsoft Docs
description: 了解如何在訂用帳戶移到不同租用戶之後，切換金鑰保存庫的租用戶識別碼
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 4531d3bdeab96cb4a753060d59d07e155f4a358e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197329"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>在訂用帳戶移動之後變更金鑰保存庫租用戶識別碼

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


當您在訂用帳戶中建立新的金鑰保存庫時，它會自動繫結到該訂用帳戶的預設 Azure Active Directory 租用戶識別碼。 所有存取原則項目也會繫結到此租用戶識別碼。 

如果您將 Azure 訂用帳戶從租用戶 A 移到租用戶 B，租用戶 B 中的主體 (使用者和應用程式) 無法存取現有的金鑰保存庫。若要修正這個問題，您需要：

* 將訂用帳戶中與所有現有金鑰保存庫相關聯的租用戶識別碼變更為租用戶 B。
* 移除所有現有的存取原則項目。
* 新增與租用戶 B 相關聯的存取原則項目。

例如，如果您在已從租用戶 A 移到租用戶 B 的訂用帳戶中有金鑰保存庫 'myvault'，您可以使用 Azure PowerShell 變更租用戶識別碼，並移除舊的存取原則。

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.
````

或者，您可以使用 Azure CLI。

```azurecli
az account set <your-subscriptionId>                                       # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

既然您的保存庫已與正確的租用戶識別碼相關聯，而且移除了舊的存取原則項目，請使用 Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) Cmdlet 或 Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令，設定新的存取原則項目。

如果您使用 Azure 資源的受控識別，您也必須將其更新為新的 Azure AD 租用戶。 如需有關受控識別的詳細資訊，請參閱[使用受控識別提供金鑰保存庫驗證](managed-identity.md)。


如果您使用的是 MSI，您也必須更新 MSI 身分識別，因為舊的身分識別不會再出現在正確的 AAD 租用戶中。

## <a name="next-steps"></a>後續步驟

如果您有關於 Azure 金鑰保存庫的問題，請造訪 [Azure 金鑰保存庫論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)。
