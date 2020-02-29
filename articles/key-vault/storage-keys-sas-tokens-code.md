---
title: Azure Key Vault 受控儲存體帳戶 - PowerShell 版本
description: 受控儲存體帳戶功能可提供 Azure Key Vault 與 Azure 儲存體帳戶之間的完美整合。
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: cbd7bd034c5cbbdf5308ec660a96dc52a9a2b310
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200697"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>擷取程式碼中的共用存取簽章權杖

您可以使用金鑰保存庫中的[共用存取](../storage/common/storage-dotnet-shared-access-signature-part-1.md)簽章權杖來管理儲存體帳戶。 本文提供的範例C#會提取 SAS 權杖，並使用它來執行作業。  如需如何建立及儲存 SAS 權杖的相關資訊，請參閱[使用 Key Vault 和 Azure CLI 管理儲存體帳戶金鑰](key-vault-ovw-storage-keys.md)，或[使用 Key Vault 和 Azure PowerShell 來管理儲存體帳戶金鑰](key-vault-overview-storage-keys-powershell.md)。

## <a name="code-samples"></a>程式碼範例

在此範例中，程式碼會從您的金鑰保存庫提取 SAS 權杖，並使用它來建立新的儲存體帳戶，並建立新的 Blob 服務用戶端。  

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

如果您的共用存取簽章權杖即將到期，您可以從金鑰保存庫提取共用存取簽章權杖，並更新程式碼。

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>後續步驟
- 瞭解如何[使用 Key Vault 和 Azure CLI 或 [Azure PowerShell](key-vault-overview-storage-keys-powershell.md) 來管理儲存體帳戶金鑰](key-vault-ovw-storage-keys.md)。
- 請參閱[受控儲存體帳戶金鑰範例](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [關於金鑰、密碼與憑證](about-keys-secrets-and-certificates.md)
- [Key Vault PowerShell 參考](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
