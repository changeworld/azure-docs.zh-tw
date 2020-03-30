---
title: 使用 PowerShell 配置客戶管理的金鑰
titleSuffix: Azure Storage
description: 瞭解如何使用 PowerShell 配置客戶管理的金鑰以進行 Azure 存儲加密。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 264dbbaedca5a28c8741d699a683b3e2b2385383
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061146"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>使用 PowerShell 使用 Azure 金鑰保存庫配置客戶管理的金鑰

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文演示如何使用 PowerShell 使用客戶管理的金鑰配置 Azure 金鑰保存庫。 若要瞭解如何使用 Azure CLI 創建金鑰保存庫，請參閱[快速入門：使用 PowerShell 從 Azure 金鑰保存庫設置和檢索機密](../../key-vault/quick-create-powershell.md)。

## <a name="assign-an-identity-to-the-storage-account"></a>將標識分配給存儲帳戶

要為存儲帳戶啟用客戶管理的金鑰，請先將系統分配的託管標識分配給存儲帳戶。 您將使用此託管標識授予訪問金鑰保存庫的存儲帳戶許可權。

要使用 PowerShell 分配託管標識，請調用[Set-AzStorage 帳戶](/powershell/module/az.storage/set-azstorageaccount)。 請記住將括弧中的預留位置值替換為您自己的值。

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

有關使用 PowerShell 配置系統分配的託管標識的詳細資訊，請參閱[使用 PowerShell 在 Azure VM 上配置 Azure 資源的託管標識](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)。

## <a name="create-a-new-key-vault"></a>創建新金鑰保存庫

要使用 PowerShell 創建新金鑰保存庫，請致電[New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)。 用於存儲 Azure 存儲加密的客戶管理金鑰的金鑰保存庫必須啟用兩個金鑰保護設置，**即"虛刪除****"和"不清除**"。

請記住將括弧中的預留位置值替換為您自己的值。

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

要瞭解如何使用 PowerShell 在現有金鑰保存庫中啟用 **"虛刪除****"和"不清除"，** 請參閱標題為"**啟用虛刪除**和**啟用清除保護**"的部分，瞭解如何[使用 PowerShell 使用虛刪除](../../key-vault/key-vault-soft-delete-powershell.md)。

## <a name="configure-the-key-vault-access-policy"></a>配置金鑰保存庫訪問策略

接下來，為金鑰保存庫配置訪問策略，以便存儲帳戶具有訪問它的許可權。 在此步驟中，您將使用以前分配給存儲帳戶的託管標識。

要設置金鑰保存庫的訪問策略，請調用[Set-AzKeyVault 訪問策略](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)。 請記住將括弧中的預留位置值替換為您自己的值，並使用前面示例中定義的變數。

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>建立新的金鑰

接下來，在金鑰保存庫中創建一個新金鑰。 要創建新金鑰，請調用[Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)。 請記住將括弧中的預留位置值替換為您自己的值，並使用前面示例中定義的變數。

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>使用客戶管理的金鑰配置加密

預設情況下，Azure 存儲加密使用 Microsoft 管理的金鑰。 在此步驟中，將 Azure 存儲帳戶配置為使用客戶管理的金鑰，並指定要與存儲帳戶關聯的金鑰。

調用[Set-AzStorage 帳戶](/powershell/module/az.storage/set-azstorageaccount)以更新存儲帳戶的加密設定，如以下示例所示。 包括 **-Keyvault 加密**選項，用於為存儲帳戶啟用客戶管理的金鑰。 請記住將括弧中的預留位置值替換為您自己的值，並使用前面示例中定義的變數。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>更新金鑰版本

創建金鑰的新版本時，需要更新存儲帳戶才能使用新版本。 首先，調用[Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey)獲取金鑰的最新版本。 然後調用[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)更新存儲帳戶的加密設定以使用金鑰的新版本，如上一節所示。

## <a name="use-a-different-key"></a>使用其他鍵

要更改用於 Azure 存儲加密的金鑰，請調用[Set-AzStorageAccount，](/powershell/module/az.storage/set-azstorageaccount)如[使用客戶管理的金鑰配置加密](#configure-encryption-with-customer-managed-keys)並提供新的金鑰名稱和版本。 如果新金鑰位於其他金鑰保存庫中，則還要更新金鑰保存庫 URI。

## <a name="revoke-customer-managed-keys"></a>撤銷客戶管理的金鑰

如果您認為金鑰可能已洩露，則可以通過刪除金鑰保存庫訪問策略來撤銷客戶管理的金鑰。 要撤銷客戶管理的金鑰，請調用[刪除-AzKeyVaultAccessPolicy 命令](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy)，如以下示例所示。 請記住將括弧中的預留位置值替換為您自己的值，並使用前面示例中定義的變數。

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>禁用客戶管理的金鑰

禁用客戶管理的金鑰時，您的存儲帳戶將再次使用 Microsoft 管理的金鑰進行加密。 要禁用客戶管理的金鑰，請使用`-StorageEncryption`選項調用[Set-AzStorage 帳戶](/powershell/module/az.storage/set-azstorageaccount)，如以下示例所示。 請記住將括弧中的預留位置值替換為您自己的值，並使用前面示例中定義的變數。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>後續步驟

- [靜態資料的 Azure 存儲加密](storage-service-encryption.md)
- [什麼是 Azure 金鑰保存庫](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
