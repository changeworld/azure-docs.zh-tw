---
title: Azure Cosmos DB 查詢語言的 RAND
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數 RAND。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e44878b6d65725f08aeca4eb07088315ae2bb78a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302214"
---
# <a name="rand-azure-cosmos-db"></a>RAND （Azure Cosmos DB）
 從 [0，1）傳回隨機產生的數位值。
 
## <a name="syntax"></a>Syntax
  
```sql
RAND ()  
```  

## <a name="return-types"></a>傳回類型

  傳回數值運算式。

## <a name="remarks"></a>備註

  `RAND` 是非決定性函數。 的重複呼叫 `RAND` 不會傳回相同的結果。

## <a name="examples"></a>範例
  
  下列範例會傳回隨機產生的數位值。
  
```sql
SELECT RAND() AS rand 
```  
  
 以下為結果集。  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="remarks"></a>備註

這個系統函數不會使用索引。

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
