---
title: Azure Cosmos DB 中的位移限制子句
description: 瞭解如何使用 OFFSET LIMIT 子句來略過，並在中查詢時採用某些特定值 Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76771579"
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
  
  子句中`OFFSET`都需要計數`LIMIT`和計數。 `OFFSET LIMIT` 如果使用了`ORDER BY`選擇性的子句，就會藉由跳過已排序的值來產生結果集。 否則，查詢會傳回固定的值順序。

  具有`OFFSET LIMIT`之查詢的 RU 費用會隨著位移增加的詞彙數目而增加。 對於包含多個頁面結果的查詢，我們通常建議使用接續 token。 接續 token 是一個「書簽」，可供稍後繼續查詢的位置使用。 如果您使用`OFFSET LIMIT`，則不會有「書簽」。 如果您想要傳回查詢的下一個頁面，您必須從頭開始。
  
  當您想`OFFSET LIMIT`要完全略過檔並儲存用戶端資源時，應該使用。 例如，如果您想要`OFFSET LIMIT`跳到1000th 查詢結果，而不需要看到結果1到999，則應該使用。 在後端， `OFFSET LIMIT`仍然會載入每份檔，包括已略過的檔。 效能優勢是避免處理不需要的檔，而節省用戶端資源。

## <a name="examples"></a>範例

例如，以下的查詢會略過第一個值，並傳回第二個值（依常駐城市名稱的順序）：

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

- [開始使用](sql-query-getting-started.md)
- [SELECT 子句](sql-query-select.md)
- [ORDER BY 子句](sql-query-order-by.md)
