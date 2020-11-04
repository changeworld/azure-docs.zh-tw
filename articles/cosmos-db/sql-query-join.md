---
title: Azure Cosmos DB 的 SQL 聯結查詢
description: 瞭解如何聯結 Azure Cosmos DB 中的多個資料表來查詢資料
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: c3fad0c7eed2b42a9ac340a091f17b90e22b000a
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333829"
---
# <a name="joins-in-azure-cosmos-db"></a>Azure Cosmos DB 中的聯結
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

在關係資料庫中，跨資料表的聯結是設計正規化架構的邏輯必然結果。 相反地，SQL API 會使用無架構專案的反正規化資料模型，這是 *自我聯結* 的邏輯對等專案。

內部聯結是參與聯結之集的完整交叉乘積。 N 方聯結的結果為一組 N 元素 Tuple，其中 Tuple 中的每個值都與參與聯結的別名集相關聯，而且可以透過參考其他子句中的別名加以存取。

## <a name="syntax"></a>語法

語言支援語法 `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>` 。 此查詢會傳回一組具有值的元組 `N` 。 每個 Tuple 所擁有的值，都是將所有容器別名在其個別集合上反覆運算所產生的。 

我們來看看下面的 FROM 子句：`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 讓每個來源定義 `input_alias1, input_alias2, …, input_aliasN`。 這個 FROM 子句會傳回一組 N-Tuple (具有 N 個值的 Tuple)。 每個 Tuple 所擁有的值，都是將所有容器別名在其個別集合上反覆運算所產生的。  
  
**範例 1** - 2 個來源  
  
- 讓 `<from_source1>` 為容器範圍並代表集 {A, B, C}。  
  
- 讓 `<from_source2>` 為參照 input_alias1 的文件範圍，並代表以下集：  
  
    {1, 2} 為 `input_alias1 = A,`  
  
    {3} 為 `input_alias1 = B,`  
  
    {4, 5} 為 `input_alias1 = C,`  
  
- FROM 子句 `<from_source1> JOIN <from_source2>` 會產生下列 Tuple：  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**範例 2** - 3 個來源  
  
- 讓 `<from_source1>` 為容器範圍並代表集 {A, B, C}。  
  
- 讓 `<from_source2>` 為參照 `input_alias1` 的文件範圍，並代表以下集：  
  
    {1, 2} 為 `input_alias1 = A,`  
  
    {3} 為 `input_alias1 = B,`  
  
    {4, 5} 為 `input_alias1 = C,`  
  
- 讓 `<from_source3>` 為參照 `input_alias2` 的文件範圍，並代表以下集：  
  
    {100, 200} 為 `input_alias2 = 1,`  
  
    {300} 為 `input_alias2 = 3,`  
  
- FROM 子句 `<from_source1> JOIN <from_source2> JOIN <from_source3>` 會產生下列 Tuple：  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > 缺少 `input_alias1`、`input_alias2` 的其他值 Tuple，其中 `<from_source3>` 並未傳回任何值。  
  
**範例 3** - 3 個來源  
  
- 讓 <from_source1> 為容器範圍並代表集 {A, B, C}。  
  
- 讓 `<from_source1>` 為容器範圍並代表集 {A, B, C}。  
  
- 讓 <from_source2> 為參照 input_alias1 的文件範圍，並代表以下集：  
  
    {1, 2} 為 `input_alias1 = A,`  
  
    {3} 為 `input_alias1 = B,`  
  
    {4, 5} 為 `input_alias1 = C,`  
  
- 讓 `<from_source3>` 將範圍設定為 `input_alias1` 並代表集：  
  
    {100, 200} 為 `input_alias2 = A,`  
  
    {300} 為 `input_alias2 = C,`  
  
- FROM 子句 `<from_source1> JOIN <from_source2> JOIN <from_source3>` 會產生下列 Tuple：  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200),  (C, 4, 300) ,  (C, 5, 300)  
  
  > [!NOTE]
  > 這會在 `<from_source2>` 和 `<from_source3>` 之間產生交叉乘積，因為兩者都只限於相同的 `<from_source1>` 範圍。  這會讓 4 (2x2) Tuple 具備值 A，0 Tuple 具備值 B (1x0) 而 2 (2x1) Tuple 具備值 C。  
  
## <a name="examples"></a>範例

下列範例示範 JOIN 子句的運作方式。 執行這些範例之前，請先上傳範例 [系列資料](sql-query-getting-started.md#upload-sample-data)。 在下列範例中，結果是空的，因為來自來源的每個專案與空集合的交叉乘積是空的：

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

結果如下：

```json
    [{
    }]
```

在下列範例中，聯結是兩個 JSON 物件（專案根目錄和子根目錄）之間的交叉乘積 `id` `children` 。 陣列中的事實不 `children` 會有效，因為它會處理屬於陣列的單一根 `children` 。 結果只會包含兩個結果，因為具有陣列的每個專案的交叉乘積只會產生一個專案。

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

結果為：

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

下列範例示範較傳統的聯結：

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

結果為：

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

JOIN 子句的 FROM 來源是反覆運算器。 因此，上述範例中的流程為：  

1. 展開 `c` 陣列中的每個子項目。
2. 套用具有專案根目錄的交叉乘積， `f` 以及第一個步驟簡維的每個子項目 `c` 。
3. 最後，單獨投影根物件 `f` `id` 屬性。

第一個專案 `AndersenFamily` 只包含一個 `children` 元素，因此結果集只包含單一物件。 第二個專案 `WakefieldFamily` 包含兩個 `children` ，因此交叉乘積會產生兩個物件，每個 `children` 元素各一個。 這兩個項目中的根欄位相同，就像您在交叉乘積中預期地一樣。

JOIN 子句的實際公用程式是在圖形中形成交叉乘積的元組，而這種方式很難投影。 下列範例會篩選元組的組合，讓使用者選擇整體的元組所滿足的條件。

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

結果為：

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

上述範例的下列延伸模組會執行雙重聯結。 您可以用下列虛擬程式碼來查看交叉乘積：

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` 有一個具有一個寵物的子系，因此交叉乘積 \* 從這個系列 (1 1 1) 產生一個資料列 \* 。 `WakefieldFamily` 有兩個子系，其中只有一個是寵物，但該子系有兩個寵物。 此系列的交叉乘積會產生 1 \* 1 \* 2 = 2 個數據列。

在下一個範例中，有一個額外的篩選 `pet` ，它會排除寵物名稱不是所有的元組 `Shadow` 。 您可以從陣列建立元組、篩選元組的任何元素，以及投影元素的任意組合。

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

結果如下：

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a name="next-steps"></a>後續步驟

- [快速入門](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [子查詢](sql-query-subquery.md)