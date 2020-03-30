---
title: 在 Azure 宇宙 DB 查詢語言中的 D
description: 瞭解 Azure Cosmos DB 中的德波 SQL 系統函數，以返回以弧度為單位的角度的相應角度
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d175ba53a71998fc8e7812a1b761f9cd264c38a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299465"
---
# <a name="degrees-azure-cosmos-db"></a>德科（Azure 宇宙資料庫）
 針對以弧度指定的角度，傳回對應的角度 (以度為單位)。  
  
## <a name="syntax"></a>語法
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回 PI/2 弧度的角度度數。  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 以下為結果集。  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>備註

此系統功能不會利用索引。

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure 宇宙 DB](sql-query-mathematical-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
