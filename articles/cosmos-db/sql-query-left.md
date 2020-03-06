---
title: Azure Cosmos DB 查詢語言中的左邊
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0eac35a91e4d5158335d6797d49a09f8f6f391e3
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303744"
---
# <a name="left-azure-cosmos-db"></a>左方（Azure Cosmos DB）
 傳回具有指定字元數目的字串左側部分。  
  
## <a name="syntax"></a>語法
  
```sql
LEFT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   這是要從中解壓縮字元的字串運算式。  
  
*num_expr*  
   這是指定字元數的數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回 "abc"左側部分的各種長度值。  
  
```sql
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 以下為結果集。  
  
```json
[{"l1": "a", "l2": "ab"}]  
```  

## <a name="remarks"></a>備註

這個系統函數將受益于[範圍索引](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
