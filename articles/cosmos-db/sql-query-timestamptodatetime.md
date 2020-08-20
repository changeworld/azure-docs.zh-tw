---
title: Azure Cosmos DB 查詢語言中的 TimestampToDateTime
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 TimestampToDateTime。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 9d4b5179ea08d5d6eca03422db7dfc7c8c4b5c3e
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608733"
---
# <a name="timestamptodatetime-azure-cosmos-db"></a>TimestampToDateTime (Azure Cosmos DB) 

將指定的時間戳記值轉換成日期時間。
  
## <a name="syntax"></a>語法
  
```sql
TimestampToDateTime (<Timestamp>)
```

## <a name="arguments"></a>引數

*Timestamp*  

帶正負號的數值，這是目前自 Unix epoch 以來經過的毫秒數。 換句話說，自 00:00:00 1970 年1月1日星期四起經過的毫秒數。

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

`undefined`如果指定的時間戳記值無效，TimestampToDateTime 就會傳回。

## <a name="examples"></a>範例
  
下列範例會將時間戳記轉換為日期時間：

```sql
SELECT TimestampToDateTime(1594227912345) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-08T17:05:12.3450000Z"
    }
]
```  

## <a name="next-steps"></a>後續步驟

- [日期和時間函數 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
