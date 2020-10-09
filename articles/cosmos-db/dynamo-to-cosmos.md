---
title: 將您的應用程式從 Amazon DynamoDB 遷移至 Azure Cosmos DB
description: 了解如何將您的 .NET 應用程式從 Amazon 的 DynamoDB 遷移至 Azure Cosmos DB
author: manishmsfte
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/29/2020
ms.author: mansha
ms.openlocfilehash: cfdeda8ac3957da272ab4c47fb93930c826d55aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85261863"
---
# <a name="migrate-your-application-from-amazon-dynamodb-to-azure-cosmos-db"></a>將您的應用程式從 Amazon DynamoDB 遷移至 Azure Cosmos DB

Azure Cosmos DB 是可調整、全域散發的完全受控資料庫。 其可為您的資料提供保證低度延遲的存取。 若要深入了解 Azure Cosmos DB，請參閱[概觀](introduction.md)一文。 本文說明如何使用最少的程式碼變更，將您的 .NET 應用程式從 DynamoDB 遷移至 Azure Cosmos DB。

## <a name="conceptual-differences"></a>概念差異

以下是 Azure Cosmos DB 和 DynamoDB 之間的主要概念差異：

|  DynamoDB | Azure Cosmos DB  |
|---|---|
|不適用|  資料庫 |
|Table      |  集合 |
|  Item |  Document |
|屬性|欄位|
|次要索引|次要索引|
|主索引鍵 – 資料分割索引鍵|資料分割索引鍵|
|主索引鍵 – 排序索引鍵| 非必要 |
|STREAM|ChangeFeed|
|寫入計算單位|要求單位 (彈性，可用於讀取或寫入)|
|讀取計算單位    |要求單位 (彈性，可用於讀取或寫入)|
|全域資料表| 不需要。 您可以在佈建 Azure Cosmos 帳戶時，直接選取區域 (您稍後可以變更區域)|

## <a name="structural-differences"></a>結構差異

相較於 DynamoDB，Azure Cosmos DB 具有較簡單的 JSON 結構。 下列範例顯示差異

**DynamoDB**：

下列 JSON 物件代表 DynamoDB 中的資料格式

```json
{
TableName: "Music",
KeySchema: [
{ 
  AttributeName: "Artist",
  KeyType: "HASH", //Partition key
},
{ 
  AttributeName: "SongTitle",
  KeyType: "RANGE" //Sort key
}
],
AttributeDefinitions: [
{ 
  AttributeName: "Artist",
  AttributeType: "S"
},
{ 
  AttributeName: "SongTitle",
  AttributeType: "S"
}
],
ProvisionedThroughput: {
  ReadCapacityUnits: 1,
  WriteCapacityUnits: 1
 }
}
 ```

**Azure Cosmos DB**：

下列 JSON 物件代表 Azure Cosmos DB 中的資料格式

```json
{
"Artist": "",
"SongTitle": "",
"AlbumTitle": "",
"Year": 9999,
"Price": 0.0,
"Genre": "",
"Tags": ""
}
```

## <a name="migrate-your-data"></a>遷移資料

有各種選項可用來將您的資料遷移至 Azure Cosmos DB。 若要深入了解，請參閱[將內部部署或雲端資料遷移至 Azure Cosmos DB 的選項](cosmosdb-migrationchoices.md)一文。

## <a name="migrate-your-code"></a>遷移程式碼

本文的範圍是將應用程式的程式碼遷移至 Azure Cosmos DB，這是資料庫移轉的重要層面。 為了協助您減少學習曲線，下列章節包含 Amazon DynamoDB 與 Azure Cosmos DB 對等程式碼片段之間的並存程式碼比較。

若要下載原始程式碼，請複製下列存放庫：

```bash
git clone https://github.com/Azure-Samples/DynamoDB-to-CosmosDB
```

### <a name="pre-requisites"></a>必要條件

- .NET Framework 4.7.2
- Visual Studio 2019
- Azure Cosmos DB SQL API 帳戶的存取權
- Amazon DynamoDB 的本機安裝
- Java 8
- 在連接埠 8000 執行 Amazon DynamoDB 的可下載版本 (您可以變更及設定程式碼)

### <a name="set-up-your-code"></a>設定您的程式碼

將下列「NuGet 套件」新增至您的專案：

```bash
Install-Package Microsoft.Azure.Cosmos 
```

### <a name="establish-connection"></a>建立連線

**DynamoDB**：

在 Amazon DynamoDB 中，下列程式碼是用來進行連線：

