---
title: 在 Azure Cosmos DB 中使用陣列和物件
description: 瞭解在 Azure Cosmos DB 中建立陣列和物件的 SQL 語法。 本文也提供一些在陣列物件上執行作業的範例
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 5b2801b0a71f04803955e9d8bc18a97133019996
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246548"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中使用陣列和物件

Azure Cosmos DB SQL API 的主要功能是陣列和物件建立。

## <a name="arrays"></a>陣列

您可以建立陣列，如下列範例所示：

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

您也可以使用[陣列運算式](sql-query-subquery.md#array-expression)，從[子查詢的](sql-query-subquery.md)結果來建立陣列。 此查詢會取得陣列中子系的所有不同指定名稱。

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>反覆運算

SQL API 支援反覆運算 JSON 陣列，並透過從來源的[in 關鍵字](sql-query-keywords.md#in)新增結構。 在下例中︰

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

下一個查詢會在 `Families` 容器中的 `children` 上執行反復專案。 輸出陣列與先前的查詢不同。 這個範例會分割 `children`，並將結果簡維成單一陣列：  

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

您可以進一步篩選陣列的每個個別專案，如下列範例所示：

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

您也可以匯總陣列反復專案的結果。 例如，下列查詢會計算所有系列中的子係數目：

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

- [快速入門](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [聯結](sql-query-join.md)