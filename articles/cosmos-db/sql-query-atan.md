---
title: Azure Cosmos DB 查詢語言中的 ATAN
description: 瞭解 Azure Cosmos DB 中的反正切 (ATAN ) SQL 系統函數如何傳回以弧度為單位的角度，其正切函數是指定的數值運算式
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a85967e97668af44c1f896d431c887cba2afb03c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081709"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 傳回角度，以弧度為單位，其正切函數是指定的數值運算式。 這也稱為反正切函數 (Arctangent)。  
  
## <a name="syntax"></a>語法
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回 `ATAN` 指定值的。  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 以下為結果集。  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  
## <a name="remarks"></a>備註

這個系統函數將不會使用索引。

## <a name="next-steps"></a>下一步

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
