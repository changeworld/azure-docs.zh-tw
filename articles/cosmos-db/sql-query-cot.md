---
title: Azure 宇宙 DB 查詢語言中的 COT
description: 瞭解 Azure Cosmos DB 中的共切 （COT） SQL 系統功能如何在指定數值運算式中返回指定角度（以弧度為單位）的三角共量
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25c907644f58ee40ea08e5636d68dc0e84564a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299482"
---
# <a name="cot-azure-cosmos-db"></a>COT（Azure 宇宙資料庫）
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
  
  下面的示例計算`COT`指定角度。  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 以下為結果集。  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>備註

此系統功能不會利用索引。

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure 宇宙 DB](sql-query-mathematical-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