```csharp
    AmazonDynamoDBConfig addbConfig = new AmazonDynamoDBConfig() ;
        addbConfig.ServiceURL = "endpoint";
        try { aws_dynamodbclient = new AmazonDynamoDBClient( addbConfig ); }
```

**Azure Cosmos DB**：

若要連線 Azure Cosmos DB，請將您的程式碼更新為：

```csharp
client_documentDB = new CosmosClient("your connectionstring from the Azure portal");
```

**將 Azure Cosmos DB 中的連線最佳化**

您可以透過 Azure Cosmos DB，使用下列選項將您的連線最佳化：

* **ConnectionMode** - 使用直接連線模式來連線到 Azure Cosmos DB 服務中的資料節點。 僅使用閘道模式來初始化及快取邏輯位址，並在更新時重新整理。 如需詳細資料，請參閱[連線模式](performance-tips.md#networking)一文。

* **ApplicationRegion** - 這個選項是用來設定慣用的異地複寫區域，以用來與 Azure Cosmos DB 進行互動。 若要深入了解，請參閱[全域散發](distribute-data-globally.md)一文。

* **ConsistencyLevel** - 這個選項是用來覆寫預設的一致性層級。 若要深入了解，請參閱[一致性層級](consistency-levels.md)一文。

* **BulkExecutionMode** - 這個選項是藉由將 AllowBulkExecution 屬性設定為 true，以用來執行大量作業。 若要深入了解，請參閱[大量匯入](tutorial-sql-api-dotnet-bulk-import.md)一文。

   ```csharp
   client_cosmosDB = new CosmosClient(" Your connection string ",new CosmosClientOptions()
   { 
    ConnectionMode=ConnectionMode.Direct,
    ApplicationRegion=Regions.EastUS2,
    ConsistencyLevel=ConsistencyLevel.Session,
    AllowBulkExecution=true  
   });
   ```

### <a name="provision-the-container"></a>佈建容器

**DynamoDB**：

若要將資料儲存到 Amazon DynamoDB 中，您必須先建立資料表。 在此程序中，您會定義結構描述、索引鍵類型和屬性，如下列程式碼所示：

```csharp
// movies_key_schema
public static List<KeySchemaElement> movies_key_schema
  = new List<KeySchemaElement>
{
  new KeySchemaElement
  {
    AttributeName = partition_key_name,
    KeyType = "HASH"
  },
  new KeySchemaElement
  {
    AttributeName = sort_key_name,
    KeyType = "RANGE"
  }
};

// key names for the Movies table
public const string partition_key_name = "year";
public const string sort_key_name      = "title";
  public const int readUnits=1, writeUnits=1; 

    // movie_items_attributes
    public static List<AttributeDefinition> movie_items_attributes
  = new List<AttributeDefinition>
{
  new AttributeDefinition
  {
    AttributeName = partition_key_name,
    AttributeType = "N"
  },
  new AttributeDefinition
  {
    AttributeName = sort_key_name,
    AttributeType = "S"
  }

CreateTableRequest  request;
CreateTableResponse response;

// Build the 'CreateTableRequest' structure for the new table
request = new CreateTableRequest
{
  TableName             = table_name,
  AttributeDefinitions  = table_attributes,
  KeySchema             = table_key_schema,
  // Provisioned-throughput settings are always required,
  // although the local test version of DynamoDB ignores them.
  ProvisionedThroughput = new ProvisionedThroughput( readUnits, writeUnits );
};
```

**Azure Cosmos DB**：

在 Amazon DynamoDB 中，您需要佈建讀取計算單位與寫入計算單位。 而在 Azure Cosmos DB 中，您會將輸送量指定為[要求單位 (RU/秒)](request-units.md)，以動態方式用於任何作業。 資料會組織成資料庫 --> 容器 --> 項目。 您可以指定資料庫層級和/或集合層級的輸送量。

若要建立資料庫：

```csharp
await client_cosmosDB.CreateDatabaseIfNotExistsAsync(movies_table_name);
```

若要建立容器：

```csharp
await cosmosDatabase.CreateContainerIfNotExistsAsync(new ContainerProperties() { PartitionKeyPath = "/" + partitionKey, Id = new_collection_name }, provisionedThroughput);
```

### <a name="load-the-data"></a>載入資料

**DynamoDB**：

下列程式碼顯示如何在 Amazon DynamoDB 中載入資料。 moviesArray 是由 JSON 文件的清單所組成，因此您需要逐一查看 JSON 文件並將其載入 Amazon DynamoDB：

```csharp
int n = moviesArray.Count;
for( int i = 0, j = 99; i < n; i++ )
    {
  try
  {
    string itemJson = moviesArray[i].ToString();
    Document doc = Document.FromJson(itemJson);
    Task putItem = moviesTable.PutItemAsync(doc);
    if( i >= j )
    {
      j++;
      Console.Write( "{0,5:#,##0}, ", j );
      if( j % 1000 == 0 )
        Console.Write( "\n " );
      j += 99;
    }
    await putItem;
```

**Azure Cosmos DB**：

在 Azure Cosmos DB 中，您可以選擇使用 `moviesContainer.CreateItemStreamAsync()` 來進行串流和寫入。 不過，在此範例中，JSON 會還原序列化為 MovieModel 類型，以示範類型轉換功能。 這段程式碼是多執行緒的，其會使用 Azure Cosmos DB 的分散式架構，並加快載入速度：

```csharp
List<Task> concurrentTasks = new List<Task>();
for (int i = 0, j = 99; i < n; i++)
{
  try
  {
      MovieModel doc= JsonConvert.DeserializeObject<MovieModel>(moviesArray[i].ToString());
      doc.Id = Guid.NewGuid().ToString();
      concurrentTasks.Add(moviesContainer.CreateItemAsync(doc,new PartitionKey(doc.Year)));
      {
          j++;
          Console.Write("{0,5:#,##0}, ", j);
          if (j % 1000 == 0)
              Console.Write("\n               ");
          j += 99;
      }
      
  }
  catch (Exception ex)
  {
      Console.WriteLine("\n     ERROR: Could not write the movie record #{0:#,##0}, because:\n       {1}",
                          i, ex.Message);
      operationFailed = true;
      break;
  }
}
await Task.WhenAll(concurrentTasks);
```

### <a name="create-a-document"></a>建立文件

**DynamoDB**：

在 Amazon DynamoDB 中寫入新文件並非安全類型，下列範例會使用 newItem 做為文件類型：

```csharp
Task<Document> writeNew = moviesTable.PutItemAsync(newItem, token);
await writeNew;
```

**Azure Cosmos DB**：

Azure Cosmos DB 會透過資料模型提供您安全類型。 我們使用名為 'MovieModel' 的資料模型：

```csharp
public class MovieModel
{
    [JsonProperty("id")]
    public string Id { get; set; }
    [JsonProperty("title")]
    public string Title{ get; set; }
    [JsonProperty("year")]
    public int Year { get; set; }
    public MovieModel(string title, int year)
    {
        this.Title = title;
        this.Year = year;
    }
    public MovieModel()
    {

    }
    [JsonProperty("info")]
    public   MovieInfo MovieInfo { get; set; }

    internal string PrintInfo()
    {
        if(this.MovieInfo!=null)
        return            string.Format("\nMovie with title:{1}\n Year: {2}, Actors: {3}\n Directors:{4}\n Rating:{5}\n", this.Id, this.Title, this.Year, String.Join(",",this.MovieInfo.Actors), this.MovieInfo, this.MovieInfo.Rating);
        else
            return string.Format("\nMovie with  title:{0}\n Year: {1}\n",  this.Title, this.Year);
    }
}
```

在 Azure Cosmos DB 中，newItem 將為 MovieModel：

```csharp
 MovieModel movieModel = new MovieModel()
            {
                Id = Guid.NewGuid().ToString(),
                Title = "The Big New Movie",
                Year = 2018,
                MovieInfo = new MovieInfo() { Plot = "Nothing happens at all.", Rating = 0 }
            };
    var writeNew= moviesContainer.CreateItemAsync(movieModel, new Microsoft.Azure.Cosmos.PartitionKey(movieModel.Year));
    await writeNew;
```

### <a name="read-a-document"></a>讀取文件

**DynamoDB**：

若要在 Amazon DynamoDB 中閱讀，您需要定義基本項目：

```csharp
// Create Primitives for the HASH and RANGE portions of the primary key
Primitive hash = new Primitive(year.ToString(), true);
Primitive range = new Primitive(title, false);

  Task<Document> readMovie = moviesTable.GetItemAsync(hash, range, token);
  movie_record = await readMovie;
```

**Azure Cosmos DB**：

不過，使用 Azure Cosmos DB，則查詢是自然的 (linq)：

```csharp
IQueryable<MovieModel> movieQuery = moviesContainer.GetItemLinqQueryable<MovieModel>(true)
                        .Where(f => f.Year == year && f.Title == title);
// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
    foreach (MovieModel movie in movieQuery)
    {
      movie_record_cosmosdb = movie;
    }
```

上述範例中的文件集合將會是：

- 安全類型
- 提供自然查詢選項。

### <a name="update-an-item"></a>更新項目

**DynamoDB**：若要更新 Amazon DynamoDB 中的項目：

```csharp
updateResponse = await client.UpdateItemAsync( updateRequest );
````

**Azure Cosmos DB**：

在 Azure Cosmos DB 中，會將更新視為 Upsert 作業，這表示如果文件不存在，則會將其插入：

```csharp
await moviesContainer.UpsertItemAsync<MovieModel>(updatedMovieModel);
```

### <a name="delete-a-document"></a>刪除文件

**DynamoDB**：

若要刪除 Amazon DynamoDB 中的項目，您還是必須執行基本項目：

```csharp
Primitive hash = new Primitive(year.ToString(), true);
      Primitive range = new Primitive(title, false);
      DeleteItemOperationConfig deleteConfig = new DeleteItemOperationConfig( );
      deleteConfig.ConditionalExpression = condition;
      deleteConfig.ReturnValues = ReturnValues.AllOldAttributes;
      
  Task<Document> delItem = table.DeleteItemAsync( hash, range, deleteConfig );
        deletedItem = await delItem;
```

**Azure Cosmos DB**：

在 Azure Cosmos DB 中，我們可以取得文件並以非同步方式加以刪除：

```csharp
var result= ReadingMovieItem_async_List_CosmosDB("select * from c where c.info.rating>7 AND c.year=2018 AND c.title='The Big New Movie'");
while (result.HasMoreResults)
{
  var resultModel = await result.ReadNextAsync();
  foreach (var movie in resultModel.ToList<MovieModel>())
  {
    await moviesContainer.DeleteItemAsync<MovieModel>(movie.Id, new PartitionKey(movie.Year));
  }
  }
```

### <a name="query-documents"></a>查詢文件

**DynamoDB**：

在 Amazon DynamoDB 中，需要 API 函式來查詢資料：

```csharp
QueryOperationConfig config = new QueryOperationConfig( );
  config.Filter = new QueryFilter( );
  config.Filter.AddCondition( "year", QueryOperator.Equal, new DynamoDBEntry[ ] { 1992 } );
  config.Filter.AddCondition( "title", QueryOperator.Between, new DynamoDBEntry[ ] { "B", "Hzz" } );
  config.AttributesToGet = new List<string> { "year", "title", "info" };
  config.Select = SelectValues.SpecificAttributes;
  search = moviesTable.Query( config ); 
```

**Azure Cosmos DB**：

在 Azure Cosmos DB 中，您可以在簡單的 SQL 查詢內進行投影和篩選：

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.Year, c.Title, c.info from c where Year=1998 AND (CONTAINS(Title,'B') OR CONTAINS(Title,'Hzz'))");
```

針對範圍作業，例如 'between'，您必須在 Amazon DynamoDB 中執行掃描：

```csharp
ScanRequest sRequest = new ScanRequest
{
  TableName = "Movies",
  ExpressionAttributeNames = new Dictionary<string, string>
  {
    { "#yr", "year" }
  },
  ExpressionAttributeValues = new Dictionary<string, AttributeValue>
  {
      { ":y_a", new AttributeValue { N = "1960" } },
      { ":y_z", new AttributeValue { N = "1969" } },
  },
  FilterExpression = "#yr between :y_a and :y_z",
  ProjectionExpression = "#yr, title, info.actors[0], info.directors, info.running_time_secs"
};

ClientScanning_async( sRequest ).Wait( );
```

在 Azure Cosmos DB 中，您可以使用 SQL 查詢和單行陳述式：

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.title, c.info.actors[0], c.info.directors,c.info.running_time_secs from c where BETWEEN year 1960 AND 1969");
```

### <a name="delete-a-container"></a>刪除容器

**DynamoDB**：

若要刪除 Amazon DynamoDB 中的資料表，您可以指定：

```csharp
client.DeleteTableAsync( tableName );
```

**Azure Cosmos DB**：

若要刪除 Azure Cosmos DB 中的集合，您可以指定：

```csharp
await moviesContainer.DeleteContainerAsync();
```
然後，您也可以視需要刪除資料庫：

```csharp
await cosmosDatabase.DeleteAsync();
```

如您所見，Azure Cosmos DB 支援自然查詢 (SQL)，作業為非同步且更加輕鬆。 您可以輕鬆地將複雜程式碼遷移至 Azure Cosmos DB，在移轉之後會變得更簡單。

### <a name="next-steps"></a>後續步驟

- 了解[效能最佳化](performance-tips.md)。
- 了解[最佳化讀取和寫入](key-value-store-cost.md)
- 了解 [Cosmos DB 中的監視](monitor-cosmos-db.md)

