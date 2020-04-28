---
title: 取得 SQL 查詢效能 & 執行計量
description: 瞭解如何取出 SQL 查詢執行計量和分析 Azure Cosmos DB 要求的 SQL 查詢效能。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: 48b9a67de5c870a187ee008bd97265760ca6c341
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "70998373"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>使用 .NET SDK 取得 SQL 查詢執行計量和分析查詢效能

本文說明如何在 Azure Cosmos DB 上分析 SQL 查詢效能。 您可以使用`QueryMetrics`從 .net SDK 抓取的來完成這種分析，詳細資訊請參閱這裡。 [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx)是具有後端查詢執行相關資訊的強型別物件。 這些計量在[微調查詢效能](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)一文中有更詳細的記錄。

## <a name="set-the-feedoptions-parameter"></a>設定 FeedOptions 參數

[DocumentClient](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx)的所有多載都會接受選擇性的[FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx)參數。 此選項可讓您微調查詢執行，並將其參數化。 

若要收集 Sql 查詢執行計量，您必須將[FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx)中的參數[為 feedoptions.populatequerymetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics)設定為`true`。 將`PopulateQueryMetrics`設定為 true 會使成為，使`FeedResponse`會包含相關`QueryMetrics`的。 

## <a name="get-query-metrics-with-asdocumentquery"></a>使用 AsDocumentQuery （）取得查詢計量
下列程式碼範例示範如何在使用[AsDocumentQuery （）](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx)方法時，取出計量：

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
## <a name="aggregating-querymetrics"></a>匯總 QueryMetrics

在上一節中，請注意，有多個[ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx)方法呼叫。 每個呼叫都會`FeedResponse`傳回具有字典的`QueryMetrics`物件;一個用於查詢的每個接續。 下列範例顯示如何使用 LINQ 匯總這些`QueryMetrics` ：

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

## <a name="grouping-query-metrics-by-partition-id"></a>依分割區識別碼將查詢計量分組

您可以`QueryMetrics`依據分割區識別碼來分組。 依據分割區識別碼分組可讓您查看特定分割區是否會造成效能問題（相較于其他分割區）。 下列範例顯示如何使用 LINQ 進行`QueryMetrics`分組：

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

## <a name="linq-on-documentquery"></a>DocumentQuery 上的 LINQ

您也可以使用`FeedResponse` `AsDocumentQuery()`方法，從 LINQ 查詢取得：

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

您可以捕捉每個查詢所耗用的要求單位，以調查耗用高輸送量的昂貴查詢或查詢。 您可以使用中`FeedResponse`的[RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx)屬性來取得要求費用。 若要深入瞭解如何使用 Azure 入口網站和不同的 Sdk 取得要求費用，請參閱[尋找要求單位費用](find-request-unit-charge.md)一文。

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

## <a name="get-the-query-execution-time"></a>取得查詢執行時間

計算執行用戶端查詢所需的時間時，請確定您只包含呼叫方法的`ExecuteNextAsync`時間，而不是程式碼基底的其他部分。 只有這些呼叫可協助您計算查詢執行所花的時間，如下列範例所示：

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

## <a name="scan-queries-commonly-slow-and-expensive"></a>掃描查詢（通常緩慢且昂貴）

掃描查詢指的是索引未提供的查詢，因為在傳回結果集之前，會載入許多檔。

以下是掃描查詢的範例：

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

此查詢的篩選器使用的系統函數 UPPER （不是從索引提供）。 針對大型集合執行此查詢時，會針對第一個接續產生下列查詢計量：

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

請注意查詢計量輸出中的下列值：

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

此查詢已載入60951個檔，總計399998938個位元組。 載入此多個位元組會導致高成本或要求單位費用。 執行查詢也需要很長的時間，這會清楚地加上花費的總時間屬性：

```
Total Query Execution Time               :        4,500.34 milliseconds
```

這表示查詢需要4.5 秒的時間來執行（而且這只是一個接續）。

若要優化此範例查詢，請避免在篩選準則中使用上限。 相反地，當建立或更新檔時， `c.description`這些值必須全部以大寫字元插入。 查詢就會變成： 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

現在可以從索引提供此查詢。

若要深入瞭解如何調整查詢效能，請參閱[微調查詢效能](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)一文。

## <a name="references"></a><a id="References"></a>參考

- [Azure Cosmos DB SQL 規格](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>後續步驟

- [微調查詢效能](sql-api-query-metrics.md)
- [編製索引概觀](index-overview.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
