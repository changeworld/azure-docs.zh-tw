---
title: 管理 Blob 儲存體中的並行
titleSuffix: Azure Storage
description: 瞭解如何在應用程式中執行開放式或封閉式平行存取，以管理多個寫入器至 blob。 開放式平行存取會檢查 blob 的 ETag 值，並將它與所提供的 ETag 進行比較。 封閉式平行存取會使用獨佔租用將 blob 鎖定至其他寫入器。
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: ea0bed0884a3a03e2cd15b274b2afb0f054b0cbd
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523405"
---
# <a name="managing-concurrency-in-blob-storage"></a>管理 Blob 儲存體中的並行存取

新式應用程式通常會有多位使用者同時查看和更新資料。 應用程式開發人員必須仔細考慮如何為其使用者提供可預測的體驗，特別是在多個使用者可以更新相同資料的情況下。 開發人員通常會考量三個主要的資料並行存取策略：  

- **開放式並行** 存取：執行更新的應用程式將作為其更新的一部分，以判斷自從應用程式上次讀取該資料之後，資料是否已變更。 例如，如果兩個使用者看到 wiki 頁面對該頁面進行更新，則 wiki 平臺必須確保第二個更新不會覆寫第一個更新。 它也必須確保這兩個使用者都能瞭解其更新是否成功。 此策略最常用在 Web 應用程式中。

- 封閉式 **並行** 存取：若要執行更新的應用程式將會鎖定物件，以防止其他使用者更新資料，直到釋放鎖定為止。 例如，在只有主要複本執行更新的主要/次要資料複寫案例中，主要複本通常會保留資料的獨佔鎖定一段長時間，以確保沒有其他人可以更新它。

- **最後寫入者獲勝**：一種方法，可讓更新作業繼續進行，而不需要先判斷是否有其他應用程式在讀取之後更新資料。 當資料分割時，通常會使用這種方法，讓多個使用者不會同時存取相同的資料。 在處理暫時性資料串流的情況中，也可以利用此策略。

Azure 儲存體支援這三個策略，雖然它的功能是提供開放式和封閉式平行存取的完整支援。 Azure 儲存體的設計目的是採用強式一致性模型，以保證在服務執行插入或更新作業之後，後續的讀取作業會傳回最新的更新。

除了選取適當的平行存取策略之外，開發人員也應該留意儲存體平臺如何隔離變更，尤其是跨交易對相同物件進行的變更。 Azure 儲存體使用快照集隔離，在單一資料分割內同時允許讀取作業與寫入作業。 快照集隔離可保證即使在進行更新時，所有讀取作業仍會傳回一致的資料快照集。

您可以選擇使用開放式或封閉式平行存取模型來管理 blob 和容器的存取權。 如果您未明確指定策略，則預設為最後一個寫入器獲勝。  

## <a name="optimistic-concurrency"></a>開放式並行存取

Azure 儲存體會將識別碼指派給每個儲存的物件。 每次在物件上執行寫入作業時，就會更新此識別碼。 在 HTTP 通訊協定所定義的 ETag 標頭中，會將識別碼傳回給用戶端，做為 HTTP GET 回應的一部分。

執行更新的用戶端可以將原始 ETag 與條件式標頭一起傳送，以確保只有在符合特定條件時才會進行更新。 例如，如果指定了 **if-match** 標頭，Azure 儲存體會確認更新要求中指定的 etag 值與要更新之物件的 etag 相同。 如需條件式標頭的詳細資訊，請參閱 [指定 Blob 服務作業的條件式標頭](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)。

此程序大致如下：  

1. 從 Azure 儲存體取出 blob。 回應包含 HTTP ETag 標頭值，可識別物件的目前版本。
1. 當您更新 blob 時，請在寫入要求的 **if-match 條件式** 標頭中包含您在步驟1中收到的 ETag 值。 Azure 儲存體會將要求中的 ETag 值與 blob 目前的 ETag 值進行比較。
1. 如果 blob 目前的 ETag 值與要求中所提供之 **if-match** 條件標頭中指定的值不同，則 Azure 儲存體會傳回 HTTP 狀態碼 412 (前置條件失敗) 。 此錯誤指出用戶端第一次抓取 blob 之後，另一個進程已更新 blob。
1. 如果 blob 目前的 ETag 值與要求中 **if-match 條件式** 標頭的 etag 版本相同，Azure 儲存體會執行要求的作業，並更新 blob 目前的 etag 值。  

