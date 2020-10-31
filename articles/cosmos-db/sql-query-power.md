---
title: POWER in Azure Cosmos DB 查詢語言
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數功能。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 41c5cc1a6032a0aee16f1922fc59349449c65b55
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93091917"
---
# <a name="power-azure-cosmos-db"></a>POWER (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 將指定之運算式的值傳回給指定的乘冪。  
  
## <a name="syntax"></a>語法
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr1*  
   為數值運算式。  
  
*numeric_expr2*  
   這是 *numeric_expr1* 引發的能力。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例示範將某個數字自乘 3 次 (數字的立方)。  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 以下為結果集。  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>下一步

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
