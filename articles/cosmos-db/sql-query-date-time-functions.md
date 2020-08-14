---
title: Azure Cosmos DB 查詢語言中的日期和時間函數
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數，以執行日期時間和時間戳記作業。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 460abbc0b2a2f277aaeed57c5b938de530696776
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224946"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>日期和時間函數 (Azure Cosmos DB) 

日期和時間函數可讓您在 Azure Cosmos DB 中執行日期時間和時間戳記作業。

## <a name="functions-to-obtain-the-date-and-time"></a>取得日期和時間的函式

下列純量函數可讓您以兩種形式取得目前的 UTC 日期和時間：符合 ISO 8601 格式的字串，或其值為 Unix epoch 的數值時間戳記（以毫秒為單位）：

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [GetCurrentTicks](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>使用 DateTime 值的函數

下列函數可讓您輕鬆地操作 DateTime 值：

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)
* [DateTimePart](sql-query-datetimepart.md)
* [DateTimeToTicks](sql-query-datetimetoticks.md)

## <a name="next-steps"></a>後續步驟

- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
- [使用者定義的函式](sql-query-udfs.md)
- [彙總](sql-query-aggregates.md)
