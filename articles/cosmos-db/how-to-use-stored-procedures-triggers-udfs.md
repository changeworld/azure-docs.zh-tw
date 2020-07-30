---
title: 在 Azure Cosmos DB Sdk 中註冊和使用預存程式、觸發程式和使用者定義函數
description: 了解如何使用 Azure Cosmos DB SDK 來註冊和呼叫預存程序、觸發程序和使用者定義函式
author: timsander1
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tisande
ms.custom: tracking-python, devx-track-javascript
ms.openlocfilehash: 85d5a7d67474259abf5c5e8b318ae7092d5ed283
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420187"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>如何在 Azure Cosmos DB 中註冊和使用預存程序、觸發程序和使用者定義函式

Azure Cosmos DB 中的 SQL API 支援註冊和叫用以 JavaScript 撰寫的預存程序、觸發程序和使用者定義函式 (UDF)。 您可以使用 SQL API [.net](sql-api-sdk-dotnet.md)、 [.Net Core](sql-api-sdk-dotnet-core.md)、 [JAVA](sql-api-sdk-java.md)、 [JavaScript](sql-api-sdk-node.md)、 [Node.js](sql-api-sdk-node.md)或[Python](sql-api-sdk-python.md) sdk 來註冊及叫用預存程式。 定義一或多個預存程式、觸發程式和使用者定義函數之後，您就可以使用資料總管在[Azure 入口網站](https://portal.azure.com/)中載入和查看它們。

## <a name="how-to-run-stored-procedures"></a><a id="stored-procedures"></a>如何執行預存程序

預存程序須以 JavaScript 撰寫。 這些程序可建立、更新、讀取、查詢和刪除 Azure Cosmos 容器內的項目。 如需如何在 Azure Cosmos DB 中撰寫預存程序的詳細資訊，請參閱[如何在 Azure Cosmos DB 中撰寫預存程序](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures)一文。

下列範例說明如何使用 Azure Cosmos DB SDK 來註冊和呼叫預存程序。 請參閱[建立文件](how-to-write-stored-procedures-triggers-udfs.md#create-an-item)作為來源，因為此預存程序儲存為 `spCreateToDoItem.js`。

> [!NOTE]
> 對分割容器執行預存程序時，必須在要求選項中提供分割區索引鍵值。 預存程序的範圍一律為分割區索引鍵。 具有不同分割區索引鍵值的項目，將不會對預存程序顯示。 這也適用於觸發程序。

### <a name="stored-procedures---net-sdk-v2"></a>預存程式-.NET SDK V2

下列範例顯示如何使用 .NET SDK V2 來註冊預存程式：

```csharp
string storedProcedureId = "spCreateToDoItems";
StoredProcedure newStoredProcedure = new StoredProcedure
   {
       Id = storedProcedureId,
       Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
   };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var response = await client.CreateStoredProcedureAsync(containerUri, newStoredProcedure);
StoredProcedure createdStoredProcedure = response.Resource;
```

下列程式碼說明如何使用 .NET SDK V2 呼叫預存程式：

```csharp
dynamic[] newItems = new dynamic[]
{
    new {
        category = "Personal",
        name = "Groceries",
        description = "Pick up strawberries",
        isComplete = false
    },
    new {
        category = "Personal",
        name = "Doctor",
        description = "Make appointment for check up",
        isComplete = false
    }
};

Uri uri = UriFactory.CreateStoredProcedureUri("myDatabase", "myContainer", "spCreateToDoItem");
RequestOptions options = new RequestOptions { PartitionKey = new PartitionKey("Personal") };
var result = await client.ExecuteStoredProcedureAsync<string>(uri, options, new[] { newItems });
```

### <a name="stored-procedures---net-sdk-v3"></a>預存程式-.NET SDK V3

下列範例顯示如何使用 .NET SDK V3 來註冊預存程式：

```csharp
string storedProcedureId = "spCreateToDoItems";
StoredProcedureResponse storedProcedureResponse = await client.GetContainer("myDatabase", "myContainer").Scripts.CreateStoredProcedureAsync(new StoredProcedureProperties
{
    Id = storedProcedureId,
    Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
});
```

下列程式碼說明如何使用 .NET SDK V3 呼叫預存程式：

```csharp
dynamic[] newItems = new dynamic[]
{
    new {
        category = "Personal",
        name = "Groceries",
        description = "Pick up strawberries",
        isComplete = false
    },
    new {
        category = "Personal",
        name = "Doctor",
        description = "Make appointment for check up",
        isComplete = false
    }
};

var result = await client.GetContainer("database", "container").Scripts.ExecuteStoredProcedureAsync<string>("spCreateToDoItem", new PartitionKey("Personal"), new[] { newItems });
```

### <a name="stored-procedures---java-sdk"></a>預存程序 - Java SDK

下列範例說明如何使用 Java SDK 註冊預存程序：

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
StoredProcedure newStoredProcedure = new StoredProcedure(
    "{" +
        "  'id':'spCreateToDoItems'," +
        "  'body':" + new String(Files.readAllBytes(Paths.get("..\\js\\spCreateToDoItems.js"))) +
    "}");
//toBlocking() blocks the thread until the operation is complete and is used only for demo.  
StoredProcedure createdStoredProcedure = asyncClient.createStoredProcedure(containerLink, newStoredProcedure, null)
    .toBlocking().single().getResource();
```

下列程式碼說明如何使用 Java SDK 呼叫預存程序：

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String sprocLink = String.format("%s/sprocs/%s", containerLink, "spCreateToDoItems");
final CountDownLatch successfulCompletionLatch = new CountDownLatch(1);

List<ToDoItem> ToDoItems = new ArrayList<ToDoItem>();

class ToDoItem {
    public String category;
    public String name;
    public String description;
    public boolean isComplete;
}

ToDoItem newItem = new ToDoItem();
newItem.category = "Personal";
newItem.name = "Groceries";
newItem.description = "Pick up strawberries";
newItem.isComplete = false;

ToDoItems.add(newItem)

newItem.category = "Personal";
newItem.name = "Doctor";
newItem.description = "Make appointment for check up";
newItem.isComplete = false;

ToDoItems.add(newItem)

RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("Personal"));

Object[] storedProcedureArgs = new Object[] { ToDoItems };
asyncClient.executeStoredProcedure(sprocLink, requestOptions, storedProcedureArgs)
    .subscribe(storedProcedureResponse -> {
        String storedProcResultAsString = storedProcedureResponse.getResponseAsString();
        successfulCompletionLatch.countDown();
        System.out.println(storedProcedureResponse.getActivityId());
    }, error -> {
        successfulCompletionLatch.countDown();
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>預存程序 - JavaScript SDK

下列範例說明如何使用 JavaScript SDK 註冊預存程序

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItems";
await container.scripts.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

下列程式碼說明如何使用 JavaScript SDK 呼叫預存程序：

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItems";
const {body: result} = await container.scripts.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>預存程序 - Python SDK

下列範例說明如何使用 Python SDK 註冊預存程序

```python
with open('../js/spCreateToDoItems.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
sproc_definition = {
    'id': 'spCreateToDoItems',
    'serverScript': file_contents,
}
sproc = client.CreateStoredProcedure(container_link, sproc_definition)
```

下列程式碼說明如何使用 Python SDK 呼叫預存程序

```python
sproc_link = 'dbs/myDatabase/colls/myContainer/sprocs/spCreateToDoItems'
new_item = [{
    'category':'Personal',
    'name':'Groceries',
    'description':'Pick up strawberries',
    'isComplete': False
}]
client.ExecuteStoredProcedure(sproc_link, new_item, {'partitionKey': 'Personal'}
```

## <a name="how-to-run-pre-triggers"></a><a id="pre-triggers"></a>如何執行預先觸發程序

下列範例說明如何使用 Azure Cosmos DB SDK 來註冊和呼叫預先觸發程序。 請參閱[預先觸發程序範例](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers)作為來源，因為此預先觸發程序儲存為 `trgPreValidateToDoItemTimestamp.js`。

預先觸發程序在執行時，會藉由指定 `PreTriggerInclude` 並在清單物件中傳入觸發程序的名稱，來傳入 RequestOptions 物件中。

> [!NOTE]
> 即使以清單的形式傳入觸發程序的名稱，每個作業仍然只能執行一個觸發程序。

### <a name="pre-triggers---net-sdk-v2"></a>預先觸發程式-.NET SDK V2

下列程式碼說明如何使用 .NET SDK V2 註冊預先觸發程式：

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

下列程式碼說明如何使用 .NET SDK V2 呼叫預先觸發程式：

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---net-sdk-v3"></a>預先觸發程式-.NET SDK V3

下列程式碼說明如何使用 .NET SDK V3 註冊預先觸發程式：

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPreValidateToDoItemTimestamp",
    Body = File.ReadAllText("@..\js\trgPreValidateToDoItemTimestamp.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
});
```

下列程式碼說明如何使用 .NET SDK V3 呼叫預先觸發程式：

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PreTriggers = new List<string> { "trgPreValidateToDoItemTimestamp" } });
```

### <a name="pre-triggers---java-sdk"></a>預先觸發程序 - Java SDK

下列程式碼說明如何使用 Java SDK 註冊預先觸發程序：

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Pre);
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

下列程式碼說明如何使用 Java SDK 呼叫預先觸發程序：

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
    Document item = new Document("{ "
            + "\"category\": \"Personal\", "
            + "\"name\": \"Groceries\", "
            + "\"description\": \"Pick up strawberries\", "
            + "\"isComplete\": false, "
            + "}"
            );
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPreTriggerInclude(Arrays.asList("trgPreValidateToDoItemTimestamp"));
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="pre-triggers---javascript-sdk"></a>預先觸發程序 - JavaScript SDK

下列程式碼說明如何使用 JavaScript SDK 註冊預先觸發程序：

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "pre"
});
```

下列程式碼說明如何使用 JavaScript SDK 呼叫預先觸發程序：

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.items.create({
    category: "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
}, {preTriggerInclude: [triggerId]});
```

### <a name="pre-triggers---python-sdk"></a>預先觸發程序 - Python SDK

下列程式碼說明如何使用 Python SDK 註冊預先觸發程序：

```python
with open('../js/trgPreValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPreValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Pre,
    'triggerOperation': documents.TriggerOperation.Create
}
trigger = client.CreateTrigger(container_link, trigger_definition)
```

下列程式碼說明如何使用 Python SDK 呼叫預先觸發程序：

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
client.CreateItem(container_link, item, {
                  'preTriggerInclude': 'trgPreValidateToDoItemTimestamp'})
```

## <a name="how-to-run-post-triggers"></a><a id="post-triggers"></a>如何執行後續觸發程序

下列範例說明如何使用 Azure Cosmos DB SDK 來註冊後續觸發程序。 請參閱[後續觸發程序範例](how-to-write-stored-procedures-triggers-udfs.md#post-triggers)作為來源，因為此後續觸發程序儲存為 `trgPostUpdateMetadata.js`。

### <a name="post-triggers---net-sdk-v2"></a>後續觸發程式-.NET SDK V2

下列程式碼示範如何使用 .NET SDK V2 註冊後置觸發程式：

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

下列程式碼說明如何使用 .NET SDK V2 呼叫後續觸發程式：

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

RequestOptions options = new RequestOptions { PostTriggerInclude = new List<string> { "trgPostUpdateMetadata" } };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---net-sdk-v3"></a>後續觸發程式-.NET SDK V3

下列程式碼示範如何使用 .NET SDK V3 註冊後置觸發程式：

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPostUpdateMetadata",
    Body = File.ReadAllText(@"..\js\trgPostUpdateMetadata.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
});
```

下列程式碼說明如何使用 .NET SDK V3 呼叫後續觸發程式：

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PostTriggers = new List<string> { "trgPostUpdateMetadata" } });
```

### <a name="post-triggers---java-sdk"></a>後續觸發程序 - Java SDK

下列程式碼說明如何使用 Java SDK 註冊後續觸發程序：

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)))));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Post);
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

