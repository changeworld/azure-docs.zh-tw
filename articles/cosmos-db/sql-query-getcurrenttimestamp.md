---
title: Azure Cosmos DB 查詢語言中的 GetCurrentTimestamp
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 GetCurrentTimestamp。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6720b0e5d13f2baaaf063fef2244b0c1f1863571
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341921"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 傳回自 00:00:00 1970 年1月1日星期四起經過的毫秒數。
  
## <a name="syntax"></a>語法
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>傳回類型
  
傳回帶正負號的數值，這是自 Unix epoch 以來經過的目前毫秒數，也就是自 00:00:00 1970 年1月1日星期四起經過的毫秒數。

## <a name="remarks"></a>備註

GetCurrentTimestamp ( # A1 是不具決定性的函數。 傳回的結果為 UTC (國際標準時間) 。

這個系統函數將不會使用索引。

## <a name="examples"></a>範例
  
  下列範例顯示如何使用 GetCurrentTimestamp ( # A1 內建函數取得目前的時間戳記。
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 以下是範例結果集。
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>後續步驟

- [日期和時間函數 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
