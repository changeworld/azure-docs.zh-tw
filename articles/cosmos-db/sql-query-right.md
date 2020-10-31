---
title: Azure Cosmos DB 查詢語言的右邊
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3219ccc58d5a4c517adfaf9fef196fae3a955d3e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082822"
---
# <a name="right-azure-cosmos-db"></a>RIGHT (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 傳回具有指定字元數目的字串右側部分。  
  
## <a name="syntax"></a>語法
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   這是要從中解壓縮字元的字串運算式。  
  
*num_expr*  
   這是指定字元數的數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回 "abc"右側部分的各種長度值。  
  
```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 以下為結果集。  
  
```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="remarks"></a>備註

這個系統函數將不會使用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
