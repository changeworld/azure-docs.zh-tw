---
title: Azure Cosmos DB 查詢語言中的 COS
description: 瞭解 Azure Cosmos DB 中的余弦 (COS) SQL 系統函數如何在指定的運算式中傳回指定角度的三角余弦值（以弧度為單位）。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5f11307d4f24bab06973e33b4640e26b1c445336
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339016"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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

這個系統函數將不會使用索引。

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
