---
title: 在 Azure 宇宙 DB 中 LINQ 到 SQL 轉換
description: 瞭解支援的 LINQ 運算子以及如何將 LINQ 查詢映射到 Azure Cosmos DB 中的 SQL 查詢。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d43f95b91df7d0c9c442339de51936200f4688e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441261"
---
# <a name="linq-to-sql-translation"></a>LINQ 至 SQL 轉譯

Azure Cosmos DB 查詢提供程式執行從 LINQ 查詢到 Cosmos DB SQL 查詢的盡最大努力映射。 以下描述假定對 LINQ 的基本熟悉程度。

查詢提供程式類型系統僅支援 JSON 基元類型：數位、布林、字串和 null。

查詢提供程式支援以下標量運算式：

- 常量值，包括查詢評估時基元資料類型的常量值。
  
- 引用物件或陣列元素的屬性/陣列索引運算式。 例如：
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- 算術運算式，包括數值和布林值上的常用算術運算式。 有關完整清單，請參閱[Azure Cosmos DB SQL 規範](https://go.microsoft.com/fwlink/p/?LinkID=510612)。
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- 字串比較運算式，包括將字串值與一些常量字串值進行比較。  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- 物件/陣列創建運算式，返回複合數值型別或匿名型別的物件，或此類物件的陣列。 您可以嵌套這些值。
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>支援的 LINQ 運算子

SQL .NET SDK 中包含的 LINQ 提供程式支援以下運算子：

- **選擇**：投影轉換為 SQL SELECT，包括物件構造。
- **其中**：篩選器轉換為 SQL WHERE，並支援`&&`在`||`和`!`之間轉換 。
- **SelectMany**：可讓陣列回溯到 SQL JOIN 子句。 用於連結或嵌套運算式以篩選陣列元素。
- **訂單乘****和訂單分遞**：使用 ASC 或 DESC 轉換為 ORDER BY。
- 彙總的 **Count**、**Sum**、**Min**、**Max** 與 **Average** 運算子，以及其非同步對應項 **CountAsync**、**SumAsync**、**MinAsync**、**MaxAsync** 與 **AverageAsync**。
- **CompareTo**：轉譯為範圍比較。 通常用於字串，因為它們在 .NET 中不可比。
- **跳過**和**獲取**：轉換為 SQL OFFSET 和 LIMIT，以限制查詢的結果並執行分頁。
- **數學函數**：支援從`Abs``Acos``Asin``Atan``Ceiling``Cos``Exp``Floor``Log``Pow``Round``Sign``Sin``Sqrt``Tan``Truncate`.NET、、、、、、、、、、、、、、、、、、、以及等效 SQL 內建函數進行轉換。 `Log10`
- **字串函數**：支援`Concat`從 .NET、、、、、、、、、、、、、、、、、、、`Contains``Count``EndsWith``IndexOf``Replace``Reverse``StartsWith``SubString``ToLower``ToUpper``TrimEnd`以及`TrimStart`等效 SQL 內建函數進行轉換。
- **陣列函數**：支援從`Concat`.NET`Contains`轉換`Count`和 到等效 SQL 內建函數。
- **地理空間擴展函數**：支援從存根方法`Distance` `IsValid`、`IsValidDetailed`和`Within`轉換到等效 SQL 內建函數。
- **使用者定義的函數擴展函數**：支援從存根方法`UserDefinedFunctionProvider.Invoke`轉換到相應的使用者定義的函數。
- **雜項**：支援`Coalesce`和 條件運算子的轉換。 可以轉換為`Contains`字串 CONTAINS、ARRAY_CONTAINS 或 SQL IN，具體取決於上下文。

## <a name="examples"></a>範例

以下示例說明了某些標準 LINQ 查詢運算子如何轉換為 Cosmos DB 查詢。

### <a name="select-operator"></a>選擇運算子

語法為 `input.Select(x => f(x))`，其中 `f` 是純量運算式。

**選擇運算子，示例 1：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**選擇運算子，示例 2：** 

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**選擇運算子，示例 3：**

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

**其中運算子，示例 1：**

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
  
**其中運算子，示例 2：**

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

您可以組合前面的運算子以形成更強大的查詢。 由於 Cosmos DB 支援嵌套容器，因此可以串聯或嵌套合成。

### <a name="concatenation"></a>串連

語法為 `input(.|.SelectMany())(.Select()|.Where())*`。 串聯查詢可以從可選`SelectMany`查詢開始，後跟多個`Select`或`Where`運算子。

**串聯，示例 1：**

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

**串聯，示例 2：**

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

**串聯，示例 3：**

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

**串聯，示例 4：**

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

`input.SelectMany(x=>x.Q())`語法是`Q` `Select`、`SelectMany`或`Where`運算子的位置。

巢狀查詢將內部查詢應用於外部容器的每個元素。 一個重要功能是內部查詢可以引用外部容器中元素的欄位，如自聯接。

**嵌套，示例 1：**

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

**嵌套，示例 2：**

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

**嵌套，示例 3：**

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
