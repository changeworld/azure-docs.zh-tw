---
title: Azure Cosmos DB 查詢語言的度數
description: 瞭解 Azure Cosmos DB 中的程度 SQL 系統函數，以弧度為單位傳回相對應的角度（以度為單位）。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 991d83a329603342975a8186fe704afc53813e08
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095759"
---
# <a name="degrees-azure-cosmos-db"></a>度數 (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 針對以弧度指定的角度，傳回對應的角度 (以度為單位)。  
  
## <a name="syntax"></a>語法
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回 PI/2 弧度的角度度數。  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 以下為結果集。  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>備註

這個系統函數將不會使用索引。

## <a name="next-steps"></a>下一步

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
