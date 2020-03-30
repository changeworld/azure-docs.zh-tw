---
title: Azure 宇宙 DB 查詢語言中的 RAND
description: 在 Azure 宇宙 DB 中瞭解 SQL 系統函數 RAND。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e44878b6d65725f08aeca4eb07088315ae2bb78a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302214"
---
# <a name="rand-azure-cosmos-db"></a>蘭德（Azure 宇宙資料庫）
 從 {0，1} 返回隨機生成的數值。
 
## <a name="syntax"></a>語法
  
```sql
RAND ()  
```  

## <a name="return-types"></a>傳回類型

  傳回數值運算式。

## <a name="remarks"></a>備註

  `RAND` 是非決定性函數。 重複調用`RAND`不會返回相同的結果。

## <a name="examples"></a>範例
  
  下面的示例返回隨機生成的數值。
  
```sql
SELECT RAND() AS rand 
```  
  
 以下為結果集。  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="remarks"></a>備註

此系統功能不會利用索引。

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure 宇宙 DB](sql-query-mathematical-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
