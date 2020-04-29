---
title: Azure Cosmos DB 查詢語言中的 TAN
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 TAN。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9d7187ba116067445e835769fc33aa70677ef80b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78301976"
---
# <a name="tan-azure-cosmos-db"></a>TAN （Azure Cosmos DB）
 在指定運算式中傳回指定角度的正切函式 (以弧度為單位)。  
  
## <a name="syntax"></a>語法
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會計算 PI()/2 的正切函數。  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 以下為結果集。  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="remarks"></a>備註

這個系統函數不會使用索引。

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
