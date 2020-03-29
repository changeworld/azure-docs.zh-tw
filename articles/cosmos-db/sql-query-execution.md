---
title: Azure 宇宙 DB 中的 SQL 查詢執行
description: 瞭解如何創建 SQL 查詢並在 Azure Cosmos DB 中執行它。 本文介紹如何使用 REST API、.Net SDK、JavaScript SDK 和各種其他 SDK 創建和執行 SQL 查詢。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871256"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Azure 宇宙資料庫 SQL 查詢執行

任何能夠發出 HTTP/HTTPS 請求的語言都可以調用 Cosmos DB REST API。 Cosmos DB 還為 .NET、Node.js、JavaScript 和 Python 程式設計語言提供程式設計庫。 REST API 和庫都支援通過 SQL 進行查詢，.NET SDK 也支援[LINQ 查詢](sql-query-linq-to-sql.md)。

以下示例演示如何創建查詢並針對 Cosmos 資料庫帳戶提交查詢。

## <a name="rest-api"></a><a id="REST-API"></a>REST API

Cosmos DB 提供透過 HTTP 的開放 RESTful 程式設計模型。 資源模型由資料庫帳戶下的一組資源組成，Azure 訂閱提供這些資源。 資料庫帳戶由一組資料庫組成，每個*資料庫*可以包含多個容器，這些*容器*又包含*項*、UF 和其他資源類型。 每個 Cosmos DB 資源都可以使用邏輯和穩定的 URI 進行定址。 一組資源稱為*源*。 

與這些資源`GET`的基本交互模型是通過 HTTP 謂詞 、`PUT``POST`和`DELETE`，及其標準解釋。 用於`POST`創建新資源、執行預存程序或發出 Cosmos DB 查詢。 查詢一律是唯讀作業，而且沒有任何副作用。

以下示例顯示`POST`針對示例項的 SQL API 查詢。 查詢在 JSON`name`屬性上具有一個簡單的篩選器。 和`x-ms-documentdb-isquery`內容類型：`application/query+json`標頭表示操作是查詢。 替換為`mysqlapicosmosdb.documents.azure.com:443`Cosmos DB 帳戶的 URI。

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

結果如下：

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

下一個更複雜的查詢返回聯接中的多個結果：

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": []
    }
```

結果如下： 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

如果查詢的結果不能放入單個頁面，REST API 將通過`x-ms-continuation-token`回應標頭返回延續權杖。 用戶端可以通過在後續結果中包括標頭來分頁結果。 您還可以通過`x-ms-max-item-count`數位標頭控制每頁的結果數。

如果查詢具有 COUNT 等彙總函式，則查詢頁可能僅返回一個結果頁上的部分聚合值。 用戶端必須對這些結果執行第二級聚合才能生成最終結果。 例如，對單個頁中返回的計數的總和以返回總計數。

要管理查詢的資料一致性策略，請使用標頭，`x-ms-consistency-level`就像所有 REST API 請求一樣。 會話一致性還需要在查詢請求中`x-ms-session-token`呼應最新的 Cookie 標頭。 所查詢容器的編製索引原則也會影響查詢結果的一致性。 對於容器的預設索引策略設置，索引始終與項內容保持最新，查詢結果與為數據選擇的一致性匹配。 有關詳細資訊，請參閱 [Azure 宇宙 DB 一致性級別][一致性級別]。

如果容器上的配置索引策略不支援指定的查詢，Azure Cosmos DB 伺服器將返回 400 個"錯誤請求"。 對於從索引中明確排除路徑的查詢，此錯誤訊息返回。 您可以指定`x-ms-documentdb-query-enable-scan`標頭以允許查詢在索引無法接通執行掃描。

通過將`x-ms-documentdb-populatequerymetrics`標頭設置為`true`，可以獲取有關查詢執行的詳細指標。 如需詳細資訊，請參閱[適用於 Azure Cosmos DB 的 SQL 查詢計量](sql-api-query-metrics.md)。

## <a name="c-net-sdk"></a>C# （.NET SDK）

.NET SDK 支援 LINQ 和 SQL 查詢。 下面的示例演示如何使用 .NET 執行前面的篩選器查詢：

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

下面的示例比較每個專案中的相等性兩個屬性，並使用匿名投影。

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

下一個示例顯示聯接，通過 LINQ`SelectMany`表示。

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

.NET 用戶端自動遍通`foreach`所有查詢頁，結果在塊中，如前面的示例所示。 [REST API](#REST-API)部分中引入的查詢選項在 .NET SDK 中也使用`FeedOptions``CreateDocumentQuery`方法`FeedResponse`中的 和 類。 您可以使用`MaxItemCount`該設置控制頁數。

還可以通過使用`IDocumentQueryable``IQueryable`物件創建，然後`ResponseContinuationToken`讀取值並將其傳遞回`RequestContinuationToken``FeedOptions`頁，從而顯式控制分頁。 您可以設置為`EnableScanInQuery`在配置的索引策略不支援查詢時啟用掃描。 對於分區容器，可以使用`PartitionKey`對單個分區執行查詢，儘管 Azure Cosmos DB 可以從查詢文本中自動提取此查詢。 可用於`EnableCrossPartitionQuery`對多個分區執行查詢。

有關包含查詢的更多 .NET 示例，請參閱 GitHub 中的[Azure Cosmos DB .NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)。

## <a name="javascript-server-side-api"></a><a id="JavaScript-server-side-API"></a>JavaScript 伺服器端 API

Azure Cosmos DB 提供了一個程式設計模型，用於使用預存程序和觸發器直接在容器上[執行基於 JavaScript 的應用程式](stored-procedures-triggers-udfs.md)邏輯。 然後在容器級別註冊的 JavaScript 邏輯可以對包在環境 ACID 事務中的給定容器的項發出資料庫操作。

下面的示例演示如何`queryDocuments`在 JavaScript 伺服器 API 中使用從內部預存程序和觸發器進行查詢：

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a name="next-steps"></a>後續步驟

- [Azure 宇宙 DB 簡介](introduction.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Azure 宇宙 DB 一致性級別](consistency-levels.md)
