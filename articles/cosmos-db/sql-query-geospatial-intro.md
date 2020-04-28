---
title: Azure Cosmos DB 中的地理空間和 GeoJSON 位置資料
description: 瞭解如何使用 Azure Cosmos DB 和 SQL API 來建立空間物件。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 59c8b31dcc8594d2cafb2db7832e290b01026f60
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79367579"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Azure Cosmos DB 中的地理空間和 GeoJSON 位置資料

本文將介紹 Azure Cosmos DB 中的地理空間功能。 目前僅 Azure Cosmos DB SQL API 帳戶支援儲存和存取地理空間資料。 閱讀我們的關於地理空間索引的檔之後，您將能夠回答下列問題：

* 如何在 Azure Cosmos DB 中儲存空間資料？
* 如何以 SQL 和 LINQ 查詢 Azure Cosmos DB 中的地理空間資料？
* 如何在 Azure Cosmos DB 中啟用或停用空間編製索引？

## <a name="introduction-to-spatial-data"></a>空間資料簡介

空間資料可描述空間中物件的位置和形狀。 在大部分的應用程式中，這些會對應至地球上的物件和地理空間資料。 空間資料可以用來代表人、感興趣的地方、城市邊界或湖泊。 常見使用案例通常涉及鄰近性查詢，例如「尋找我目前位置附近的所有咖啡廳」。

Azure Cosmos DB 的 SQL API 支援兩種空間資料類型： **geometry**資料類型和**geography**資料類型。

- **Geometry**類型代表 Euclidean （平面）座標系統中的資料
- **geography** 類型代表圓形表面座標系統中的資料。

## <a name="supported-data-types"></a>支援的資料類型

Azure Cosmos DB 支援對使用 [GeoJSON 規格 (英文)](https://tools.ietf.org/html/rfc7946) 表示的地理空間點資料執行編製索引和查詢。 GeoJSON 資料結構一律為有效的 JSON 物件，因此可透過 Azure Cosmos DB 來儲存及查詢，無須使用任何特殊的工具或程式庫。

Azure Cosmos DB 支援下列空間資料類型：

- Point
- LineString
- Polygon
- MultiPolygon

### <a name="points"></a>點

**點** 代表空間中的單一位置。 在地理空間資料中，某個點所代表的確切位置可能是雜貨店的街道地址、電話亭、汽車或城市。  點會使用其座標組或經緯度，以 GeoJSON (和 Azure Cosmos DB) 來表示。

以下是某個點的 JSON 範例：

**Azure Cosmos DB 中的點**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

空間資料類型可以內嵌在 Azure Cosmos DB 檔中，如下列包含位置資料的使用者設定檔範例所示：

**儲存在 Azure Cosmos DB 中含有位置的使用設定檔**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

### <a name="points-in-a-geometry-coordinate-system"></a>Geometry 座標系統中的點

若為**geometry**資料類型，GeoJSON 規格會先指定水準軸和垂直軸秒。

### <a name="points-in-a-geography-coordinate-system"></a>地理座標系統中的點

對於**geography**資料類型，GeoJSON 規格會指定經度 first 和緯度 second。 如同其他的地圖應用程式，經度和緯度為角度，並以度為表示單位。 經度值是從本初子午線測量，並介於 -180 度和 180.0 度之間；緯度值是從赤道測量，並介於 -90.0 度和 90.0 度之間。

Azure Cosmos DB 會依照 WGS-84 參考系統所表示的方式來解譯座標。 請參閱下方詳細的座標參考系統資料。

### <a name="linestrings"></a>Linestring

**Linestring** 表示空間中一連串的點 (兩個以上)，以及連接這些點的線段。 在地理空間資料中，LineStrings 通常用來代表高速公路或河流。

**GeoJSON 中的 Linestring**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>多邊形

**多邊形**是連接點的界限，會形成封閉的 LineString。 多邊形常用來代表自然構成物，例如湖泊，或代表政治管轄權，例如城市和州省。 以下是 Azure Cosmos DB 中多邊形的範例：

**GeoJSON 中的多邊形**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> GeoJSON 規格需要此資料才能形成有效的多邊形；若要建立一個封閉的形狀，最後一個座標組應該與第一個座標組相同。
>
> 多邊形內的點必須以逆時針順序指定。 以順時針順序指定的多邊形，代表區域內的反轉。
>
>

### <a name="multipolygons"></a>MultiPolygons

**MultiPolygon**是零個或多個多邊形的陣列。 **MultiPolygons**不能重迭邊或具有任何通用區域。 他們可能會接觸一或多個點。

**GeoJSON 中的 MultiPolygons**

```json
{
    "type":"MultiPolygon",
    "coordinates":[[[
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
        ]],
        [[
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
        ]]]
}
```

## <a name="coordinate-reference-systems"></a>座標參考系統

由於地球的形狀是不規則的，地理地理空間資料的座標會以許多座標參考系統（CRS）來表示，而每一個都有自己的參考框架和測量單位。 例如「英國國家格網參考系統」對英國而言是精確的參考系統，但對其他地區則不是。

現今最常使用的 CRS 是「全球大地座標系統」[WGS-84](https://earth-info.nga.mil/GandG/update/index.php)。 GPS 裝置和許多地圖服務，包括 Google 地圖與 Bing Maps API 均是使用 WGS-84。 Azure Cosmos DB 僅支援使用 WGS-84 CRS 編制和查詢地理空間資料。

## <a name="creating-documents-with-spatial-data"></a>建立具有空間資料的文件
當您建立包含 GeoJSON 值的文件時，值會根據容器的索引編製原則，自動以空間索引進行索引編製。 如果您是以動態類型的語言 (如 Python 或 Node.js) 使用 Azure Cosmos DB SDK，則必須建立有效的 GeoJSON。

**以 Node.js 建立具有地理空間資料的文件**

```javascript
var userProfileDocument = {
    "id":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

如果您使用的是 SQL api，您可以`Point`使用`LineString` `Polygon` `MultiPolygon` `Microsoft.Azure.Cosmos.Spatial`命名空間中的、、和類別，將位置資訊內嵌在應用程式物件中。 這些類別可協助將空間資料序列化和還原序列化簡化成 GeoJSON。

**以 .NET 建立具有地理空間資料的文件**

```csharp
using Microsoft.Azure.Cosmos.Spatial;

public class UserProfile
{
    [JsonProperty("id")]
    public string id { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await container.CreateItemAsync( new UserProfile
    {
        id = "cosmosdb",
        Location = new Point (-122.12, 47.66)
    });
```

如果您沒有緯度和經度資訊，但具有實體位址或位置名稱（例如城市或國家/地區），您可以使用地理編碼服務（例如 Bing 地圖 REST 服務）來查詢實際的座標。 在 [這裡](https://msdn.microsoft.com/library/ff701713.aspx)深入了解 Bing Maps 地理編碼。

## <a name="next-steps"></a>後續步驟

既然您已了解如何開始使用 Azure Cosmos DB 中的地理空間支援，您可以接著：

* 深入了解 [Azure Cosmos DB 查詢](sql-query-getting-started.md)
* 深入瞭解如何[使用 Azure Cosmos DB 來查詢空間資料](sql-query-geospatial-query.md)
* 深入瞭解如何[使用 Azure Cosmos DB 編制索引空間資料](sql-query-geospatial-index.md)