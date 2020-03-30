---
title: Azure 宇宙 DB 查詢語言中的 ATN2
description: 瞭解 Azure Cosmos DB 中的 ATN2 SQL 系統功能如何返回以弧形表示的 y/x 弧切線的主要值
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 696e14e75998ead04c99fab2b84fc4c742d5f54a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302656"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2（Azure 宇宙資料庫）
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

此系統功能不會利用索引。

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure 宇宙 DB](sql-query-mathematical-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
