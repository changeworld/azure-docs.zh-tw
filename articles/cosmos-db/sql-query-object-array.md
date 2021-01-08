---
title: 使用 Azure Cosmos DB 中的陣列和物件
description: 瞭解在 Azure Cosmos DB 中建立陣列和物件的 SQL 語法。 本文也提供一些範例來執行陣列物件的作業
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: tisande
ms.openlocfilehash: f959e4e230c1d9f89ad5141713b6a17a8cbb17a2
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018899"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的陣列和物件
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB SQL API 的主要功能是建立陣列和物件。

## <a name="arrays"></a>陣列

您可以建立陣列，如下列範例所示：

```sql
SELECT [f.address.city, f.address.state] AS CityState
FROM Families f
```

結果為：

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

您也可以使用 [陣列運算式](sql-query-subquery.md#array-expression) ，從 [子查詢的](sql-query-subquery.md) 結果中建立陣列。 此查詢會取得陣列中所有子系的不同指定名稱。

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

結果為：

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

## <a name="iteration"></a><a id="Iteration"></a>迭 代

SQL API 支援使用 FROM 來源的 [in 關鍵字](sql-query-keywords.md#in) ，逐一查看 JSON 陣列。 在下例中︰

```sql
SELECT *
FROM Families.children
```

結果為：

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

下一個查詢會 `children` 在容器中執行反復專案 `Families` 。 輸出陣列與上述查詢不同。 此範例會分割 `children` 並將結果壓平合併成單一陣列：  

```sql
SELECT *
FROM c IN Families.children
```

結果為：

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

結果為：

```json
[{
  "givenName": "Lisa"
}]
```

您也可以對陣列反覆運算的結果進行匯總。 例如，下列查詢會計算所有系列中的子係數目：

```sql
SELECT COUNT(1) AS Count
FROM child IN Families.children
```

結果為：

```json
[
  {
    "Count": 3
  }
]
```

> [!NOTE]
> 使用 IN 關鍵字進行反復專案時，您無法篩選或投射陣列以外的任何屬性。 相反地，您應該 [使用聯結](sql-query-join.md)。

## <a name="next-steps"></a>後續步驟

- [快速入門](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [聯結](sql-query-join.md)
