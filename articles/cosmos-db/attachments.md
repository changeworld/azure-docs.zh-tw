---
title: Azure Cosmos DB 附件
description: 本文提供 Azure Cosmos DB 附件的總覽。
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/07/2020
ms.reviewer: sngun
ms.openlocfilehash: b6504c0521328edc356dea1c146fe9aeb6bde55f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092733"
---
# <a name="azure-cosmos-db-attachments"></a>Azure Cosmos DB 附件
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 附件是特殊的專案，其中包含與外部 blob 或媒體檔案相關聯之中繼資料的參考。

Azure Cosmos DB 支援兩種類型的附件：

* **非受控附件** 是 blob 參考的包裝函式，該 blob 會儲存在外部服務 (例如 Azure 儲存體、OneDrive 等 ) 。 這種方法類似于在標準 Azure Cosmos DB 專案中儲存 URI 屬性。
* **受控附件** 是由 Azure Cosmos DB 在內部進行管理和儲存的 blob，並透過系統產生的 mediaLink 公開。


> [!NOTE]
> 附件是舊版的功能。 如果您已經在使用這項功能，其支援範圍會提供持續的功能。
> 
> 我們建議您使用 Azure Blob 儲存體作為用來儲存 blob 資料的特殊用途 Blob 儲存體服務，而不是使用附件。 您可以繼續在 Azure Cosmos DB 中將與 blob 相關的中繼資料（以及參考 URI 連結）儲存為專案屬性。 在 Azure Cosmos DB 中儲存此資料可讓您查詢中繼資料，並連結至儲存在 Azure Blob 儲存體中的 blob。
> 
> Microsoft 致力於在完整淘汰附件之前，提供至少36個月的通知，這會在日後宣佈。

## <a name="known-limitations"></a>已知的限制

Azure Cosmos DB 的受控附件與其對標準專案的支援不同-其提供無限制的擴充性、全域散發，以及與其他 Azure 服務的整合。

- 所有版本的 Azure Cosmos DB Sdk 都不支援附件。
- 針對每個資料庫帳戶，受控附件的儲存空間限制為 2 GB。
- 受控附件與 Azure Cosmos DB 的全域散發不相容，且不會跨區域複寫。

## <a name="migrating-attachments-to-azure-blob-storage"></a>將附件遷移至 Azure Blob 儲存體

建議您依照下列步驟，將 Azure Cosmos DB 附件遷移至 Azure Blob 儲存體：

1. 將來源 Azure Cosmos DB 容器中的附件資料複製到目標 Azure Blob 儲存體容器。
2. 驗證目標 Azure Blob 儲存體容器中已上傳的 blob 資料。
3. 如果適用的話，請將 URI 參考新增至 Azure Blob 儲存體中包含的 blob，作為 Azure Cosmos DB 資料集內的字串屬性。
4. 重構您的應用程式程式碼，以從新的 Azure Blob 儲存體容器讀取和寫入 blob。

下列 dotnet 程式碼範例示範如何使用 Azure Cosmos DB 的 .NET SDK v2 和 Azure Blob 儲存體 .NET SDK v12，將附件從 Azure Cosmos DB 複製到 Azure Blob 儲存體作為遷移流程的一部分。 請務必取代 `<placeholder values>` 來源 Azure Cosmos DB 帳戶和目標 Azure Blob 儲存體容器的。

