---
title: 為具有 .NET 的容器或 blob 創建服務 SAS
titleSuffix: Azure Storage
description: 瞭解如何使用 .NET 用戶端庫為容器或 Blob 創建服務共用訪問簽名 （SAS）。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 10045a760d7e0fcb02a754bc9bb52a5ebca970f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137206"
---
# <a name="create-a-service-sas-for-a-container-or-blob-with-net"></a>為具有 .NET 的容器或 blob 創建服務 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文演示如何使用存儲帳戶金鑰為容器或 Blob 創建服務 SAS，以及用於 .NET 的[Azure 存儲用戶端庫](/dotnet/api/overview/azure/storage?view=azure-dotnet)。

## <a name="create-a-service-sas-for-a-blob-container"></a>為 Blob 容器創建服務 SAS

要為容器創建服務 SAS，請調用[CloudBlob 容器.獲取共用訪問簽名](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature)方法。

以下代碼示例在容器上創建 SAS。 如果提供現有預存存取原則的名稱，該原則將與 SAS 相關聯。 如果未提供存儲的訪問策略，則代碼會在容器上創建一個臨時 SAS。

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

## <a name="create-a-service-sas-for-a-blob"></a>為 Blob 創建服務 SAS

要為 Blob 創建服務 SAS，請調用[CloudBlob.GetSharedAccessSign 方法](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature)。

以下代碼示例在 Blob 上創建 SAS。 如果提供現有預存存取原則的名稱，該原則將與 SAS 相關聯。 如果未提供存儲的訪問策略，則代碼會在 blob 上創建一個臨時 SAS。

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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>後續步驟

- [使用共用訪問簽名 （SAS） 授予對 Azure 存儲資源的有限存取權限](../common/storage-sas-overview.md)
- [建立服務 SAS](/rest/api/storageservices/create-service-sas)
