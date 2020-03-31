---
title: Azure 宇宙 DB 中的 OFFSET LIMIT 子句
description: 瞭解如何在 Azure Cosmos DB 中查詢時使用 OFFSET LIMIT 子句跳過並獲取某些值
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76771579"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Azure 宇宙 DB 中的 OFFSET LIMIT 子句

OFFSET LIMIT 子句是一個可選子句，用於跳過，然後從查詢中獲取一定數量的值。 OFFSET 計數和 LIMIT 計數在 OFFSET LIMIT 子句中是必需的。

當 OFFSET LIMIT 與 ORDER BY 子句結合使用時，結果集是通過執行跳過並獲取有序值來生成的。 如果未使用 ORDER BY 子句，則會導致值的確定性順序。

## <a name="syntax"></a>語法
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>引數

- `<offset_amount>`

   指定查詢結果應跳過的項的整數數。

- `<limit_amount>`
  
   指定查詢結果應包括的項的整數數

## <a name="remarks"></a>備註
  
  子句中都需要計數`LIMIT`和計數。 `OFFSET` `OFFSET LIMIT` 如果使用可選`ORDER BY`子句，則通過跳過排序值生成結果集。 否則，查詢將返回固定的值順序。

  帶有`OFFSET LIMIT`查詢的 RU 電荷將隨著偏移量的增加而增加。 對於具有多個頁結果的查詢，我們通常建議使用延續權杖。 延續權杖是查詢以後可以恢復的地方的"書簽"。 如果使用`OFFSET LIMIT`，則沒有"書簽"。 如果要返回查詢的下一頁，必須從一開始就返回。
  
  當要完全`OFFSET LIMIT`跳過文檔並保存用戶端資源時，應使用這種情況。 例如，如果要跳到第`OFFSET LIMIT`1000 個查詢結果，並且無需查看結果 1 到 999，則應使用。 在後端，`OFFSET LIMIT`仍載入每個文檔，包括跳過的文檔。 性能優勢是通過避免處理不需要的文檔來節省用戶端資源。

## <a name="examples"></a>範例

例如，下面是跳過第一個值並返回第二個值（按居民城市名稱順序排列）的查詢：

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

結果如下：

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

下面是跳過第一個值並返回第二個值（不排序）的查詢：

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

結果如下：

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>後續步驟

- [開始使用](sql-query-getting-started.md)
- [選擇子句](sql-query-select.md)
- [訂單 BY 子句](sql-query-order-by.md)
