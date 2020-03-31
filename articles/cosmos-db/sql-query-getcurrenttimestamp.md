---
title: 在 Azure Cosmos DB 查詢語言中獲取目前時間戳
description: 瞭解 SQL 系統函數獲取 Azure 宇宙 DB 中的目前時間戳。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71351004"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>獲取目前時間戳（Azure 宇宙資料庫）
 返回自 1970 年 1 月 1 日星期四 00：00：00 以來經過的毫秒數。 
  
## <a name="syntax"></a>語法
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>傳回類型
  
  返回一個數值，即自 Unix 紀元以來經過的當前毫秒數，即自 1970 年 1 月 1 日星期四 00：00：00 以來經過的毫秒數。

## <a name="remarks"></a>備註

  GetCurrentTimestamp（）是一個非確定性函數。
  
  返回的結果為 UTC（協調通用時間）。

## <a name="examples"></a>範例
  
  下面的示例演示如何使用 GetCurrentTimestamp（） 內建函數獲取目前時間戳。
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 下面是一個示例結果集。
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>後續步驟

- [日期和時間函數 Azure 宇宙 DB](sql-query-date-time-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
