---
title: Azure Key Vault 將保存庫移至不同的訂用帳戶 |Microsoft Docs
description: 將金鑰保存庫移至不同訂用帳戶的指引。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: 4046d4ec5f62ffc4fab50e8c5a4a08fad326aa04
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796950"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>將 Azure Key Vault 移至另一個訂用帳戶

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>總覽

**將金鑰保存庫移至另一個訂用帳戶將會對您的環境造成重大變更。**

請確定您瞭解這項變更的影響，並在決定將金鑰保存庫移至新的訂用帳戶之前，仔細遵循本文中的指導方針。

當您建立金鑰保存庫時，它會自動系結至其建立所在之訂用帳戶的預設 Azure Active Directory 租使用者識別碼。 所有存取原則項目也會繫結到此租用戶識別碼。 如果您將 Azure 訂用帳戶從租使用者 A 移動至租使用者 B，則租使用者 B 中的服務主體（使用者和應用程式）將無法存取您現有的金鑰保存庫。若要修正此問題，您需要：

* 將訂用帳戶中與所有現有金鑰保存庫相關聯的租用戶識別碼變更為租用戶 B。
* 移除所有現有的存取原則項目。
* 新增與租用戶 B 相關聯的存取原則項目。

## <a name="limitations"></a>限制

某些服務主體（使用者和應用程式）會系結至特定的租使用者。 如果您將金鑰保存庫移至另一個租使用者中的訂用帳戶，則可能無法還原特定服務主體的存取權。 請檢查並確定所有基本的服務主體都存在於您要移動金鑰保存庫的租使用者中。

## <a name="design-considerations"></a>設計考量

您的組織可能已在訂用帳戶層級使用強制或排除來執行 Azure 原則。 在您的金鑰保存庫目前所在的訂用帳戶中，可能會有一組不同的原則指派，以及您要移動金鑰保存庫的訂用帳戶。 原則需求的衝突可能會中斷您的應用程式。

### <a name="example"></a>範例

您的應用程式已連線到金鑰保存庫，以建立兩年的有效憑證。 您嘗試移動金鑰保存庫的訂用帳戶具有原則指派，其會封鎖憑證的建立，且有效期超過一年。 將金鑰保存庫移至新的訂用帳戶之後，Azure 原則指派將會封鎖用來建立有效期為兩年之憑證的作業。

### <a name="solution"></a>解決方法

請確定您已移至 Azure 入口網站上的 [Azure 原則] 頁面，並查看您目前訂用帳戶的原則指派，以及您要移至的訂用帳戶，並確定沒有任何不符。

## <a name="prerequisites"></a>Prerequisites

* 您的金鑰保存庫所在的目前訂用帳戶的參與者層級存取權或更高等級。
* 參與者層級存取或更高版本，可供您想要移動金鑰保存庫的訂用帳戶使用。
* 新訂用帳戶中的資源群組。

## <a name="procedure"></a>程序

### <a name="initial-steps-moving-key-vault"></a>初始步驟（移動 Key Vault）

1. 登入 Azure 入口網站
2. 流覽至您的金鑰保存庫
3. 按一下 [總覽] 索引標籤
4. 選取 [移動] 按鈕
5. 從下拉式清單選項中選取 [移至另一個訂用帳戶]
6. 選取您想要移動金鑰保存庫的資源群組
7. 選取您想要移動金鑰保存庫的資源群組
8. 認可有關移動資源的警告
9. 選取 [確定]

### <a name="additional-steps-post-move"></a>其他步驟（移動後）

現在您已將金鑰保存庫移至新的訂用帳戶，您必須更新租使用者識別碼，並移除舊的存取原則。 以下是 PowerShell 和 Azure CLI 中這些步驟的教學課程。

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

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

既然您的保存庫已與正確的租用戶識別碼相關聯，而且移除了舊的存取原則項目，請使用 Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) Cmdlet 或 Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令，設定新的存取原則項目。

如果您使用 Azure 資源的受控識別，您也必須將其更新為新的 Azure AD 租用戶。 如需有關受控識別的詳細資訊，請參閱[使用受控識別提供金鑰保存庫驗證](managed-identity.md)。

如果您使用的是 MSI，您也必須更新 MSI 身分識別，因為舊的身分識別不會再出現在正確的 AAD 租用戶中。


