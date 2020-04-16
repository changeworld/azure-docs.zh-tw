---
title: Azure Key Vault 受控儲存體帳戶 - PowerShell 版本
description: 託管儲存帳戶功能在 Azure 密鑰保管庫和 Azure 儲存帳戶之間提供無縫整合。
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 7307741e56c7fc912f60d0496979243eb4be77a4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431263"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>擷取程式碼中的共用存取簽章權杖

您可以使用金鑰保管庫中的[共享存取簽署權杖](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)管理儲存帳戶。 本文提供了獲取 SAS 權杖並執行操作的 C# 代碼的範例。  此如何建立與儲存 SAS 權杖資訊,請參考[金鑰保存與 Azure CLI 管理儲存帳戶金鑰](overview-storage-keys.md),或使用[金鑰保存庫和 Azure PowerShell 管理儲存帳戶金鑰](overview-storage-keys-powershell.md)。

## <a name="code-samples"></a>程式碼範例

在此示例中,代碼從密鑰保管庫獲取 SAS 令牌,使用它創建新的存儲帳戶,並創建新的 Blob 服務用戶端。  

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<YourKeyVaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

如果您的共享訪問簽名權杖即將過期,則可以從金鑰保管庫獲取共享存取簽名權杖並更新代碼。

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>後續步驟
- 瞭解如何[使用 Key Vault 和 Azure CLI 或 [Azure PowerShell](overview-storage-keys-powershell.md) 來管理儲存體帳戶金鑰](overview-storage-keys.md)。
- 請參考[託管儲存帳戶金鑰範例](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Key Vault PowerShell 參考](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
