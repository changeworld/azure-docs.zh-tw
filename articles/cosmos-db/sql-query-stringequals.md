---
title: Azure Cosmos DB 查詢語言中的 StringEquals
description: 了解 Azure Cosmos DB 中的 StringEquals SQL 系統函數如何傳回布林值，指出第一個字串運算式是否符合第二個字串運算式
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a34ea2e2421433777ff1b9dd44d2ffc5599017bc
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079779"
---
# <a name="stringequals-azure-cosmos-db"></a>STRINGEQUALS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 傳回布林值，表示第一個字串運算式是否符合第二個字串運算式。  
  
## <a name="syntax"></a>語法
  
```sql
STRINGEQUALS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>引數
  
*str_expr1*  
   這是所要比較的第一個字串運算式。  
  
*str_expr2*  
   這是所要比較的第二個字串運算式。  

*bool_expr* 忽略大小寫的選擇性值。 設定為 true 時，StringEquals 會執行不區分大小寫的搜尋。 若未指定，則此值為 false。
  
## <a name="return-types"></a>傳回類型
  
  傳回布林運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會檢查 "abc" 是否包含 "abc" 及 "abc" 是否包含 "ABC"。  
  
```sql
SELECT STRINGEQUALS("abc", "abc", false) AS c1, STRINGEQUALS("abc", "ABC", false) AS c2,  STRINGEQUALS("abc", "ABC", true) AS c3
```  
  
 以下為結果集。  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>備註

此系統函數將受益於[範圍索引](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
