---
title: 使用 .NET 列出 blob 容器-Azure 儲存體
description: 瞭解如何使用 .NET 用戶端程式庫，列出 Azure 儲存體帳戶中的 blob 容器。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: da0c5bf6bc371bc512d9264afeab52b9908396fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84463553"
---
# <a name="list-blob-containers-with-net"></a>使用 .NET 列出 blob 容器

當您從程式碼列出 Azure 儲存體帳戶中的容器時，您可以指定數個選項來管理從 Azure 儲存體傳回結果的方式。 本文說明如何使用[適用于 .net 的 Azure 儲存體用戶端程式庫](/dotnet/api/overview/azure/storage?view=azure-dotnet)來列出容器。  

## <a name="understand-container-listing-options"></a>瞭解容器清單選項

若要列出儲存體帳戶中的容器，請呼叫下列其中一個方法：

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

這些方法的多載會提供其他選項來管理清單作業傳回容器的方式。 下列各節描述這些選項。

### <a name="manage-how-many-results-are-returned"></a>管理傳回的結果數目

根據預設，清單作業一次最多會傳回 5000 個結果。 若要傳回較小的結果集，請 `maxresults` 在呼叫其中一個**ListContainerSegmented**方法時，為參數提供非零值。

如果您的儲存體帳戶包含超過5000個容器，或如果您已指定一個值讓清單作業傳回 `maxresults` 儲存體帳戶中的容器子集，則 Azure 儲存體會傳回包含容器清單的*接續權杖*。 接續權杖是不透明的值，其可用來從 Azure 儲存體中擷取下一組結果。

在程式碼中檢查接續權杖的值，以判斷該值是否為 null。 當接續權杖為 null 時，結果集就會完成。 如果接續 token 不是 null，則再次呼叫**ListContainersSegmented**或**ListContainersSegmentedAsync** ，傳入接續 token 來抓取下一組結果，直到接續 token 為 null 為止。

### <a name="filter-results-with-a-prefix"></a>使用前置詞篩選結果

若要篩選容器清單，請指定 `prefix` 參數的字串。 前置詞字串可包含一或多個字元。 Azure 儲存體接著只會傳回名稱開頭為該前置詞的容器。

### <a name="return-metadata"></a>傳回中繼資料

若要傳回具有結果的容器中繼資料，請指定[ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails)列舉的**中繼資料**值。 Azure 儲存體包含每個傳回的容器的中繼資料，因此您也不需要呼叫其中一個**FetchAttributes**方法來取得容器中繼資料。

## <a name="example-list-containers"></a>範例：列出容器

下列範例會以非同步方式列出儲存體帳戶中以指定前置詞開頭的容器。 此範例會一次以5個結果的增量來列出容器，並使用接續 token 來取得結果的下一個區段。 此範例也會傳回具有結果的容器中繼資料。

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

[列出容器](/rest/api/storageservices/list-containers2) 
[列舉 Blob 資源](/rest/api/storageservices/enumerating-blob-resources)
