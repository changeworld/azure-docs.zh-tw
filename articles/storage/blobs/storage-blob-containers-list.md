---
title: 使用 .NET 列出 blob 容器-Azure 儲存體
description: 瞭解如何使用 .NET 用戶端程式庫列出您 Azure 儲存體帳戶中的 blob 容器。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ab7749c93f39d0c7b630b63e0b0e68589b61ede2
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090942"
---
# <a name="list-blob-containers-with-net"></a>使用 .NET 列出 blob 容器

當您從程式碼列出 Azure 儲存體帳戶中的容器時，可以指定許多選項來管理從 Azure 儲存體傳回結果的方式。 本文說明如何使用 [適用于 .net 的 Azure 儲存體用戶端程式庫](/dotnet/api/overview/azure/storage)來列出容器。  

## <a name="understand-container-listing-options"></a>瞭解容器清單選項

若要列出儲存體帳戶中的容器，請呼叫下列其中一種方法：

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

- [GetBlobContainers](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainers)
- [GetBlobContainersAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainersasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

---

這些方法的多載會提供其他選項，以管理清單作業傳回容器的方式。 下列各節描述這些選項。

### <a name="manage-how-many-results-are-returned"></a>管理傳回的結果數目

根據預設，清單作業一次最多會傳回 5000 個結果。 若要傳回一組較小的結果，請為要傳回的結果頁面大小提供非零值。

如果您的儲存體帳戶包含5000個以上的容器，或您已指定頁面大小，使清單作業傳回儲存體帳戶中的容器子集，則 Azure 儲存體會傳回包含容器清單的 *接續權杖* 。 接續權杖是不透明的值，其可用來從 Azure 儲存體中擷取下一組結果。

在您的程式碼中，檢查接續權杖的值，以判斷它是否為空的 (適用于 .NET v12) 或適用于 .NET v11 的 null (或較早的) 。 當接續權杖為 null 時，結果集就會完成。 如果接續 token 不是 null，請再次呼叫清單方法，並傳入接續 token 以取得下一組結果，直到接續 token 為 null 為止。

### <a name="filter-results-with-a-prefix"></a>使用前置詞篩選結果

若要篩選容器清單，請指定 `prefix` 參數的字串。 前置詞字串可包含一或多個字元。 Azure 儲存體然後只會傳回名稱開頭為該前置詞的容器。

### <a name="return-metadata"></a>傳回中繼資料

若要傳回容器中繼資料和結果，請為 .NET v12 指定[BlobContainerTraits](/dotnet/api/azure.storage.blobs.models.blobcontainertraits)列舉 (的**中繼資料**值) 或針對 .net v11 和較早的) 指定[ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) enum (。 Azure 儲存體包含每個傳回的容器的中繼資料，因此您不需要也提取容器中繼資料。

## <a name="example-list-containers"></a>範例：列出容器

下列範例會以非同步方式列出儲存體帳戶中以指定前置詞開頭的容器。 此範例會列出以指定的前置詞開頭的容器，並傳回每次呼叫清單作業的指定結果數目。 然後，它會使用接續 token 來取得下一個結果區段。 此範例也會傳回包含結果的容器中繼資料。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="ListContainers":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix,
                                                        int? segmentSize)
{
    Console.WriteLine("List containers beginning with prefix {0}, plus container metadata:", prefix);

    BlobContinuationToken continuationToken = null;
    ContainerResultSegment resultSegment;

    try
    {
        do
        {
            // List containers beginning with the specified prefix,
            // returning segments of 5 results each.
            // Passing in null for the maxResults parameter returns the maximum number of results (up to 5000).
            // Requesting the container's metadata as part of the listing operation populates the metadata,
            // so it's not necessary to call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, segmentSize, continuationToken, null, null);

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

            // Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);

        Console.WriteLine();
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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>另請參閱

- [列出容器](/rest/api/storageservices/list-containers2)
- [列舉 Blob 資源](/rest/api/storageservices/enumerating-blob-resources)
