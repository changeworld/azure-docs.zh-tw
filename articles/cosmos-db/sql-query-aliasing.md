---
title: Azure Cosmos DB 中的別名
description: 瞭解如何在 Azure Cosmos DB SQL 查詢中使用別名，以區別具有相同名稱的兩個屬性
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74873466"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Azure Cosmos DB 中的別名

您可以在查詢中明確地別名值。 如果查詢有兩個具有相同名稱的屬性，請使用別名來重新命名其中一個或兩個屬性，以便在預測的結果中加以區分。

## <a name="examples"></a>範例

用於別名的 AS 關鍵字是選擇性的，如下列範例所示，將第二個值投射`NameInfo`為：

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果如下：

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT 子句](sql-query-select.md)
- [FROM 子句](sql-query-from.md)
