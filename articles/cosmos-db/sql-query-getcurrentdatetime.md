---
title: Azure Cosmos DB 查詢語言中的 GetCurrentDateTime
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數 GetCurrentDateTime。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4db2ec7bbd05d982af57c05fd46ef7da7c682e15
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86258803"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB) 

傳回目前 UTC (國際標準時間) 日期和時間，做為 ISO 8601 字串。
  
## <a name="syntax"></a>語法
  
```sql
GetCurrentDateTime ()
```

## <a name="return-types"></a>傳回類型
  
  以下列格式傳回目前的 UTC 日期和時間 ISO 8601 字串值 `YYYY-MM-DDThh:mm:ss.fffffffZ` ：
  
  |[格式]|描述|
  |-|-|
  |YYYY|四位數的年份|
  |MM|兩位數的月份 (01 = 一月等等 ) |
  |DD|兩位數的當月日期 (01 到 31) |
  |T|時間元素開頭的 signifier|
  |hh|兩位數的小時 (00 到 23) |
  |mm|兩位數的分鐘 (00 到 59) |
  |ss|兩位數的秒 (00 到 59) |
  |. fffffff|七位數的小數秒數|
  |Z|UTC (國際標準時間) 指示項||
  
  如需 ISO 8601 格式的詳細資訊，請參閱[ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>備註

  GetCurrentDateTime ( # A1 是不具決定性的函數。
  
  傳回的結果是 UTC。

  精確度是7位數，精確度為100毫微秒。

## <a name="examples"></a>範例
  
  下列範例顯示如何使用 GetCurrentDateTime ( # A1 內建函數來取得目前的 UTC 日期時間。
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 以下是範例結果集。
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>後續步驟

- [日期和時間函數 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
