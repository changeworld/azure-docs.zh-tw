---
title: Azure Cosmos DB 中的 LINQ to SQL 轉譯
description: 瞭解支援的 LINQ 運算子，以及 LINQ 查詢如何對應至 Azure Cosmos DB 中的 SQL 查詢。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: tisande
ms.openlocfilehash: 35f212ea246e03be02fa082ef1b55dcb7cae1575
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94538643"
---
# <a name="linq-to-sql-translation"></a>LINQ 至 SQL 轉譯
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 查詢提供者會執行從 LINQ 查詢到 Cosmos DB SQL 查詢的最佳對應。 如果您想要取得從 LINQ 轉譯的 SQL 查詢，請 `ToString()` 在產生的物件上使用方法 `IQueryable` 。 下列說明假設您已對 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries)有基本的熟悉度。 除了 LINQ 之外，Azure Cosmos DB 也支援可搭配 SQL API 使用的 [Entity Framework Core](/ef/core/providers/cosmos/?tabs=dotnet-core-cli) 。

查詢提供者類型系統僅支援 JSON 基本類型：數值、布林值、字串和 null。

查詢提供者支援下列純量運算式：

- 常數值，包括查詢評估時間的基本資料類型的常數值。
  
- 參考物件或陣列元素之屬性的屬性/陣列索引運算式。 例如：
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- 算術運算式，包括數值和布林值上的一般算術運算式。 如需完整清單，請參閱 [AZURE COSMOS DB SQL 規格](sql-query-system-functions.md)。
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- 字串比較運算式，包括將字串值與某個常數位串值進行比較。  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- 物件/陣列建立運算式，會傳回復合實數值型別或匿名型別的物件，或這類物件的陣列。 您可以嵌套這些值。
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="using-linq"></a>使用 LINQ

您可以使用建立 LINQ 查詢 `GetItemLinqQueryable` 。 此範例顯示 LINQ 查詢產生和非同步執行 `FeedIterator` ：

```csharp
using (FeedIterator<Book> setIterator = container.GetItemLinqQueryable<Book>()
                      .Where(b => b.Title == "War and Peace")
                      .ToFeedIterator<Book>())
 {
     //Asynchronous query execution
     while (setIterator.HasMoreResults)
     {
         foreach(var item in await setIterator.ReadNextAsync()){
         {
             Console.WriteLine(item.cost);
         }
       }
     }
 }
```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>支援的 LINQ 運算子

SQL .NET SDK 隨附的 LINQ 提供者支援下列運算子：

- **Select** ：投射轉譯為 [Select](sql-query-select.md)，包括物件結構。
- **Where** ：篩選器會轉譯為 [where](sql-query-where.md)，並支援將、和轉換成 `&&` `||` `!` SQL 運算子
- **SelectMany** ：允許將陣列回溯至 [JOIN](sql-query-join.md) 子句。 用來鏈狀或嵌套運算式以篩選陣列元素。
- **OrderBy** 和 **ORDERBYDESCENDING** ：使用 ASC 或 DESC 轉譯為 [ORDER BY](sql-query-order-by.md) 。
- **Count** 、 **Sum** 、 **Min** 、 **Max** 和 **Average** 運算子（用於 [匯總](sql-query-aggregates.md)）及其 async 對等 **CountAsync** 、 **SumAsync** 、 **MinAsync** 、 **MaxAsync** 和 **AverageAsync** 。
- **CompareTo** ：轉譯為範圍比較。 常用於字串，因為它們在 .NET 中是無法比較的。
- **Skip** 和 **Take** ：轉譯為 [位移和限制](sql-query-offset-limit.md) ，以限制查詢的結果並執行分頁。
- **數學** 函式：支援從 .net、、、、、、、、、、、、、、、 `Abs` `Acos` `Asin` `Atan` `Ceiling` `Cos` `Exp` `Floor` `Log` `Log10` `Pow` `Round` `Sign` `Sin` `Sqrt` `Tan` 和 `Truncate` 到相等 [內建數學函數](sql-query-mathematical-functions.md)的轉譯。
- **字串函數** ：支援從 .net、、、、、、、、、、、 `Concat` `Contains` `Count` 和轉換 `EndsWith` `IndexOf` `Replace` `Reverse` `StartsWith` `SubString` `ToLower` `ToUpper` `TrimEnd` `TrimStart` 成相等的 [內建字串函數](sql-query-string-functions.md)。
- **陣列** 函式：支援從 .net `Concat` 、 `Contains` 和轉換 `Count` 為相等的 [內建陣列函數](sql-query-array-functions.md)。
- **地理空間延伸** 模組函式：支援從存根方法 `Distance` 、、 `IsValid` `IsValidDetailed` 和 `Within` 到相等 [內建地理空間函數](sql-query-geospatial-query.md)的轉譯。
- **使用者定義函數延伸函式** ：支援從 stub 方法轉譯 `UserDefinedFunctionProvider.Invoke` 為對應的 [使用者定義函數](sql-query-udfs.md)。
- **其他** ：支援 `Coalesce` 和條件 [運算子](sql-query-operators.md)的轉譯。 視內容而定，可以轉譯 `Contains` 為字串 CONTAINS、ARRAY_CONTAINS 或 IN。

## <a name="examples"></a>範例

下列範例說明一些標準 LINQ 查詢運算子如何轉譯成 Azure Cosmos DB 中的查詢。

### <a name="select-operator"></a>Select 運算子

語法為 `input.Select(x => f(x))`，其中 `f` 是純量運算式。 `input`在此案例中，會是 `IQueryable` 物件。

**Select 運算子，範例1：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Select 運算子，範例2：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Select 運算子，範例3：**

- **LINQ Lambda 運算式**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

### <a name="selectmany-operator"></a>SelectMany 運算子

語法為 `input.SelectMany(x => f(x))`，其中 `f` 是傳回容器類型的純量運算式。

- **LINQ Lambda 運算式**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Where 運算子

語法為 `input.Where(x => f(x))`，其中 `f` 是傳回布林值的純量運算式。

**Where 運算子，範例1：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Where 運算子，範例2：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

## <a name="composite-sql-queries"></a>複合 SQL 查詢

您可以撰寫上述運算子來形成更強大的查詢。 由於 Cosmos DB 支援嵌套的容器，因此您可以串連或嵌套組合。

### <a name="concatenation"></a>串連

語法是 `input(.|.SelectMany())(.Select()|.Where())*`。 串連查詢可以從選擇性 `SelectMany` 查詢開始，後面接著多個 `Select` or `Where` 運算子。

**串連，範例1：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**串連，範例2：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**串連，範例3：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**串連，範例4：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

### <a name="nesting"></a>巢狀

語法為 `input.SelectMany(x=>x.Q())` `Q` `Select` 、或運算子的位置 `SelectMany` `Where` 。

巢狀查詢會將內部查詢套用至外部容器的每個元素。 其中一個重要功能是內部查詢可以參考外部容器中元素的欄位，例如自我聯結。

**嵌套，範例1：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**嵌套，範例2：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**嵌套，範例3：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [模型文件資料](modeling-data.md)