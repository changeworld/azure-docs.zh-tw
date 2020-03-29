---
title: 在使用 .NET - Azure 存儲請求 Blob 存儲時指定客戶提供的金鑰
description: 瞭解如何使用 .NET 在請求 Blob 存儲時指定客戶提供的金鑰。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ea8254f108aed9d40e6970a27409035b1e10ab41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806995"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>在請求使用 .NET 的 Blob 存儲時指定客戶提供的金鑰

針對 Azure Blob 存儲發出請求的用戶端可以選擇在單個請求上提供加密金鑰。 在請求中包括加密金鑰可對 Blob 存儲操作的加密設定進行精細控制。 客戶提供的金鑰（預覽）可以存儲在 Azure 金鑰保存庫或其他金鑰存儲中。

本文演示如何在 .NET 的請求上指定客戶提供的金鑰。

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

要瞭解有關如何使用 Azure 存儲中的 Azure 標識用戶端庫進行身份驗證的詳細資訊，請參閱"使用**Azure 標識庫進行身份驗證**"的部分，這些部分將[授權訪問具有 Azure 活動目錄的 Blob 和佇列以及 Azure 資源的託管標識](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)。

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>示例：使用客戶提供的金鑰上載 Blob

下面的示例創建客戶提供的金鑰，並使用該金鑰上載 Blob。 代碼上載塊，然後將塊清單提交到 Azure 存儲。

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

- [靜態資料的 Azure 存儲加密](../common/storage-service-encryption.md)
- [使用 Azure 活動目錄和 Azure 資源的託管標識授權對 Blob 和佇列的訪問](../common/storage-auth-aad-msi.md)
