---
title: 更改卡珊多拉的 Azure 宇宙 DB API 中的源
description: 瞭解如何在 Cassandra 的 Azure Cosmos DB API 中使用更改源來獲取對資料所做的更改。
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: c2c695608653130b97bf29cc9ce48e2fbb429209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74694619"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>更改卡珊多拉的 Azure 宇宙 DB API 中的源

Cassandra 的 Azure Cosmos DB API 中的[更改源](change-feed.md)支援可通過卡珊多拉查詢語言 （CQL） 中的查詢謂詞獲得。 使用這些謂詞條件，可以查詢更改源 API。 應用程式可以使用 CQL 中所需的主鍵（也稱為分區鍵）獲取對表所做的更改。 然後，您可以根據結果執行進一步操作。 對表中行的更改按其修改時間的順序捕獲，並且每個分區鍵都保證排序次序。

下面的示例演示如何使用 .NET 在 Cassandra API Keyspace 表中的所有行上獲取更改源。 謂詞COSMOS_CHANGEFEED_START_TIME（） 直接用於 CQL 中，從指定的開始時間（在本例中當前日期時間）查詢更改源中的項。 您可以[在此處](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/)下載完整示例。

在每個反覆運算中，查詢在讀取最後一個點時恢復，使用分頁狀態。 我們可以看到 Keyspace 中表的新更改源源不斷。 我們將看到對插入或更新的行的更改。 當前不支援在 Cassandra API 中使用更改源監視刪除操作。 

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

為了按主鍵將更改獲取到單個行，可以在查詢中添加主鍵。 下面的示例演示如何跟蹤行的更改，其中"user_id = 1"

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

## <a name="current-limitations"></a>目前的限制

在將更改源與 Cassandra API 一起使用時，以下限制適用：

* 當前支援插入和更新。 尚不支援刪除操作。 作為解決方法，您可以在要刪除的行上添加軟標記。 例如，在行中添加名為"已刪除"的欄位並將其設置為"true"。
* 上次更新將保留為核心 SQL API 中，並且實體的中間更新不可用。


## <a name="error-handling"></a>錯誤處理

在 Cassandra API 中使用更改源時，支援以下錯誤代碼和消息：

* **HTTP 錯誤代碼 429** - 當更改源速率受限時，它將返回一個空頁。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 資源管理器範本管理 Azure 宇宙 DB Cassandra API 資源](manage-cassandra-with-resource-manager.md)
