---
title: 適用于 Azure Functions 的 Azure Blob 儲存體輸出系結
description: 瞭解如何將 Azure Blob 儲存體資料提供給 Azure 函數。
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: tracking-python
ms.openlocfilehash: d7ba52c34c376139538a5d0bf7747cceb6b46cb2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87056127"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>適用于 Azure Functions 的 Azure Blob 儲存體輸出系結

輸出系結可讓您修改和刪除 Azure 函式中的 blob 儲存體資料。

如需安裝和設定詳細資料的相關資訊，請參閱[概觀](./functions-bindings-storage-blob.md)。

## <a name="example"></a>範例

# <a name="c"></a>[C#](#tab/csharp)

下列範例是使用一個 Blob 觸發程序和兩個輸出 Blob 繫結的 [C# 函式](functions-dotnet-class-library.md)。 此函式是藉由在 *sample-images* 容器中建立映像 Blob 而觸發。 它會建立映像 Blob 的小型及中型複本。

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageMedium, ImageSize.Medium, format);
        }
    }

    public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
    {
        var dimensions = imageDimensionsTable[size];

        input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
        input.Save(output, format);
    }

    public enum ImageSize { ExtraSmall, Small, Medium }

    private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
        { ImageSize.ExtraSmall, (320, 200) },
        { ImageSize.Small,      (640, 400) },
        { ImageSize.Medium,     (800, 600) }
    };

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
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
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


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="java"></a>[Java](#tab/java)

本區段包含下列範例：

* [HTTP 觸發程序，使用 OutputBinding](#http-trigger-using-outputbinding-java)
* [佇列觸發程序，使用函式傳回值](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP 觸發程序，使用 OutputBinding (Java)

 下列範例示範使用 `HttpTrigger` 註解接收參數 (其中包含 Blob 儲存體容器中的檔案名稱) 的 Java 函式。 `BlobInput` 註解接著會讀取檔案，並將其內容傳遞給函式做為 `byte[]`。 `BlobOutput` 註釋會繫結至 `OutputBinding outputItem`，函式接著會用來將輸入 Blob 的內容寫入到設定的儲存體容器中。

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>佇列觸發程序，使用函式傳回值 (Java)

 下列範例示範使用 `QueueTrigger` 註解接收訊息 (其中包含 Blob 儲存體容器中的檔案名稱) 的 Java 函式。 `BlobInput` 註解接著會讀取檔案，並將其內容傳遞給函式做為 `byte[]`。 `BlobOutput` 註釋會繫結至函式傳回值，執行階段接著會用來將輸入 Blob 的內容寫入到設定的儲存體容器中。

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 在 [Java 函式執行階段程式庫](/java/api/overview/azure/functions/runtime)中，對其值要寫入至 Blob 儲存體中物件的函式參數使用 `@BlobOutput` 註釋。  參數類型應為 `OutputBinding<T>`，其中 T 是任何原生 Java 類型的 POJO。

---

## <a name="attributes-and-annotations"></a>屬性和註釋

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 類別庫](functions-dotnet-class-library.md)中，使用 [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)。

屬性的建構函式會採用 Blob 路徑和指示讀取或寫入的 `FileAccess` 參數，如下列範例所示：

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

您可以設定 `Connection` 屬性來指定要使用的儲存體帳戶，如下列範例所示：

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

C# 指令碼不支援屬性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 指令碼不支援屬性。

# <a name="java"></a>[Java](#tab/java)

`@BlobOutput`屬性可讓您存取觸發函數的 blob。 如果您使用具有屬性的位元組陣列，請將設定 `dataType` 為 `binary` 。 如需詳細資訊，請參閱[輸出範例](#example)。

---

如需完整範例，請參閱[輸出範例](#example)。

您可以使用 `StorageAccount` 屬性來指定類別、方法或參數層級的儲存體帳戶。 如需詳細資訊，請參閱[觸發程序 - 屬性](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations)。

## <a name="configuration"></a>組態

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `Blob` 屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**type** | n/a | 必須設為 `blob`。 |
|**direction** | n/a | 必須針對輸出繫結設定為 `out`。 例外狀況在[使用方式](#usage)一節中會加以說明。 |
|**name** | n/a | 表示函式程式碼中 Blob 的變數名稱。  設為 `$return` 以參考函式傳回值。|
|**path** |**BlobPath** | Blob 容器的路徑。 |
|**connection** |**連線**| 應用程式設定的名稱包含要用於此繫結的儲存體連接字串。 如果應用程式設定名稱是以「AzureWebJobs」開頭，於此僅能指定名稱的其餘部分。 例如，如果您將 `connection` 設定為「MyStorage」，則函式執行階段會尋找名稱為「AzureWebJobsMyStorage」的應用程式設定。 如果您將 `connection` 保留空白，則函式執行階段會使用應用程式設定中名稱為 `AzureWebJobsStorage` 的預設儲存體連接字串。<br><br>連接字串必須為一般用途的儲存體帳戶，不可為[僅限 Blob 的儲存體帳戶](../storage/common/storage-account-overview.md#types-of-storage-accounts)。|
|n/a | **存取** | 指出您是否將讀取或寫入。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用量

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

在 JavaScript 中，使用 `context.bindings.<name from function.json>` 存取 Blob 資料。

# <a name="python"></a>[Python](#tab/python)

您可以將函式參數宣告為下列類型，以將其寫出至 blob 儲存體：

* 字串為`func.Out(str)`
* 資料流程為`func.Out(func.InputStream)`

如需詳細資訊，請參閱[輸出範例](#example)。

# <a name="java"></a>[Java](#tab/java)

`@BlobOutput`屬性可讓您存取觸發函數的 blob。 如果您使用具有屬性的位元組陣列，請將設定 `dataType` 為 `binary` 。 如需詳細資訊，請參閱[輸出範例](#example)。

---

## <a name="exceptions-and-return-codes"></a>例外狀況和傳回碼

| 繫結 |  參考資料 |
|---|---|
| Blob | [Blob 錯誤碼](/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Bob、資料表、佇列 |  [儲存體錯誤碼](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Bob、資料表、佇列 |  [疑難排解](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>後續步驟

- [在 blob 儲存體資料變更時執行函式](./functions-bindings-storage-blob-trigger.md)
- [函式執行時讀取 blob 儲存體資料](./functions-bindings-storage-blob-input.md)
