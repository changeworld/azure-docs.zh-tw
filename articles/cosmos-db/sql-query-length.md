---
title: Azure Cosmos DB 查詢語言的長度
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數長度。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0c7766ac81df02755fad741c6fa733ba3163baff
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083646"
---
# <a name="length-azure-cosmos-db"></a>長度 (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 傳回指定字串運算式的字元數目。  
  
## <a name="syntax"></a>語法
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   要評估的字串運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回字串的長度。  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 以下為結果集。  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>備註

這個系統函數將不會使用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
