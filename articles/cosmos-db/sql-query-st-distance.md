---
title: Azure Cosmos DB 查詢語言中的ST_DISTANCE
description: 瞭解 Azure Cosmos DB 中ST_DISTANCE SQL 系統函數。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 02844569137a46ea030b2189191b84a9db24ed22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537290"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE（Azure 宇宙資料庫）
 返回兩個 GeoJSON 點、多邊形、多面或線弦運算式之間的距離。 要瞭解更多資訊，請參閱[地理空間和地理JSON位置資料](sql-query-geospatial-intro.md)一文。
  
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
  
  下面的示例演示如何使用`ST_DISTANCE`內建函數返回距離指定位置 30 公里以內的所有族文檔。 .  
  
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

該系統功能將受益于[地理空間索引](index-policy.md#spatial-indexes)。

## <a name="next-steps"></a>後續步驟

- [空間函數 Azure 宇宙 DB](sql-query-spatial-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
