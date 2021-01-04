---
title: Azure Key Vault 將保存庫移至不同的訂用帳戶 |Microsoft Docs
description: 將金鑰保存庫移至不同訂用帳戶的指引。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: d881394391b7967fe602155eefc9844e013de34e
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724743"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>將 Azure Key Vault 移至另一個訂用帳戶

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>總覽

> [!IMPORTANT]
> **將金鑰保存庫移至另一個訂用帳戶，將會對您的環境造成重大變更。**
> 確定您瞭解這項變更的影響，並在決定將金鑰保存庫移至新的訂用帳戶之前，仔細遵循本文中的指導方針。
> 如果您使用受控服務識別 (MSI) 請閱讀檔結尾的移動後指示。 

[Azure Key Vault](overview.md) 會自動系結至其建立所在之訂用帳戶的預設 [AZURE ACTIVE DIRECTORY](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) 租使用者識別碼。 您可以遵循本 [指南](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-to-find-tenant)，找到與您的訂用帳戶相關聯的租使用者識別碼。 所有存取原則專案和角色指派也會系結至此租使用者識別碼。  如果您將 Azure 訂用帳戶從租使用者 A 移到租使用者 B， (使用者和應用程式在租使用者 B 中) 的服務主體將無法存取現有的金鑰保存庫。若要修正此問題，您需要：

* 將訂用帳戶中與所有現有金鑰保存庫相關聯的租用戶識別碼變更為租用戶 B。
* 移除所有現有的存取原則項目。
* 新增與租用戶 B 相關聯的存取原則項目。

如需 Azure Key Vault 和 Azure Active Directory 的詳細資訊，請參閱
- [關於 Azure Key Vault](overview.md)
- [什麼是 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
- [如何尋找租用戶識別碼](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-to-find-tenant)

## <a name="limitations"></a>限制

> [!IMPORTANT]
> **無法移動用於磁片加密的金鑰保存庫** 如果您使用 key vault 搭配 VM 的磁片加密，則在啟用磁片加密時，金鑰保存庫無法移至不同的資源群組或訂用帳戶。 將金鑰保存庫移至新的資源群組或訂用帳戶之前，您必須先停用磁片加密。 

某些服務主體 (使用者和應用程式) 系結至特定的租使用者。 如果您將金鑰保存庫移至另一個租使用者中的訂用帳戶，則有可能無法還原特定服務主體的存取權。 請檢查以確定您要移動金鑰保存庫的租使用者中有所有必要的服務主體。

## <a name="prerequisites"></a>必要條件

* 目前訂用帳戶的[參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)層級存取權或更高版本，您的金鑰保存庫存在。 您可以使用 [Azure 入口網站](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)、 [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)或 [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)來指派角色。
* [參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) 層級存取權或更高版本，可存取您想要移動金鑰保存庫的訂用帳戶。您可以使用 [Azure 入口網站](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)、 [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)或 [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)來指派角色。
* 新訂用帳戶中的資源群組。 您可以使用 [Azure 入口網站](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal)、 [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-powershell)或 [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-cli)建立一個。

您可以使用 [Azure 入口網站](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal)、 [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-powershell)、 [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-cli)或 [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-rest)來檢查現有的角色。


## <a name="moving-a-key-vault-to-a-new-subscription"></a>將金鑰保存庫移至新的訂用帳戶

1. 在 https://portal.azure.com 登入 Azure 入口網站。
2. 流覽至您的 [金鑰保存庫](overview.md)
3. 按一下 [總覽] 索引標籤
4. 選取 [移動] 按鈕
5. 從下拉式選項中選取 [移至另一個訂用帳戶]
6. 選取您要在其中移動金鑰保存庫的資源群組
7. 確認關於移動資源的警告
8. 選取 [確定]

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>當訂用帳戶位於新的租使用者時的其他步驟

如果您將金鑰保存庫移至新租使用者中的訂用帳戶，您需要手動更新租使用者識別碼，並移除舊的存取原則和角色指派。 以下是 PowerShell 和 Azure CLI 中這些步驟的教學課程。 如果您使用 PowerShell，您可能需要執行以下所述的 Clear-AzContext 命令，讓您能夠查看目前所選範圍之外的資源。 

### <a name="update-tenant-id-in-a-key-vault"></a>更新金鑰保存庫中的租使用者識別碼

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```
### <a name="update-access-policies-and-role-assignments"></a>更新存取原則和角色指派

> [!NOTE]
> 如果 Key Vault 使用 [AZURE RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) 許可權模型。 您也必須移除金鑰保存庫角色指派。 您可以使用 [Azure 入口網站](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)、 [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)或 [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)來移除角色指派。 

現在，您的保存庫已與正確的租使用者識別碼相關聯，且已移除舊的存取原則專案或角色指派，請設定新的存取原則專案或角色指派。

若要指派原則，請參閱：
- [使用入口網站指派存取原則](assign-access-policy-portal.md)
- [使用 Azure CLI 指派存取原則](assign-access-policy-cli.md)
- [使用 PowerShell 指派存取原則](assign-access-policy-powershell.md)

若要新增角色指派，請參閱：
- [使用入口網站新增角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
- [使用 Azure CLI 新增角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)
- [使用 PowerShell 新增角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)


### <a name="update-managed-identities"></a>更新受控識別

如果您要傳送整個訂用帳戶，並使用適用于 Azure 資源的受控識別，您也必須將其更新為新的 Azure Active Directory 租使用者。 如需受控識別的詳細資訊，請 [流覽受控識別](../../active-directory/managed-identities-azure-resources/overview.md)。

如果您使用受控識別，您也必須更新身分識別，因為舊的身分識別將不再位於正確的 Azure Active Directory 租使用者中。 請參閱下列檔，以協助解決此問題。 

* [更新 MSI](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [將訂用帳戶轉移至新的目錄](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>後續步驟

- 深入了解[金鑰、祕密與憑證](about-keys-secrets-certificates.md)
- 如需概念性資訊（包括如何解讀 Key Vault 記錄），請參閱 [Key Vault 記錄](logging.md)
- [Key Vault 開發人員指南](../general/developers-guide.md)
- [保護您的金鑰保存庫](secure-your-key-vault.md)
- [設定 Azure Key Vault 防火牆和虛擬網路](network-security.md)
