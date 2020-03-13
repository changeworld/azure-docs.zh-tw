---
title: 使用 PowerShell 來設定客戶管理的金鑰
titleSuffix: Azure Storage
description: 瞭解如何使用 PowerShell 來設定客戶管理的金鑰，以進行 Azure 儲存體加密。 客戶管理的金鑰可讓您建立、輪替、停用及撤銷存取控制。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: d7e4843bfbd622ad99cad4d9048e91a0cb49b1c1
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136239"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>使用 PowerShell 以 Azure Key Vault 設定客戶管理的金鑰

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文說明如何使用 PowerShell 以客戶管理的金鑰來設定 Azure Key Vault。 若要瞭解如何使用 Azure CLI 建立金鑰保存庫，請參閱[快速入門：使用 PowerShell 從 Azure Key Vault 設定和取出秘密](../../key-vault/quick-create-powershell.md)。

## <a name="assign-an-identity-to-the-storage-account"></a>將身分識別指派給儲存體帳戶

若要為您的儲存體帳戶啟用客戶管理的金鑰，請先將系統指派的受控識別指派給儲存體帳戶。 您將使用此受控識別來授與儲存體帳戶存取金鑰保存庫的許可權。

若要使用 PowerShell 指派受控識別，請呼叫[new-azstorageaccount](/powershell/module/az.storage/set-azstorageaccount)。 請記得以您自己的值取代括弧中的預留位置值。

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

如需有關使用 PowerShell 來設定系統指派的受控識別的詳細資訊，請參閱[使用 powershell 在 AZURE VM 上設定 azure 資源的受控](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)識別。

## <a name="create-a-new-key-vault"></a>建立新的金鑰保存庫

若要使用 PowerShell 建立新的金鑰保存庫，請呼叫[AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)。 您用來儲存 Azure 儲存體加密客戶管理金鑰的金鑰保存庫，必須啟用兩個金鑰保護設定： [虛**刪除**] 和 [不要**清除**]。

請記得以您自己的值取代括弧中的預留位置值。

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

若要瞭解如何使用 PowerShell 啟用「虛**刪除**」和「不要**清除**」現有的金鑰保存庫，請參閱[如何搭配使用虛刪除與 powershell](../../key-vault/key-vault-soft-delete-powershell.md)中的 < 啟用虛**刪除**和**啟用清除保護**的章節。

## <a name="configure-the-key-vault-access-policy"></a>設定 key vault 存取原則

接下來，設定金鑰保存庫的存取原則，讓儲存體帳戶有權存取它。 在此步驟中，您將使用先前指派給儲存體帳戶的受控識別。

若要設定金鑰保存庫的存取原則，請呼叫[set-set-azkeyvaultaccesspolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)。 請記得以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>建立新的金鑰

接下來，在金鑰保存庫中建立新的金鑰。 若要建立新的金鑰，請呼叫[AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)。 請記得以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>使用客戶管理的金鑰設定加密

根據預設，Azure 儲存體加密會使用 Microsoft 管理的金鑰。 在此步驟中，請將您的 Azure 儲存體帳戶設定為使用客戶管理的金鑰，並指定要與儲存體帳戶建立關聯的金鑰。

呼叫[new-azstorageaccount](/powershell/module/az.storage/set-azstorageaccount)以更新儲存體帳戶的加密設定，如下列範例所示。 包含 **-KeyvaultEncryption**選項，可為儲存體帳戶啟用客戶管理的金鑰。 請記得以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>更新金鑰版本

當您建立新版本的金鑰時，您必須更新儲存體帳戶，才能使用新版本。 首先，呼叫[AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey)以取得最新版本的金鑰。 然後呼叫[new-azstorageaccount](/powershell/module/az.storage/set-azstorageaccount) ，將儲存體帳戶的加密設定更新為使用新版本的金鑰，如上一節所示。

## <a name="use-a-different-key"></a>使用不同的金鑰

若要變更用於 Azure 儲存體加密的金鑰，請呼叫[new-azstorageaccount](/powershell/module/az.storage/set-azstorageaccount) ，如使用[客戶管理的金鑰設定加密](#configure-encryption-with-customer-managed-keys)中所示，並提供新的金鑰名稱和版本。 如果新的金鑰位於不同的金鑰保存庫中，請同時更新金鑰保存庫 URI。

## <a name="revoke-customer-managed-keys"></a>撤銷客戶管理的金鑰

如果您認為金鑰可能遭到入侵，您可以藉由移除 key vault 存取原則來撤銷客戶管理的金鑰。 若要撤銷客戶管理的金鑰，請呼叫[set-azkeyvaultaccesspolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy)命令，如下列範例所示。 請記得以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>停用客戶管理的金鑰

當您停用客戶管理的金鑰時，您的儲存體帳戶會再次使用 Microsoft 管理的金鑰進行加密。 若要停用客戶管理的金鑰，請使用 `-StorageEncryption` 選項呼叫[new-azstorageaccount](/powershell/module/az.storage/set-azstorageaccount) ，如下列範例所示。 請記得以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>後續步驟

- [待用資料的 Azure 儲存體加密](storage-service-encryption.md)
- [什麼是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
