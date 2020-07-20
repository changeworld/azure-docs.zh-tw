---
title: Azure Cosmos DB 查詢語言中的 GetCurrentTimestamp
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數 GetCurrentTimestamp。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9c35f83ce7a9a478f706e9ed560d884d9bf5e508
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261300"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB) 

 傳回自00:00:00 年 1970 1 月1日星期四以來經過的毫秒數。
  
## <a name="syntax"></a>語法
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值，這是自 Unix epoch 以來已過的毫秒數，也就是自00:00:00 年1月 1 1970 日星期四以來所經過的毫秒數。

## <a name="remarks"></a>備註

  GetCurrentTimestamp ( # A1 是不具決定性的函數。
  
  傳回的結果是 UTC (國際標準時間) 。

## <a name="examples"></a>範例
  
  下列範例顯示如何使用 GetCurrentTimestamp ( # A1 內建函數來取得目前的時間戳記。
  
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
