---
title: 在使用 .NET 的 Blob 儲存體要求上指定客戶提供的金鑰-Azure 儲存體
description: 瞭解如何使用 .NET 在對 Blob 儲存體的要求上指定客戶提供的金鑰。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/20/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 001b1e0b9c738e263e8425f43076ef6e451a297f
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018819"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>使用 .NET 在對 Blob 儲存體的要求上指定客戶提供的金鑰

對 Azure Blob 儲存體提出要求的用戶端，可以選擇在個別要求上提供加密金鑰。 在要求中包含加密金鑰可讓您更精確地控制 Blob 儲存體作業的加密設定。 客戶提供的金鑰可以儲存在 Azure Key Vault 或另一個金鑰存放區中。

本文說明如何在使用 .NET 的要求上指定客戶提供的金鑰。

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

若要深入瞭解如何向 Azure 儲存體驗證 Azure 身分識別用戶端程式庫，請參閱標題為使用 **Azure 身分識別程式庫進行驗證** 的一節， [其中包含 azure 資源的 Azure Active Directory 和受控識別授權存取 blob 和佇列](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)。

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>範例：使用客戶提供的金鑰上傳 blob

下列範例會建立客戶提供的金鑰，並使用該金鑰來上傳 blob。 程式碼會上傳區塊，然後認可封鎖清單以將 blob 寫入 Azure 儲存體。

```csharp
async static Task UploadBlobWithClientKey(string accountName, string containerName,
    string blobName, Stream data, byte[] key)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri accountUri = new Uri("https://" + accountName + blobServiceEndpointSuffix);

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = new CustomerProvidedKey(key)
    };

    // Create a client object for the Blob service, including options.
    BlobServiceClient serviceClient = new BlobServiceClient(accountUri, 
        new DefaultAzureCredential(), options);

    // Create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient = serviceClient.GetBlobContainerClient(containerName);

    // Create a new block blob client object.
    // The blob client retains the credential and client options.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

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

- [待用資料的 Azure 儲存體加密](../common/storage-service-encryption.md)
- [使用適用于 Azure 資源的 Azure Active Directory 和受控識別來授權 blob 和佇列的存取權](../common/storage-auth-aad-msi.md)
