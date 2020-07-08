---
title: Azure Cosmos DB 查詢語言中的 COS
description: 瞭解 Azure Cosmos DB 中的余弦（COS） SQL 系統函數如何在指定的運算式中傳回指定角度的三角余弦值（以弧度為單位）
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40d277ff38691e2cb74bd4d5d78a666c304acfcd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78304016"
---
# <a name="cos-azure-cosmos-db"></a>COS （Azure Cosmos DB）
 在指定運算式中傳回指定角度的三角餘弦函數，以弧度為單位。  
  
## <a name="syntax"></a>語法
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會計算 `COS` 指定角度的。  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 以下為結果集。  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>備註

這個系統函數不會使用索引。

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
