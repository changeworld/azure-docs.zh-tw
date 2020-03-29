---
title: Azure 宇宙 DB 中的參數化查詢
description: 瞭解 SQL 參數化查詢如何提供可靠的使用者輸入處理和轉義，以及防止通過 SQL 注入意外暴露資料。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870814"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Azure 宇宙 DB 中的參數化查詢

Cosmos DB 支援具有由熟悉的 @ 標記法表示的參數的查詢。 參數化 SQL 提供使用者輸入的可靠處理和轉義，並防止通過 SQL 注入意外暴露資料。

## <a name="examples"></a>範例

例如，可以編寫一個`lastName`以 作為`address.state`參數的查詢，並`lastName``address.state`基於使用者輸入對 各種值執行查詢。

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

然後，您可以將此請求作為參數化的 JSON 查詢發送到 Cosmos DB，如下所示：

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

以下示例使用參數化查詢設置 TOP 參數： 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

參數值可以是任何有效的 JSON：字串、數位、布林、空、偶數陣列或嵌套 JSON。 由於 Cosmos DB 是無架構的，因此不會針對任何類型的參數進行驗證。


## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [模型文件資料](modeling-data.md)
