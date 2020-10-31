---
title: Azure Cosmos DB 查詢語言的 SQRT
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 SQRT。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 64cef5fc3c6a90f31674cee245a7669addadb427
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082414"
---
# <a name="sqrt-azure-cosmos-db"></a>SQRT (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 傳回指定之數值的平方根。  
  
## <a name="syntax"></a>語法
  
```sql
SQRT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回數字 1 到 3 的平方根值。  
  
```sql
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 以下為結果集。  
  
```json
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  

## <a name="remarks"></a>備註

這個系統函數將不會使用索引。

## <a name="next-steps"></a>下一步

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
