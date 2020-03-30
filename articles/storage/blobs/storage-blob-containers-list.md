---
title: 列出具有 .NET - Azure 存儲的 blob 容器
description: 瞭解如何使用 .NET 用戶端庫在 Azure 存儲帳戶中列出 blob 容器。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 155b8f5d50c7b106daff8dab4df17200b844c988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135899"
---
# <a name="list-blob-containers-with-net"></a>列出具有 .NET 的 blob 容器

從代碼中列出 Azure 存儲帳戶中的容器時，可以指定許多選項來管理如何從 Azure 存儲返回結果。 本文演示如何使用 .NET 的[Azure 存儲用戶端庫](/dotnet/api/overview/azure/storage?view=azure-dotnet)列出容器。  

## <a name="understand-container-listing-options"></a>瞭解容器清單選項

要列出存儲帳戶中的容器，請調用以下方法之一：

- [清單容器分段](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [清單容器分段 Async](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

這些方法的重載提供了用於管理清單操作返回容器方式的其他選項。 這些選項在以下各節仲介紹。

### <a name="manage-how-many-results-are-returned"></a>管理返回的結果數

預設情況下，清單操作一次最多返回 5000 個結果。 要返回較小的結果集，請為`maxresults`參數在調用**ListContainer 分段**方法之一時提供非零值。

如果存儲帳戶包含超過 5000 個容器，或者已指定一個值，`maxresults`以便清單操作返回存儲帳戶中的容器子集，則 Azure 存儲將返回包含容器清單的*延續權杖*。 延續權杖是一個不透明值，可用於從 Azure 存儲檢索下一組結果。

在代碼中，檢查延續權杖的值以確定它是否為空。 當延續權杖為空時，結果集即完成。 如果延續權杖不是 null，則再次調用**List 容器分段**或**清單容器分段 Async，** 傳入延續權杖以檢索下一組結果，直到延續權杖為空。

### <a name="filter-results-with-a-prefix"></a>使用首碼篩選結果

要篩選容器清單，請為`prefix`參數指定字串。 首碼字串可以包含一個或多個字元。 然後，Azure 存儲僅返回其名稱以該首碼開頭的容器。

### <a name="return-metadata"></a>返回中繼資料

要返回具有結果的容器中繼資料，請指定[容器清單詳細資訊](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails)枚舉的**中繼資料**值。 Azure 存儲包括返回每個容器的中繼資料，因此您無需調用其中一個**FetchAttributes**方法來檢索容器中繼資料。

## <a name="example-list-containers"></a>示例：清單容器

下面的示例非同步列出存儲帳戶中的容器，這些容器以指定的首碼開頭。 該示例以一次 5 個結果的增量列出容器，並使用延續權杖獲取下一段結果。 該示例還返回具有結果的容器中繼資料。

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix)
{
    Console.WriteLine("List all containers beginning with prefix {0}, plus container metadata:", prefix);

    try
    {
        ContainerResultSegment resultSegment = null;
        BlobContinuationToken continuationToken = null;

        do
        {
            // List containers beginning with the specified prefix, returning segments of 5 results each.
            // Passing null for the maxResults parameter returns the max number of results (up to 5000).
            // Requesting the container's metadata with the listing operation populates the metadata,
            // so it's not necessary to also call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, 5, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token. If not null, get the next segment.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0} : {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>另請參閱

[列出](/rest/api/storageservices/list-containers2)
[枚舉 Blob 資源的](/rest/api/storageservices/enumerating-blob-resources)容器
