---
title: Azure Cosmos DB 中的 LINQ to SQL 轉譯
description: 瞭解支援的 LINQ 運算子，以及如何將 LINQ 查詢對應至 Azure Cosmos DB 中的 SQL 查詢。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 7/29/2020
ms.author: tisande
ms.openlocfilehash: f2a7570b7ebed26a06e1bd075c2904bc29061c21
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498849"
---
# <a name="linq-to-sql-translation"></a>LINQ 至 SQL 轉譯

Azure Cosmos DB 查詢提供者會執行從 LINQ 查詢到 Cosmos DB SQL 查詢的最佳工作對應。 如果您想要取得從 LINQ 轉譯的 SQL 查詢，請 `ToString()` 在產生的物件上使用方法 `IQueryable` 。 下列描述假設對[LINQ](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries)有基本的熟悉度。

查詢提供者類型系統僅支援 JSON 基本類型：數值、布林值、字串和 null。

查詢提供者支援下列純量運算式：

- 常數值，包括在查詢評估時間的基本資料類型常數值。
  
- 屬性/陣列索引運算式，參考物件或陣列元素的屬性。 例如：
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- 算術運算式，包括數值和布林值的一般算術運算式。 如需完整清單，請參閱[AZURE COSMOS DB SQL 規格](sql-query-system-functions.md)。
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- 字串比較運算式，其中包括比較字串值與某個常數位串值。  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- 物件/陣列建立運算式，其會傳回復合實數值型別或匿名型別的物件，或這類物件的陣列。 您可以將這些值加以嵌套。
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="using-linq"></a>使用 LINQ

您可以使用建立 LINQ 查詢 `GetItemLinqQueryable` 。 這個範例會示範如何產生 LINQ 查詢和非同步執行 `FeedIterator` ：

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

包含在 SQL .NET SDK 中的 LINQ 提供者支援下列運算子：

- **Select**：投影會轉譯為[Select](sql-query-select.md)，包括物件結構。
- **Where**：篩選器會轉譯為[where](sql-query-where.md)，並支援將 `&&` 、和之間的轉譯 `||` `!` 為 SQL 運算子
- **SelectMany**：允許將陣列回溯至[聯結](sql-query-join.md)子句。 使用來連鎖或嵌套運算式，以篩選陣列元素。
- **OrderBy**和**OrderByDescending**：轉譯為[ORDER BY](sql-query-order-by.md)加上 ASC 或 DESC。
- 用於[匯總](sql-query-aggregates.md)的**Count**、 **Sum**、 **Min**、 **Max**和**Average**運算子，以及其非同步對等**CountAsync**、 **SumAsync**、 **MinAsync**、 **MaxAsync**和**AverageAsync**。
- **CompareTo**：轉譯為範圍比較。 通常用於字串，因為它們在 .NET 中無法比較。
- **Skip**和**Take**：轉譯為[位移和限制](sql-query-offset-limit.md)，以限制查詢的結果並執行分頁。
- **Math 函數**：支援從 .net、、、、、、、、、、、、、、、 `Abs` `Acos` 和轉換 `Asin` `Atan` `Ceiling` `Cos` `Exp` `Floor` 為對 `Log` `Log10` `Pow` `Round` `Sign` `Sin` `Sqrt` `Tan` `Truncate` 等的[內建數學函數](sql-query-mathematical-functions.md)。
- **字串函數**：支援從 .net、、、、、、、、、、、 `Concat` `Contains` 和轉換 `Count` `EndsWith` 為對 `IndexOf` `Replace` `Reverse` `StartsWith` `SubString` `ToLower` `ToUpper` `TrimEnd` `TrimStart` 等的[內建字串函數](sql-query-string-functions.md)。
- **陣列函數**：支援從 .net `Concat` 、 `Contains` 和轉換為對 `Count` 等的[內建陣列函數](sql-query-array-functions.md)。
- **地理空間擴充功能**：支援從存根方法 `Distance` 、 `IsValid` 、 `IsValidDetailed` 和轉換 `Within` 為對等的[內建地理空間函數](sql-query-geospatial-query.md)。
- **使用者定義函數擴充函數**：支援從 stub 方法 `UserDefinedFunctionProvider.Invoke` 到對應的[使用者定義函數](sql-query-udfs.md)的轉譯。
- **其他**：支援 `Coalesce` 和條件[運算子](sql-query-operators.md)的轉譯。 視內容而定，可以轉譯 `Contains` 為字串 CONTAINS、ARRAY_CONTAINS 或中的。

## <a name="examples"></a>範例

下列範例說明部分標準 LINQ 查詢運算子如何轉譯為 Azure Cosmos DB 中的查詢。

### <a name="select-operator"></a>選取運算子

語法為 `input.Select(x => f(x))`，其中 `f` 是純量運算式。 `input`在此情況下，會是 `IQueryable` 物件。

**選取運算子，範例1：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**選取運算子，範例2：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**選取運算子，範例3：**

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

您可以撰寫前面的運算子來形成更強大的查詢。 由於 Cosmos DB 支援嵌套的容器，因此您可以串連或嵌套組合。

### <a name="concatenation"></a>串連

語法為 `input(.|.SelectMany())(.Select()|.Where())*`。 串連查詢的開頭可以是選擇性 `SelectMany` 查詢，後面接著多個 `Select` or `Where` 運算子。

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

語法為 `input.SelectMany(x=>x.Q())` ，其中 `Q` 是 `Select` 、 `SelectMany` 或 `Where` 運算子。

嵌套的查詢會將內部查詢套用至外部容器的每個元素。 其中一個重要的功能是，內部查詢可以參考外部容器中元素的欄位，例如自我聯結。

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
