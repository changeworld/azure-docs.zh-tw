---
title: Azure Cosmos DB 中的參數化查詢
description: 瞭解 SQL 參數化查詢如何提供強大的使用者輸入處理和植入，以及防止透過 SQL 插入的資料意外洩漏。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74870814"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Azure Cosmos DB 中的參數化查詢

Cosmos DB 支援具有以熟悉 @ notation 表示之參數的查詢。 參數化 SQL 提供強大的使用者輸入處理和植入，並可防止透過 SQL 插入的資料意外洩漏。

## <a name="examples"></a>範例

例如，您可以撰寫接受 `lastName` 和 `address.state` 做為參數的查詢，並 `lastName` 根據使用者輸入，對的各種值執行 `address.state` 。

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

然後，您可以將此要求以參數化 JSON 查詢的形式傳送給 Cosmos DB，如下所示：

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

下列範例會使用參數化查詢來設定 TOP 引數： 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

參數值可以是任何有效的 JSON：字串、數位、布林值、null，甚至是陣列或嵌套 JSON。 因為 Cosmos DB 是無架構，所以不會針對任何類型驗證參數。


## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [模型文件資料](modeling-data.md)
