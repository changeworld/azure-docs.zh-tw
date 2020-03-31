---
title: Azure 宇宙 DB 查詢語言中的 COS
description: 瞭解 Azure Cosmos DB 中的 Cosine （COS） SQL 系統函數如何在指定運算式中返回指定角度（以弧度為單位）的三角再成因
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40d277ff38691e2cb74bd4d5d78a666c304acfcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304016"
---
# <a name="cos-azure-cosmos-db"></a>COS（Azure 宇宙資料庫）
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
  
  下面的示例計算`COS`指定角度。  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 以下為結果集。  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>備註

此系統功能不會利用索引。

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure 宇宙 DB](sql-query-mathematical-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
