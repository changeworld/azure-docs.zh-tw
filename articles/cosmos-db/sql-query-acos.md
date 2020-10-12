---
title: Azure Cosmos DB 查詢語言中的 ACOS
description: 瞭解 Azure Cosmos DB 中的 ACOS (arccosice) SQL 系統函數如何傳回其餘弦值為指定之數值運算式的角度（以弧度為單位）
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 733d6b009f03d61c37170cc506a3b2ec842d7c47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "78300956"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB) 
 傳回角度，以弧度為單位，它的餘弦是指定的數值運算式；也稱為反餘弦值。  
  
## <a name="syntax"></a>語法
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回 `ACOS` -1 的。  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 以下為結果集。  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>備註

這個系統函數將不會使用索引。

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
