---
title: Azure Cosmos DB 中的位移限制子句
description: 瞭解如何使用 OFFSET LIMIT 子句來略過，並在中查詢時採用某些特定值 Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: 9342c0c10ed4f7287716d21823fddfe992e0568f
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87432717"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的位移限制子句

OFFSET LIMIT 子句是選擇性的子句，可略過查詢中的一些值。 Offset 限制子句中需要有位移計數和限制計數。

當位移限制與 ORDER BY 子句一起使用時，結果集會藉由執行 skip 並採取排序的值來產生。 如果未使用 ORDER BY 子句，則會產生具有決定性的值順序。

## <a name="syntax"></a>語法
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>引數

- `<offset_amount>`

   指定查詢結果應略過的專案整數數目。

- `<limit_amount>`
  
   指定查詢結果應包含的整數專案數

## <a name="remarks"></a>備註
  
  `OFFSET` `LIMIT` 子句中都需要計數和計數 `OFFSET LIMIT` 。 如果使用了選擇性的 `ORDER BY` 子句，就會藉由跳過已排序的值來產生結果集。 否則，查詢會傳回固定的值順序。

  具有之查詢的 RU 費用 `OFFSET LIMIT` 會隨著位移增加的詞彙數目而增加。 對於包含[多個頁面結果](sql-query-pagination.md)的查詢，我們通常建議使用[接續](sql-query-pagination.md#continuation-tokens)token。 接續 token 是一個「書簽」，可供稍後繼續查詢的位置使用。 如果您使用 `OFFSET LIMIT` ，則不會有「書簽」。 如果您想要傳回查詢的下一個頁面，您必須從頭開始。
  
  `OFFSET LIMIT`當您想要完全略過專案並儲存用戶端資源時，應該使用。 例如， `OFFSET LIMIT` 如果您想要跳到1000th 查詢結果，而不需要看到結果1到999，則應該使用。 在後端， `OFFSET LIMIT` 仍然會載入每個專案，包括已略過的專案。 效能優勢是藉由避免處理不需要的專案，來節省用戶端資源。

## <a name="examples"></a>範例

例如，以下的查詢會略過第一個值，並傳回第二個值（依常駐城市名稱的順序）：

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

以下是略過第一個值並傳回第二個值的查詢（沒有排序）：

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
