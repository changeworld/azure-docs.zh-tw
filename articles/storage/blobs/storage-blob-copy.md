---
title: 使用 Azure 儲存體 Api 複製 blob
description: 瞭解如何使用 Azure 儲存體用戶端程式庫來複製 blob。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/10/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 2c474ed4d4158356075f861c3c0d5ace69173255
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90014645"
---
# <a name="copy-a-blob-with-azure-storage-client-libraries"></a>使用 Azure 儲存體用戶端程式庫複製 blob

本文示範如何在 Azure 儲存體帳戶中複製 blob。 它也會說明如何中止非同步複製作業。 範例程式碼會使用 Azure 儲存體的用戶端程式庫。

## <a name="about-copying-blobs"></a>關於複製 blob

當您在相同的儲存體帳戶內複製 blob 時，它會是同步操作。 當您複製跨帳戶時，它是非同步作業。

複製作業的來源 blob 可以是區塊 blob、附加 blob、分頁 blob 或快照集。 如果目的地 Blob 已經存在，則必須使用與來源 Blob 相同的 Blob 類型。 將會覆寫現有的目的地 blob。

複製作業正在進行時，無法修改目的地 blob。 目的地 blob 只能有一個未完成的複製操作。 換句話說，blob 不可以是多項暫止的複製作業的目的地。

一律會複製整個來源 blob 或檔案。 不支援複製位元組範圍或一組區塊。

複製 blob 時，系統會將它的系統屬性複製到具有相同值的目的地 blob。

複製作業可以採用下列任何形式：

- 將來源 blob 複製到具有不同名稱的目的地 blob。 目的地 blob 可以是相同 blob 類型 (區塊、附加或頁面) 的現有 blob，也可以是複製作業所建立的新 blob。
- 將來源 blob 複製到具有相同名稱的目的地 blob，有效地取代目的地 blob。 這類複製作業會移除任何未認可的區塊，並覆寫目的地 blob 的中繼資料。
- 將 Azure 檔案服務中的原始檔複製到目的地 blob。 目的地 blob 可以是現有的區塊 blob，也可以是複製作業所建立的新區塊 blob。 不支援從檔案複製到分頁 blob 或附加 blob。
- 將快照集複製到其基底 blob。 藉由將快照集升級到基底 Blob 的位置，您可以還原舊版的 Blob。
- 將快照集複製到具有不同名稱的目的地 blob。 產生的目的地 Blob 是可寫入的 Blob，而不是快照集。

## <a name="copy-a-blob"></a>複製 blob

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

若要複製 blob，請呼叫下列其中一種方法：

- [StartCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuri)
- [StartCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuriasync)

`StartCopyFromUri`和方法會傳回 `StartCopyFromUriAsync` 包含複製作業相關資訊的[CopyFromUriOperation](/dotnet/api/azure.storage.blobs.models.copyfromurioperation)物件。

下列程式碼範例會取得代表先前建立之 blob 的 [>blobclient](/dotnet/api/azure.storage.blobs.blobclient) ，並將它複製到相同容器中的新 blob：

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_CopyBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

若要複製 blob，請呼叫下列其中一種方法：

- [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy)
- [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync)

`StartCopy`和 `StartCopyAsync` 方法會傳回復制識別碼值，用來檢查狀態或中止複製作業。

下列程式碼範例會取得先前建立之 blob 的參考，並將其複製到相同容器中的新 blob：

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

        // Lease the source blob for the copy operation 
        // to prevent another client from modifying it.
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

# <a name="python-v12"></a>[Python v12](#tab/python)

若要複製 blob，請呼叫 [start_copy_from_url](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#start-copy-from-url-source-url--metadata-none--incremental-copy-false----kwargs-) 方法。 方法會傳回 `start_copy_from_url` 包含複製作業相關資訊的字典。

下列程式碼範例會取得代表先前建立之 blob 的 [>blobclient](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient) ，並將它複製到相同容器中的新 blob：

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_BlobCopy":::

---

## <a name="abort-a-copy-operation"></a>中止複製作業

中止複製作業會導致目的地 blob 的長度為零。 不過，目的地 blob 的中繼資料將會有從來源 blob 複製的新值，或在複製作業期間明確設定的值。 若要保留複製前的原始中繼資料，請先建立目的地 blob 的快照集，再呼叫其中一個複製方法。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

檢查目的地 blob 的 [blobproperties.cachecontrol. CopyStatus](/dotnet/api/azure.storage.blobs.models.blobproperties.copystatus) 屬性，以取得複製作業的狀態。 複製完成時，會認可最後的 Blob。

當您中止複製作業時，目的地 blob 的複製狀態會設為 [CopyStatus。會中止](/dotnet/api/microsoft.azure.storage.blob.copystatus)。

[AbortCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuri)和[AbortCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuriasync)方法會取消進行中的複製操作。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_StopBlobCopy":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

檢查目的地 blob 的 [ [CopyState](/dotnet/api/microsoft.azure.storage.blob.copystate.status) ] 屬性，以取得複製作業的狀態。 複製完成時，會認可最後的 Blob。

當您中止複製作業時，目的地 blob 的複製狀態會設為 [CopyStatus。會中止](/dotnet/api/microsoft.azure.storage.blob.copystatus)。

[AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy)和[AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync)方法會取消進行中的複製操作。

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

# <a name="python-v12"></a>[Python v12](#tab/python)

檢查[get_blob_properties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#get-blob-properties---kwargs-)方法所傳回之[CopyProperties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties)字典中的「狀態」專案，以取得複製作業的狀態。 複製完成時，會認可最後的 Blob。

當您中止複製作業時， [狀態](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) 會設定為「已中止」。

[Abort_copy](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#abort-copy-copy-id----kwargs-)方法會取消進行中的複製操作。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_StopBlobCopy":::

---

## <a name="azure-sdks"></a>Azure SDK

取得 Azure Sdk 的詳細資訊：

 - [Azure SDK for .NET](https://github.com/azure/azure-sdk-for-net)
 - [Azure SDK for Java](https://github.com/azure/azure-sdk-for-java)
 - [適用於 Python 的 Azure SDK](https://github.com/azure/azure-sdk-for-python)
 - [適用於 JavaScript 的 Azure SDK](https://github.com/azure/azure-sdk-for-js)

## <a name="next-steps"></a>接下來的步驟

下列主題包含使用 Azure REST Api 複製 blob 和中止進行中複製作業的相關資訊。

- [複製 Blob](/rest/api/storageservices/copy-blob)
- [中止複製 Blob](/rest/api/storageservices/abort-copy-blob)
