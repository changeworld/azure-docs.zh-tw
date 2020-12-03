---
title: Azure Cosmos DB 查詢語言的計數
description: 瞭解 Azure Cosmos DB 中) SQL 系統函數計數 (計數。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 5228558f4bcb146ec08ee5fff45fb1bdf4d56f01
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551865"
---
# <a name="count-azure-cosmos-db"></a>計數 (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

這個系統函數會傳回運算式中值的計數。
  
## <a name="syntax"></a>語法
  
```sql
COUNT(<scalar_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*scalar_expr*  
   是否為任何純量運算式
  
## <a name="return-types"></a>傳回類型
  
傳回數值運算式。  
  
## <a name="examples"></a>範例
  
下列範例會傳回容器中的總專案數：
  
```sql
SELECT COUNT(1)
FROM c
``` 
COUNT 可以採用任何純量運算式做為輸入。 下列查詢會產生對等的結果：

```sql
SELECT COUNT(2)
FROM c
```

## <a name="remarks"></a>備註

這個系統函數將受益于查詢篩選中任何屬性的 [範圍索引](index-policy.md#includeexclude-strategy) 。

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 中的數學函數](sql-query-mathematical-functions.md)
- [Azure Cosmos DB 中的系統函數](sql-query-system-functions.md)
- [Azure Cosmos DB 中的彙總函式](sql-query-aggregate-functions.md)