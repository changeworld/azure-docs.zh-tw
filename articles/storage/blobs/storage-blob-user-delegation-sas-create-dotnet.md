---
title: 使用 .NET 為容器或 Blob 創建使用者委派 SAS
titleSuffix: Azure Storage
description: 瞭解如何使用 .NET 用戶端庫進行 Azure 存儲，使用 Azure 活動目錄憑據創建使用者委派 SAS。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 385d2c3b88bc2e4d653dae2dc9670cb9e9388faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371831"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>為具有 .NET 的容器或 blob 創建使用者委派 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文演示如何使用 Azure 活動目錄 （Azure AD） 憑據為容器或 Blob 創建使用者委派 SAS 的容器或 Blob 的 Azure 存儲用戶端庫為 .NET。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-rbac-roles-for-access-to-data"></a>分配 RBAC 角色以訪問資料

當 Azure AD 安全主體嘗試訪問 Blob 資料時，該安全主體必須具有對資源的許可權。 無論安全主體是 Azure 中的託管標識還是開發環境中運行代碼的 Azure AD 使用者帳戶，都必須為安全主體分配一個 RBAC 角色，該角色授予對 Azure 存儲中的 Blob 資料的訪問。 有關通過 RBAC 分配許可權的資訊，請參閱標題為 **"分配 RBAC 角色以使用** [Azure 活動目錄 訪問 Azure blob 和佇列](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)"中的存取權限的部分。

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

要瞭解有關如何使用 Azure 存儲中的 Azure 標識用戶端庫進行身份驗證的詳細資訊，請參閱"使用**Azure 標識庫進行身份驗證**"的部分，這些部分將[授權訪問具有 Azure 活動目錄的 Blob 和佇列以及 Azure 資源的託管標識](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)。

## <a name="add-using-directives"></a>新增 using 指示詞

將以下`using`指令添加到代碼中，以使用 Azure 標識和 Azure 存儲用戶端庫。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure;
using Azure.Identity;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>獲取經過身份驗證的權杖憑據

要獲取代碼可用於授權對 Azure 存儲的請求的權杖憑據，請創建[預設 Azure 憑據](/dotnet/api/azure.identity.defaultazurecredential)類的實例。

以下程式碼片段演示如何獲取經過身份驗證的權杖憑據，並使用它為 Blob 存儲創建服務用戶端：

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>獲取使用者委派金鑰

每個 SAS 都使用金鑰進行簽名。 要創建使用者委派 SAS，必須首先請求使用者委派金鑰，然後用於對 SAS 進行簽名。 使用者委派金鑰類似于用於對服務 SAS 或帳戶 SAS 進行簽名的帳戶金鑰，只不過它依賴于 Azure AD 憑據。 當用戶端使用 OAuth 2.0 權杖請求使用者委派金鑰時，Azure 存儲會代表使用者返回使用者委派金鑰。

獲得使用者委派金鑰後，可以使用該金鑰在金鑰的存留期內創建任意數量的使用者委派共用訪問簽名。 使用者委派金鑰獨立于用於獲取它的 OAuth 2.0 權杖，因此只要金鑰仍然有效，就不需要續訂權杖。 您可以指定金鑰的有效期最長為 7 天。

使用以下方法之一請求使用者委派金鑰：

- [獲取使用者委派金鑰](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [獲取使用者授權鍵同步](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

以下程式碼片段獲取使用者委派金鑰並寫入其屬性：

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>創建 SAS 權杖

以下程式碼片段顯示創建新的[BlobSasBuilder，](/dotnet/api/azure.storage.sas.blobsasbuilder)並為使用者委派 SAS 提供參數。 然後，程式碼片段調用[ToSasQuery 參數](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters)來獲取 SAS 權杖字串。 最後，代碼生成完整的 URI，包括資源位址和 SAS 權杖。

```csharp
// Create a SAS token that's valid for one hour.
BlobSasBuilder sasBuilder = new BlobSasBuilder()
{
    BlobContainerName = containerName,
    BlobName = blobName,
    Resource = "b",
    StartsOn = DateTimeOffset.UtcNow,
    ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
};

// Specify read permissions for the SAS.
sasBuilder.SetPermissions(BlobSasPermissions.Read);

// Use the key to get the SAS token.
string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

// Construct the full URI, including the SAS token.
UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>示例：獲取使用者委派 SAS

下面的示例方法顯示了用於驗證安全主體和創建使用者委派 SAS 的完整代碼：

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // You can use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow, 
                                                                        DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
    Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);
    Console.WriteLine();

    // Create a SAS token that's valid for one hour.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        BlobContainerName = containerName,
        BlobName = blobName,
        Resource = "b",
        StartsOn = DateTimeOffset.UtcNow,
        ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
    };

    // Specify read permissions for the SAS.
    sasBuilder.SetPermissions(BlobSasPermissions.Read);

    // Use the key to get the SAS token.
    string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

    // Construct the full URI, including the SAS token.
    UriBuilder fullUri = new UriBuilder()
    {
        Scheme = "https",
        Host = string.Format("{0}.blob.core.windows.net", accountName),
        Path = string.Format("{0}/{1}", containerName, blobName),
        Query = sasToken
    };

    Console.WriteLine("User delegation SAS URI: {0}", fullUri);
    Console.WriteLine();
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>示例：使用使用者委派 SAS 讀取 Blob

以下示例測試從類比用戶端應用程式在上一個示例中創建的使用者委派 SAS。 如果 SAS 有效，則用戶端應用程式能夠讀取 blob 的內容。 如果 SAS 無效（例如，如果已過期），Azure 存儲將返回錯誤代碼 403（禁止）。

```csharp
private static async Task ReadBlobWithSasAsync(Uri sasUri)
{
    // Try performing blob operations using the SAS provided.

    // Create a blob client object for blob operations.
    BlobClient blobClient = new BlobClient(sasUri, null);

    // Download and read the contents of the blob.
    try
    {
        // Download blob contents to a stream and read the stream.
        BlobDownloadInfo blobDownloadInfo = await blobClient.DownloadAsync();
        using (StreamReader reader = new StreamReader(blobDownloadInfo.Content, true))
        {
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }

        Console.WriteLine();
        Console.WriteLine("Read operation succeeded for SAS {0}", sasUri);
        Console.WriteLine();
    }
    catch (RequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid,
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Read operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>另請參閱

- [使用共用訪問簽名 （SAS） 授予對 Azure 存儲資源的有限存取權限](../common/storage-sas-overview.md)
- [獲取使用者委派金鑰操作](/rest/api/storageservices/get-user-delegation-key)
- [創建使用者委派 SAS （REST API）](/rest/api/storageservices/create-user-delegation-sas)
