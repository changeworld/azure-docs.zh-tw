---
title: Azure Functions 系結運算式和模式
description: 瞭解如何根據常見模式建立不同的 Azure Functions 系結運算式。
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 2d0cf18de09932c5d66e269a85919f4d85383c5b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277644"
---
# <a name="azure-functions-binding-expression-patterns"></a>Azure Functions 系結運算式模式

[觸發程式和](./functions-triggers-bindings.md)系結的其中一個最強大功能就是系結*運算式*。 在 function.json** 檔案以及函式參數與程式碼中，您可以使用多個運算式，這些運算式會將各種來源解析為相對應的多個值。

大多數運算式會藉由以大括號裹住來供您識別。 例如，在佇列觸發程序函式中，`{queueTrigger}` 會解析為佇列訊息文字。 如果 Blob 輸出繫結的 `path` 屬性是 `container/{queueTrigger}`，且函式是由佇列訊息 `HelloWorld` 所觸發，則系統會建立名為 `HelloWorld` 的 Blob。

繫結運算式的類型

* [應用程式設定](#binding-expressions---app-settings)
* [觸發程序檔案名稱](#trigger-file-name)
* [觸發程序中繼資料](#trigger-metadata)
* [JSON 承載](#json-payloads)
* [新 GUID](#create-guids)
* [目前的日期和時間](#current-time)

## <a name="binding-expressions---app-settings"></a>繫結運算式 - 應用程式設定

為了遵循最佳做法，祕密和連接字串應使用應用程式設定來管理，而不是使用組態檔。 這會限制對這些祕密的存取，並保護儲存在公用原始檔控制存放庫的檔案，例如 function.json**。

當您想要根據環境來變更設定時，應用程式設定也很有用。 例如，在測試環境中，您可能會想要監視不同佇列或 Blob 儲存體容器。

應用程式設定繫結運算式可藉由不同於其他繫結運算式的方式來識別：它們會以百分比符號裹住，而不是以大括號裹住。 例如，如果 Blob 輸出繫結路徑是 `%Environment%/newblob.txt` 而 `Environment` 應用程式設定值是 `Development`，則系統會在 `Development` 容器中建立 Blob。

在本機執行函式時，應用程式設定值來自 local.settings.json** 檔案。

請注意，觸發程序和繫結的 `connection` 屬性是特殊案例，且會自動將值解析為應用程式設定，不含百分比符號。 

下列範例是使用應用程式設定 `%input-queue-name%` 來定義要觸發之佇列的 Azure 佇列儲存體觸發程序。

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

您可以在類別庫中使用相同的方法：

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>觸發程序檔案名稱

Blob 觸發程序的 `path` 可以是可讓您參考其他繫結和函式程式碼中之觸發 Blob 名稱的模式。 此模式也可以包含篩選條件，該條件會指定哪個 Blob 可以觸發函式引動過程。

例如，在下列 Blob 觸發程序繫結中，`path` 模式是 `sample-images/{filename}`，它會建立名為 `filename` 的繫結運算式：

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

然後可以在輸出繫結中使用運算式 `filename` 以指定要建立之 Blob 的名稱：

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

函式程式碼可以使用 `filename` 作為參數名稱來存取此相同值：

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

類別庫中的所有屬性都可以使用繫結運算式和模式。 在下列範例中，屬性建構函式參數是與上述 function.json** 範例相同的 `path` 值： 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

您也可以為部分檔案名建立運算式。 在下列範例中，只會在符合模式的檔案名上觸發函數：`anyname-anyfile.csv`

```json
{
    "name": "myBlob",
    "type": "blobTrigger",
    "direction": "in",
    "path": "testContainerName/{date}-{filetype}.csv",
    "connection": "OrderStorageConnection"
}
```

如需如何在 Blob 路徑字串中使用運算式和模式的詳細資訊，請參閱[儲存體 Blob 繫結參考](functions-bindings-storage-blob.md)。

## <a name="trigger-metadata"></a>觸發程序中繼資料

除了觸發程序提供的資料承載 (例如觸發函式的佇列訊息內容) 之外，許多觸發程序都提供額外的中繼資料值。 這些值可以在 C# 和 F# 中作為輸入參數使用，或在 JavaScript 中做為 `context.bindings` 物件上的屬性使用。 

例如，Azure 佇列儲存體觸發程序支援下列屬性：

* QueueTrigger - 如果是有效字串，便觸發訊息內容
* DequeueCount
* ExpirationTime
* Id
* InsertionTime
* NextVisibleTime
* PopReceipt

您可以在 *function.json* 檔案屬性中存取這些中繼資料值。 例如，假設您使用佇列觸發程序，且佇列訊息包含您想要讀取的 Blob 名稱。 在 *function.json* 檔案中，您可以使用 Blob `path` 屬性中的 `queueTrigger` 中繼資料屬性，如下列範例所示：

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

在對應的參考文章中，會描述每個觸發程序之中繼資料屬性的詳細資料。 如需範例，請參閱[佇列觸發程序中繼資料](functions-bindings-storage-queue-trigger.md#message-metadata)。 您也可以在入口網站的 [整合]**** 索引標籤中，繫結設定區域之下的 [文件]**** 區段取得文件。  

## <a name="json-payloads"></a>JSON 承載

當觸發程序承載為 JSON 時，您可以在相同函式與函式程式碼內之其他繫結的設定中參考其屬性。

下列範例針對接收 JSON 中 Blob 名稱的 Webhook 函式顯示 function.json** 檔案：`{"BlobName":"HelloWorld.txt"}`。 Blob 輸入繫結會讀取 Blob，HTTP 輸出繫結則會在 HTTP 回應中傳回 Blob 內容。 請注意，Blob 輸入繫結會藉由直接參考 `BlobName` 屬性 (`"path": "strings/{BlobName}"`) 來取得 Blob 名稱

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

為了能夠在 C# 和 F# 中運作，您需要一個類別來定義要還原序列化的欄位，如下列範例所示：

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

JavaScript 會自動執行 JSON 還原序列化。

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

### <a name="dot-notation"></a>點標記法

如果 JSON 承載中的某些屬性是具有屬性的物件，您可以使用點標記法來直接參考這些屬性。 例如，假設您的 JSON 看起來如下：

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

您可直接以 `BlobName.FileName` 的形式參考 `FileName`。 在使用此 JSON 格式時，以下是上述範例中 `path` 屬性的樣貌：

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

在 C# 中，您需要兩個類別：

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

## <a name="create-guids"></a>建立 GUID

`{rand-guid}` 繫結運算式可建立 GUID。 `function.json` 檔案中的下列 Blob 路徑會建立具有如 50710cb5-84b9-4d87-9d83-a03d6976a682.txt** 之名稱的 Blob。

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}.txt"
}
```

## <a name="current-time"></a>目前時間

繫結運算式 `DateTime` 會解析成 `DateTime.UtcNow`。 `function.json` 檔案中的下列 Blob 路徑會建立具有如 2018-02-16T17-59-55Z.txt** 之名稱的 Blob。

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}.txt"
}
```
## <a name="binding-at-runtime"></a>執行階段的繫結

在 C# 和其他 .NET 語言中，您可以使用相對於 *function.json* 和屬性中宣告式繫結的命令式繫結模式。 當繫結參數需要在執行階段而不是設計階段中計算時，命令式繫結非常有用。 若要深入了解，請參閱 [C# 開發人員參考](functions-dotnet-class-library.md#binding-at-runtime)或 [C# 指令碼開發人員參考](functions-reference-csharp.md#binding-at-runtime)。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用 Azure 函數傳回值](./functions-bindings-return-value.md)
