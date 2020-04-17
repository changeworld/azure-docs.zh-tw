---
title: 使用 PowerShell 設定客戶管理的金鑰
titleSuffix: Azure Storage
description: 瞭解如何使用 PowerShell 設定客戶管理的密鑰以進行 Azure 儲存加密。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bfc2e256396904456a7ee0fd8b6173c00a5f53d7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456391"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>使用 PowerShell 使用 Azure 金鑰保管庫設定客戶管理的金鑰

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文展示如何使用 PowerShell 使用客戶管理的密鑰配置 Azure 密鑰保管庫。 若要瞭解如何使用 Azure CLI 建立金鑰保管庫,請參閱[快速入門:使用 PowerShell 從 Azure 金鑰保管庫設定和檢索機密](../../key-vault/secrets/quick-create-powershell.md)。

## <a name="assign-an-identity-to-the-storage-account"></a>將識別配置給儲存帳戶

要為存儲帳戶啟用客戶管理的密鑰,請先將系統分配的託管標識分配給存儲帳戶。 您將使用此託管標識授予存取金鑰保管庫的儲存帳戶許可權。

要使用 PowerShell 分配管理員識別,請呼叫[Set-AzStorage 帳戶](/powershell/module/az.storage/set-azstorageaccount)。 請記住將括弧中的占位符值替換為您自己的值。

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

有關使用 PowerShell 設定系統分配的託管識別的詳細資訊,請參閱[使用 PowerShell 在 Azure VM 上設定 Azure 資源的託管標識](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)。

## <a name="create-a-new-key-vault"></a>建立新金鑰保存庫

要使用 PowerShell 建立新金鑰保管庫,請致電[New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)。 用於存儲 Azure 儲存加密的客戶管理金鑰的金鑰保管庫必須啟用兩個金鑰保護設置,**即「軟刪除****」和「不清除**」 。

請記住將括弧中的占位符值替換為您自己的值。

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

要瞭解如何使用 PowerShell 在現有金鑰保管庫中啟用 **「軟刪除****」和「不清除」 ,** 請參考標題為「**啟用軟刪除**與**開啟清除保護**」的部分,瞭解如何[使用 PowerShell 使用軟刪除](../../key-vault/general/soft-delete-powershell.md)。

## <a name="configure-the-key-vault-access-policy"></a>設定金鑰保存庫存取原則

接下來,為金鑰保管庫配置訪問策略,以便存儲帳戶具有訪問它的許可權。 在此步驟中,您將使用以前分配給存儲帳戶的託管標識。

要設定金鑰保存的金鑰,請呼叫[Set-AzKeyVault 存取原則](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)。 請記住將括弧中的占位符值替換為您自己的值,並使用前面示例中定義的變數。

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>建立新的金鑰

接下來,在密鑰保管庫中創建一個新密鑰。 要建立新金鑰,請呼叫[Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)。 請記住將括弧中的占位符值替換為您自己的值,並使用前面示例中定義的變數。

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

Azure 儲存加密僅支援 2048 位 RSA 和 RSA-HSM 密鑰。 關於金鑰的詳細資訊,請參考[「關於 Azure 金鑰保管庫金鑰、機密和憑證」](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)的**金鑰保管庫金鑰**。

## <a name="configure-encryption-with-customer-managed-keys"></a>使用客戶管理的金鑰設定加密

默認情況下,Azure 儲存加密使用Microsoft管理的密鑰。 在此步驟中,將 Azure 儲存帳戶配置為使用客戶管理的密鑰,並指定要與存儲帳戶關聯的金鑰。

調用[Set-AzStorage 帳戶](/powershell/module/az.storage/set-azstorageaccount)以更新儲存帳戶的加密設置,如以下範例所示。 包括 **-Keyvault 加密**選項,用於為存儲帳戶啟用客戶管理的密鑰。 請記住將括弧中的占位符值替換為您自己的值,並使用前面示例中定義的變數。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>更新金鑰版本

建立金鑰的新版本時,需要更新存儲帳戶才能使用新版本。 首先,調用[Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey)獲取密鑰的最新版本。 然後調用[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)更新儲存帳戶的加密設置以使用密鑰的新版本,如上一節所示。

## <a name="use-a-different-key"></a>使用其他鍵

要更改用於 Azure 儲存加密的金鑰,請呼叫[Set-AzStorageAccount,](/powershell/module/az.storage/set-azstorageaccount)如[使用客戶管理的金鑰設定加密](#configure-encryption-with-customer-managed-keys)並提供新的密鑰名稱和版本。 如果新密鑰位於其他密鑰保管庫中,則還要更新密鑰保管庫URI。

## <a name="revoke-customer-managed-keys"></a>復原客戶管理的金鑰

如果您認為密鑰可能已洩露,則可以通過刪除密鑰保管庫訪問策略來撤銷客戶管理的金鑰。 要撤銷客戶管理的金鑰,請呼叫[刪除-AzKeyVaultAccessPolicy 命令](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy),如以下範例所示。 請記住將括弧中的占位符值替換為您自己的值,並使用前面示例中定義的變數。

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>關閉客戶管理的金鑰

禁用客戶管理的密鑰時,您的儲存帳戶將再次使用 Microsoft 管理的密鑰進行加密。 要關閉客戶管理的金鑰,請使用`-StorageEncryption`選項調用[Set-AzStorage 帳戶](/powershell/module/az.storage/set-azstorageaccount),如以下範例所示。 請記住將括弧中的占位符值替換為您自己的值,並使用前面示例中定義的變數。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>後續步驟

- [靜態資料的 Azure 儲存加密](storage-service-encryption.md)
- [什麼是 Azure 金鑰保存嗎](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