下列程式碼說明如何使用 Java SDK 呼叫後續觸發程序：

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Document item = new Document(String.format("{ "
    + "\"name\": \"artist_profile_1023\", "
    + "\"artist\": \"The Band\", "
    + "\"albums\": [\"Hellujah\", \"Rotators\", \"Spinning Top\"]"
    + "}"
));
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPostTriggerInclude(Arrays.asList("trgPostUpdateMetadata"));
//toBlocking() blocks the thread until the operation is complete, and is used only for demo.
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="post-triggers---javascript-sdk"></a>後續觸發程序 - JavaScript SDK

下列程式碼說明如何使用 JavaScript SDK 註冊後續觸發程序：

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "post"
});
```

下列程式碼說明如何使用 JavaScript SDK 呼叫後續觸發程序：

```javascript
const item = {
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.items.create(item, {postTriggerInclude: [triggerId]});
```

### <a name="post-triggers---python-sdk"></a>後續觸發程序 - Python SDK

下列程式碼說明如何使用 Python SDK 註冊後續觸發程序：

```python
with open('../js/trgPostUpdateMetadata.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPostUpdateMetadata',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Post,
    'triggerOperation': documents.TriggerOperation.Create
}
trigger = client.CreateTrigger(container_link, trigger_definition)
```

下列程式碼說明如何使用 Python SDK 呼叫後續觸發程序：

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'name': 'artist_profile_1023', 'artist': 'The Band',
        'albums': ['Hellujah', 'Rotators', 'Spinning Top']}
client.CreateItem(container_link, item, {
                  'postTriggerInclude': 'trgPostUpdateMetadata'})
```

## <a name="how-to-work-with-user-defined-functions"></a><a id="udfs"></a>如何使用使用者定義函式

下列範例說明如何使用 Azure Cosmos DB SDK 來註冊使用者定義函式。 請參閱[使用者定義函式範例](how-to-write-stored-procedures-triggers-udfs.md#udfs)作為來源，因為此後續觸發程序儲存為 `udfTax.js`。

### <a name="user-defined-functions---net-sdk-v2"></a>使用者定義函數-.NET SDK V2

下列程式碼說明如何使用 .NET SDK V2 註冊使用者定義函式：

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js")
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

下列程式碼說明如何使用 .NET SDK V2 呼叫使用者定義函數：

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---net-sdk-v3"></a>使用者定義函數-.NET SDK V3

下列程式碼說明如何使用 .NET SDK V3 註冊使用者定義函式：

```csharp
await client.GetContainer("database", "container").Scripts.CreateUserDefinedFunctionAsync(new UserDefinedFunctionProperties
{
    Id = "Tax",
    Body = File.ReadAllText(@"..\js\Tax.js")
});
```

下列程式碼說明如何使用 .NET SDK V3 呼叫使用者定義函數：

```csharp
var iterator = client.GetContainer("database", "container").GetItemQueryIterator<dynamic>("SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000");
while (iterator.HasMoreResults)
{
    var results = await iterator.ReadNextAsync();
    foreach (var result in results)
    {
        //iterate over results
    }
}
```

### <a name="user-defined-functions---java-sdk"></a>使用者定義函式 - Java SDK

下列程式碼說明如何使用 Java SDK 註冊使用者定義函式：

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

下列程式碼說明如何使用 Java SDK 呼叫使用者定義函式：

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000", new FeedOptions());
final CountDownLatch completionLatch = new CountDownLatch(1);
queryObservable.subscribe(
        queryResultPage -> {
            System.out.println("Got a page of query result with " +
                    queryResultPage.getResults().size());
        },
        // terminal error signal
        e -> {
            e.printStackTrace();
            completionLatch.countDown();
        },

        // terminal completion signal
        () -> {
            completionLatch.countDown();
        });
completionLatch.await();
```

### <a name="user-defined-functions---javascript-sdk"></a>使用者定義函式 - JavaScript SDK

下列程式碼說明如何使用 JavaScript SDK 註冊使用者定義函式：

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

下列程式碼說明如何使用 JavaScript SDK 呼叫使用者定義函式：

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>使用者定義函式 - Python SDK

下列程式碼說明如何使用 Python SDK 註冊使用者定義函式：

```python
with open('../js/udfTax.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
udf_definition = {
    'id': 'Tax',
    'serverScript': file_contents,
}
udf = client.CreateUserDefinedFunction(container_link, udf_definition)
```

下列程式碼說明如何使用 Python SDK 呼叫使用者定義函式：

```python
container_link = 'dbs/myDatabase/colls/myContainer'
results = list(client.QueryItems(
    container_link, 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>後續步驟

了解更多概念，以及如何在 Azure Cosmos DB 中寫入或使用預存程序、觸發程序和使用者定義函式：

- [在 Azure Cosmos DB 中使用 Azure Cosmos DB 預存程序、觸發程序及使用者定義函式](stored-procedures-triggers-udfs.md)
- [在 Azure Cosmos DB 中使用 JavaScript Language-integrated Query (LINQ) API](javascript-query-api.md)
- [如何在 Azure Cosmos DB 中撰寫預存程序、觸發程序和使用者定義函式](how-to-write-stored-procedures-triggers-udfs.md)
- [如何使用 Javascript 查詢 API 在 Azure Cosmos DB 中撰寫預存程序和觸發程序](how-to-write-javascript-query-api.md)
