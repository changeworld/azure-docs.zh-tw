---
title: 開始在 Azure Cosmos DB 中的 SQL 查詢
description: 瞭解如何使用 SQL 查詢查詢來自 Azure Cosmos DB 的資料。 您可以將示例資料上載到 Azure Cosmos DB 中的容器並查詢它。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873330"
---
# <a name="getting-started-with-sql-queries"></a>開始使用 SQL 查詢

Azure Cosmos DB SQL API 帳戶支援使用結構化查詢語言 （SQL） 作為 JSON 查詢語言的查詢項。 Azure Cosmos DB 查詢語言的設計目標是：

* 支援 SQL，這是最熟悉和最流行的查詢語言之一，而不是發明新的查詢語言。 SQL 為對 JSON 項的豐富查詢提供了正式的程式設計模型。  

* 使用 JavaScript 的程式設計模型作為查詢語言的基礎。 JavaScript 的類型系統、運算式評估和函式呼叫是 SQL API 的根源。 這些根為關係投影、跨越 JSON 項的分層導航、自聯接、空間查詢以及完全用 JavaScript 編寫的使用者定義函數 （UdF） 調用等功能提供了自然程式設計模型。

## <a name="upload-sample-data"></a>上傳示例資料

在 SQL API Cosmos DB 帳戶中`Families`，創建一個名為 的容器。 在容器中創建兩個簡單的 JSON 項。 您可以使用此資料集運行 Azure Cosmos DB 查詢文檔中的大多數依例查詢。

### <a name="create-json-items"></a>創建 JSON 專案

以下代碼創建兩個關於族的簡單 JSON 項。 安徒生和韋克菲爾德家庭的簡單JSON專案包括父母、孩子及其寵物、位址和註冊資訊。 第一個專案具有字串、數位、布林、陣列和嵌套屬性。


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

第二個專案使用`givenName``familyName`和`firstName`而不是`lastName`。

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

### <a name="query-the-json-items"></a>查詢 JSON 項

嘗試針對 JSON 資料進行一些查詢，以瞭解 Azure Cosmos DB 的 SQL 查詢語言的一些關鍵方面。

以下查詢返回欄位匹配`id``AndersenFamily`的項。 因為它是查詢`SELECT *`，查詢的輸出是完整的 JSON 項。 有關 SELECT 語法的詳細資訊，請參閱[SELECT 語句](sql-query-select.md)。 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

查詢結果是： 

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

以下查詢將 JSON 輸出重新格式化為不同形狀。 當地址城市與狀態相同時`Family`，`Name``City`查詢將生成具有兩個選定欄位的新 JSON 物件， "紐約，紐約"與本案相符。

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

查詢結果是：

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

以下查詢返回按城市排序的`id`匹配`WakefieldFamily`家庭中所有給定的子名。

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

結果如下：

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>備註

前面的示例顯示了 Cosmos DB 查詢語言的幾個方面：  

* 由於 SQL API 適用于 JSON 值，因此它處理樹形實體而不是行和列。 可以引用任意深度的樹節點，例如`Node1.Node2.Node3…..Nodem`，類似于 ANSI SQL 中的兩`<table>.<column>`部分引用。

* 由於查詢語言適用于無架構資料，因此必須動態繫結類型系統。 相同的運算式可能會對不同的項目產生不同的類型。 查詢的結果是有效的 JSON 值，但不保證為固定架構。  

* Azure Cosmos DB 只支援嚴謹的 JSON 項目。 類型系統和運算式僅限於處理 JSON 類型。 有關詳細資訊，請參閱[JSON 規範](https://www.json.org/)。  

* Cosmos 容器是 JSON 項的無架構集合。 容器項內和容器內的關係通過包含隱式捕獲，而不是通過主鍵和外鍵關係捕獲。 此功能對於本文後面討論的專案內聯接非常重要。

## <a name="next-steps"></a>後續步驟

- [Azure 宇宙 DB 簡介](introduction.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [選擇子句](sql-query-select.md)
