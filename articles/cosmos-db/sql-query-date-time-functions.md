---
title: Azure Cosmos DB 查詢語言的日期和時間函數
description: 瞭解 Azure Cosmos DB 中的日期和時間 SQL 系統函數，以執行 DateTime 和 timestamp 作業。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1ea838224f9d91b000100d5fa9308289619fd963
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605196"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>日期和時間函式 (Azure Cosmos DB) 

日期和時間函數可讓您在 Azure Cosmos DB 中執行 DateTime 和 timestamp 作業。

## <a name="functions-to-obtain-the-date-and-time"></a>取得日期和時間的函式

下列純量函數可讓您以三種形式取得目前的 UTC 日期和時間：符合 ISO 8601 格式的字串、數值時間戳記，其值為 Unix epoch 以來經過的毫秒數，或數值為 Unix epoch 以來經過的100毫微秒滴答數的數值刻度:

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [GetCurrentTicks](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>使用日期時間值的函數

下列函數可讓您輕鬆地操作 DateTime、timestamp 和 tick 值：

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)
* [DateTimePart](sql-query-datetimepart.md)
* [DateTimeToTicks](sql-query-datetimetoticks.md)
* [DateTimeToTimestamp](sql-query-datetimetotimestamp.md)
* [TicksToDateTime](sql-query-tickstodatetime.md)
* [TimestampToDateTime](sql-query-timestamptodatetime.md)

## <a name="next-steps"></a>後續步驟

- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
- [使用者定義的函式](sql-query-udfs.md)
- [彙總](sql-query-aggregates.md)
