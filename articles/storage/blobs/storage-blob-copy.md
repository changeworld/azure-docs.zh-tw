---
title: 使用 .NET - Azure 存儲複製 Blob
description: 瞭解如何使用 .NET 用戶端庫複製 Azure 存儲帳戶中的 Blob。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9ffa69980f020580376aea447f40ac615f26cf03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135882"
---
# <a name="copy-a-blob-with-net"></a>使用 .NET 複製 Blob

本文演示如何使用 Azure 存儲帳戶複製 Blob。 它還演示如何中止非同步複製操作。 示例代碼使用[.NET 的 Azure 存儲用戶端庫](/dotnet/api/overview/azure/storage?view=azure-dotnet)。

## <a name="about-copying-blobs"></a>關於複製 Blob

複製同一存儲帳戶中的 Blob 時，它是同步操作。 跨帳戶複製時，它是非同步作業。 [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)和[StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)方法返回用於檢查狀態或中止複製操作的複本識別碼 值。

複製操作的源 Blob 可以是塊 Blob、追加 Blob、頁面 Blob 或快照。 如果目的地 Blob 已經存在，則必須使用與來源 Blob 相同的 Blob 類型。 所有現有的目的地 Blob 都會被覆寫。 

複製操作正在進行時，無法修改目標 Blob。 目的地 Blob 只能有一個未處理的複製 Blob 作業。 換句話說，Blob 不能是多個掛起的複製操作的目標。

始終複製整個源 Blob 或檔。 不支援複製位元組範圍或塊集。

複製 Blob 時，其系統屬性將複製到具有相同值的目標 Blob。

對於所有 Blob 類型，可以檢查目標 Blob 上的[CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet)屬性，以獲取複製操作的狀態。 複製完成時，會認可最後的 Blob。

複製操作可以採取以下任何形式：

  - 您可以將來源 Blob 複製到不同名稱的目的地 Blob。 目標 Blob 可以是相同 Blob 類型的現有 Blob（塊、追加或頁），也可以是複製操作創建的新 Blob。
  - 您可以將源 Blob 複製到具有相同名稱的目標 blob，從而有效地替換目標 Blob。 此類複製操作刪除任何未提交的塊並覆蓋目標 Blob 的中繼資料。
  - 您可以將 Azure 檔服務中的源檔案複製到目標 Blob。 目標 Blob 可以是現有塊 Blob，也可以是複製操作創建的新塊 Blob。 不支援從檔案複製到頁面 blob 或追加 blob。
  - 您可以將快照集複製到其基底 Blob 之上。 藉由將快照集升級到基底 Blob 的位置，您可以還原舊版的 Blob。
  - 您可以將快照集複製到不同名稱的目的地 Blob。 產生的目的地 Blob 是可寫入的 Blob，而不是快照集。

## <a name="copy-a-blob"></a>複製 Blob

要複製 Blob，請調用以下方法之一：

 - [啟動複製](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [啟動複製同步](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

以下代碼示例獲取對以前創建的 Blob 的引用，並將其複製到同一容器中的新 Blob：

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
            Console.WriteLine();
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

## <a name="abort-a-blob-copy-operation"></a>中止 Blob 複製操作

中止複製操作會導致塊 blob、追加 Blob 和頁面 blob 的目標 Blob 長度為零。 但是，目標 Blob 的中繼資料將從源 Blob 複製新值，或在[StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)或[StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)調用中顯式設置。 要在複製之前保留原始中繼資料，請先在調用`StartCopy`或`StartCopyAsync`之前創建目標 blob 的快照。

中止正在進行的 Blob 複製操作時，目標 blob 的[複製狀態設置為](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status)["複製狀態"。](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet)

[AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet)和[AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet)方法取消正在進行的 Blob 複製操作，並保留長度為零且完整中繼資料的目標 Blob。

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>後續步驟

以下主題包含有關使用 Azure REST API 複製 blob 和中止正在進行的複製操作的資訊。

- [複製 Blob](/rest/api/storageservices/copy-blob)
- [中止複製 Blob](/rest/api/storageservices/abort-copy-blob)
