---
title: 在 Azure 宇宙 DB 中進行別名化
description: 瞭解如何在 Azure Cosmos DB SQL 查詢中使用別名來區分兩個具有相同名稱的屬性
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873466"
---
# <a name="aliasing-in-azure-cosmos-db"></a>在 Azure 宇宙 DB 中進行別名化

您可以顯式別名查詢中的值。 如果查詢具有兩個具有相同名稱的屬性，請使用別名重命名其中一個或兩個屬性，以便在投影結果中消除它們歧義。

## <a name="examples"></a>範例

用於別名的 AS 關鍵字是可選的，將第二個值投影為`NameInfo`：

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
- [選擇子句](sql-query-select.md)
- [from 子句](sql-query-from.md)
