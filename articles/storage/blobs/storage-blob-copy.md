---
title: 使用 .NET 複製 blob-Azure 儲存體
description: 瞭解如何使用 .NET 用戶端程式庫，在您的 Azure 儲存體帳戶中複製 blob。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9ffa69980f020580376aea447f40ac615f26cf03
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2020
ms.locfileid: "79135882"
---
# <a name="copy-a-blob-with-net"></a>使用 .NET 複製 blob

本文示範如何使用 Azure 儲存體帳戶來複製 blob。 它也會顯示如何中止非同步複製作業。 範例程式碼會使用[適用于 .net 的 Azure 儲存體用戶端程式庫](/dotnet/api/overview/azure/storage?view=azure-dotnet)。

## <a name="about-copying-blobs"></a>關於複製 blob

當您複製相同儲存體帳戶內的 blob 時，它是同步作業。 當您在帳戶之間複製時，它是非同步作業。 [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)和[StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)方法會傳回用來檢查狀態或中止複製作業的複製識別碼值。

複製作業的來源 blob 可以是區塊 blob、附加 blob、分頁 blob 或快照集。 如果目的地 Blob 已經存在，則必須使用與來源 Blob 相同的 Blob 類型。 所有現有的目的地 Blob 都會被覆寫。 

複製作業正在進行時，無法修改目的地 blob。 目的地 Blob 只能有一個未處理的複製 Blob 作業。 換句話說，blob 不能是多個暫止複製作業的目的地。

一律會複製整個來源 blob 或檔案。 不支援複製位元組範圍或一組區塊。

複製 blob 時，系統屬性會複製到具有相同值的目的地 blob。

針對所有 blob 類型，您可以檢查目的地 blob 上的[CopyState](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet)屬性，以取得複製作業的狀態。 複製完成時，會認可最後的 Blob。

複製作業可以採用下列任一形式：

  - 您可以將來源 Blob 複製到不同名稱的目的地 Blob。 目的地 blob 可以是相同 blob 類型（區塊、附加或分頁）的現有 blob，或者可以是複製作業所建立的新 blob。
  - 您可以將來源 blob 複製到具有相同名稱的目的地 blob，以有效地取代目的地 blob。 這類複製作業會移除所有未認可的區塊，並覆寫目的地 blob 的中繼資料。
  - 您可以將 Azure 檔案服務中的來源檔案複製到目的地 blob。 目的地 blob 可以是現有的區塊 blob，也可以是複製作業所建立的新區塊 blob。 不支援從檔案複製到分頁 blob 或附加 blob。
  - 您可以將快照集複製到其基底 Blob 之上。 藉由將快照集升級到基底 Blob 的位置，您可以還原舊版的 Blob。
  - 您可以將快照集複製到不同名稱的目的地 Blob。 產生的目的地 Blob 是可寫入的 Blob，而不是快照集。

## <a name="copy-a-blob"></a>複製 blob

若要複製 blob，請呼叫下列其中一個方法：

 - [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

下列程式碼範例會取得先前建立之 blob 的參考，並將它複製到相同容器中的新 blob：

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

## <a name="abort-a-blob-copy-operation"></a>中止 blob 複製作業

中止複製作業會導致區塊 blob、附加 blob 和分頁 blob 的目的地 blob 長度為零。 不過，目的地 blob 的中繼資料會有從來源 blob 複製的新值，或在[StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)或[StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)呼叫中明確地設定。 若要保留複製之前的原始中繼資料，請先建立目的地 blob 的快照集，再呼叫 `StartCopy` 或 `StartCopyAsync`。

當您中止正在進行的 blob 複製作業時，目的地 blob 的[CopyState。狀態](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status)會設定為[CopyStatus。已中止](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet)。

[AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet)和[AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet)方法會取消進行中的 blob 複製作業，並保留長度為零的目的地 blob 和完整的中繼資料。

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

下列主題包含使用 Azure REST Api 複製 blob 和中止進行中複製作業的相關資訊。

- [複製 Blob](/rest/api/storageservices/copy-blob)
- [中止複製 Blob](/rest/api/storageservices/abort-copy-blob)
