---
title: 還原金鑰保存庫金鑰&加密 VM 的機密
description: 了解如何使用 PowerShell 以 Azure 備份還原金鑰保存庫金鑰與密碼
ms.topic: conceptual
ms.date: 08/28/2017
ms.openlocfilehash: 35bcb919cadd46c603b1f2ad49742c5435f873d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75450058"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>使用 Azure 備份還原已加密 VM 的金鑰保存庫金鑰與密碼

本文將討論如果您的金鑰和密碼不存在於金鑰保存庫中時，如何使用 Azure VM 備份還原已加密的 Azure VM。 這些步驟也可用於您想要為已還原的 VM 另外維護一份金鑰 (金鑰加密金鑰) 與密碼 (BitLocker 加密金鑰) 時。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

* **備份已加密的 VM** - 已使用 Azure 備份將已加密的 Azure VM 備份。 有關如何備份加密 Azure VM 的詳細資訊，請參閱[使用 PowerShell 管理 Azure VM 的備份和恢復](backup-azure-vms-automation.md)一文。
* **設定 Azure 金鑰保存庫** – 先確定金鑰保存庫已存在，才能將金鑰和密碼還原至該金鑰保存庫。 請參閱[開始使用 Azure 金鑰保存庫](../key-vault/key-vault-get-started.md)一文，以取得金鑰保存庫管理的相關詳細資料。
* **還原磁碟** - 請確定您已使用 [PowerShell 步驟](backup-azure-vms-automation.md#restore-an-azure-vm)觸發還原作業，以還原加密 VM 的磁碟。 這是因為此作業會在您的儲存體帳戶中產生 JSON 檔案，其中包含要還原之加密 VM 的金鑰和祕密。

## <a name="get-key-and-secret-from-azure-backup"></a>從 Azure 備份取得金鑰和祕密

> [!NOTE]
> 一旦還原加密 VM 的磁碟後，請確定：
>
> * 系統會將還原磁碟作業詳細資料填入 $details，如[還原 [磁碟] 區段中的 PowerShell 步驟](backup-azure-vms-automation.md#restore-an-azure-vm)中所述
> * 僅在**將金鑰和秘密還原至金鑰保存庫之後**，才應該從還原的硬碟建立 VM。

查詢工作詳細資料的已還原磁碟內容。

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

設定 Azure 儲存體內容並還原 JSON 組態檔，其中包含加密 VM 之金鑰和秘密的詳細資料。

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>還原金鑰

一旦在上述目的地路徑中產生 JSON 檔案後，請從 JSON 產生金鑰 blob 檔案，並將其饋送至還原金鑰 Cmdlet，以將金鑰 (KEK) 放回金鑰保存庫中。

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>還原密碼

使用上面所產生的 JSON 檔案來取得秘密名稱和值，並將其饋送至設定祕密 Cmdlet，以將祕密 (BEK) 放回金鑰保存庫中。如果您的**VM 使用 BEK 和 KEK 進行加密，** 請使用這些 Cmdlet。

**如果使用 BEK 和 KEK 加密您的 Windows VM，請使用這些 Cmdlet。**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**如果使用 BEK 和 KEK 加密您的 Linux VM，請使用這些 Cmdlet。**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = <Key_url_of_newly_restored_key>;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

使用上面所產生的 JSON 檔案來取得秘密名稱和值，並將其饋送至設定祕密 Cmdlet，以將祕密 (BEK) 放回金鑰保存庫中。如果**僅使用 BEK 加密您的 VM**，請使用這些 Cmdlet。

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
>
> * $secretname的值可以通過引用$encryptionObject.OsdiskKey和秘密細節的輸出獲得，秘密後使用文本/例如輸出機密 URL 是https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163，並且機密名稱為 B3284AA-DAAA-4AA-B393-60CAA848AAAAA
> * 標記磁片加密金鑰檔案名稱的值與機密名稱相同。
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>從已還原的磁碟建立虛擬機器

如果您已使用 Azure VM 備份將已加密的 VM 備份，上述的 PowerShell Cmdlet 可幫助您將金鑰與密碼還原回金鑰保存庫。 還原金鑰與密碼後，請參閱[使用 PowerShell 管理 Azure VM 的備份和還原](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)一文，從已還原的磁碟、金鑰和密碼建立加密的 VM。

## <a name="legacy-approach"></a>舊版方法

上述方法可適用於所有復原點。 不過，從復原點取得金鑰和密碼資訊的較舊方法，對於 2017 年 7 月 11 日以前使用 BEK 和 KEK 加密之 VM 的復原點仍有效。 一旦使用 [PowerShell 步驟](backup-azure-vms-automation.md#restore-an-azure-vm)完成加密 VM 的還原磁碟作業後，確保 $rp 填入有效的值。

### <a name="restore-key"></a>還原金鑰

使用下列 Cmdlet 從復原點取得金鑰 (KEK) 資訊，並將其饋送至還原金鑰 Cmdlet 以放回金鑰保存庫中。

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>還原密碼

使用下列 Cmdlet 從復原點取得祕密 (BEK) 資訊，並將其饋送至設定祕密 Cmdlet 以放回金鑰保存庫中。

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
>
> * 可透過參考 $rp1.KeyAndSecretDetails.SecretUrl 的輸出，並根據祕密之後的文字來取得 $secretname 的值/ 例如輸出秘密 URL 是 https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 且祕密名稱就是 B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * DiskEncryptionKeyFileName 標記的值與祕密名稱相同。
> * 還原回金鑰並使用 [Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey) Cmdlet 後，便可從金鑰保存庫取得 DiskEncryptionKeyEncryptionKeyURL 的值
>
>

## <a name="next-steps"></a>後續步驟

將金鑰和機密還原回金鑰保存庫後，請參閱[使用 PowerShell 管理 Azure VM 的備份和恢復](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)文章，從還原的磁片、金鑰和機密創建加密 VM。
