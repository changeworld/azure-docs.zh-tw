---
title: 使用 Azure Cosmos DB 中的陣列和物件
description: 瞭解 SQL 語法，在 Azure Cosmos DB 中創建陣列和物件。 本文還提供了一些對陣列物件執行操作的示例
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 5b2801b0a71f04803955e9d8bc18a97133019996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246548"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的陣列和物件

Azure Cosmos DB SQL API 的一個關鍵功能是陣列和物件創建。

## <a name="arrays"></a>陣列

可以構造陣列，如以下示例所示：

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

結果如下：

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

您還可以使用[ARRAY 運算式](sql-query-subquery.md#array-expression)從[子查詢](sql-query-subquery.md)的結果構造陣列。 此查詢獲取陣列中子級的所有不同給定名稱。

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a name="iteration"></a><a id="Iteration"></a>迭 代

SQL API 支援通過 JSON 陣列進行反覆運算，並通過 FROM 源中的[IN 關鍵字](sql-query-keywords.md#in)添加新構造。 在下例中︰

```sql
    SELECT *
    FROM Families.children
```

結果如下：

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

下一個查詢`children`在容器中`Families`執行反覆運算。 輸出陣列與前面的查詢不同。 此示例拆分`children`，並將結果拼平到單個陣列中：  

```sql
    SELECT *
    FROM c IN Families.children
```

結果如下：

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

您可以進一步篩選陣列的每個條目，如以下示例所示：

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

結果如下：

```json
    [{
      "givenName": "Lisa"
    }]
```

您還可以聚合陣列反覆運算的結果。 例如，以下查詢計算所有家庭中的子級數：

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

結果如下：

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>後續步驟

- [開始使用](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [聯結](sql-query-join.md)