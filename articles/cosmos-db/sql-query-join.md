---
title: Azure 宇宙資料庫的 SQL JOIN 查詢
description: 瞭解如何在 Azure Cosmos DB 中聯接多個表以查詢資料
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 38e80f1597a08b8db7cbfa852d1bcf38ac768b1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871137"
---
# <a name="joins-in-azure-cosmos-db"></a>在 Azure 宇宙 DB 中聯接

在關係資料庫中，跨表聯接是設計正常化架構的邏輯推論。 相反，SQL API 使用無架構項的非正常化資料模型，這是*自聯接*的邏輯等效項。

內部聯結是參與聯結之集的完整交叉乘積。 N 方聯結的結果為一組 N 元素 Tuple，其中 Tuple 中的每個值都與參與聯結的別名集相關聯，而且可以透過參考其他子句中的別名加以存取。

## <a name="syntax"></a>語法

語言支援語法`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`。 此查詢返回一組具有`N`值的元組。 每個 Tuple 所擁有的值，都是將所有容器別名在其個別集合上反覆運算所產生的。 

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

下列範例示範 JOIN 子句的運作方式。 在運行這些示例之前，請上載示例[族資料](sql-query-getting-started.md#upload-sample-data)。 在下面的示例中，結果為空，因為源和空集的每個項的交叉積為空：

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

在下面的示例中，聯接是兩個 JSON 物件（項根`id`和子根）`children`之間的交叉產品。 陣列在`children`聯接中無效，因為它處理的是陣列的`children`單個根。 結果僅包含兩個結果，因為具有陣列的每個項的交叉積僅生成一個項。

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

結果如下：

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

結果如下：

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

JOIN 子句的 FROM 源是反覆運算器。 因此，前面的示例中的流是：  

1. 展開陣列中的每個子`c`元素。
2. 應用具有項`f`根的交叉產品，其中每個子項目`c`的第一步都展平。
3. 最後，單獨投影根物件`f``id`屬性。

第一個項`AndersenFamily`僅包含一個`children`元素，因此結果集僅包含一個物件。 第二個項`WakefieldFamily`包含兩`children`個 ，因此交叉積生成兩個物件，每個元素`children`一個。 這兩個項目中的根欄位相同，就像您在交叉乘積中預期地一樣。

JOIN 子句的真正效用是從交叉產品中形成 tup，形狀本來很難投影。 下面的示例篩選了元組的組合，允許使用者選擇由總組滿足的條件。

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

結果如下：

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

上述示例的以下擴展執行雙聯接。 您可以將交叉產品視為以下偽代碼：

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

`AndersenFamily`有一個孩子有一個寵物，所以交叉產品產生一行（1\*1\*1）從這個家庭。 `WakefieldFamily`有兩個孩子，只有一個有寵物，但那孩子有兩個寵物。 此系列的交叉產品產生 1\*1\*2 = 2 行。

在下一個示例中，在 上`pet`有一個額外的篩選器，它排除了寵物名稱不是`Shadow`的所有元。 可以從陣列構建元組，對元組的任何元素進行篩選，並投影元素的任意組合。

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

- [開始使用](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [子查詢](sql-query-subquery.md)