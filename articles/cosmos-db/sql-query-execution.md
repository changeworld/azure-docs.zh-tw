---
title: Azure Cosmos DB 中的 SQL 查詢執行
description: 瞭解如何建立 SQL 查詢，並在 Azure Cosmos DB 中執行。 本文說明如何使用 REST API、.Net SDK、JavaScript SDK 和各種其他 Sdk 來建立和執行 SQL 查詢。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74871256"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Azure Cosmos DB SQL 查詢執行

任何能夠發出 HTTP/HTTPS 要求的語言都可以呼叫 Cosmos DB REST API。 Cosmos DB 也提供適用于 .NET、node.js、JavaScript 和 Python 程式設計語言的程式設計程式庫。 REST API 和程式庫全都支援透過 SQL 進行查詢，而 .NET SDK 也支援[LINQ 查詢](sql-query-linq-to-sql.md)。

下列範例示範如何建立查詢，並針對 Cosmos 資料庫帳戶進行提交。

## <a name="rest-api"></a><a id="REST-API"></a>REST API

Cosmos DB 提供透過 HTTP 的開放 RESTful 程式設計模型。 資源模型包含一個資料庫帳戶下的一組資源，而 Azure 訂用帳戶會布建該帳戶。 資料庫帳戶是由一組*資料庫*所組成，其中每個都可以包含多個*容器*，而其中又包含*專案*、udf 和其他資源類型。 每個 Cosmos DB 資源都可透過邏輯和穩定 URI 來定址。 一組資源稱為*摘要。* 

具有這些`GET`資源的基本互動模型是透過 HTTP 動詞命令、 `PUT` `POST`、和， `DELETE`並加上其標準的解釋。 使用`POST`來建立新的資源、執行預存程式，或發出 Cosmos DB 查詢。 查詢一律是唯讀作業，而且沒有任何副作用。

下列範例顯示`POST`針對範例專案的 SQL API 查詢。 查詢在 JSON `name`屬性上有一個簡單的篩選準則。 `x-ms-documentdb-isquery`和 content-type： `application/query+json`標頭表示作業是查詢。 將`mysqlapicosmosdb.documents.azure.com:443`取代為您 Cosmos DB 帳戶的 URI。

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

下一個較複雜的查詢會傳回聯結的多個結果：

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

如果查詢的結果無法放入單一頁面中，REST API 會透過`x-ms-continuation-token`回應標頭傳回接續 token。 用戶端可以在後續的結果中包含標頭，以將結果分頁。 您也可以透過`x-ms-max-item-count`數位標頭來控制每頁的結果數目。

如果查詢具有 COUNT 之類的彙總函式，則查詢頁面可能只會傳回一頁結果的部分匯總值。 用戶端必須對這些結果執行第二層匯總以產生最終結果。 例如，在個別頁面中傳回的計數加總，以傳回總計數。

若要管理查詢的資料一致性原則，請使用`x-ms-consistency-level`所有 REST API 要求中的標頭。 會話一致性也需要在查詢要求`x-ms-session-token`中回應最新的 cookie 標頭。 所查詢容器的編製索引原則也會影響查詢結果的一致性。 使用容器的預設索引編制原則設定時，索引一律是最新的專案內容，而查詢結果會符合針對資料所選擇的一致性。 如需詳細資訊，請參閱 [Azure Cosmos DB 一致性層級] [一致性層級]。

如果容器上設定的編制索引原則無法支援指定的查詢，Azure Cosmos DB 伺服器會傳回400「錯誤的要求」。 此錯誤訊息會針對已從索引中明確排除路徑的查詢傳回。 您可以指定`x-ms-documentdb-query-enable-scan`標頭，以允許查詢在無法使用索引時執行掃描。

您可以藉由將`x-ms-documentdb-populatequerymetrics`標頭設定為`true`，來取得查詢執行的詳細計量。 如需詳細資訊，請參閱[適用於 Azure Cosmos DB 的 SQL 查詢計量](sql-api-query-metrics.md)。

## <a name="c-net-sdk"></a>C # （.NET SDK）

.NET SDK 支援 LINQ 和 SQL 查詢。 下列範例示範如何使用 .NET 執行前述的篩選查詢：

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

下列範例會比較每個專案中的兩個屬性是否相等，並使用匿名投射。

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

下一個範例會顯示透過 LINQ `SelectMany`表示的聯結。

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

.NET 用戶端會自動逐一查看`foreach`區塊中查詢結果的所有頁面，如上述範例所示。 在 [ [REST API](#REST-API) ] 區段中引進的查詢選項也適用于 .net SDK，其使用`FeedOptions` `CreateDocumentQuery`方法`FeedResponse`中的和類別。 您可以使用`MaxItemCount`設定來控制頁面的數目。

`IDocumentQueryable`您也可以使用`IQueryable`物件建立來明確控制分頁，然後藉由讀取`ResponseContinuationToken`值，並將其傳回為`RequestContinuationToken`中`FeedOptions`的。 您可以設定`EnableScanInQuery` ，以便在設定的索引編制原則不支援查詢時啟用掃描。 針對已分割的容器，您`PartitionKey`可以使用針對單一分割區執行查詢，雖然 Azure Cosmos DB 可以自動從查詢文字中將其解壓縮。 您可以使用`EnableCrossPartitionQuery`來對多個分割區執行查詢。

如需更多查詢的 .NET 範例，請參閱 GitHub 中的[Azure Cosmos DB .net 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)。

## <a name="javascript-server-side-api"></a><a id="JavaScript-server-side-API"></a>JavaScript 伺服器端 API

Azure Cosmos DB 提供程式設計模型，可使用預存程式和觸發程式，直接在容器上[執行 JavaScript 型應用程式](stored-procedures-triggers-udfs.md)邏輯。 在容器層級註冊的 JavaScript 邏輯接著可以對指定容器的專案發出資料庫作業，包裝在環境 ACID 交易中。

下列範例示範如何在 JavaScript 伺服器`queryDocuments` API 中使用，以從預存程式和觸發程式內進行查詢：

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

- [Azure Cosmos DB 簡介](introduction.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Azure Cosmos DB 一致性層級](consistency-levels.md)
