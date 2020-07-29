---
title: Azure Cosmos DB 中的 SQL 查詢入門
description: 瞭解如何使用 SQL 查詢來查詢 Azure Cosmos DB 中的資料。 您可以在 Azure Cosmos DB 中將範例資料上傳至容器，並加以查詢。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.author: tisande
ms.openlocfilehash: d292b7cfcda73cb4cd6ac2535c7e27fc675e1030
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87308180"
---
# <a name="getting-started-with-sql-queries"></a>開始使用 SQL 查詢

在 Azure Cosmos DB SQL API 帳戶中，有兩種方式可以讀取資料：

**點讀取**-您可以在單一*專案識別碼*和分割區索引鍵上執行索引鍵/值查閱。 *專案識別碼*和資料分割索引鍵組合是索引鍵，而專案本身則是值。 針對 1 KB 檔，點讀取通常是成本 1[要求單位](request-units.md)，延遲低於10毫秒。 點讀取會傳回單一專案。

**SQL 查詢**-您可以使用結構化查詢語言 (SQL) （SQL）當做 JSON 查詢語言來撰寫查詢，以查詢資料。 查詢一律會產生至少2.3 個要求單位的成本，而且通常會有比點讀取更高和更多的變數延遲。 查詢可以傳回許多專案。

Azure Cosmos DB 上大部分的大量讀取工作負載都使用點讀取和 SQL 查詢的組合。 如果您只需要讀取單一專案，則點讀取比查詢便宜且快速。 點讀取不需要使用查詢引擎來存取資料，而且可以直接讀取資料。 當然，並非所有工作負載都可以使用點讀取以獨佔方式讀取資料，因此支援 SQL 做為查詢語言，而不受[架構的索引編制](index-overview.md)則提供更有彈性的方式來存取您的資料。

以下是如何使用每個 SDK 進行點讀取的一些範例：

- [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet)
- [Java SDK](https://docs.microsoft.com/java/api/com.azure.cosmos.cosmoscontainer.readitem?view=azure-java-stable#com_azure_cosmos_CosmosContainer__T_readItem_java_lang_String_com_azure_cosmos_models_PartitionKey_com_azure_cosmos_models_CosmosItemRequestOptions_java_lang_Class_T__)
- [Node.js SDK](https://docs.microsoft.com/javascript/api/@azure/cosmos/item?view=azure-node-latest#read-requestoptions-)
- [Python SDK](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.containerproxy?view=azure-python#read-item-item--partition-key--populate-query-metrics-none--post-trigger-include-none----kwargs-)

本檔的其餘部分說明如何開始在 Azure Cosmos DB 中撰寫 SQL 查詢。 您可以透過 SDK 或 Azure 入口網站執行 SQL 查詢。

## <a name="upload-sample-data"></a>上傳範例資料

在您的 SQL API Cosmos DB 帳戶中，建立名為的容器 `Families` 。 在容器中建立兩個簡單的 JSON 專案。 您可以使用此資料集，在 Azure Cosmos DB 查詢檔中執行大部分的範例查詢。

### <a name="create-json-items"></a>建立 JSON 專案

下列程式碼會建立兩個關於家族的簡單 JSON 專案。 Andersen 和 Wakefield 系列的簡單 JSON 專案包括父系、小孩及其寵物、位址和註冊資訊。 第一個專案具有字串、數位、布林值、陣列和嵌套屬性。

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

第二個專案使用 `givenName` 和， `familyName` 而不是 `firstName` 和 `lastName` 。

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>查詢 JSON 專案

對 JSON 資料嘗試一些查詢，以瞭解 Azure Cosmos DB SQL 查詢語言的一些重要層面。

下列查詢會傳回欄位符合的專案 `id` `AndersenFamily` 。 因為這是 `SELECT *` 查詢，所以查詢的輸出是完整的 JSON 專案。 如需 SELECT 語法的詳細資訊，請參閱[select 語句](sql-query-select.md)。

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

查詢結果如下：

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

下列查詢會將 JSON 輸出重新格式化為不同的圖形。 `Family` `Name` `City` 當地址城市與狀態相同時，查詢會使用兩個選取的欄位來投影新的 JSON 物件。 「NY，NY」符合此案例。

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

查詢結果如下：

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

下列查詢會傳回家族中符合的所有子系名稱 `id` `WakefieldFamily` （依城市排序）。

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

結果為：

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>備註

前述範例顯示 Cosmos DB 查詢語言的幾個層面：  

* 因為 SQL API 適用于 JSON 值，所以它會處理樹狀結構實體，而不是資料列和資料行。 您可以參考任何任意深度的樹狀節點，例如 `Node1.Node2.Node3…..Nodem` ，類似于 ANSI SQL 中的兩部分參考 `<table>.<column>` 。

* 因為查詢語言會使用無架構資料，所以必須以動態方式系結型別系統。 相同的運算式可能會對不同的項目產生不同的類型。 查詢的結果是有效的 JSON 值，但不保證是固定的架構。  

* Azure Cosmos DB 只支援嚴謹的 JSON 項目。 類型系統和運算式僅限於處理 JSON 類型。 如需詳細資訊，請參閱 [JSON 規格](https://www.json.org/) \(英文\)。  

* Cosmos 容器是一種無架構的 JSON 專案集合。 在容器專案內和之間的關聯會由內含專案隱含地捕捉，而不是由主鍵和外鍵關聯所控制。 這項功能對於本文稍後討論的內部專案聯結非常重要。

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 簡介](introduction.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT 子句](sql-query-select.md)
