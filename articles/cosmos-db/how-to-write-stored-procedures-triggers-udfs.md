---
title: 在 Azure Cosmos DB 中撰寫預存程式、觸發程式和 Udf
description: 了解如何在 Azure Cosmos DB 中定義預存程序、觸發程序和使用者定義函式
author: timsander1
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: bc7bc677fb44600721b580ac9efc42feea4d98da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91252947"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>如何在 Azure Cosmos DB 中撰寫預存程序、觸發程序和使用者定義函式

Azure Cosmos DB 所提供的語言整合式、交易式 JavaScript 執行，可讓您撰寫**預存程序**、**觸發程序**和**使用者定義函式 (UDF)**。 在 Azure Cosmos DB 中使用 SQL API 時，您可以使用 JavaScript 語言定義預存程序預存程序、觸發程序和 UDF。 您可以使用 JavaScript 撰寫邏輯，並在資料庫引擎內加以執行。 您可以使用 [Azure 入口網站](https://portal.azure.com/)、[Azure Cosmos DB 中的 JavaScript 語言整合式查詢 API](javascript-query-api.md) 和 [Cosmos DB SQL API 用戶端 SDK](sql-api-dotnet-samples.md)，來建立與執行觸發程序、預存程序及 UDF。 

若要呼叫預存程序、觸發程序和使用者定義函式，您必須加以註冊。 如需詳細資訊，請參閱[如何在 Azure Cosmos DB 中使用預存程序、觸發程序、使用者定義函式](how-to-use-stored-procedures-triggers-udfs.md)。

> [!NOTE]
> 對分割容器執行預存程序時，必須在要求選項中提供分割區索引鍵值。 預存程序的範圍一律為分割區索引鍵。 具有不同分割區索引鍵值的項目，將不會對預存程序顯示。 這也適用於觸發程序。
> [!Tip]
> Cosmos 支援使用預存程式、觸發程式和使用者定義函數來部署容器。 如需詳細資訊，請參閱 [使用伺服器端功能建立 Azure Cosmos DB 的容器。](manage-sql-with-resource-manager.md#create-sproc)

## <a name="how-to-write-stored-procedures"></a><a id="stored-procedures"></a>如何撰寫預存程序

預存程序須以 JavaScript 撰寫，這些程序可建立、更新、讀取、查詢和刪除 Azure Cosmos 容器內的項目。 預存程序會按照集合進行註冊，而且可執行於該集合中現有的文件或附件。

以下提供會傳回 "Hello World" 回應的簡單預存程序。

```javascript
var helloWorldStoredProc = {
    id: "helloWorld",
    serverScript: function () {
        var context = getContext();
        var response = context.getResponse();

        response.setBody("Hello, World");
    }
}
```

內容物件可讓您存取可在 Azure Cosmos DB 中執行的所有作業，以及存取要求和回應物件。 在此案例中，您會使用回應物件來設定要傳回給用戶端的回應本文。

撰寫完成後，預存程序必須對集合註冊。 若要深入了解，請參閱[如何在 Azure Cosmos DB 中使用預存程序](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)一文。

### <a name="create-an-item-using-stored-procedure"></a><a id="create-an-item"></a>使用預存程序建立項目

當您使用預存程式建立專案時，該專案會插入 Azure Cosmos 容器中，並傳回新建立之專案的識別碼。 建立項目是非同步作業，並依存於 JavaScript 回呼函式。 此回呼函式有兩個參數 - 一個用於作業失敗時的錯誤物件，一個用於傳回值 (在此案例中為已建立的物件)。 在回呼內，您可以處理例外狀況或擲回錯誤。 如果未提供回呼，而且發生錯誤，則 Azure Cosmos DB 執行階段會擲回錯誤。

預存程序也包含用來設定描述的參數，此為布林值。 當此參數設定為 true 時，若沒有描述，則預存程序將會擲回例外狀況。 否則，預存程序的其餘部分會繼續執行。

下列範例預存程式會採用新的 Azure Cosmos 專案陣列作為輸入，並將其插入 Azure Cosmos 容器中，並傳回插入的專案計數。 在此範例中，我們使用[快速入門 .NET SQL API](create-sql-api-dotnet.md) 中提供的 ToDoList 範例

```javascript
function createToDoItems(items) {
    var collection = getContext().getCollection();
    var collectionLink = collection.getSelfLink();
    var count = 0;

    if (!items) throw new Error("The array is undefined or null.");

    var numItems = items.length;

    if (numItems == 0) {
        getContext().getResponse().setBody(0);
        return;
    }

    tryCreate(items[count], callback);

    function tryCreate(item, callback) {
        var options = { disableAutomaticIdGeneration: false };

        var isAccepted = collection.createDocument(collectionLink, item, options, callback);

        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    function callback(err, item, options) {
        if (err) throw err;
        count++;
        if (count >= numItems) {
            getContext().getResponse().setBody(count);
        } else {
            tryCreate(items[count], callback);
        }
    }
}
```

### <a name="arrays-as-input-parameters-for-stored-procedures"></a>以陣列作為預存程序的輸入參數

使用 Azure 入口網站定義預存程序時，輸入參數一律會以字串形式傳送到預存程序。 即使您以輸入形式傳遞字串陣列，陣列會轉換成字串並傳送至預存程序。 若要解決此問題，您可以在預存程序內定義一個函式，將字串剖析為陣列。 下列程式碼說明如何將字串輸入參數剖析為陣列：

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);

    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

### <a name="transactions-within-stored-procedures"></a><a id="transactions"></a>預存程序內的交易

您可以使用預存程序，對容器內的項目實作交易。 下列範例將在有趣的足球遊戲應用程式內使用交易，透過單一作業讓兩隊互相交易球員。 預存程序嘗試讀取兩個 Azure Cosmos 項目，這兩個項目分別對應於以引數形式傳入的球員識別碼。 如果有找到這兩個球員，則預存程序會藉由交換他們的球隊來更新項目。 如果過程中發生任何錯誤，預存程序會擲回以隱含方式中止交易的 JavaScript 例外狀況。

```javascript
// JavaScript source code
function tradePlayers(playerId1, playerId2) {
    var context = getContext();
    var container = context.getCollection();
    var response = context.getResponse();

    var player1Document, player2Document;

    // query for players
    var filterQuery =
    {
        'query' : 'SELECT * FROM Players p where p.id = @playerId1',
        'parameters' : [{'name':'@playerId1', 'value':playerId1}] 
    };

    var accept = container.queryDocuments(container.getSelfLink(), filterQuery, {},
        function (err, items, responseOptions) {
            if (err) throw new Error("Error" + err.message);

            if (items.length != 1) throw "Unable to find both names";
            player1Item = items[0];

            var filterQuery2 =
            {
                'query' : 'SELECT * FROM Players p where p.id = @playerId2',
                'parameters' : [{'name':'@playerId2', 'value':playerId2}]
            };
            var accept2 = container.queryDocuments(container.getSelfLink(), filterQuery2, {},
                function (err2, items2, responseOptions2) {
                    if (err2) throw new Error("Error" + err2.message);
                    if (items2.length != 1) throw "Unable to find both names";
                    player2Item = items2[0];
                    swapTeams(player1Item, player2Item);
                    return;
                });
            if (!accept2) throw "Unable to read player details, abort ";
        });

    if (!accept) throw "Unable to read player details, abort ";

    // swap the two players’ teams
    function swapTeams(player1, player2) {
        var player2NewTeam = player1.team;
        player1.team = player2.team;
        player2.team = player2NewTeam;

        var accept = container.replaceDocument(player1._self, player1,
            function (err, itemReplaced) {
                if (err) throw "Unable to update player 1, abort ";

                var accept2 = container.replaceDocument(player2._self, player2,
                    function (err2, itemReplaced2) {
                        if (err) throw "Unable to update player 2, abort"
                    });

                if (!accept2) throw "Unable to update player 2, abort";
            });

        if (!accept) throw "Unable to update player 1, abort";
    }
}
```

### <a name="bounded-execution-within-stored-procedures"></a><a id="bounded-execution"></a>預存程序內的界限執行

下列範例說明，將項目大量匯入 Azure Cosmos 容器中的預存程序。 預存程序會檢查 `createDocument` 所傳回的布林值來處理界限執行，然後使用每次叫用預存程序時所插入的項目計數，來追蹤和繼續各批次的進度。

```javascript
function bulkImport(items) {
    var container = getContext().getCollection();
    var containerLink = container.getSelfLink();

    // The count of imported items, also used as current item index.
    var count = 0;

    // Validate input.
    if (!items) throw new Error("The array is undefined or null.");

    var itemsLength = items.length;
    if (itemsLength == 0) {
        getContext().getResponse().setBody(0);
    }

    // Call the create API to create an item.
    tryCreate(items[count], callback);

    // Note that there are 2 exit conditions:
    // 1) The createDocument request was not accepted.
    //    In this case the callback will not be called, we just call setBody and we are done.
    // 2) The callback was called items.length times.
    //    In this case all items were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
    function tryCreate(item, callback) {
        var isAccepted = container.createDocument(containerLink, item, callback);

        // If the request was accepted, callback will be called.
        // Otherwise report current count back to the client,
        // which will call the script again with remaining set of items.
        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    // This is called when container.createDocument is done in order to process the result.
    function callback(err, item, options) {
        if (err) throw err;

        // One more item has been inserted, increment the count.
        count++;

        if (count >= itemsLength) {
            // If we created all items, we are done. Just set the response.
            getContext().getResponse().setBody(count);
        } else {
            // Create next document.
            tryCreate(items[count], callback);
        }
    }
}
```

### <a name="async-await-with-stored-procedures"></a><a id="async-promises"></a>具有預存程式的 Async await

以下是使用 async await 搭配使用 helper 函式之承諾的預存程式範例。 預存程式會查詢專案並加以取代。

```javascript
function async_sample() {
    const ERROR_CODE = {
        NotAccepted: 429
    };

    const asyncHelper = {
        queryDocuments(sqlQuery, options) {
            return new Promise((resolve, reject) => {
                const isAccepted = __.queryDocuments(__.getSelfLink(), sqlQuery, options, (err, feed, options) => {
                    if (err) reject(err);
                    resolve({ feed, options });
                });
                if (!isAccepted) reject(new Error(ERROR_CODE.NotAccepted, "replaceDocument was not accepted."));
            });
        },

        replaceDocument(doc) {
            return new Promise((resolve, reject) => {
                const isAccepted = __.replaceDocument(doc._self, doc, (err, result, options) => {
                    if (err) reject(err);
                    resolve({ result, options });
                });
                if (!isAccepted) reject(new Error(ERROR_CODE.NotAccepted, "replaceDocument was not accepted."));
            });
        }
    };

    async function main() {
        let continuation;
        do {
            let { feed, options } = await asyncHelper.queryDocuments("SELECT * from c", { continuation });

            for (let doc of feed) {
                doc.newProp = 1;
                await asyncHelper.replaceDocument(doc);
            }

            continuation = options.continuation;
        } while (continuation);
    }

    main().catch(err => getContext().abort(err));
}
```

## <a name="how-to-write-triggers"></a><a id="triggers"></a>如何撰寫觸發程序

Azure Cosmos DB 支援預先觸發程序和後續觸發程序。 預先觸發程序會在修改資料庫項目之前執行，而後續觸發程序則在修改資料庫項目執行之後執行。

### <a name="pre-triggers"></a><a id="pre-triggers"></a>預先觸發程式

下列範例說明如何使用預先觸發程序對要建立的 Azure Cosmos 項目驗證屬性。 在此範例中，我們使用[快速入門 .NET SQL API](create-sql-api-dotnet.md) 中提供的 ToDoList 範例，為新增的項目加上時間戳記屬性 (如果還沒有的話)。

```javascript
function validateToDoItemTimestamp() {
    var context = getContext();
    var request = context.getRequest();

    // item to be created in the current operation
    var itemToCreate = request.getBody();

    // validate properties
    if (!("timestamp" in itemToCreate)) {
        var ts = new Date();
        itemToCreate["timestamp"] = ts.getTime();
    }

    // update the item that will be created
    request.setBody(itemToCreate);
}
```

預先觸發程序不能有任何輸入參數。 觸發程序中的要求物件可用來操作與作業相關聯的要求訊息。 在前述範例中，預先觸發程序會在建立 Azure Cosmos 項目時執行，而且要求訊息本文會包含要以 JSON 格式建立的項目。

註冊觸發程序時，您可以指定可與其搭配執行的作業。 此觸發程序應使用 `TriggerOperation` 值 `TriggerOperation.Create` 來建立，這表示不允許在取代作業中使用此觸發程序，如下列程式碼所示。

如需如何註冊及呼叫預先觸發程序的範例，請參閱[預先觸發程序](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers)和[後續觸發程序](how-to-use-stored-procedures-triggers-udfs.md#post-triggers)文章。 

### <a name="post-triggers"></a><a id="post-triggers"></a>後續觸發程式

下列範例說明後續觸發程序。 此觸發程序會查詢中繼資料項目，並使用新建項目的詳細資料加以更新。


```javascript
function updateMetadata() {
var context = getContext();
var container = context.getCollection();
var response = context.getResponse();

// item that was created
var createdItem = response.getBody();

// query for metadata document
var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
var accept = container.queryDocuments(container.getSelfLink(), filterQuery,
    updateMetadataCallback);
if(!accept) throw "Unable to update metadata, abort";

function updateMetadataCallback(err, items, responseOptions) {
    if(err) throw new Error("Error" + err.message);
        if(items.length != 1) throw 'Unable to find metadata document';

        var metadataItem = items[0];

        // update metadata
        metadataItem.createdItems += 1;
        metadataItem.createdNames += " " + createdItem.id;
        var accept = container.replaceDocument(metadataItem._self,
            metadataItem, function(err, itemReplaced) {
                    if(err) throw "Unable to update metadata, abort";
            });
        if(!accept) throw "Unable to update metadata, abort";
        return;
}
```

有一點務必要注意，那就是在 Azure Cosmos DB 中執行的交易式觸發程序。 後續觸發程序也會在基礎項目本身的相同交易中執行。 後續觸發程序執行期間發生的例外狀況會導致整個交易失敗。 系統會復原已認可的所有項目，並傳回例外狀況。

如需如何註冊及呼叫預先觸發程序的範例，請參閱[預先觸發程序](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers)和[後續觸發程序](how-to-use-stored-procedures-triggers-udfs.md#post-triggers)文章。 

## <a name="how-to-write-user-defined-functions"></a><a id="udfs"></a>如何撰寫使用者定義函式

下列範例會建立可為各種收入階層計算所得稅的 UDF。 接著將在查詢內使用這個使用者定義函式。 為了方便此範例的說明，我們假設有名為「收入」的容器，且其屬性顯示如下：

```json
{
   "name": "Satya Nadella",
   "country": "USA",
   "income": 70000
}
```

以下是可為各種收入階層計算所得稅的函式定義：

```javascript
function tax(income) {

        if(income == undefined)
            throw 'no input';

        if (income < 1000)
            return income * 0.1;
        else if (income < 10000)
            return income * 0.2;
        else
            return income * 0.4;
    }
```

如需如何註冊及使用「使用者定義函式」的範例，請參閱[如何在 Azure Cosmos DB 中使用使用者定義函式](how-to-use-stored-procedures-triggers-udfs.md#udfs)一文。

## <a name="logging"></a>記錄 

使用預存程式、觸發程式或使用者定義函數時，您可以使用命令來記錄步驟 `console.log()` 。 當設為 true 時，此命令會專注于要進行調試的字串， `EnableScriptLogging` 如下列範例所示：

```javascript
var response = await client.ExecuteStoredProcedureAsync(
document.SelfLink,
new RequestOptions { EnableScriptLogging = true } );
Console.WriteLine(response.ScriptLog);
```

## <a name="next-steps"></a>後續步驟

了解更多概念，以及如何在 Azure Cosmos DB 中寫入或使用預存程序、觸發程序和使用者定義函式：

* [如何在 Azure Cosmos DB 中註冊和使用預存程序、觸發程序和使用者定義函式](how-to-use-stored-procedures-triggers-udfs.md)

* [如何使用 Javascript 查詢 API 在 Azure Cosmos DB 中撰寫預存程序和觸發程序](how-to-write-javascript-query-api.md)

* [在 Azure Cosmos DB 中使用 Azure Cosmos DB 預存程序、觸發程序及使用者定義函式](stored-procedures-triggers-udfs.md)

* [在 Azure Cosmos DB 中使用 JavaScript Language-integrated Query (LINQ) API](javascript-query-api.md)
