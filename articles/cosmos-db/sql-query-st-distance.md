---
title: Azure Cosmos DB 查詢語言中的 ST_DISTANCE
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 ST_DISTANCE。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4908d5f9f6eccaaaf71308b868d712f0eb96cb52
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303149"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE （Azure Cosmos DB）
 傳回兩個 GeoJSON Point、Polygon 或 LineString 運算式之間的距離。  
  
## <a name="syntax"></a>語法
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*spatial_expr*  
   為任何有效的 GeoJSON 點、Polygon 或 LineString 物件運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式，其中包含距離。 這在預設參照系統中以公尺表示。  
  
## <a name="examples"></a>範例
  
  下列範例示範如何使用 `ST_DISTANCE` 內建函數，傳回位於指定位置30公里內的所有家族檔。 。  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 以下為結果集。  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>備註

此系統函數可受益于[地理空間索引](index-policy.md#spatial-indexes)。

## <a name="next-steps"></a>後續步驟

- [空間函數 Azure Cosmos DB](sql-query-spatial-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
