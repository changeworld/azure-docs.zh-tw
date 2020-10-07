---
title: Azure Cosmos DB 中的 SQL 查詢入門
description: 瞭解如何使用 SQL 查詢來查詢 Azure Cosmos DB 中的資料。 您可以將範例資料上傳至 Azure Cosmos DB 中的容器，並進行查詢。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.author: tisande
ms.openlocfilehash: f4ee0c0af6939e71f696fc900ec2ab1343ca91df
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91802509"
---
# <a name="getting-started-with-sql-queries"></a>開始使用 SQL 查詢

在 Azure Cosmos DB SQL API 帳戶中，有兩種方式可以讀取資料：

**點讀取** -您可以針對單一 *專案識別碼* 和資料分割索引鍵進行索引鍵/值查閱。 *專案識別碼*和資料分割索引鍵組合是索引鍵，而專案本身是值。 若是 1 KB 檔，點讀取通常是成本 1 [要求單位](request-units.md) ，延遲低於10毫秒。 點讀取會傳回單一專案。

**Sql 查詢** -您可以使用結構化查詢語言 (SQL)  (SQL) 作為 JSON 查詢語言來撰寫查詢，以查詢資料。 查詢一律會產生至少2.3 的要求單位，而且通常會有比點讀取更高且更有變化的延遲。 查詢可能會傳回許多專案。

Azure Cosmos DB 上大部分的大量讀取工作負載都會使用點讀取和 SQL 查詢的組合。 如果您只需要讀取單一專案，則點讀取比查詢更便宜且更快。 點讀取不需要使用查詢引擎來存取資料，而且可以直接讀取資料。 當然，所有工作負載都無法使用點讀取來獨佔讀取資料，因此支援 SQL 作為查詢語言，以及不 [限架構的索引編制](index-overview.md) ，可提供更有彈性的方式來存取您的資料。

以下是如何使用每個 SDK 來執行端點讀取的一些範例：

- [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet&preserve-view=true)
- [Java SDK](https://docs.microsoft.com/java/api/com.azure.cosmos.cosmoscontainer.readitem?view=azure-java-stable&preserve-view=true#com_azure_cosmos_CosmosContainer__T_readItem_java_lang_String_com_azure_cosmos_models_PartitionKey_com_azure_cosmos_models_CosmosItemRequestOptions_java_lang_Class_T__)
- [Node.js SDK](https://docs.microsoft.com/javascript/api/@azure/cosmos/item?view=azure-node-latest&preserve-view=true#read-requestoptions-)
- [Python SDK](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.containerproxy?view=azure-python&preserve-view=true#read-item-item--partition-key--populate-query-metrics-none--post-trigger-include-none----kwargs-)

本檔的其餘部分將說明如何開始在 Azure Cosmos DB 中撰寫 SQL 查詢。 您可以透過 SDK 或 Azure 入口網站執行 SQL 查詢。

## <a name="upload-sample-data"></a>上傳範例資料

在您的 SQL API Cosmos DB 帳戶中，建立名為的容器 `Families` 。 在容器中建立兩個簡單的 JSON 專案。 您可以使用此資料集，在 Azure Cosmos DB 查詢檔中執行大部分的範例查詢。

### <a name="create-json-items"></a>建立 JSON 專案

下列程式碼會建立兩個有關家族的簡單 JSON 專案。 Andersen 和 Wakefield 系列的簡單 JSON 專案包括家長、子女及其寵物、位址和註冊資訊。 第一個專案具有字串、數位、布林值、陣列和嵌套屬性。

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

第二個專案會使用 `givenName` 和， `familyName` 而不是 `firstName` 和 `lastName` 。

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

針對 JSON 資料嘗試一些查詢，以瞭解 Azure Cosmos DB SQL 查詢語言的一些重要層面。

下列查詢會傳回欄位相符的專案 `id` `AndersenFamily` 。 由於它是 `SELECT *` 查詢，因此查詢的輸出是完整的 JSON 專案。 如需 SELECT 語法的詳細資訊，請參閱 [select 語句](sql-query-select.md)。

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

下列查詢會將 JSON 輸出重新格式化為不同的圖形。 此查詢 `Family` 會使用兩個選取的欄位來投影新的 JSON 物件， `Name` 而 `City` 當地址城市與狀態相同時，則為。 "NY，NY" 符合此案例。

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

下列查詢會傳回家族中的所有子系名稱，其符合專案會 `id` `WakefieldFamily` 依城市排序。

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

上述範例顯示 Cosmos DB 查詢語言的幾個層面：  

* 因為 SQL API 適用于 JSON 值，所以它會處理樹狀結構的實體，而不是資料列和資料行。 您可以參考任何任意深度的樹狀節點，例如 `Node1.Node2.Node3…..Nodem` ，類似于 ANSI SQL 中的兩部分參考 `<table>.<column>` 。

* 因為查詢語言適用于無架構資料，所以型別系統必須動態地系結。 相同的運算式可能會對不同的項目產生不同的類型。 查詢的結果是有效的 JSON 值，但不保證是固定的架構。  

* Azure Cosmos DB 只支援嚴謹的 JSON 項目。 類型系統和運算式僅限於處理 JSON 類型。 如需詳細資訊，請參閱 [JSON 規格](https://www.json.org/) \(英文\)。  

* Cosmos 容器是 JSON 專案的無架構集合。 容器專案內和跨容器專案的關聯性會由內含專案（而非主鍵和外鍵關聯）隱含地捕捉。 這項功能對於本文稍後討論的專案內聯接很重要。

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 簡介](introduction.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT 子句](sql-query-select.md)
