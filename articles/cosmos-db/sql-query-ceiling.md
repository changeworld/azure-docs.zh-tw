---
title: Azure 宇宙 DB 查詢語言中的"天花板"
description: 瞭解 Azure Cosmos DB 中的"天花板 SQL 系統"功能如何返回大於或等於指定數值運算式的最小整數值。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 81f113aa51a7f739b506ec7e3eb5bf2cb9f49a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302639"
---
# <a name="ceiling-azure-cosmos-db"></a>天花板（Azure 宇宙資料庫）
 傳回大於或等於指定之數值運算式的最小整數值。  
  
## <a name="syntax"></a>語法
  
```sql
CEILING (<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下面的示例顯示函數的`CEILING`正數值、負值和零值。  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 以下為結果集。  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="remarks"></a>備註

該系統功能將受益于[範圍索引](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure 宇宙 DB](sql-query-mathematical-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
