---
title: Azure Cosmos DB 中的 LINQ to SQL 轉譯
description: 瞭解支援的 LINQ 運算子，以及如何將 LINQ 查詢對應至 Azure Cosmos DB 中的 SQL 查詢。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 3f8753518e1d54ddba4fc15a5a030308d0c112a1
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86042487"
---
# <a name="linq-to-sql-translation"></a>LINQ 至 SQL 轉譯

Azure Cosmos DB 查詢提供者會執行從 LINQ 查詢到 Cosmos DB SQL 查詢的最佳工作對應。 如果您想要取得已轉譯為 LINQ 的 SQL 查詢，請 `ToString()` 在產生的物件上使用方法 `IQueryable` 。 下列描述假設對 LINQ 有基本的熟悉度。

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
  
- 算術運算式，包括數值和布林值的一般算術運算式。 如需完整清單，請參閱[AZURE COSMOS DB SQL 規格](https://go.microsoft.com/fwlink/p/?LinkID=510612)。
  
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

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>支援的 LINQ 運算子

包含在 SQL .NET SDK 中的 LINQ 提供者支援下列運算子：

- **Select**：投影會轉譯為 SQL Select，包括物件結構。
- **Where**：篩選器會轉譯為 SQL，其中、和支援將、和之間 `&&` `||` 的轉譯 `!` 為 sql 運算子
- **SelectMany**：可讓陣列回溯到 SQL JOIN 子句。 使用來連鎖或嵌套運算式，以篩選陣列元素。
- **OrderBy**和**OrderByDescending**：轉譯為 ORDER BY 加上 ASC 或 DESC。
- 彙總的 **Count**、**Sum**、**Min**、**Max** 與 **Average** 運算子，以及其非同步對應項 **CountAsync**、**SumAsync**、**MinAsync**、**MaxAsync** 與 **AverageAsync**。
- **CompareTo**：轉譯為範圍比較。 通常用於字串，因為它們在 .NET 中無法比較。
- **Skip**和**Take**：轉譯為 SQL 位移和限制，以限制查詢的結果並執行分頁。
- **Math 函數**：支援從 .net、、、、、、、、、、、、、、、 `Abs` `Acos` 和轉換 `Asin` `Atan` `Ceiling` `Cos` `Exp` `Floor` 為對 `Log` `Log10` `Pow` `Round` `Sign` `Sin` `Sqrt` `Tan` `Truncate` 等的 SQL 內建函數。
- **字串函數**：支援從 .net、、、、、、、、、、、 `Concat` `Contains` 和轉換 `Count` `EndsWith` 為對 `IndexOf` `Replace` `Reverse` `StartsWith` `SubString` `ToLower` `ToUpper` `TrimEnd` `TrimStart` 等的 SQL 內建函數。
- **陣列函數**：支援從 .net `Concat` 、 `Contains` 和轉換 `Count` 為對等的 SQL 內建函數。
- **地理空間擴充功能**：支援從存根方法 `Distance` 、 `IsValid` 、 `IsValidDetailed` 和轉換 `Within` 為對等的 SQL 內建函數。
- **使用者定義函數擴充函數**：支援從 stub 方法 `UserDefinedFunctionProvider.Invoke` 到對應的使用者定義函數的轉譯。
- **其他**：支援 `Coalesce` 和條件運算子的轉譯。 視內容而定，可以轉譯 `Contains` 成中的字串 CONTAINS、ARRAY_CONTAINS 或 SQL。

## <a name="examples"></a>範例

下列範例說明一些標準 LINQ 查詢運算子如何轉譯為 Cosmos DB 查詢。

### <a name="select-operator"></a>選取運算子

語法為 `input.Select(x => f(x))`，其中 `f` 是純量運算式。

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
