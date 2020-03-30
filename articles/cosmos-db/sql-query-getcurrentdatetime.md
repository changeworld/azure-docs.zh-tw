---
title: 以 Azure 宇宙 DB 查詢語言獲取 CurrentDateTime
description: 瞭解 SQL 系統功能 GetCurrentDateTime 在 Azure 宇宙 DB 中。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d50b08ab85c7e299c465c3eb6f34e867d6634006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303897"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>獲取當前日期時間（Azure 宇宙資料庫）
 將當前 UTC（協調通用時間）日期和時間作為 ISO 8601 字串返回。
  
## <a name="syntax"></a>語法
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>傳回類型
  
  以以下格式`YYYY-MM-DDThh:mm:ss.fffffffZ`返回當前 UTC 日期和時間 ISO 8601 字串值：
  
  |||
  |-|-|
  |YYYY|四位數年份|
  |MM|兩位數月份（01 = 1 月等）|
  |DD|每月兩位數的一天 （01 到 31）|
  |T|時間元素開始的標誌|
  |hh|兩位數小時（00 至 23）|
  |mm|兩位數分鐘（00 到 59）|
  |ss|兩位數秒 （00 到 59）|
  |.fffffff|七位小數秒|
  |Z|UTC（協調通用時間）指定器||
  
  有關 ISO 8601 格式的詳細資訊，請參閱[ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>備註

  GetCurrentDateTime（） 是一個非確定性函數。 
  
  返回的結果為 UTC。

  精度為 7 位數位，精度為 100 納秒。

## <a name="examples"></a>範例
  
  下面的示例演示如何使用 GetCurrentDateTime（） 內建函數獲取當前 UTC 日期時間。
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 下面是一個示例結果集。
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>後續步驟

- [日期和時間函數 Azure 宇宙 DB](sql-query-date-time-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
