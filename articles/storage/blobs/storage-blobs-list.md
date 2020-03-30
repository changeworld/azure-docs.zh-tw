---
title: 使用 .NET - Azure 存儲列出 blob
description: 瞭解如何使用 .NET 用戶端庫在 Azure 存儲帳戶中的容器中列出 blob。 代碼示例演示如何在平面清單中列出 blob，或者如何分層列出 blob，就像它們被組織到目錄或資料夾中一樣。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: eb62883859a3efeb1c05deb38d8a40fba76e9cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137915"
---
# <a name="list-blobs-with-net"></a>列出帶 .NET 的 blob

從代碼中列出 Blob 時，可以指定許多選項來管理如何從 Azure 存儲返回結果。 您可以指定要在每組結果中返回的結果數，然後檢索後續集。 您可以指定一個首碼來返回其名稱以該字元或字串開頭的 Blob。 您可以在平面清單結構中列出 blob，也可以分層列出 Blob。 分層清單返回 blob，就像它們被組織到資料夾中一樣。 

本文演示如何使用 .NET 的[Azure 存儲用戶端庫](/dotnet/api/overview/azure/storage?view=azure-dotnet)列出 blob。  

## <a name="understand-blob-listing-options"></a>瞭解 Blob 清單選項

要列出存儲帳戶中的 Blob，請調用以下方法之一：

- [雲BlobClient.listBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [雲 BlobClient.清單Blob分段](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [雲 BlobClient.清單Blobs分段Async](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

要列出容器中的 blob，請調用以下方法之一：

- [雲 Blob 容器.清單Blobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [雲 Blob 容器.清單 Blob 分段](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [雲 Blob 容器.清單 Blob 分段 Async](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

這些方法的重載提供了用於管理清單操作如何返回 Blob 的其他選項。 這些選項在以下各節仲介紹。

### <a name="manage-how-many-results-are-returned"></a>管理返回的結果數

預設情況下，清單操作一次最多返回 5000 個結果。 要返回較小的結果集，在調用**ListBlobs**方法之`maxresults`一時，為參數提供非零值。

如果清單操作返回超過 5000 個 blob，或者已為`maxresults`清單操作指定了一個值，以便清單操作返回存儲帳戶中的容器子集，則 Azure 存儲將返回包含 blob 清單的*延續權杖*。 延續權杖是一個不透明值，可用於從 Azure 存儲檢索下一組結果。

在代碼中，檢查延續權杖的值以確定它是否為空。 當延續權杖為空時，結果集即完成。 如果延續權杖不是 null，則再次調用清單操作，傳入延續權杖以檢索下一組結果，直到延續權杖為空。

### <a name="filter-results-with-a-prefix"></a>使用首碼篩選結果

要篩選容器清單，請為`prefix`參數指定字串。 首碼字串可以包含一個或多個字元。 然後，Azure 存儲僅返回其名稱以該首碼開頭的 Blob。

### <a name="return-metadata"></a>返回中繼資料

要返回具有結果的 Blob 中繼資料，請指定[Blob 清單詳細資訊](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails)枚舉的**中繼資料**值。 Azure 存儲包括返回每個 Blob 的中繼資料，因此無需在此上下文中調用其中一個**FetchAttributes**方法來檢索 Blob 中繼資料。

### <a name="flat-listing-versus-hierarchical-listing"></a>平面清單與分層清單

Azure 存儲中的 Blob 以平面範例組織，而不是分層範例（如經典檔案系統）。 但是，您可以將 blob 組織到*虛擬目錄中*，以便類比資料夾結構。 虛擬目錄是 blob 名稱的一部分，由分隔符號字元指示。

要將 blob 組織到虛擬目錄中，請使用 blob 名稱中的分隔符號字元。 預設分隔符號字元是向前斜杠 （/），但您可以將任何字元指定為分隔符號。

如果使用分隔符號命名 Blob，則可以選擇分層列出 Blob。 對於分層清單操作，Azure 存儲返回父物件下的任何虛擬目錄和 Blob。 您可以遞迴地調用清單操作來遍歷層次結構，類似于以程式設計方式遍歷經典檔案系統的方式。

## <a name="use-a-flat-listing"></a>使用平面清單

預設情況下，清單操作返回平面清單中的 blob。 在平面清單中，blob 不由虛擬目錄組織。

下面的示例使用平面清單列出指定容器中的 blob，並指定可選的段大小，並將 blob 名稱寫入主控台視窗。

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

示例輸出類似于：

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

## <a name="use-a-hierarchical-listing"></a>使用分層清單

分層調用清單操作時，Azure 存儲將返回層次結構第一級的虛擬目錄和 blob。 設置每個虛擬目錄[的首碼](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix)屬性，以便您可以傳遞遞迴呼叫中的首碼以檢索下一個目錄。

要分層列出 blob，將`useFlatBlobListing`清單方法的參數設置為**false**。

下面的示例使用平面清單列出指定容器中的 blob，並指定可選的段大小，並將 blob 名稱寫入主控台視窗。

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

示例輸出類似于：

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
> Blob 快照無法在分層清單操作中列出。

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>後續步驟

- [清單 Blob](/rest/api/storageservices/list-blobs)
- [列舉 Blob 資源](/rest/api/storageservices/enumerating-blob-resources)
