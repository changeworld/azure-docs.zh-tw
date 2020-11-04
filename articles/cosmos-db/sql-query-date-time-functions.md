---
title: Azure Cosmos DB 查詢語言的日期和時間函數
description: 瞭解 Azure Cosmos DB 中的日期和時間 SQL 系統函數，以執行 DateTime 和 timestamp 作業。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d8b6e932f4cd5f4119d93f3fed7db524f65cbc1f
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93338946"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>日期和時間函式 (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
