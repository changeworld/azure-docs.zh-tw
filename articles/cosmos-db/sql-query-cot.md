---
title: Azure Cosmos DB 查詢語言中的 COT
description: 瞭解 Azure Cosmos DB 中的 (COT) SQL 系統函數如何在指定的數值運算式中傳回指定角度的三角餘切值（以弧度為單位）
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c247ac477f92985fc722f9c06655b6a7474876fb
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339017"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 在指定的數值運算式中傳回指定角度的三角餘切函數，以弧度為單位。  
  
## <a name="syntax"></a>語法
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會計算 `COT` 指定角度的。  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 以下為結果集。  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>備註

這個系統函數將不會使用索引。

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
