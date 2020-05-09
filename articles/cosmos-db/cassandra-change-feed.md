---
title: 變更 Cassandra 的 Azure Cosmos DB API 中的摘要
description: 瞭解如何在適用于 Cassandra 的 Azure Cosmos DB API 中使用變更摘要，以取得對您的資料所做的變更。
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: 43743f62b08bb00403f5dac88682d06daab757a4
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872553"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>變更 Cassandra 的 Azure Cosmos DB API 中的摘要

您可以透過 Cassandra 查詢語言（CQL）中的查詢述詞，取得 Cassandra 的 Azure Cosmos DB API 中的[變更](change-feed.md)摘要支援。 您可以使用這些述詞條件來查詢變更摘要 API。 應用程式可以使用 CQL 中所需的主鍵（也稱為資料分割索引鍵），來取得對資料表所做的變更。 接著，您可以根據結果採取進一步的動作。 對資料表中的資料列所做的變更會依照其修改時間的順序加以捕獲，而排序次序則是依據分割區索引鍵來保證。

下列範例顯示如何使用 .NET 取得 Cassandra API Keyspace 資料表中所有資料列的變更摘要。 述詞 COSMOS_CHANGEFEED_START_TIME （）直接用於 CQL 內，以從指定的開始時間（在此案例中為目前的日期時間）查詢變更摘要中的專案。 您可以在[這裡](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/)下載適用于 c # 的完整範例，以及[這裡](https://github.com/Azure-Samples/cosmos-changefeed-cassandra-java)的 JAVA。

在每個反復專案中，會使用分頁狀態，在最後一次讀取變更時繼續查詢。 我們可以看到 Keyspace 中資料表的新變更的連續串流。 我們會看到所插入或更新之資料列的變更。 目前不支援在 Cassandra API 中使用變更摘要來監看刪除作業。

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```

# <a name="java"></a>[Java](#tab/java)

```java
        Session cassandraSession = utils.getSession();

        try {
              DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");  
               LocalDateTime now = LocalDateTime.now().minusHours(6).minusMinutes(30);  
               String query="SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME()='" 
                    + dtf.format(now)+ "'";
               
             byte[] token=null; 
             System.out.println(query); 
             while(true)
             {
                 SimpleStatement st=new  SimpleStatement(query);
                 st.setFetchSize(100);
                 if(token!=null)
                     st.setPagingStateUnsafe(token);
                 
                 ResultSet result=cassandraSession.execute(st) ;
                 token=result.getExecutionInfo().getPagingState().toBytes();
                 
                 for(Row row:result)
                 {
                     System.out.println(row.getString("user_name"));
                 }
             }
                    

        } finally {
            utils.close();
            LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL");
        }

```
---

若要依照主要索引鍵來取得單一資料列的變更，您可以在查詢中加入主要索引鍵。 下列範例顯示如何追蹤資料列的變更，其中 "user_id = 1"

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

# <a name="java"></a>[Java](#tab/java)

```java
    String query="SELECT * FROM uprofile.user where user_id=1 and COSMOS_CHANGEFEED_START_TIME()='" 
                    + dtf.format(now)+ "'";
    SimpleStatement st=new  SimpleStatement(query);
```
---
## <a name="current-limitations"></a>目前的限制

搭配 Cassandra API 使用變更摘要時，適用下列限制：

* 目前支援插入和更新。 尚未支援刪除作業。 因應措施是，您可以在要刪除的資料列上加入軟標記。 例如，在名為「已刪除」的資料列中加入欄位，並將它設定為 "true"。
* 上次更新會以核心 SQL API 的形式保存，而實體的元更新則無法使用。


## <a name="error-handling"></a>錯誤處理

在 Cassandra API 中使用變更摘要時，支援下列錯誤碼和訊息：

* **HTTP 錯誤碼 429** -當變更摘要的速率受到限制時，它會傳回空白頁面。

## <a name="next-steps"></a>後續步驟

* [使用 Azure Resource Manager 範本管理 Azure Cosmos DB Cassandra API 資源](manage-cassandra-with-resource-manager.md)
