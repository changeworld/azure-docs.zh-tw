---
title: Azure Cosmos DB 查詢語言中的 ATN2
description: 瞭解 Azure Cosmos DB 中的 ATN2 SQL 系統函數如何傳回 y/x 之反正切值的主體值（以弧度為單位）
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f8fa4738cdd165d068f05cead09e25b382a83730
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081700"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 傳回 y/x 有向徑正切函數的主體值，以弧度表示。  
  
## <a name="syntax"></a>語法
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會計算指定 X 和 Y 元件的 ATN2 值。  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 以下為結果集。  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="remarks"></a>備註

這個系統函數將不會使用索引。

## <a name="next-steps"></a>下一步

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
