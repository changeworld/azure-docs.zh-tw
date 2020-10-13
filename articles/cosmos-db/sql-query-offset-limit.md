---
title: Azure Cosmos DB 中的 OFFSET LIMIT 子句
description: 瞭解如何在中查詢時，使用 OFFSET LIMIT 子句略過並採用一些特定值 Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: 9342c0c10ed4f7287716d21823fddfe992e0568f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87432717"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 OFFSET LIMIT 子句

OFFSET LIMIT 子句是選擇性的子句，可以略過查詢中的一些值。 Offset LIMIT 子句中需要 OFFSET count 和 LIMIT count。

當位移限制與 ORDER BY 子句一起使用時，結果集是藉由執行 skip 來產生，並採用已排序的值。 如果未使用 ORDER BY 子句，則會產生決定性的值順序。

## <a name="syntax"></a>語法
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>引數

- `<offset_amount>`

   指定查詢結果應略過的整數專案數。

- `<limit_amount>`
  
   指定查詢結果應包含的整數專案數

## <a name="remarks"></a>備註
  
  在 `OFFSET` 子句中，計數和 `LIMIT` 計數都是必要的 `OFFSET LIMIT` 。 如果使用選擇性 `ORDER BY` 子句，則會藉由對排序的值進行跳過來產生結果集。 否則，查詢會傳回固定的值順序。

  查詢的 RU 費用 `OFFSET LIMIT` 將會隨著所位移的詞彙數目增加而增加。 如果查詢有 [多個頁面的結果](sql-query-pagination.md)，我們通常會建議使用 [接續權杖](sql-query-pagination.md#continuation-tokens)。 接續 token 是「書簽」，可供稍後繼續查詢。 如果您使用 `OFFSET LIMIT` ，就不會有「書簽」。 如果您想要傳回查詢的下一個頁面，就必須從頭開始。
  
  `OFFSET LIMIT`當您想要完全略過專案並儲存用戶端資源時，應該使用的情況。 例如， `OFFSET LIMIT` 如果您想要跳至1000查詢結果，而不需要查看結果1到999，則應該使用。 在後端， `OFFSET LIMIT` 仍會載入每個專案，包括已略過的專案。 效能優勢是避免處理不需要的專案，以節省用戶端資源。

## <a name="examples"></a>範例

例如，以下的查詢會略過第一個值，並傳回第二個值 (以常駐城市的名稱) ：

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

結果為：

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

以下查詢會略過第一個值，並傳回第二個值 (而不需要排序) ：

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

- [快速入門](sql-query-getting-started.md)
- [SELECT 子句](sql-query-select.md)
- [ORDER BY 子句](sql-query-order-by.md)
