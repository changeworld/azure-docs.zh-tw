---
title: 在使用 .NET 的 Blob 儲存體要求上指定客戶提供的金鑰-Azure 儲存體
description: 瞭解如何使用 .NET 在對 Blob 儲存體的要求上指定客戶提供的金鑰。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: c3096da8b3c83dbfe8cfdd6a5fa4d177241334de
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693510"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>使用 .NET 在對 Blob 儲存體的要求上指定客戶提供的金鑰

對 Azure Blob 儲存體提出要求的用戶端，可以選擇在個別要求上提供 AES-256 加密金鑰。 在要求中包含加密金鑰可讓您更精確地控制 Blob 儲存體作業的加密設定。 客戶提供的金鑰可以儲存在 Azure Key Vault 或另一個金鑰存放區中。

本文說明如何在使用 .NET 的要求上指定客戶提供的金鑰。

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

若要深入瞭解如何使用 Azure 身分識別用戶端程式庫進行驗證，請參閱 [適用于 .net 的 azure 身分識別用戶端程式庫](/dotnet/api/overview/azure/identity-readme)。

## <a name="use-a-customer-provided-key-to-write-to-a-blob"></a>使用客戶提供的金鑰來寫入 blob

下列範例會在使用適用于 Blob 儲存體的 v12 用戶端程式庫上傳 blob 時，提供 AES-256 金鑰。 此範例會使用 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 物件來授權 Azure AD 的寫入要求，但您也可以使用共用金鑰認證來授權要求。

```csharp
async static Task UploadBlobWithClientKey(Uri blobUri,
                                          Stream data,
                                          byte[] key,
                                          string keySha256)
{
    // Create a new customer-provided key.
    // Key must be AES-256.
    var cpk = new CustomerProvidedKey(key);

    // Check the key's encryption hash.
    if (cpk.EncryptionKeyHash != keySha256)
    {
        throw new InvalidOperationException("The encryption key is corrupted.");
    }

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = cpk
    };

    // Create the client object with options specified.
    BlobClient blobClient = new BlobClient(
        blobUri,
        new DefaultAzureCredential(),
        options);

    // If the container may not exist yet,
    // create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient =
        blobClient.GetParentBlobContainerClient();

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>後續步驟

- [針對 Blob 儲存體的要求提供加密金鑰](encryption-customer-provided-keys.md)
- [待用資料的 Azure 儲存體加密](../common/storage-service-encryption.md)
