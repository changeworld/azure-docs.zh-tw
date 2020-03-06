---
title: Azure Cosmos DB 查詢語言中的度數
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數，傳回以弧度指定之角度的對應角度（以度為單位）
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d175ba53a71998fc8e7812a1b761f9cd264c38a9
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78299465"
---
# <a name="degrees-azure-cosmos-db"></a>度數（Azure Cosmos DB）
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

這個系統函數不會使用索引。

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
