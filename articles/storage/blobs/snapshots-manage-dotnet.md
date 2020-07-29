---
title: 在 .NET 中建立和管理 Blob 快照集
titleSuffix: Azure Storage
description: 瞭解如何建立 blob 的唯讀快照集，以便在指定的時間點備份 blob 資料。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: be3d06d8ea493ab7c246ace5c49d5e5bc0036108
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84463530"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>在 .NET 中建立和管理 Blob 快照集

快照集是在某個點時間取得的唯讀 Blob 版本。 本文說明如何使用[適用于 .net 的 Azure 儲存體用戶端程式庫](/dotnet/api/overview/azure/storage?view=azure-dotnet)來建立和管理 blob 快照集。

如需 Azure 儲存體中 blob 快照的詳細資訊，請參閱[在 .net 中建立和管理 blob 快照](snapshots-overview.md)集。

## <a name="create-a-snapshot"></a>建立快照集

# <a name="net-version-12x"></a>[.NET 版本 12. x](#tab/v12)

若要使用適用于 .NET 的 Azure 儲存體用戶端程式庫版本5.x 建立區塊 blob 的快照集，請使用下列其中一種方法：

- [Icloudblob.createsnapshot](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshotasync)

下列程式碼範例示範如何建立版本為 12. x 的快照集。 包含對[Azure 身分識別](https://www.nuget.org/packages/azure.identity)程式庫的參考，以便使用您的 Azure AD 認證來授權服務的要求。

```csharp
private static async Task CreateBlockBlobSnapshot(string accountName, string containerName, string blobName, Stream data)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri containerUri = new Uri("https://" + accountName + blobServiceEndpointSuffix + "/" + containerName);

    // Get a container client object and create the container.
    BlobContainerClient containerClient = new BlobContainerClient(containerUri,
        new DefaultAzureCredential());
    await containerClient.CreateIfNotExistsAsync();

    // Get a blob client object.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Upload text to create a block blob.
        await blobClient.UploadAsync(data);

        // Add blob metadata.
        IDictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "ApproxBlobCreatedDate", DateTime.UtcNow.ToString() },
            { "FileType", "text" }
        };
        await blobClient.SetMetadataAsync(metadata);

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        await blobClient.CreateSnapshotAsync();
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="net-version-11x"></a>[.NET 版本 11. x](#tab/v11)

若要使用適用于 .NET 的 Azure 儲存體用戶端程式庫版本 11. x 建立區塊 blob 的快照集，請使用下列其中一種方法：

- [Icloudblob.createsnapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

下列程式碼範例示範如何建立版本 11. x 的快照集。 此範例會在建立快照集時為其指定其他中繼資料。

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        Console.WriteLine(snapshot.SnapshotQualifiedStorageUri.PrimaryUri);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

## <a name="delete-snapshots"></a>刪除快照集

若要刪除 blob，您必須先刪除該 blob 的任何快照集。 您可以個別刪除快照集，或指定在刪除來源 Blob 時刪除所有的快照集。 如果您嘗試刪除仍具有快照集的 Blob，則會發生錯誤。

# <a name="net-version-12x"></a>[.NET 版本 12. x](#tab/v12)

若要使用適用于 .NET 的 Azure 儲存體用戶端程式庫版本5.x 來刪除 blob 及其快照集，請使用下列其中一種方法，並包含[DeleteSnapshotsOption](/dotnet/api/azure.storage.blobs.models.deletesnapshotsoption)列舉：

- [刪除](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexists)
- [Cloudblobcontainer.deleteifexistsasync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexistsasync)

下列程式碼範例示範如何在 .NET 中刪除 blob 及其快照集，其中 `blobClient` 是[BlobClient](/dotnet/api/azure.storage.blobs.blobclient)類型的物件）：

```csharp
await blobClient.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, default);
```

# <a name="net-version-11x"></a>[.NET 版本 11. x](#tab/v11)

若要使用適用于 .NET 的 Azure 儲存體用戶端程式庫版本 11. x 刪除 blob 及其快照集，請使用下列其中一種 blob 刪除方法，並包含[DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption)列舉：

- [刪除](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [Cloudblobcontainer.deleteifexistsasync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

下列程式碼範例示範如何在 .NET 中刪除 blob 及其快照集，其中 `blockBlob` 是 [CloudBlockBlob] [dotnet_CloudBlockBlob] 類型的物件：

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

---

## <a name="next-steps"></a>後續步驟

- [Blob 快照集](snapshots-overview.md)
- [Blob 版本（預覽）](versioning-overview.md)
- [Blob 的虛刪除](storage-blob-soft-delete.md)