```csharp

using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;

namespace attachments
{
    class Program
    {
        private static string cosmosAccount = "<Your_Azure_Cosmos_account_URI>";
        private static string cosmosKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>";
        private static string cosmosDatabaseName = "<Your_Azure_Cosmos_database>";
        private static string cosmosCollectionName = "<Your_Azure_Cosmos_collection>";
        private static string storageConnectionString = "<Your_Azure_Storage_connection_string>";
        private static string storageContainerName = "<Your_Azure_Storage_container_name>";
        private static DocumentClient cosmosClient = new DocumentClient(new Uri(cosmosAccount), cosmosKey);
        private static BlobServiceClient storageClient = new BlobServiceClient(storageConnectionString);
        private static BlobContainerClient storageContainerClient = storageClient.GetBlobContainerClient(storageContainerName);

        static void Main(string[] args)
        {
            CopyAttachmentsToBlobsAsync().Wait();
        }

        private async static Task CopyAttachmentsToBlobsAsync()
        {
            Console.WriteLine("Copying Azure Cosmos DB Attachments to Azure Blob Storage ...");

            int totalCount = 0;
            string docContinuation = null;

            // Iterate through each item (document in v2) in the Azure Cosmos DB container (collection in v2) to look for attachments.
            do
            {
                FeedResponse<dynamic> response = await cosmosClient.ReadDocumentFeedAsync(
                    UriFactory.CreateDocumentCollectionUri(cosmosDatabaseName, cosmosCollectionName),
                    new FeedOptions
                    {
                        MaxItemCount = -1,
                        RequestContinuation = docContinuation
                    });
                docContinuation = response.ResponseContinuation;

                foreach (Document document in response)
                {
                    string attachmentContinuation = null;
                    PartitionKey docPartitionKey = new PartitionKey(document.Id);

                    // Iterate through each attachment within the item (if any).
                    do
                    {
                        FeedResponse<Attachment> attachments = await cosmosClient.ReadAttachmentFeedAsync(
                            document.SelfLink,
                            new FeedOptions
                            {
                                PartitionKey = docPartitionKey,
                                RequestContinuation = attachmentContinuation
                            }
                        );
                        attachmentContinuation = attachments.ResponseContinuation;

                        foreach (var attachment in attachments)
                        {
                            // Download the attachment in to local memory.
                            MediaResponse content = await cosmosClient.ReadMediaAsync(attachment.MediaLink);

                            byte[] buffer = new byte[content.ContentLength];
                            await content.Media.ReadAsync(buffer, 0, buffer.Length);

                            // Upload the locally buffered attachment to blob storage
                            string blobId = String.Concat(document.Id, "-", attachment.Id);

                            Azure.Response<BlobContentInfo> uploadedBob = await storageContainerClient.GetBlobClient(blobId).UploadAsync(
                                new MemoryStream(buffer, writable: false),
                                true
                            );

                            Console.WriteLine("Copied attachment ... Item Id: {0} , Attachment Id: {1}, Blob Id: {2}", document.Id, attachment.Id, blobId);
                            totalCount++;

                            // Clean up attachment from Azure Cosmos DB.
                            // Warning: please verify you've succesfully migrated attachments to blog storage prior to cleaning up Azure Cosmos DB.
                            // await cosmosClient.DeleteAttachmentAsync(
                            //     attachment.SelfLink,
                            //     new RequestOptions { PartitionKey = docPartitionKey }
                            // );

                            // Console.WriteLine("Cleaned up attachment ... Document Id: {0} , Attachment Id: {1}", document.Id, attachment.Id);
                        }

                    } while (!string.IsNullOrEmpty(attachmentContinuation));
                }
            }
            while (!string.IsNullOrEmpty(docContinuation));

            Console.WriteLine("Finished copying {0} attachments to blob storage", totalCount);
        }
    }
}

```

## <a name="next-steps"></a>下一步

- 開始使用 [Azure Blob 儲存體](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- 取得透過[Azure Cosmos DB 的 .NET SDK v2](/dotnet/api/microsoft.azure.documents.attachment?preserve-view=true&view=azure-dotnet)使用附件的參考
- 取得透過[Azure Cosmos DB 的 JAVA SDK v2](/java/api/com.microsoft.azure.documentdb.attachment?preserve-view=true&view=azure-java-stable)使用附件的參考
- 取得透過[Azure Cosmos DB 的 REST API](/rest/api/cosmos-db/attachments)使用附件的參考