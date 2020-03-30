---
title: 在 Azure 宇宙 DB 查詢語言中的 POWER
description: 瞭解 Azure 宇宙 DB 中的 SQL 系統函數 POWER。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683c53c369f136ad4b917b93e9a92a71072d05e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349645"
---
# <a name="power-azure-cosmos-db"></a>電源（Azure 宇宙資料庫）
 將指定之運算式的值傳回給指定的乘冪。  
  
## <a name="syntax"></a>語法
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr1*  
   為數值運算式。  
  
*numeric_expr2*  
   是提高*numeric_expr1*的力量。  
  
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

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure 宇宙 DB](sql-query-mathematical-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
