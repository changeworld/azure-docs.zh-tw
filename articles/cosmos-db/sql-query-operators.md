---
title: Azure Cosmos DB 的 SQL 查詢運算子
description: 深入瞭解 SQL 運算子，例如 Azure Cosmos DB 所支援的相等、比較和邏輯運算子。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: c1409bd7f098c24efbb4196d78c6dffb6048119b
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335429"
---
# <a name="operators-in-azure-cosmos-db"></a>Azure Cosmos DB 中的運算子
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

本文詳細說明 Azure Cosmos DB 支援的各種運算子。

## <a name="equality-and-comparison-operators"></a>相等和比較運算子

下表顯示 SQL API 中任何兩個 JSON 類型之間的相等比較結果。

| **主管** | **定義** | **Null** | **布林值** | **Number** | **String** | **Object** | **陣列** |
|---|---|---|---|---|---|---|---|
| **定義** | 未定義 | 未定義 | 未定義 | 未定義 | 未定義 | 未定義 | 未定義 |
| **Null** | 未定義 | **還行** | 未定義 | 未定義 | 未定義 | 未定義 | 未定義 |
| **布林值** | 未定義 | 未定義 | **還行** | 未定義 | 未定義 | 未定義 | 未定義 |
| **Number** | 未定義 | 未定義 | 未定義 | **還行** | 未定義 | 未定義 | 未定義 |
| **String** | 未定義 | 未定義 | 未定義 | 未定義 | **還行** | 未定義 | 未定義 |
| **Object** | 未定義 | 未定義 | 未定義 | 未定義 | 未定義 | **還行** | 未定義 |
| **陣列** | 未定義 | 未定義 | 未定義 | 未定義 | 未定義 | 未定義 | **還行** |

若為比較運算子（例如 `>` 、 `>=` 、 `!=` 、 `<` 和 `<=` ），則會在類型之間或在兩個物件或陣列之間產生比較 `Undefined` 。  

如果純量運算式的結果為 `Undefined` ，該專案就不會包含在結果中，因為 `Undefined` 不等於 `true` 。

例如，下列查詢會產生數位和字串值之間的比較 `Undefined` 。 因此，篩選不會包含任何結果。

```sql
SELECT *
FROM c
WHERE 7 = 'a'
```

## <a name="logical-and-or-and-not-operators"></a>邏輯 (AND、OR 和 NOT) 運算子

邏輯運算子的運算對象是布林值。 下表顯示這些運算子的邏輯事實資料表：

**OR 運算子**

`true`當其中一個條件為時傳回 `true` 。

|  | **True** | **False** | **定義** |
| --- | --- | --- | --- |
| **True** |True |True |True |
| **False** |True |False |未定義 |
| **定義** |True |未定義 |未定義 |

**AND 運算子**

`true`當兩個運算式都為時傳回 `true` 。

|  | **True** | **False** | **定義** |
| --- | --- | --- | --- |
| **True** |True |False |未定義 |
| **False** |False |False |False |
| **定義** |未定義 |False |未定義 |

**NOT 運算子**

反轉任何布林運算式的值。

|  | **NOT** |
| --- | --- |
| **True** |False |
| **False** |True |
| **定義** |未定義 |

**運算子優先順序**

邏輯運算子 `OR` 、 `AND` 和具有如下 `NOT` 所示的優先順序層級：

| **運算子** | **優先順序** |
| --- | --- |
| **NOT** |1 |
| **AND** |2 |
| **OR** |3 |

## <a name="-operator"></a>* 運算子

特殊運算子 * 會依原樣投射整個專案。 使用時，它必須是唯一投射的欄位。 類似的查詢 `SELECT * FROM Families f` 有效，但無效 `SELECT VALUE * FROM Families f`  `SELECT *, f.id FROM Families f` 。

## <a name="-and--operators"></a>? 和？？ 運算子

您可以使用三元 (？ ) 並聯合 (？？) 運算子來建立條件運算式，如同 c # 和 JavaScript 等程式設計語言。

您可以使用？ 用來即時建立新 JSON 屬性的運算子。 例如，下列查詢會將等級等級分類為 `elementary` 或 `other` ：

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

您也可以將呼叫嵌入至？ 運算子，如下列查詢所示： 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

和其他查詢運算子一樣，？ 如果遺漏參考的屬性或要比較的類型不同，運算子就會排除專案。

使用？？ 運算子，可在查詢半結構化或混合類型的資料時，有效率地檢查項目中的屬性。 例如，下列查詢會傳回 `lastName` （如果有的話）， `surname` 如果 `lastName` 不存在則傳回。

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [關鍵字](sql-query-keywords.md)
- [SELECT 子句](sql-query-select.md)
