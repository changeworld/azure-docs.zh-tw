---
title: 使用 .NET 建立容器或 blob 的服務 SAS
titleSuffix: Azure Storage
description: 瞭解如何使用 .NET 用戶端程式庫，為容器或 blob (SAS) 建立服務共用存取簽章。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/05/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: eab8993a98ce202964ade9fb5deae5c741ac7eb6
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090347"
---
# <a name="create-a-service-sas-for-a-container-or-blob-with-net"></a>使用 .NET 建立容器或 blob 的服務 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文說明如何使用儲存體帳戶金鑰，透過 [適用于 .net 的 Azure 儲存體用戶端程式庫](/dotnet/api/overview/azure/storage)來建立容器或 blob 的服務 SAS。

## <a name="create-a-service-sas-for-a-blob-container"></a>建立 blob 容器的服務 SAS

下列程式碼範例會建立容器的 SAS。 如果提供現有預存存取原則的名稱，該原則將與 SAS 相關聯。 如果未提供任何預存存取原則，則程式碼會在容器上建立特定 SAS。

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

服務 SAS 會以帳戶存取金鑰簽署。 使用 [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) 類別來建立用來簽署 SAS 的認證。 接著，建立新的 [>blobsasbuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) 物件，並呼叫 [TOSASQUERYPARAMETERS](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) 以取得 SAS 權杖字串。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetContainerSasUri":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

若要建立容器的服務 SAS，請呼叫 [>cloudblobcontainer.listblobs. >cloudblobcontainer.getsharedaccesssignature](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature) 方法。

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}

```
---

## <a name="create-a-service-sas-for-a-blob"></a>建立 blob 的服務 SAS

下列程式碼範例會在 blob 上建立 SAS。 如果提供現有預存存取原則的名稱，該原則將與 SAS 相關聯。 如果未提供任何預存存取原則，則程式碼會在 blob 上建立特定 SAS。

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

服務 SAS 會以帳戶存取金鑰簽署。 使用 [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) 類別來建立用來簽署 SAS 的認證。 接著，建立新的 [>blobsasbuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) 物件，並呼叫 [TOSASQUERYPARAMETERS](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) 以取得 SAS 權杖字串。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetBlobSasUri":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

若要建立 blob 的服務 SAS，請呼叫 [cloudblob.. >cloudblobcontainer.getsharedaccesssignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) 方法。

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>後續步驟

- [使用 (SAS) 的共用存取簽章來授與 Azure 儲存體資源的有限存取權 ](../common/storage-sas-overview.md)
- [建立服務 SAS](/rest/api/storageservices/create-service-sas)
