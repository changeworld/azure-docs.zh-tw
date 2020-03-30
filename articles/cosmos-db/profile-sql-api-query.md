---
title: 獲取 SQL 查詢性能&執行指標
description: 瞭解如何檢索 Azure Cosmos DB 請求的 SQL 查詢執行指標和設定檔 SQL 查詢性能。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: 48b9a67de5c870a187ee008bd97265760ca6c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70998373"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>使用 .NET SDK 獲取 SQL 查詢執行指標並分析查詢性能

本文介紹如何在 Azure Cosmos DB 上分析 SQL 查詢性能。 此分析可以使用從 .NET SDK`QueryMetrics`檢索完成，此處對此進行了詳細介紹。 [查詢指標](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx)是一個強型別物件，包含有關後端查詢執行的資訊。 這些指標在["調子查詢性能"](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)一文中詳細記錄。

## <a name="set-the-feedoptions-parameter"></a>設置"饋送選項"參數

[DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx)的所有重載都採用可選的[FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx)參數。 此選項允許調整查詢執行並參數化。 

要收集 Sql 查詢執行指標，必須在[Feed 選項](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx)中將[參數 PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) `true`設置為 。 設置為`PopulateQueryMetrics`true 將使 將`FeedResponse`包含相關的`QueryMetrics`。 

## <a name="get-query-metrics-with-asdocumentquery"></a>使用 AsDocument 查詢獲取查詢指標（）
以下代碼示例演示如何在使用[AsDocumentQuery（）](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx)方法時執行檢索指標：

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>彙總查詢指標

在上一節中，請注意對[ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx)方法有多個調用。 每個調用返回一`FeedResponse`個具有 的字典的物件`QueryMetrics`。查詢的每個延續項之一。 下面的示例演示如何使用 LINQ`QueryMetrics`聚合這些示例：

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>按分區 ID 對查詢指標進行分組

您可以按分區`QueryMetrics`ID 對 。 通過分區 ID 分組，可以查看特定分區是否導致性能問題。 下面的示例演示如何與 LINQ`QueryMetrics`進行分組：

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>文檔查詢上的 LINQ

您還可以使用 以下`FeedResponse``AsDocumentQuery()`方法從 LINQ 查詢獲取 。

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>佔用大量資源的查詢

您可以捕獲每個查詢使用的請求單位，以調查消耗高輸送量的昂貴查詢或查詢。 您可以使用 中的`FeedResponse`["請求Charge"](https://msdn.microsoft.com/library/azure/dn948712.aspx)屬性獲取請求費用。 要瞭解有關如何使用 Azure 門戶和不同的 SDK 獲取請求費用的更多內容，請參閱[查找請求單位費用](find-request-unit-charge.md)一文。

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>獲取查詢執行時間

在計算執行用戶端查詢所需的時間時，請確保只包括調用`ExecuteNextAsync`方法的時間，而不是代碼庫的其他部分。 只需這些調用，您即可計算查詢執行所花的時間，如以下示例所示：

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>掃描查詢（通常速度慢且成本高昂）

掃描查詢是指索引未提供的查詢，因此，在返回結果集之前載入了許多文檔。

下面是掃描查詢的示例：

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

此查詢的篩選器使用系統函數 UPPER，該函數不從索引中提供。 針對大型集合執行此查詢，為第一個延續生成以下查詢指標：

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

請注意查詢指標輸出中的以下值：

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

此查詢載入了 60，951 個文檔，總計 399，998，938 位元組。 載入此多個位元組會導致高成本或請求單位費用。 執行查詢也需要很長時間，這與花費的總金額屬性一起是清楚的：

```
Total Query Execution Time               :        4,500.34 milliseconds
```

這意味著查詢執行時需要 4.5 秒（這只是一個延續）。

要優化此依例查詢，請避免在篩選器中使用 UPPER。 相反，在創建或更新文檔時，`c.description`必須將值插入到所有大寫字元中。 然後，查詢變為： 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

現在可以從索引中提供此查詢。

要瞭解有關調優查詢性能的更多詳細資訊，請參閱[調優查詢性能](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)一文。

## <a name="references"></a><a id="References"></a>引用

- [Azure Cosmos DB SQL 規格](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [Json](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>後續步驟

- [微調查詢效能](sql-api-query-metrics.md)
- [索引編製概觀](index-overview.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
