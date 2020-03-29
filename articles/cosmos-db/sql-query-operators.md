---
title: Azure 宇宙資料庫的 SQL 查詢運算子
description: 瞭解 Azure Cosmos DB 支援的 SQL 運算子，如相等性、比較性和邏輯運算子。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: tisande
ms.openlocfilehash: 8ef41edb687a5df39243880c897d12e83c008ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063565"
---
# <a name="operators-in-azure-cosmos-db"></a>Azure 宇宙 DB 中的運算子

本文詳細介紹了 Azure Cosmos DB 支援的各種運算子。

## <a name="equality-and-comparison-operators"></a>相等和比較運算子

下表顯示 SQL API 中任何兩個 JSON 類型之間的相等比較結果。

| **操作** | **未定義** | **空** | **布林** | **數量** | **字串** | **物件** | **Array** |
|---|---|---|---|---|---|---|---|
| **未定義** | 未定義 | 未定義 | 未定義 | 未定義 | 未定義 | 未定義 | 未定義 |
| **空** | 未定義 | **還行** | 未定義 | 未定義 | 未定義 | 未定義 | 未定義 |
| **布林** | 未定義 | 未定義 | **還行** | 未定義 | 未定義 | 未定義 | 未定義 |
| **數量** | 未定義 | 未定義 | 未定義 | **還行** | 未定義 | 未定義 | 未定義 |
| **字串** | 未定義 | 未定義 | 未定義 | 未定義 | **還行** | 未定義 | 未定義 |
| **物件** | 未定義 | 未定義 | 未定義 | 未定義 | 未定義 | **還行** | 未定義 |
| **Array** | 未定義 | 未定義 | 未定義 | 未定義 | 未定義 | 未定義 | **還行** |

對於比較運算子，如`>` `>=` `!=`、、`<`和`<=`等，跨類型或兩個物件或陣列之間生成`Undefined`比較。  

如果標量運算式的結果為`Undefined`，則項不包括在結果中，因為`Undefined`不等於`true`。

## <a name="logical-and-or-and-not-operators"></a>邏輯 (AND、OR 和 NOT) 運算子

邏輯運算子的運算對象是布林值。 下表顯示了這些運算子的邏輯真理表：

**OR 運算子**

當`true`任一條件為`true`時返回 。

|  | **True** | **False** | **未定義** |
| --- | --- | --- | --- |
| **True** |True |True |True |
| **False** |True |False |未定義 |
| **未定義** |True |未定義 |未定義 |

**AND 運算子**

當`true`兩個運算式都是`true`時返回。

|  | **True** | **False** | **未定義** |
| --- | --- | --- | --- |
| **True** |True |False |未定義 |
| **False** |False |False |False |
| **未定義** |未定義 |False |未定義 |

**NOT 運算子**

反轉任何布林運算式的值。

|  | **不** |
| --- | --- |
| **True** |False |
| **False** |True |
| **未定義** |未定義 |

**運算子優先順序**

邏輯運算子`OR`、`AND`和`NOT`具有以下優先順序顯示：

| **運算元** | **優先順序** |
| --- | --- |
| **不** |1 |
| **和** |2 |
| **或** |3 |

## <a name="-operator"></a>* 運算子

特殊運算子 = 將整個專案以現在的身份進行預測。 使用時，它必須是唯一投射的欄位。 這樣的`SELECT * FROM Families f`查詢是有效的，但`SELECT VALUE * FROM Families f``SELECT *, f.id FROM Families f`無效。

## <a name="-and--operators"></a>? 和？？ 運算子

您可以使用三元 （？） 和"合併"運算子來構建條件運算式，如在程式設計語言（如 C# 和 JavaScript） 中。

您可以使用 ？ 運算子動態構造新的 JSON 屬性。 例如，以下查詢將等級級別分類為`elementary`或`other`：

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

您還可以嵌套對 ？ 的調用。 運算子，如以下查詢中： 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

與其他查詢運算子一樣，？ 如果缺少引用的屬性或要比較的類型不同，則運算子將排除項。

使用？？ 操作員在對半結構化或混合類型資料查詢時，可有效地檢查項中的屬性。 例如，如果存在或`lastName``surname``lastName`不存在，則返回以下查詢。

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [關鍵字](sql-query-keywords.md)
- [選擇子句](sql-query-select.md)
