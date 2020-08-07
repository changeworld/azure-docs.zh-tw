---
title: 函數2.x 和更新版本的 Azure Cosmos DB 觸發程式
description: 瞭解如何在 Azure Functions 中使用 Azure Cosmos DB 觸發程式。
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-python
ms.openlocfilehash: d67a08d97a24d492b07f6a8b91dc20a4d7f16979
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87848446"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x-and-higher"></a>Azure Functions 2.x 和更新版本的 Azure Cosmos DB 觸發程式

Azure Cosmos DB 觸發程序會使用 [Azure Cosmos DB 變更摘要](../cosmos-db/change-feed.md)，跨分割區接聽插入項目及變更。 變更摘要會發行插入和更新，而非刪除。

如需安裝和設定詳細資料的相關資訊，請參閱[概觀](./functions-bindings-cosmosdb-v2.md)。

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

以下範例顯示當指定的資料庫和集合中具備插入項目或更新時，系統叫用的 [C# 函式](functions-dotnet-class-library.md)。

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

下列範例示範 function.json** 檔案中的 Cosmos DB 觸發程序繫結，以及使用此繫結的 [C# 指令碼函式](functions-reference-csharp.md)。 新增或修改 Cosmos DB 記錄時，函數會寫入記錄訊息。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

以下是 C# 指令碼程式碼：

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下列範例示範的是使用繫結之 function.json** 檔案，以及 [JavaScript 函式](functions-reference-node.md)中的 Cosmos DB 觸發程序繫結。 新增或修改 Cosmos DB 記錄時，函數會寫入記錄訊息。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

以下是 JavaScript 程式碼：

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

# <a name="python"></a>[Python](#tab/python)

下列範例示範 function.json** 檔案中的 Cosmos DB 觸發程序繫結，以及使用此繫結的 [Python 指令碼函式](functions-reference-python.md)。 修改 Cosmos DB 記錄時，函式會寫入記錄訊息。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

以下是 Python 程式碼：

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

# <a name="java"></a>[Java](#tab/java)

當指定的資料庫和集合中有插入或更新時，就會叫用這個函數。

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


在 [Java 函式執行階段程式庫](/java/api/overview/azure/functions/runtime)中，對其值來自 Cosmos DB 的參數使用 `@CosmosDBTrigger` 註釋。  此註釋可以搭配原生 Java 類型、POJO 或使用 `Optional<T>` 的可為 Null 值使用。

---

## <a name="attributes-and-annotations"></a>屬性和註釋

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 類別庫](functions-dotnet-class-library.md)中，使用 [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) 屬性。

屬性的建構函式可接受資料庫名稱和集合名稱。 如需這些設定及其他您可以設定之屬性的相關資訊，請參閱[觸發程序 - 組態](#configuration)。 以下是方法簽章中的 `CosmosDBTrigger` 屬性範例：

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

如需完整範例，請參閱[觸發](#example)程式。

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

C# 指令碼不支援屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 指令碼不支援屬性。

# <a name="java"></a>[Java](#tab/java)

從 JAVA 函式執行時間連結[庫](/java/api/overview/azure/functions/runtime)， `@CosmosDBInput` 在從 Cosmos DB 讀取資料的參數上使用注釋。

---

## <a name="configuration"></a>組態

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `CosmosDBTrigger` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type** | n/a | 必須設為 `cosmosDBTrigger`。 |
|**direction** | n/a | 必須設為 `in`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此參數。 |
|**name** | n/a | 函式程式碼中使用的變數名稱，代表有變更的文件清單。 |
|**connectionStringSetting**|**ConnectionStringSetting** | 應用程式設定的名稱，包含用來連接到要監視之 Azure Cosmos DB 帳戶的連接字串。 |
|**名稱**|**DatabaseName**  | 含有要監視之集合的 Azure Cosmos DB 資料庫名稱。 |
|**collectionName** |**CollectionName** | 要監視的集合名稱。 |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** |  (選擇性) 應用程式設定的名稱，其中包含保留租用集合之 Azure Cosmos DB 帳戶的連接字串。 如果未設定，會使用 `connectionStringSetting` 值。 在入口網站中建立繫結時，會自動設定此參數。 租用集合的連接字串必須具有寫入權限。|
|**leaseDatabaseName** |**LeaseDatabaseName** | (選擇性) 保存用來儲存租用之集合的資料庫名稱。 如果未設定，會使用 `databaseName` 設定的值。 在入口網站中建立繫結時，會自動設定此參數。 |
|**leaseCollectionName** | **LeaseCollectionName** | (選擇性) 用來儲存租用的集合名稱。 如果未設定，會使用 `leases` 值。 |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (選擇性) 設為 `true` 時，如果租用集合尚未存在，即會自動加以建立。 預設值是 `false`。 |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**|  (選擇性) 定義建立租用集合時所要指派的要求單位數目。 只有當設定為時，才會使用此設定 `createLeaseCollectionIfNotExists` `true` 。 使用入口網站建立繫結時，會自動設定此參數。
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**|  (選擇性) 設定時，會將值新增為在此函式的租用集合中建立之租用的前置詞。 使用前置詞可讓兩個不同的 Azure Functions 使用不同的首碼來共用相同的租用集合。
|**feedPollDelay**| **FeedPollDelay**|  (選擇性) 在所有目前的變更清空之後，針對摘要的新變更輪詢分割區之間的延遲時間 (（以毫秒為單位) ）。 預設值為5000毫秒，或5秒。
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (選擇性) 如果設定，將會以毫秒為單位定義啟動工作以計算分割區是否平均分散到已知主機執行個體的間隔。 預設值為 13000 (13 秒)。
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (選擇性) 如果設定，將會以毫秒為單位定義租用代表分割區的間隔。 未在此間隔內更新的租用將會過期，且分割區的擁有權會移轉給另一個執行個體。 預設值為 60000 (60 秒)。
|**leaseRenewInterval**| **LeaseRenewInterval**| (選擇性) 如果設定，將會以毫秒為單位定義目前由執行個體保有之分割區的所有租用所適用的更新間隔。 預設值為 17000 (17 秒)。
|**checkpointFrequency**| **CheckpointFrequency**| (選擇性) 如果設定，將會以毫秒為單位定義租用檢查點的間隔。 預設永遠是各函式呼叫後。
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**|  (選擇性) 設定時，此屬性會設定每個函式呼叫所接收的最大專案數。 如果受監視集合中的作業是透過預存程式執行，則從變更摘要讀取專案時，會保留[交易範圍](../cosmos-db/stored-procedures-triggers-udfs.md#transactions)。 因此，接收的專案數可能會高於指定的值，如此一來，相同交易變更的專案就會當做一個不可部分完成批次的一部分傳回。
|**startFromBeginning**| **StartFromBeginning**|  (選擇性) 此選項會告知觸發程式從集合的變更歷程記錄開始讀取變更，而不是從目前的時間開始。 從開頭開始讀取只會在觸發程式第一次啟動時運作，而在後續的執行中，檢查點已經儲存。 若已建立租用，將此選項設定為，則 `true` 不會有任何作用。 |
|**preferredLocations**| **PreferredLocations**|  (選擇性) 會針對 Azure Cosmos DB 服務中異地複寫的資料庫帳戶定義慣用位置 (區域) 。 值應該以逗號分隔。 例如，「美國東部、美國中南部、北歐」。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用量

觸發程序需要第二個集合，用來在分割區上儲存「租用」__。 要監視的集合和包含租用的集合必須可供觸發程序用來運作。

>[!IMPORTANT]
> 如果針對同一個集合設定多個函式使用 Cosmos DB 觸發程序，則每個函式都應該使用專用的租用集合，或為每個函式指定不同的 `LeaseCollectionPrefix`。 否則，將只會觸發其中一個函式。 如需前置詞的相關資訊，請參閱[組態區段](#configuration)。

觸發程序不會指示是否更新或插入文件，只是提供文件本身。 如果您需要以不同方式處理更新和插入，您可以透過實作插入或更新的時間戳記欄位執行。

## <a name="next-steps"></a>後續步驟

- [閱讀 Azure Cosmos DB 檔 (輸入系結) ](./functions-bindings-cosmosdb-v2-input.md)
- [將變更儲存至 Azure Cosmos DB 檔 (輸出系結) ](./functions-bindings-cosmosdb-v2-output.md)
