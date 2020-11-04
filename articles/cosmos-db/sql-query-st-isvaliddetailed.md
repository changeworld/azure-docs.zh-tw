---
title: Azure Cosmos DB 查詢語言中的 ST_ISVALIDDETAILED
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 ST_ISVALIDDETAILED。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 29ef1ebe4cd0eaf4d621dd6d4fc11d485e47d1c7
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335019"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 如果指定的 GeoJSON Point、Polygon 或 LineString 運算式有效，就傳回包含布林值的 JSON 值；但如果是無效的，就會額外加上做為字串值的原因。  
  
## <a name="syntax"></a>語法
  
```sql
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*spatial_expr*  
   這是 GeoJSON 點或多邊形運算式。  
  
## <a name="return-types"></a>傳回類型
  
  如果指定的 GeoJSON 點或多邊形運算式是有效的，就會傳回包含布林值的 JSON 值；但如果是無效的，就會額外加上做為字串值的原因。  
  
## <a name="examples"></a>範例
  
  下列範例說明如何使用) 的詳細資料來檢查有效性 (`ST_ISVALIDDETAILED` 。  
  
```sql
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 以下為結果集。  
  
```json
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>後續步驟

- [空間函數 Azure Cosmos DB](sql-query-spatial-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