下列程式碼範例示範如何在檢查 blob **之** ETag 值的寫入要求上，建立 if-match 條件。 Azure 儲存體會評估 blob 的目前 ETag 是否與要求所提供的 ETag 相同，而且只有在兩個 ETag 值相符時，才會執行寫入作業。 如果另一個進程已在過渡期間更新 blob，則 Azure 儲存體會傳回 HTTP 412 (前置條件失敗) 狀態訊息。  

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstrateOptimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstrateOptimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate optimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    // Create test blob. The default strategy is last writer wins, so
    // write operation will overwrite existing blob if present.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");

    // Retrieve the ETag from the newly created blob.
    string originalETag = blockBlob.Properties.ETag;
    Console.WriteLine("Blob added. Original ETag = {0}", originalETag);

    /// This code simulates an update by another client.
    string helloText = "Blob updated by another client.";
    // No ETag was provided, so original blob is overwritten and ETag updated.
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}", blockBlob.Properties.ETag);

    // Now try to update the blob using the original ETag value.
    try
    {
        Console.WriteLine(@"Attempt to update blob using original ETag
                            to generate if-match access condition");
        blockBlob.UploadText(helloText, accessCondition: AccessCondition.GenerateIfMatchCondition(originalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure as expected.
                                Blob's ETag does not match.");
        }
        else
        {
            throw;
        }
    }
    Console.WriteLine();
}
```

---

Azure 儲存體也支援其他 **條件式標** 頭，包括如同 **是否已修改-自** 和- **None 相符**。 如需詳細資訊，請參閱[指定 Blob 服務作業的條件式標頭](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)。  

## <a name="pessimistic-concurrency-for-blobs"></a>Blob 的封閉式並行存取

若要鎖定 Blob 以進行獨佔使用，您可以取得其 租用 。 當您取得租用時，您會指定租用的持續時間。 有限租用的有效時間可能介於15到60秒之間。 租用也可以是無限的，其數量為獨佔鎖定。 您可以更新有限的租用以進行擴充，而且您可以在完成時釋放租用。 Azure 儲存體會在有限租用到期時自動加以釋放。  

租用可以支援不同的同步處理策略，包括獨佔寫入/共用讀取作業、獨佔寫入/獨佔讀取作業，以及共用寫入/獨佔讀取作業。 當租用存在時，Azure 儲存體會對租用持有者強制執行寫入作業的獨佔存取權。 不過，若要確保讀取作業的獨佔性，開發人員必須確保所有用戶端應用程式都使用租用識別碼，且一次只有一個用戶端具有有效的租用識別碼。 未包含租用識別碼的讀取作業將會導致共用讀取。  

下列程式碼範例示範如何取得 blob 的獨佔租用、提供租用識別碼以更新 blob 的內容，然後釋放租用。 如果租用處于作用中狀態，且寫入要求未提供租用識別碼，則寫入作業會失敗，錯誤碼為 412 (先決條件失敗) 。  

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstratePessimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstratePessimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate pessimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");
    Console.WriteLine("Blob added.");

    // Acquire lease for 15 seconds.
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation should succeed.
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    // Simulate another client attempting to update to blob without providing lease.
    try
    {
        // Operation will fail as no valid lease was provided.
        Console.WriteLine("Now try to update blob without valid lease.");
        blockBlob.UploadText("Update operation will fail without lease.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure error as expected.
                                Blob lease not provided.");
        }
        else
        {
            throw;
        }
    }

    // Release lease proactively.
    blockBlob.ReleaseLease(accessCondition);
    Console.WriteLine();
}
```

---

## <a name="pessimistic-concurrency-for-containers"></a>容器的封閉式並行存取

容器上的租用會啟用與 blob 支援的相同同步處理策略，包括獨佔寫入/共用讀取、獨佔寫入/獨佔讀取和共用寫入/獨佔讀取。 但是針對容器，只會在刪除作業上強制執行獨佔鎖定。 若要刪除具有作用中租用的容器，用戶端必須使用刪除要求納入作用中的租用識別碼。 所有其他容器作業都將在租用的容器上成功，但不含租用識別碼。  

## <a name="next-steps"></a>後續步驟

* [指定 Blob 服務作業的條件式標頭](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)
* [租用容器](/rest/api/storageservices/lease-container)
* [租用 Blob](/rest/api/storageservices/lease-blob)
