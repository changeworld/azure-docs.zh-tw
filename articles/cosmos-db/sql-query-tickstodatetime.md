---
title: Azure Cosmos DB 查詢語言中的 TicksToDateTime
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 TicksToDateTime。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 12d286fb5c8cf6d602d95da53ef8de376f9a49cf
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93093855"
---
# <a name="tickstodatetime-azure-cosmos-db"></a>TicksToDateTime (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

將指定的刻度值轉換成日期時間。
  
## <a name="syntax"></a>語法
  
```sql
TicksToDateTime (<Ticks>)
```

## <a name="arguments"></a>引數

*蜱*  

帶正負號的數值，這是自 Unix epoch 以來經過的目前100毫微秒滴答數。 換句話說，它是自00:00:00 星期四（1月1970）以來經過的100毫微秒滴答數。

## <a name="return-types"></a>傳回類型

以下列格式傳回 UTC 日期和時間 ISO 8601 字串值 `YYYY-MM-DDThh:mm:ss.fffffffZ` ：
  
  |格式|描述|
  |-|-|
  |YYYY|四位數年份|
  |MM|兩位數的月份 (01 = 一月，依此類推 ) |
  |DD|月中的兩位數天 (01 到 31) |
  |T|時間元素開始的 signifier|
  |hh|兩位數的小時 (00 到 23) |
  |mm|兩位數的分鐘 (00 到 59) |
  |ss|兩位數的秒 (00 到 59) |
  |. fffffff|七位數的小數秒數|
  |Z|UTC (國際標準時間) 指示項||
  
  如需 ISO 8601 格式的詳細資訊，請參閱 [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>備註

`undefined`如果指定的刻度值無效，TicksToDateTime 就會傳回。

## <a name="examples"></a>範例
  
下列範例會將刻度轉換成 DateTime：

```sql
SELECT TicksToDateTime(15943368134575530) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-09T23:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>下一步

- [日期和時間函數 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
