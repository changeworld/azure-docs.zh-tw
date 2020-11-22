---
title: 建立容器或 blob 的服務 SAS
titleSuffix: Azure Storage
description: 瞭解如何使用適用于 Blob 儲存體 Azure 儲存體程式庫 (SAS) 來建立容器或 blob 的服務共用存取簽章。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/20/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 0b2d18165bf2c5a4f70f1cbc555db79020ce988f
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2020
ms.locfileid: "95250612"
---
# <a name="create-a-service-sas-for-a-container-or-blob"></a>建立容器或 blob 的服務 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文說明如何使用儲存體帳戶金鑰，以適用于 Blob 儲存體的 Azure 儲存體用戶端程式庫來建立容器或 blob 的服務 SAS。

## <a name="create-a-service-sas-for-a-blob-container"></a>建立 blob 容器的服務 SAS

下列程式碼範例會建立容器的 SAS。 如果提供現有預存存取原則的名稱，該原則將與 SAS 相關聯。 如果未提供任何預存存取原則，則程式碼會在容器上建立特定 SAS。

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

服務 SAS 會以帳戶存取金鑰簽署。 使用 [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) 類別來建立用來簽署 SAS 的認證。 接著，建立新的 [>blobsasbuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) 物件，並呼叫 [TOSASQUERYPARAMETERS](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) 以取得 SAS 權杖字串。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetServiceSasUriForContainer":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

若要建立容器的服務 SAS，請呼叫 [>cloudblobcontainer.listblobs. >cloudblobcontainer.getsharedaccesssignature](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature) 方法。

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, 
                                         string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define
        // the parameters of an ad hoc SAS, and to construct a shared access policy
        // that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed
            // to be the time when the storage service receives the request. Omitting
            // the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container,
        // setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case,
        // all of the constraints for the shared access signature are specified
        // on the stored access policy, which is provided by name. It is also possible
        // to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

服務 SAS 會以帳戶存取金鑰簽署。 使用 [StorageSharedKeyCredential](/javascript/api/@azure/storage-blob/storagesharedkeycredential) 類別來建立用來簽署 SAS 的認證。 接下來，呼叫 [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) 函式以提供必要的選項，以取得 SAS 權杖字串。

```javascript
function getContainerSasUri(containerClient, sharedKeyCredential, storedPolicyName) {
    const sasOptions = {
        containerName: containerClient.containerName,
        permissions: ContainerSASPermissions.parse("c")
    };

    if (storedPolicyName == null) {
        sasOptions.startsOn = new Date();
        sasOptions.expiresOn = new Date(new Date().valueOf() + 3600 * 1000);
    } else {
        sasOptions.identifier = storedPolicyName;
    }

    const sasToken = generateBlobSASQueryParameters(sasOptions, sharedKeyCredential).toString();
    console.log(`SAS token for blob container is: ${sasToken}`);

    return `${containerClient.url}?${sasToken}`;
}
```

---

## <a name="create-a-service-sas-for-a-blob"></a>建立 blob 的服務 SAS

下列程式碼範例會在 blob 上建立 SAS。 如果提供現有預存存取原則的名稱，該原則將與 SAS 相關聯。 如果未提供任何預存存取原則，則程式碼會在 blob 上建立特定 SAS。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

服務 SAS 會以帳戶存取金鑰簽署。 使用 [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) 類別來建立用來簽署 SAS 的認證。 接著，建立新的 [>blobsasbuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) 物件，並呼叫 [TOSASQUERYPARAMETERS](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) 以取得 SAS 權杖字串。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetServiceSasUriForBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

若要建立 blob 的服務 SAS，請呼叫 [cloudblob.. >cloudblobcontainer.getsharedaccesssignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) 方法。

```csharp
private static string GetBlobSasUri(CloudBlobContainer container,
                                    string blobName,
                                    string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters
        // of an ad hoc SAS, and to construct a shared access policy that is saved to
        // the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be
            // the time when the storage service receives the request. Omitting the start time
            // for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read |
                          SharedAccessBlobPermissions.Write |
                          SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob,
        // setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints
        // for the SAS are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

若要建立 blob 的服務 SAS，請呼叫 [cloudblob.. >cloudblobcontainer.getsharedaccesssignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) 方法。

若要建立 blob 的服務 SAS，請呼叫 [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) 函數，以提供必要的選項。

```javascript
function getBlobSasUri(containerClient, blobName, sharedKeyCredential, storedPolicyName) {
    const sasOptions = {
        containerName: containerClient.containerName,
        blobName: blobName
    };

    if (storedPolicyName == null) {
        sasOptions.startsOn = new Date();
        sasOptions.expiresOn = new Date(new Date().valueOf() + 3600 * 1000);
        sasOptions.permissions = BlobSASPermissions.parse("r");
    } else {
        sasOptions.identifier = storedPolicyName;
    }

    const sasToken = generateBlobSASQueryParameters(sasOptions, sharedKeyCredential).toString();
    console.log(`SAS token for blob is: ${sasToken}`);

    return `${containerClient.getBlockBlobClient(blobName).url}?${sasToken}`;
}
```

---

## <a name="create-a-service-sas-for-a-directory"></a>建立目錄的服務 SAS

在啟用階層命名空間的儲存體帳戶中，您可以建立目錄的服務 SAS。 若要建立服務 SAS，請確定您已安裝12.5.0 或更新版本的 [DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) 套件。

下列範例示範如何使用適用于 .NET 的 v12 用戶端程式庫來建立目錄的服務 SAS：

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetServiceSasUriForDirectory":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

[!INCLUDE [storage-blob-javascript-resources-include](../../../includes/storage-blob-javascript-resources-include.md)]

## <a name="next-steps"></a>後續步驟

- [使用 (SAS) 的共用存取簽章來授與 Azure 儲存體資源的有限存取權 ](../common/storage-sas-overview.md)
- [建立服務 SAS](/rest/api/storageservices/create-service-sas)
