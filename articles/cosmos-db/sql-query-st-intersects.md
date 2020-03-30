---
title: 在 Azure Cosmos DB 查詢語言中ST_INTERSECTS
description: 瞭解 Azure Cosmos DB 中ST_INTERSECTS的 SQL 系統函數。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f3c3878956b90ffb45556ed819046af9eb7618f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303132"
---
# <a name="st_intersects-azure-cosmos-db"></a>ST_INTERSECTS（Azure 宇宙資料庫）
 傳回布林運算式，指出第一個引數交集中指定的 GeoJSON 物件 (Point、Polygon 或 LineString) 是否位在第二個引數中的 GeoJSON (Point、Polygon 或 LineString) 內。  
  
## <a name="syntax"></a>語法
  
```sql
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*spatial_expr*  
   是 GeoJSON 點、多邊形或 LineString 物件運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回布林值。  
  
## <a name="examples"></a>範例
  
  下列範例說明如何尋找與給定的多邊形交集的所有區域。  
  
```sql
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 以下為結果集。  
  
```json
[{ "id": "IntersectingPolygon" }]  
```  

## <a name="remarks"></a>備註

該系統功能將受益于[地理空間索引](index-policy.md#spatial-indexes)。

## <a name="next-steps"></a>後續步驟

- [空間函數 Azure 宇宙 DB](sql-query-spatial-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
