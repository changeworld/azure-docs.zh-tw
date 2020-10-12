---
title: 適用于 Azure Functions 的 Azure Blob 儲存體輸入系結
description: 瞭解如何將 Azure Blob 儲存體輸入系結資料提供給 Azure 函數。
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: f5a01724bfefd50297182f998b46f99eacca5843
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325771"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>適用于 Azure Functions 的 Azure Blob 儲存體輸入系結

輸入系結可讓您讀取 blob 儲存體資料，作為 Azure 函數的輸入。

如需安裝和設定詳細資料的相關資訊，請參閱[概觀](./functions-bindings-storage-blob.md)。

## <a name="example"></a>範例

# <a name="c"></a>[C#](#tab/csharp)

下列範例是使用一個佇列觸發程序和一個輸入 Blob 繫結的 [C# 函式](functions-dotnet-class-library.md)。 佇列訊息包含 Blob 的名稱，而函式會記錄 Blob 的大小。

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

<!--Same example for input and output. -->

下列範例所示範的是使用繫結之 function.json** 檔案，以及 [C# 指令碼 (.csx)](functions-reference-csharp.md) 程式碼中的 Blob 輸入和輸出繫結。 此函式會建立文字 Blob 的複本。 此函式是由佇列訊息 (包含要複製的 Blob 名稱) 觸發。 新的 blob 名稱為 *{originalblobname}-Copy*。

在 *function.json* 檔案中，`queueTrigger` 中繼資料屬性用於指定 `path` 屬性中的 Blob 名稱：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[設定](#configuration)章節會說明這些屬性。

以下是 C# 指令碼程式碼：

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

下列範例所示範的是 *function.json* 檔案中的 blob 輸入和輸出繫結，以及使用該繫結的 [JavaScript 程式碼](functions-reference-node.md)。 此函式會建立 Blob 的複本。 此函式是由佇列訊息 (包含要複製的 Blob 名稱) 觸發。 新的 blob 名稱為 *{originalblobname}-Copy*。

在 *function.json* 檔案中，`queueTrigger` 中繼資料屬性用於指定 `path` 屬性中的 Blob 名稱：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[設定](#configuration)章節會說明這些屬性。

以下是 JavaScript 程式碼：

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

下列範例所示範的是使用繫結之 function.json** 檔案，以及 [Python 指令碼](functions-reference-python.md)程式碼中的 Blob 輸入和輸出繫結。 此函式會建立 Blob 的複本。 此函式是由佇列訊息 (包含要複製的 Blob 名稱) 觸發。 新的 blob 名稱為 *{originalblobname}-Copy*。

在 *function.json* 檔案中，`queueTrigger` 中繼資料屬性用於指定 `path` 屬性中的 Blob 名稱：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "dataType": "binary",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

[設定](#configuration)章節會說明這些屬性。

以下是 Python 程式碼：

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="java"></a>[Java](#tab/java)

本區段包含下列範例：

* [HTTP 觸發程序，從查詢字串中查閱 Blob 名稱](#http-trigger-look-up-blob-name-from-query-string)
* [佇列觸發程序，接收來自佇列訊息的 Blob 名稱](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>HTTP 觸發程序，從查詢字串中查閱 Blob 名稱

 下列範例示範使用 `HttpTrigger` 註解接收參數 (其中包含 Blob 儲存體容器中的檔案名稱) 的 Java 函式。 `BlobInput` 註解接著會讀取檔案，並將其內容傳遞給函式做為 `byte[]`。

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>佇列觸發程序，接收來自佇列訊息的 Blob 名稱

 下列範例示範使用 `QueueTrigger` 註解接收訊息 (其中包含 Blob 儲存體容器中的檔案名稱) 的 Java 函式。 `BlobInput` 註解接著會讀取檔案，並將其內容傳遞給函式做為 `byte[]`。

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

在 [Java 函式執行階段程式庫](/java/api/overview/azure/functions/runtime)中，對其值來自 Blob 的參數使用 `@BlobInput` 註釋。  此註釋可以搭配原生 Java 類型、POJO 或使用 `Optional<T>` 的可為 Null 值使用。

---

## <a name="attributes-and-annotations"></a>屬性和註釋

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 類別庫](functions-dotnet-class-library.md)中，使用 [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)。

屬性的建構函式會採用 Blob 路徑和指示讀取或寫入的 `FileAccess` 參數，如下列範例所示：

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

您可以設定 `Connection` 屬性來指定要使用的儲存體帳戶，如下列範例所示：

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

您可以使用 `StorageAccount` 屬性來指定類別、方法或參數層級的儲存體帳戶。 如需詳細資訊，請參閱 [觸發程式屬性和批註](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations)。

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

C# 指令碼不支援屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 指令碼不支援屬性。

# <a name="java"></a>[Java](#tab/java)

`@BlobInput`屬性可讓您存取觸發函數的 blob。 如果您搭配屬性使用位元組陣列，請將設定 `dataType` 為 `binary` 。 如需詳細資訊，請參閱 [輸入範例](#example) 。

---

## <a name="configuration"></a>組態

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `Blob` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type** | n/a | 必須設為 `blob`。 |
|**direction** | n/a | 必須設為 `in`。 例外狀況在[使用方式](#usage)一節中會加以說明。 |
|**name** | n/a | 表示函式程式碼中 Blob 的變數名稱。|
|**path** |**BlobPath** | blob 的路徑。 |
|**connection** |**[連接]**| 應用程式設定的名稱，其中包含要用於此系結的 [儲存體連接字串](../storage/common/storage-configure-connection-string.md) 。 如果應用程式設定名稱是以「AzureWebJobs」開頭，於此僅能指定名稱的其餘部分。 例如，如果您將設定 `connection` 為 "MyStorage"，則函式執行時間會尋找名為 "AzureWebJobsMyStorage" 的應用程式設定。 如果您將 `connection` 保留空白，則函式執行階段會使用應用程式設定中名稱為 `AzureWebJobsStorage` 的預設儲存體連接字串。<br><br>連接字串必須為一般用途的儲存體帳戶，不可為[僅限 Blob 的儲存體帳戶](../storage/common/storage-account-overview.md#types-of-storage-accounts)。|
|n/a | **存取** | 指出您是否將讀取或寫入。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用量

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用存取 blob 資料的 `context.bindings.<NAME>` 位置 `<NAME>` 符合 *function.json*中定義的值。

# <a name="python"></a>[Python](#tab/python)

透過輸入為 [InputStream](/python/api/azure-functions/azure.functions.inputstream?view=azure-python)的參數存取 blob 資料。 如需詳細資訊，請參閱 [輸入範例](#example) 。

# <a name="java"></a>[Java](#tab/java)

`@BlobInput`屬性可讓您存取觸發函數的 blob。 如果您搭配屬性使用位元組陣列，請將設定 `dataType` 為 `binary` 。 如需詳細資訊，請參閱 [輸入範例](#example) 。

---

## <a name="next-steps"></a>後續步驟

- [在 blob 儲存體資料變更時執行函數](./functions-bindings-storage-blob-trigger.md)
- [從函式寫入 blob 儲存體資料](./functions-bindings-storage-blob-output.md)
