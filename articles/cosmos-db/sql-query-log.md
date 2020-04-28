---
title: 登入 Azure Cosmos DB 查詢語言
description: 瞭解 Azure Cosmos DB 中的 LOG SQL 系統函數，以傳回指定數值運算式的自然對數
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9213ef03f383dec7109652246411fac154b4a7f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302503"
---
# <a name="log-azure-cosmos-db"></a>記錄（Azure Cosmos DB）
 傳回指定數值運算式的自然對數。  
  
## <a name="syntax"></a>語法
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
*base*  
   選用數值引數會設定對數基數。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="remarks"></a>備註
  
  依預設，LOG() 會傳回自然對數。 您可以使用選用基數參數，將對數基數變更為其他值。  
  
  自然對數是底數 **e** 的對數，其中 **e** 是大約等於 2.718281828 的無理常數。  
  
  數字指數的自然對數則是該數值本身：LOG( EXP( n ) ) = n。 而數字的自然對數之指數為數字本身：EXP( LOG( n ) ) = n。  
  
## <a name="examples"></a>範例
  
  下列範例會宣告一個變數，並傳回指定變數 (10) 的對數值。  
  
```sql
SELECT LOG(10) AS log  
```  
  
 以下為結果集。  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 下列範例會計算一個數值之指數的 `LOG`。  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 以下為結果集。  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="remarks"></a>備註

這個系統函數不會使用索引。

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
