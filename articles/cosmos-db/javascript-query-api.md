---
title: 在 Azure Cosmos DB 預存程式和觸發程式中使用 JavaScript 整合式查詢 API
description: 本文會介紹 JavaScript Language-integrated Query (LINQ) API 的概念，以在 Azure Cosmos DB 中建立預存程序和觸發程序。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 785c430347bc62a00eee80c977f2d6ce440c08db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82982270"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 JavaScript 查詢 API

除了在 Azure Cosmos DB 中使用 SQL API 發出查詢以外， [Cosmos DB 伺服器端 SDK](https://azure.github.io/azure-cosmosdb-js-server/)還提供 JavaScript 介面，可在 Cosmos DB 的預存程式和觸發程式中執行優化的查詢。 不必一定要懂得 SQL 語言才能使用此 JavaScript 介面。 JavaScript 的查詢 API 使用 ECMAScript5 陣列內建和受歡迎的 JavaScript 程式庫如 lodash 所熟悉的語法，將述詞函式依序傳遞至函式呼叫，藉此以程式設計方式建立查詢。 JavaScript 執行階段會剖析查詢，並使用 Azure Cosmos DB 的索引，有效執行該查詢。

## <a name="supported-javascript-functions"></a>支援的 JavaScript 函式

| **Function** | **描述** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|啟動鏈結的呼叫，此呼叫必須以 value() 終止。|
|`filter(predicateFunction [, options] [, callback])`|使用述詞函式來篩選輸入，此函式會傳回 true/false 以在結果集內篩出/篩除輸入文件。 此函式的行為類似於 SQL 中的 WHERE 子句。|
|`flatten([isShallow] [, options] [, callback])`|合併並且壓平每個輸入項目的陣列成為單一陣列。 此函式的行為類似於 LINQ 中的 SelectMany。|
|`map(transformationFunction [, options] [, callback])`|套用投射，其中提供了一個會將每個輸入項目對應至 JavaScript 物件或值的轉換函式。 此函式的行為類似於 SQL 中的 SELECT 子句。|
|`pluck([propertyName] [, options] [, callback])`|此函式是一個對應的捷徑，此對應會從每個輸入項目擷取單一屬性值。|
|`sortBy([predicate] [, options] [, callback])`|使用指定述詞以遞增順序排序輸入文件串流中的文件，產生一組新的文件。 此函式的行為類似於 SQL 中的 ORDER BY 子句。|
|`sortByDescending([predicate] [, options] [, callback])`|使用指定述詞以遞減順序排序輸入文件串流中的文件，產生一組新的文件。 此函式的行為類似於 SQL 中的 ORDER BY x DESC 子句。|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|使用內部陣列執行自我聯結，並將來自兩方的結果，以元組的方式新增到結果投射中。 例如，將個人文件與 person.pets 聯結後，會產生 [person, pet] 元組。 這類似於 .NET LINK 中的 SelectMany。|

當裡面包含述詞和/或選取器函式時，下列 JavaScript 建構會自動取得最佳化，以便直接在 Azure Cosmos DB 索引上執行：

- 簡單運算子 `=` `+` `-` `*` ： `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!``~`
- 常值，包含物件常值：{}
- var、return

下列 JavaScript 建構不會取得 Azure Cosmos DB 索引的最佳化：

- 控制流程 (例如 if、for、while)
- 函式呼叫

如需詳細資料，請參閱 [Cosmos DB 伺服器端 JavaScript 文件](https://azure.github.io/azure-cosmosdb-js-server/) (英文)。

## <a name="sql-to-javascript-cheat-sheet"></a>SQL 到 JavaScript 的速查表

下列表格包含各種不同的 SQL 查詢和相對應的 JavaScript 查詢。 與 SQL 查詢相同，屬性 (例如，item.id) 會區分大小寫。

> [!NOTE]
> 使用 JavaScript 查詢 API 時，`__` (雙底線) 是 `getContext().getCollection()` 的別名。

|**SQL**|**JavaScript 查詢 API**|**描述**|
|---|---|---|
|SELECT *<br>FROM docs| __.map(function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;return doc;<br>});|所有文件中的結果 (使用連續權杖分頁)。|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg,<br>&nbsp;&nbsp;&nbsp;docs.actions <br>FROM docs|__.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;actions:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|投射識別碼、訊息 (別名為 msg)，和所有文件中的動作。|
|SELECT *<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>});|使用 predicate: id = "X998_Y998" 查詢文件。|
|SELECT *<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS(docs.Tags, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return x.Tags && x.Tags.indexOf(123) > -1;<br>});|查詢具有 Tags 屬性的文件，且 Tags 是包含值 123 的陣列。|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|使用 predicate, id = "X998_Y998" 查詢文件，然後投射識別碼和訊息 (別名為 msg)。|
|SELECT VALUE tag<br>FROM docs<br>JOIN tag IN docs.Tags<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.Tags && Array.isArray(doc.Tags);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|篩選具有陣列屬性 Tags 的文件，並且依據 _ts 時間戳記系統屬性來排序結果文件，然後投射及壓平合併 Tags 陣列。|

## <a name="next-steps"></a>後續步驟

進一步了解更多相關概念，以及如何在 Azure Cosmos DB 中寫入和使用預存程序、觸發程序和使用者定義的函式：

- [如何使用 Javascript 查詢 API 寫入預存程序和觸發程序](how-to-write-javascript-query-api.md)
- [使用 Azure Cosmos DB 預存程式、觸發程式和使用者定義函數](stored-procedures-triggers-udfs.md)
- [如何在 Azure Cosmos DB 中註冊和使用預存程序、觸發程序和使用者定義函式](how-to-use-stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB JavaScript 伺服器端 API 參考](https://azure.github.io/azure-cosmosdb-js-server) \(英文\)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
