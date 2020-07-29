---
title: 使用 Azure Cosmos DB 查詢地理空間資料
description: 使用 Azure Cosmos DB 查詢空間資料
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: bbfc31e810e2c11cde4907c9d5120b66195191af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84764973"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 查詢地理空間資料

本文將討論如何使用 SQL 和 LINQ 查詢 Azure Cosmos DB 中的地理空間資料。 目前僅 Azure Cosmos DB SQL API 帳戶支援儲存和存取地理空間資料。 Azure Cosmos DB 支援下列開放地理空間協會 (OGC) 內建的地理空間查詢函式。 如需一組完整的 SQL 語言內建函數的詳細資訊，請參閱[Azure Cosmos DB 中的查詢系統函數](sql-query-system-functions.md)。

## <a name="spatial-sql-built-in-functions"></a>空間 SQL 內建函數

以下是可用來查詢 Azure Cosmos DB 的地理空間系統函數清單：

|**使用量**|**描述**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | 傳回兩個 GeoJSON Point、Polygon 或 LineString 運算式之間的距離。|
|ST_WITHIN (spatial_expr, spatial_expr) | 傳回布林運算式，指出第一個 GeoJSON 物件 (Point、Polygon 或 LineString) 是否位在第二個 GeoJSON 物件 (Point、Polygon 或 LineString) 內。|
|ST_INTERSECTS (spatial_expr, spatial_expr)| 傳回布林運算式，指出兩個指定的 GeoJSON 物件 (Point、Polygon 或 LineString) 是否相交。|
|ST_ISVALID| 傳回布林值，指出指定的 GeoJSON Point、Polygon 或 LineString 運算式是否有效。|
| ST_ISVALIDDETAILED| 如果指定的 GeoJSON 點、多邊形或 LineString 運算式是有效的，則會傳回包含布林值的 JSON 值。 如果無效，則會傳回字串值的原因。|

空間函數可以用來對空間資料執行鄰近性查詢。 例如，以下的查詢會使用內建函數，傳回在指定位置30公里內的所有家族檔 `ST_DISTANCE` 。

**查詢**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {"type": "Point", "coordinates":[31.9, -4.8]}) < 30000
```

**結果**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

如果您將空間索引編製包含在索引編製原則中，則「距離查詢」將會透過索引獲得有效利用。 如需空間索引編制的詳細資訊，請參閱[地理空間索引編制](sql-query-geospatial-index.md)。 如果您沒有指定路徑的空間索引，查詢將會執行容器的掃描。

`ST_WITHIN`可以用來檢查某個點是否位於多邊形內。 多邊形常用來表示邊界，例如郵遞區號、州省邊界或自然構成物。 此外，如果您將空間索引編製包含在索引編製原則中，則「距離內」查詢將會透過索引獲得有效利用。

中的多邊形引數 `ST_WITHIN` 只能包含單一環形，也就是多邊形不能包含其中的洞。

**查詢**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**結果**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> 與 Azure Cosmos DB 查詢中不相符類型的運作方式類似，如果任一引數中指定的位置值格式不正確或無效，則會評估為**未定義**，且會在查詢結果中略過已評估的文件。 如果您的查詢未傳回任何結果，請執行 `ST_ISVALIDDETAILED` 來進行 debug，看看空間類型不正確原因。
>
>

Azure Cosmos DB 也支援反向查詢，亦即您可以在 Azure Cosmos DB 中編製多邊形或線的索引，然後查詢包含指定點的區域。 這是物流業中常用的模式，例如，可用來識別卡車何時進入或離開指定的區域。

**查詢**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({"type": "Point", "coordinates":[31.9, -4.8]}, a.location)
```

**結果**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

`ST_ISVALID`和 `ST_ISVALIDDETAILED` 可以用來檢查空間物件是否有效。 例如，下列查詢以超出範圍的緯度值 (-132.8)，檢查點的有效性。 `ST_ISVALID`只會傳回布林值，並傳回 `ST_ISVALIDDETAILED` 布林值和字串，其中包含視為不正確原因。

**查詢**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**結果**

```json
    [{
      "$1": false
    }]
```

這些函數也可以用來驗證多邊形。 例如，在這裡我們使用 `ST_ISVALIDDETAILED` 來驗證未關閉的多邊形。

**查詢**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**結果**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>.NET SDK 中的 LINQ 查詢

SQL .NET SDK 也是虛設常式方法 `Distance()` 和 `Within()` 的提供者，供您在 LINQ 運算式中使用。 SQL LINQ 提供者會將這些方法呼叫，轉譯為同等的 SQL 內建函數呼叫 (分別為 ST_DISTANCE 和 ST_WITHIN)。

以下是 LINQ 查詢的範例，它會尋找 Azure Cosmos 容器中的所有檔，其 `location` 值在使用 LINQ 之指定點的半徑內為30公里。

**距離的 LINQ 查詢**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

同樣地，以下是尋找 `location` 指定的方塊/多邊形內所有檔的查詢。

**範圍內的 LINQ 查詢**

```csharp
    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
         .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }
```

## <a name="next-steps"></a>後續步驟

既然您已了解如何開始使用 Azure Cosmos DB 中的地理空間支援，您可以接著：

* 深入了解 [Azure Cosmos DB 查詢](sql-query-getting-started.md)
* 深入瞭解[Azure Cosmos DB 中的地理空間和 GeoJSON 位置資料](sql-query-geospatial-intro.md)
* 深入瞭解如何[使用 Azure Cosmos DB 編制索引空間資料](sql-query-geospatial-index.md)
