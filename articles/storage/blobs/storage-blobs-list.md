---
title: 使用 .NET 列出 blob-Azure 儲存體
description: 瞭解如何使用 .NET 用戶端程式庫，列出 Azure 儲存體帳戶中容器內的 blob。 程式碼範例會示範如何列出一般清單中的 blob，或如何以階層方式列出 blob，如同它們已組織成目錄或資料夾。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: eb62883859a3efeb1c05deb38d8a40fba76e9cdf
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137915"
---
# <a name="list-blobs-with-net"></a>使用 .NET 列出 blob

當您列出程式碼中的 blob 時，您可以指定數個選項來管理 Azure 儲存體傳回結果的方式。 您可以指定要在每一組結果中傳回的結果數目，然後取得後續的集合。 您可以指定前置詞，以傳回名稱開頭為該字元或字串的 blob。 而且您可以列出一般清單結構中或階層式的 blob。 階層式清單會傳回 blob，就好像它們已組織成資料夾一樣。 

本文說明如何使用[適用于 .net 的 Azure 儲存體用戶端程式庫](/dotnet/api/overview/azure/storage?view=azure-dotnet)來列出 blob。  

## <a name="understand-blob-listing-options"></a>瞭解 blob 清單選項

若要列出儲存體帳戶中的 blob，請呼叫下列其中一種方法：

- [CloudBlobClient. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

若要列出容器中的 blob，請呼叫下列其中一個方法：

- [CloudBlobContainer. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

這些方法的多載會提供其他選項來管理清單作業傳回 blob 的方式。 下列各節將說明這些選項。

### <a name="manage-how-many-results-are-returned"></a>管理傳回的結果數目

根據預設，清單作業一次最多會傳回5000個結果。 若要傳回較小的結果集，請在呼叫其中一個**ListBlobs**方法時，為 `maxresults` 參數提供非零值。

如果清單作業傳回超過5000個 blob，或如果您已指定 `maxresults` 的值，讓清單作業傳回儲存體帳戶中的容器子集，則 Azure 儲存體會傳回包含 blob 清單的*接續權杖*。 接續 token 是不透明的值，可讓您用來抓取 Azure 儲存體的下一組結果。

在您的程式碼中，檢查接續 token 的值，以判斷它是否為 null。 當接續 token 為 null 時，結果集就會完成。 如果接續 token 不是 null，則再次呼叫 [列出作業]，傳遞接續 token 來抓取下一組結果，直到接續 token 為 null 為止。

### <a name="filter-results-with-a-prefix"></a>使用前置詞篩選結果

若要篩選容器清單，請指定 `prefix` 參數的字串。 前置詞字串可以包含一或多個字元。 Azure 儲存體接著只會傳回名稱開頭為該前置詞的 blob。

### <a name="return-metadata"></a>傳回中繼資料

若要傳回含有結果的 blob 中繼資料，請指定[BlobListingDetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails)列舉的**中繼資料**值。 Azure 儲存體包含每個傳回之 blob 的中繼資料，因此您不需要在此內容中呼叫其中一個**FetchAttributes**方法來抓取 blob 中繼資料。

### <a name="flat-listing-versus-hierarchical-listing"></a>一般清單與階層式清單

Azure 儲存體中的 blob 是以一般架構來組織，而不是階層式架構（例如傳統檔案系統）。 不過，您可以將 blob 組織成*虛擬目錄*，以便模擬資料夾結構。 虛擬目錄會形成 blob 名稱的一部分，並以分隔符號來表示。

若要將 blob 組織成虛擬目錄，請在 blob 名稱中使用分隔符號。 預設的分隔符號是正斜線（/），但您可以指定任何字元做為分隔符號。

如果您使用分隔符號來命名 blob，則可以選擇階層式列出 blob。 若為階層式清單作業，Azure 儲存體會傳回父物件底下的任何虛擬目錄和 blob。 您可以遞迴呼叫清單作業來遍歷階層，類似于以程式設計方式來進行傳統檔案系統的處理方式。

## <a name="use-a-flat-listing"></a>使用一般清單

根據預設，清單作業會傳回一般清單中的 blob。 在一般清單中，blob 不會依虛擬目錄來組織。

下列範例會使用一般清單來列出指定容器中的 blob，並指定選擇性區段大小，並將 blob 名稱寫入主控台視窗。

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
                // A flat listing operation returns only blobs, not virtual directories.
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

範例輸出類似于：

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

當您以階層方式呼叫清單作業時，Azure 儲存體會在階層的第一個層級傳回虛擬目錄和 blob。 系統會設定每個虛擬目錄的[prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix)屬性，讓您可以在遞迴呼叫中傳遞前置詞，以取出下一個目錄。

若要以階層方式列出 blob，請將清單方法的 `useFlatBlobListing` 參數設定為**false**。

下列範例會使用一般清單來列出指定容器中的 blob，並指定選擇性區段大小，並將 blob 名稱寫入主控台視窗。

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

範例輸出類似于：

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
> Blob 快照集不能列在階層式列出作業中。

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>後續步驟

- [列出 Blob](/rest/api/storageservices/list-blobs)
- [列舉 Blob 資源](/rest/api/storageservices/enumerating-blob-resources)
