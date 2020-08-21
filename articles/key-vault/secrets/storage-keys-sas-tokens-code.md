---
title: 擷取程式碼中的共用存取簽章權杖 | Azure Key Vault
description: 受控儲存體帳戶功能可在 Azure Key Vault 與 Azure 儲存體帳戶之間提供完美整合。
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 6530434e36f7c9a9a60a9782bcf2dce7ba447dab
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88584908"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>擷取程式碼中的共用存取簽章權杖

您可以使用儲存在金鑰保存庫中的共用存取簽章 (SAS) 權杖來管理儲存體帳戶。 如需詳細資訊，請參閱[使用 SAS 授與 Azure 儲存體資源的存取權](../../storage/common/storage-sas-overview.md)。

本文提供的 .NET 程式碼範例會擷取 SAS 權杖，並用來執行作業。 如需如何建立及儲存 SAS 權杖的相關資訊，請參閱[使用 Key Vault 和 Azure CLI 管理儲存體帳戶金鑰](overview-storage-keys.md)，或[使用 Key Vault 和 Azure PowerShell 管理儲存體帳戶金鑰](overview-storage-keys-powershell.md)。

## <a name="code-samples"></a>程式碼範例

在此範例中，程式碼會從您的金鑰保存庫擷取 SAS 權杖，並用來建立新的儲存體帳戶，同時建立新的 Blob 服務用戶端。

```cs
// The shared access signature is stored as a secret in keyvault. 
// After you get a security token, create a new SecretClient with vault credentials and the key vault URI.
// The format for the key vault URI (kvuri) is https://<YourKeyVaultName>.vault.azure.net

var kv = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

// Now retrive your storage SAS token from Key Vault using the name of the secret (secretName).

KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;

// Create new storage credentials using the SAS token.
StorageCredentials accountSAS = new StorageCredentials(sasToken);

// Use these credentials and your storage account name to create a Blob service client.
CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();
```

如果您的共用存取簽章權杖即將到期，可以從金鑰保存庫擷取共用存取簽章權杖，並更新程式碼。

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;
accountSAS.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>後續步驟
- 了解如何[使用 SAS 授與 Azure 儲存體資源的存取權](../../storage/common/storage-sas-overview.md)。
- 瞭解如何[使用 Key Vault 和 Azure CLI 或 [Azure PowerShell](overview-storage-keys-powershell.md) 來管理儲存體帳戶金鑰](overview-storage-keys.md)。
- 請參閱[受控儲存體帳戶金鑰範例](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
