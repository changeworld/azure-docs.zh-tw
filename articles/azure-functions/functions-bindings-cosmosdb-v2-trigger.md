---
title: 函數 2.x 的 Azure 宇宙 DB 觸發器
description: 瞭解如何在 Azure 函數中使用 Azure 宇宙資料庫觸發器。
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: de8ad39ef731af3dc272d700eeee346acda64b53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277566"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x"></a>Azure 函數 2.x 的 Azure 宇宙 DB 觸發器

Azure Cosmos DB 觸發程序會使用 [Azure Cosmos DB 變更摘要](../cosmos-db/change-feed.md)，跨分割區接聽插入項目及變更。 變更摘要會發行插入和更新，而非刪除。

有關設置和配置詳細資訊的資訊，請參閱[概述](./functions-bindings-cosmosdb-v2.md)。

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

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

下列範例示範 function.json** 檔案中的 Cosmos DB 觸發程序繫結，以及使用此繫結的 [C# 指令碼函式](functions-reference-csharp.md)。 當添加或修改 Cosmos DB 記錄時，該函數寫入日誌消息。

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

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

下列範例示範的是使用繫結之 function.json** 檔案，以及 [JavaScript 函式](functions-reference-node.md)中的 Cosmos DB 觸發程序繫結。 當添加或修改 Cosmos DB 記錄時，該函數寫入日誌消息。

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

# <a name="java"></a>[JAVA](#tab/java)

當指定的資料庫和集合中插入或更新時，將調用此功能。

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

## <a name="attributes-and-annotations"></a>屬性和注釋

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

有關完整示例，請參閱[觸發器](#example)。

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

C# 腳本不支援屬性。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 不支援屬性。

# <a name="java"></a>[JAVA](#tab/java)

從[JAVA 函數運行時庫](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)，對從`@CosmosDBInput`Cosmos DB 讀取資料的參數使用注釋。

---

## <a name="configuration"></a>組態

下表介紹了您在*函數.json*檔和`CosmosDBTrigger`屬性中設置的綁定配置屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**型別** | n/a | 必須設為 `cosmosDBTrigger`。 |
|**direction** | n/a | 必須設為 `in`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此參數。 |
|**名稱** | n/a | 函式程式碼中使用的變數名稱，代表有變更的文件清單。 |
|**連接字串設置**|**ConnectionStringSetting** | 應用程式設定的名稱，包含用來連接到要監視之 Azure Cosmos DB 帳戶的連接字串。 |
|**databaseName**|**資料庫名稱**  | 含有要監視之集合的 Azure Cosmos DB 資料庫名稱。 |
|**集合名稱** |**CollectionName** | 要監視的集合名稱。 |
|**租約連接字串設置** | **LeaseConnectionStringSetting** | （可選）包含與持有租約集合的 Azure Cosmos DB 帳戶的連接字串的應用設置的名稱。 如果未設定，會使用 `connectionStringSetting` 值。 在入口網站中建立繫結時，會自動設定此參數。 租用集合的連接字串必須具有寫入權限。|
|**租約資料庫名稱** |**LeaseDatabaseName** | (選擇性) 保存用來儲存租用之集合的資料庫名稱。 如果未設定，會使用 `databaseName` 設定的值。 在入口網站中建立繫結時，會自動設定此參數。 |
|**租約集合名稱** | **LeaseCollectionName** | (選擇性) 用來儲存租用的集合名稱。 如果未設定，會使用 `leases` 值。 |
|**創建租約集合如果不存在** | **CreateLeaseCollectionIfNotExists** | (選擇性) 設為 `true` 時，如果租用集合尚未存在，即會自動加以建立。 預設值是 `false`。 |
|**leasesCollectionThroughput**| **租賃收集輸送量**| （可選）定義創建租約集合時要分配的請求單位數。 僅當設置為 時，才`createLeaseCollectionIfNotExists`使用此設置`true`。 使用入口網站建立繫結時，會自動設定此參數。
|**租賃收集首碼**| **LeaseCollectionPrefix**| （可選）設置後，該值將作為首碼添加到此函數的租約集合中創建的租約。 使用首碼允許兩個單獨的 Azure 函數使用不同的首碼共用相同的租約集合。
|**飼料延遲**| **FeedPollDelay**| （可選）在耗盡所有當前更改後，輪詢分區以在源上為新更改之間的延遲時間（以毫秒為單位）。 預設值為 5，000 毫秒或 5 秒。
|**租賃獲取間隔**| **LeaseAcquireInterval**| (選擇性) 如果設定，將會以毫秒為單位定義啟動工作以計算分割區是否平均分散到已知主機執行個體的間隔。 預設值為 13000 (13 秒)。
|**租約過期間隔**| **LeaseExpirationInterval**| (選擇性) 如果設定，將會以毫秒為單位定義租用代表分割區的間隔。 未在此間隔內更新的租用將會過期，且分割區的擁有權會移轉給另一個執行個體。 預設值為 60000 (60 秒)。
|**租約續訂間隔**| **LeaseRenewInterval**| (選擇性) 如果設定，將會以毫秒為單位定義目前由執行個體保有之分割區的所有租用所適用的更新間隔。 預設值為 17000 (17 秒)。
|**檢查點頻率**| **CheckpointFrequency**| (選擇性) 如果設定，將會以毫秒為單位定義租用檢查點的間隔。 預設永遠是各函式呼叫後。
|**maxItemsPerInvocation**| **最大專案perin調用**| （可選）設置後，此屬性將設置每個函式呼叫接收的最大項數。 如果通過預存程序執行受監視集合中的操作，則從更改源讀取項時將保留[事務範圍](../cosmos-db/stored-procedures-triggers-udfs.md#transactions)。 因此，收到的項數可以高於指定值，以便將同一事務更改的項作為一個原子批次處理的一部分返回。
|**從開始**| **StartFromBeginning**| （可選）此選項告訴觸發器從集合更改歷史記錄的開頭讀取更改，而不是從目前時間開始。 從頭開始讀取僅在觸發器初次開機時起作用，因為在後續運行中，檢查點已存儲。 將此選項設置為`true`已創建租約時不起作用。 |
|**首選位置**| **首選位置**| （可選）為 Azure Cosmos 資料庫服務中的異地複製資料庫帳戶定義首選位置（區域）。 值應為逗號分隔。 例如，"美國東部、美國中南部、北歐"。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用量

觸發程序需要第二個集合，用來在分割區上儲存「租用」__。 要監視的集合和包含租用的集合必須可供觸發程序用來運作。

>[!IMPORTANT]
> 如果針對同一個集合設定多個函式使用 Cosmos DB 觸發程序，則每個函式都應該使用專用的租用集合，或為每個函式指定不同的 `LeaseCollectionPrefix`。 否則，將只會觸發其中一個函式。 如需前置詞的相關資訊，請參閱[組態區段](#configuration)。

觸發程序不會指示是否更新或插入文件，只是提供文件本身。 如果您需要以不同方式處理更新和插入，您可以透過實作插入或更新的時間戳記欄位執行。

## <a name="next-steps"></a>後續步驟

- [閱讀 Azure 宇宙資料庫文檔（輸入綁定）](./functions-bindings-cosmosdb-v2-input.md)
- [保存對 Azure Cosmos DB 文檔的更改（輸出綁定）](./functions-bindings-cosmosdb-v2-output.md)