---
title: 使用 .NET 列出 Blob - Azure 儲存體
description: 了解如何使用 .NET 用戶端程式庫來列出 Azure 儲存體帳戶中容器內的 Blob。 程式碼範例會示範如何以簡單列表列出 Blob，或如何以階層方式列出 Blob，就好像這些 Blob 已組織成目錄或資料夾一樣。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/05/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 81eab2f3c5748c4edf7e09e5147a178a08bc546b
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185701"
---
# <a name="list-blobs-with-net"></a>使用 .NET 列出 Blob

當從程式碼列出 Blob 時，即可指定數個選項來管理從 Azure 儲存體傳回結果的方式。 您可指定要在每一組結果中傳回的結果數目，然後擷取後續集合。 您可指定前置詞，以傳回名稱開頭為該字元或字串的 Blob。 也可以使用簡單列表結構或以階層方列出 Blob。 階層式清單會傳回 Blob，就好像這些 Blob 已組織成資料夾一樣。

本文說明如何使用[適用於 .NET 的 Azure 儲存體用戶端程式庫](/dotnet/api/overview/azure/storage?view=azure-dotnet)來列出 Blob。  

## <a name="understand-blob-listing-options"></a>了解 Blob 清單選項

若要列出儲存體帳戶中的 Blob，請呼叫下列其中一種方法：

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

- [BlobContainerClient.GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs?view=azure-dotnet)
- [BlobContainerClient.GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync?view=azure-dotnet)
- [BlobContainerClient.GetBlobsByHierarchy](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy?view=azure-dotnet)
- [BlobContainerClient.GetBlobsByHierarchyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync?view=azure-dotnet)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [CloudBlobClient.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

若要列出容器中的 Blob，請呼叫下列其中一個方法：

- [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

這些方法的多載會提供其他選項來管理清單作業如何傳回 Blob。 下列各節描述這些選項。

---

### <a name="manage-how-many-results-are-returned"></a>管理傳回的結果數目

根據預設，清單作業一次最多會傳回5000個結果，但您可以指定想要讓每個清單作業傳回的結果數目。 本文中顯示的範例將示範如何執行這項操作。

如果清單作業傳回5000個以上的 blob，或可用的 blob 數目超過您指定的數目，則 Azure 儲存體會傳回包含 blob 清單的*接續 token* 。 接續權杖是不透明的值，其可用來從 Azure 儲存體中擷取下一組結果。

在程式碼中檢查接續權杖的值，以判斷該值是否為 null。 當接續權杖為 null 時，結果集就會完成。 如果接續權杖不是 null，則會再次呼叫清單作業、傳遞接續權杖來擷取下一組結果，直到接續權杖是 null 為止。

### <a name="filter-results-with-a-prefix"></a>使用前置詞篩選結果

若要篩選容器清單，請指定 `prefix` 參數的字串。 前置詞字串可包含一或多個字元。 Azure 儲存體接著只會傳回名稱開頭為該前置詞的 Blob。

### <a name="return-metadata"></a>傳回中繼資料

您可以傳回包含結果的 blob 中繼資料。 

- 如果您使用的是 .NET v12 SDK，請指定[BlobTraits](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.models.blobtraits?view=azure-dotnet)列舉的**中繼資料**值。

- 如果您使用的是 .NET v11 SDK，請指定[BlobListingDetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails)列舉的**中繼資料**值。 Azure 儲存體會在每個傳回的 Blob 中包含中繼資料，因此不需要在此內容中呼叫其中一個 **FetchAttributes** 方法，即可擷取 Blob 中繼資料。

### <a name="flat-listing-versus-hierarchical-listing"></a>簡單列表與階層式清單

Azure 儲存體中的 Blob 是以簡單架構進行組織，而不是階層式架構 (例如傳統檔案系統)。 不過，您可將 Blob 組織成「虛擬目錄」，以便模擬資料夾結構。 虛擬目錄會形成 Blob 名稱的一部分，並以分隔符號表示。

若要將 Blob 組織成虛擬目錄，請在 Blob 名稱中使用分隔符號。 預設的分隔符號是正斜線 (/)，但可指定任何字元作為分隔符號。

如果使用分隔符號來命名 Blob，則可選擇以階層方式列出 Blob。 針對階層式清單作業，Azure 儲存體會傳回父物件底下的任何虛擬目錄和 Blob。 您可遞迴呼叫清單作業來周遊階層，類似於以程式設計方式周遊傳統檔案系統的方式。

## <a name="use-a-flat-listing"></a>使用簡單列表

根據預設，清單作業會以簡單列表傳回 Blob。 在簡單列表中，Blob 不會透過虛擬目錄進行組織。

下列範例會使用簡單列表，在指定了選擇性區段大小情況下列出指定容器中的 Blob，並將 Blob 名稱寫入主控台視窗。

如果您已在您的帳戶上啟用階層命名空間功能，目錄就不是虛擬的。 相反地，它們是實體獨立的物件。 因此，目錄會在清單中顯示為長度為零的 blob。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsFlatListing":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
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

範例輸出類似於：

```
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>使用階層式清單

當以階層方式呼叫清單作業時，Azure 儲存體會在階層的第一個層級傳回虛擬目錄和 Blob。 系統會設定每個虛擬目錄的 [Prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) 屬性，如此即可在遞迴呼叫中傳遞前置詞，以擷取下一個目錄。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

若要以階層方式列出 blob，請呼叫[BlobContainerClient. GetBlobsByHierarchy](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy?view=azure-dotnet)或[BlobContainerClient. GetBlobsByHierarchyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync?view=azure-dotnet)方法。

下列範例會使用階層式清單來列出指定容器中的 blob，並指定選擇性區段大小，並將 blob 名稱寫入主控台視窗。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsHierarchicalListing":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

若要以階層方式列出 Blob，請將清單方法的 `useFlatBlobListing` 參數設定為 **false**。

下列範例會使用簡單列表，在指定了選擇性區段大小情況下列出指定容器中的 Blob，並將 Blob 名稱寫入主控台視窗。

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken = null;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, continuationToken, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
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

範例輸出類似於：

```
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> Blob 快照集不能列在階層式清單作業中。

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>後續步驟

- [列出 Blob](/rest/api/storageservices/list-blobs)
- [列舉 Blob 資源](/rest/api/storageservices/enumerating-blob-resources)
