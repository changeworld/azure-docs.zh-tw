---
title: 支援的資料格式詳細資訊 |微軟 Azure 地圖
description: 瞭解如何在空間 IO 模組中解析空間資料。
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3353620f1751e939a04543115fe704555fb3bc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334089"
---
# <a name="supported-data-format-details"></a>支援的資料格式詳細資訊

本文詳細介紹了對所有 XML 標記和已知文本幾何類型的讀寫支援。 它還詳細介紹了如何在空間 IO 模組中分析分隔的空間資料。

## <a name="supported-xml-namespaces"></a>支援的 XML 命名空間

空間 IO 模組支援來自以下命名空間的 XML 標記。

| 命名空間首碼 | 命名空間 URI   | 注意                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | 唯讀取 GeoRSS 檔中的支援。           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | 唯讀取 GeoRSS 檔中的支援。                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | 唯讀取 GPX 檔中的支援。 分析並使用顯示顏色。 添加到形狀中繼資料的所有其他屬性。 |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | GPX 檔中支援。 使用線顏色。 |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | 唯讀。 使用 Atom 格式寫入 GeoRSS。              |

## <a name="supported-xml-elements"></a>支援的 XML 元素

空間 IO 模組支援以下 XML 元素。 不支援的任何 XML 標記都將轉換為 JSON 物件。 然後，每個標記將添加為`properties`父形狀或圖層欄位中的屬性。

### <a name="kml-elements"></a>KML 元件

空間 IO 模組支援以下 KML 元素。

| 元素名稱         | 讀取    | 寫入   | 注意                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | 部分 | 是     | 物件已解析，但不用於定位形狀。                                                                    |
| `AddressDetails`     | 部分 | 否      | 物件已解析，但不用於定位形狀。                                                                    |
| `atom:author`        | 是     | 是     |                                                                                                                            |
| `atom:link`          | 是     | 是     |                                                                                                                            |
| `atom:name`          | 是     | 是     |                                                                                                                            |
| `BalloonStyle`       | 部分 | 部分 | 不支援 `displayMode`。 轉換為 。 `PopupTemplate` 要編寫，請添加`popupTemplate`屬性作為要為其編寫的要素的屬性。 |
| `begin`              | 是     | 是     |                                                                                                                            |
| `color`              | 是     | 是     | 包括`#AABBGGRR``#BBGGRR`和 。 解析為 CSS 顏色字串                                                           |
| `colorMode`          | 是     | 否      |                                                                                                                            |
| `coordinates`        | 是     | 是     |                                                                                                                            |
| `Data`               | 是     | 是     |                                                                                                                            |
| `description`        | 是     | 是     |                                                                                                                            |
| `displayName`        | 是     | 是     |                                                                                                                            |
| `Document`           | 是     | 是     |                                                                                                                            |
| `drawOrder`          | 部分 | 否      | 讀取地面疊加並用於對其進行排序。 
| `east`               | 是     | 是     |                                                                                                                            |
| `end`                | 是     | 是     |                                                                                                                            |
| `ExtendedData`       | 是     | 是     | 支援表單 的`Data`未`SimpleData`鍵入`Schema`或 和實體替換`$[dataName]`。                      |
| `extrude`            | 部分 | 部分 | 僅支援面。 具有不同高度的多邊形的多幾何體將分解為單個要素。 不支援行樣式。 高度為 0 的多邊形將呈現為平面多邊形。 讀取時，外部環中第一個座標的高度將添加為多邊形的高度屬性。 然後，第一個座標的高度將用於渲染地圖上的多邊形。 |
| `fill`               | 是     | 是     |                                                                                                                            |
| `Folder`             | 是     | 是     |                                                                                                                            |
| `GroundOverlay`      | 是     | 是     | `color`不受支援                                                                                                   |
| `heading`            | 部分 | 否      | 已分析但未由`SimpleDataLayer`呈現。 僅當資料存儲在形狀的屬性中時，才寫入。                 |
| `hotSpot`            | 是     | 部分 | 僅當資料存儲在形狀的屬性中時，才寫入。 單位僅輸出為"圖元"。                         |
| `href`               | 是     | 是     |                                                                                                                            |
| `Icon`               | 部分 | 部分 | 已分析但未由`SimpleDataLayer`呈現。 僅當形狀包含 URI 資料時，才寫入形狀的圖示屬性。 只支援 `href`。 |
| `IconStyle`          | 部分 | 部分 | `icon``heading`、、`colorMode`和`hotspots`值進行解析，但它們不由`SimpleDataLayer`         |
| `innerBoundaryIs`    | 是     | 是     |                                                                                                                            |
| `kml`                | 是     | 是     |                                                                                                                            |
| `LabelStyle`         | 否      | 否      |                                                                                                                            |
| `LatLonBox`          | 是     | 是     |                                                                                                                            |
| `gx:LatLonQuad`      | 是     | 是     |                                                                                                                            |
| `LinearRing`         | 是     | 是     |                                                                                                                            |
| `LineString`         | 是     | 是     |                                                                                                                            |
| `LineStyle`          | 是     | 是     | 不支援 `colorMode`。                                                                                         |
| `Link`               | 是     | 否      | 網路連結`href`僅支援該屬性。                                                                   |
| `MultiGeometry`      | 部分 | 部分 | 讀取時可能會分解為單個功能。                                                                     |
| `name`               | 是     | 是     |                                                                                                                            |
| `NetworkLink`        | 是     | 否      | 連結必須與文檔位於同一域上。                                                                  |
| `NetworkLinkControl` | 否      | 否      |                                                                                                                            |
| `north`              | 是     | 是     |                                                                                                                            |
| `open`               | 是     | 是     |                                                                                                                            |
| `outerBoundaryIs`    | 是     | 是     |                                                                                                                            |
| `outline`            | 是     | 是     |                                                                                                                            |
| `overlayXY`          | 否      | 否      |                                                                                                                            |
| `Pair`               | 部分 | 否      | 僅支援`normal`中的`StyleMap`樣式。 不支援 `highlight`。                                   |
| `phoneNumber`        | 是     | 是     |                                                                                                                            |
| `PhotoOverlay`       | 否      | 否      |                                                                                                                            |
| `Placemark`          | 是     | 是     |                                                                                                                            |
| `Point`              | 是     | 是     |                                                                                                                            |
| `Polygon`            | 是     | 是     |                                                                                                                            |
| `PolyStyle`          | 是     | 是     |                                                                                                                            |
| `Region`             | 部分 | 部分 | `LatLongBox`在文檔級別支援。                                                                      |
| `rotation`           | 否      | 否      |                                                                                                                            |
| `rotationXY`         | 否      | 否      |                                                                                                                            |
| `scale`              | 否      | 否      |                                                                                                                            |
| `Schema`             | 是     | 是     |                                                                                                                            |
| `SchemaData`         | 是     | 是     |                                                                                                                            |
| `schemaUrl`          | 部分 | 是     | 不支援從 KMZ 中未包括的外部文檔載入樣式。                             |
| `ScreenOverlay`      | 否      | 否      |                                                                                                                            |
| `screenXY`           | 否      | 否      |                                                                                                                            |
| `SimpleData`         | 是     | 是     |                                                                                                                            |
| `SimpleField`        | 是     | 是     |                                                                                                                            |
| `size`               | 否      | 否      |                                                                                                                            |
| `Snippet`            | 部分 | 部分 | `maxLines`屬性將被忽略。                                                                                  |
| `south`              | 是     | 是     |                                                                                                                            |
| `Style`              | 是     | 是     |                                                                                                                            |
| `StyleMap`           | 部分 | 否      | 僅支援 中`StyleMap`的正常樣式。                                                                        |
| `styleUrl`           | 部分 | 是     | 不支援外部樣式 URL。                                                                         |
| `text`               | 是     | 是     | 不支援替換`$[geDirections]`                                                                          |
| `textColor`          | 是     | 是     |                                                                                                                            |
| `TimeSpan`           | 是     | 是     |                                                                                                                            |
| `TimeStamp`          | 是     | 是     |                                                                                                                            |
| `value`              | 是     | 是     |                                                                                                                            |
| `viewRefreshMode`    | 部分 | 否      |  如果指向 WMS 服務，則僅`onStop`支援接地覆蓋。 將追加`BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]`到 URL 並隨著地圖移動而更新。  |
| `visibility`         | 是     | 是     |                                                                                                                            |
| `west`               | 是     | 是     |                                                                                                                            |
| `when`               | 是     | 是     |                                                                                                                            |
| `width`              | 是     | 是     |                                                                                                                            |

### <a name="georss-elements"></a>地理RSS元素

空間 IO 模組支援以下地理 RSS 元素。

| 元素名稱             | 讀取    | 寫入 | 注意                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | 是     | 是   |                                                                                                |
| `atom:category`          | 是     | 是   |                                                                                                |
| `atom:content`           | 是     | 是   |                                                                                                |
| `atom:contributor`       | 是     | 是   |                                                                                                |
| `atom:email`             | 是     | 是   |                                                                                                |
| `atom:entry`             | 是     | 是   |                                                                                                |
| `atom:feed`              | 是     | 是   |                                                                                                |
| `atom:icon`              | 是     | 是   |                                                                                                |
| `atom:id`                | 是     | 是   |                                                                                                |
| `atom:link`              | 是     | 是   |                                                                                                |
| `atom:logo`              | 是     | 是   |                                                                                                |
| `atom:name`              | 是     | 是   |                                                                                                |
| `atom:published`         | 是     | 是   |                                                                                                |
| `atom:rights`            | 是     | 是   |                                                                                                |
| `atom:source`            | 是     | 是   |                                                                                                |
| `atom:subtitle`          | 是     | 是   |                                                                                                |
| `atom:summary`           | 是     | 是   |                                                                                                |
| `atom:title`             | 是     | 是   |                                                                                                |
| `atom:updated`           | 是     | 是   |                                                                                                |
| `atom:uri`               | 是     | 是   |                                                                                                |
| `geo:lat`                | 是     | 否    | 寫成 . `georss:point`                                                                   |
| `geo:lon`                | 是     | 否    | 寫成 . `georss:point`                                                                   |
| `geo:long`               | 是     | 否    | 寫成 . `georss:point`                                                                   |
| `georss:box`             | 是     | 否    | 作為多邊形讀取，`subType`並給出"矩形"的屬性                                |
| `georss:circle`          | 是     | 是   |                                                                                                |
| `georss:elev`            | 是     | 是   |                                                                                                |
| `georss:featurename`     | 是     | 是   |                                                                                                |
| `georss:featuretypetag`  | 是     | 是   |                                                                                                |
| `georss:floor`           | 是     | 是   |                                                                                                |
| `georss:line`            | 是     | 是   |                                                                                                |
| `georss:point`           | 是     | 是   |                                                                                                |
| `georss:polygon`         | 是     | 是   |                                                                                                |
| `georss:radius`          | 是     | 是   |                                                                                                |
| `georss:relationshiptag` | 是     | 是   |                                                                                                |
| `georss:where`           | 是     | 是   |                                                                                                |
| `geourl:latitude`        | 是     | 否    | 寫成 . `georss:point`                                                                   |
| `geourl:longitude`       | 是     | 否    | 寫成 . `georss:point`                                                                   |
| `position`               | 是     | 否    | 某些 XML 源將用位置標記包裝 GML，而不是用`georss:where`標記包裝它。 將讀取此標記，但將使用`georss:where`標記寫入。 |
| `rss`                    | 是     | 否    | 以 ATOM 格式編寫的地理RSS。                                                                 |
| `rss:author`             | 是     | 部分 | 寫成 . `atom:author`                                                                 |
| `rss:category`           | 是     | 部分 | 寫成 . `atom:category`                                                               |
| `rss:channel`            | 是     | 否    |                                                                                                |
| `rss:cloud`              | 是     | 否    |                                                                                                |
| `rss:comments`           | 是     | 否    |                                                                                                |
| `rss:copyright`          | 是     | 部分 | 編寫為`atom:rights`if 形狀已沒有`rights``properties`屬性。       |
| `rss:description`        | 是     | 部分 | 編寫為`atom:content`if 形狀已沒有`content``properties`屬性。      |
| `rss:docs`               | 是     | 否    |                                                                                                |
| `rss:enclosure`          | 是     | 否    |                                                                                                |
| `rss:generator`          | 是     | 否    |                                                                                                |
| `rss:guid`               | 是     | 部分 | 編寫為`atom:id`if 形狀已沒有`id``properties`屬性。         |
| `rss:image`              | 是     | 部分 | 編寫為`atom:logo`if 形狀已沒有`logo``properties`屬性。      |
| `rss:item`               | 是     | 部分 | 寫成 . `atom:entry`                                                                  |
| `rss:language`           | 是     | 否    |                                                                                                |
| `rss:lastBuildDate`      | 是     | 部分 | 編寫為`atom:updated`if 形狀已沒有`updated``properties`屬性。     |
| `rss:link`               | 是     | 部分 | 寫成 . `atom:link`                                                                   |
| `rss:managingEditor`     | 是     | 部分 | 寫成 . `atom:contributor`                                                            |
| `rss:pubDate`            | 是     | 部分 | 編寫為`atom:published`if 形狀已沒有`published``properties`屬性。  |
| `rss:rating`             | 是     | 否    |                                                                                                |
| `rss:skipDays`           | 是     | 否    |                                                                                                |
| `rss:skipHours`          | 是     | 否    |                                                                                                |
| `rss:source`             | 是     | 部分 | 寫成包含`atom:source`的`atom:link`。                                       |
| `rss:textInput`          | 是     | 否    |                                                                                                |
| `rss:title`              | 是     | 部分 | 寫成 . `atom:title`                                                                  |
| `rss:ttl`                | 是     | 否    |                                                                                                |
| `rss:webMaster`          | 是     | 否    |                                                                                                |

### <a name="gml-elements"></a>GML 元素

空間 IO 模組支援以下 GML 元素。 

| 元素名稱            | 讀取 | 寫入 | 注意                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | 是  | 否    | 寫成`gml:posList`.                                                              |
| `gml:curveMember`       | 是  | 否    |                                                                                        |
| `gml:curveMembers`      | 是  | 否    |                                                                                        |
| `gml:Box`               | 是  | 否    | 寫成`gml:Envelope`.                                                             |
| `gml:description`       | 是  | 是   |                                                                                        |
| `gml:Envelope`          | 是  | 是   |                                                                                        |
| `gml:exterior`          | 是  | 是   |                                                                                        |
| `gml:Feature`           | 是  | 否    | 寫成形狀。                                                                    |
| `gml:FeatureCollection` | 是  | 否    | 編寫為幾何集合。                                                      |
| `gml:featureMember`     | 是  | 否    | 編寫為幾何集合。                                                      |
| `gml:geometry`          | 是  | 否    | 寫成形狀。                                                                    |
| `gml:geometryMember`    | 是  | 是   |                                                                                        |
| `gml:geometryMembers`   | 是  | 是   |                                                                                        |
| `gml:identifier`        | 是  | 是   |                                                                                        |
| `gml:innerBoundaryIs`   | 是  | 否    | 使用`gml.interior`編寫。                                                          |
| `gml:interior`          | 是  | 是   |                                                                                        |
| `gml:LinearRing`        | 是  | 是   |                                                                                        |
| `gml:LineString`        | 是  | 是   |                                                                                        |
| `gml:lineStringMember`  | 是  | 是   |                                                                                        |
| `gml:lineStringMembers` | 是  | 否    |                                                                                        |
| `gml:MultiCurve`        | 是  | 否    | 唯讀取`gml:LineString`成員。 寫為`gml.MultiLineString`                  |
| `gml:MultiGeometry`     | 部分  | 部分   | 僅作為功能集合讀取。                                              |
| `gml:MultiLineString`   | 是  | 是   |                                                                                        |
| `gml:MultiPoint`        | 是  | 是   |                                                                                        |
| `gml:MultiPolygon`      | 是  | 是   |                                                                                        |
| `gml:MultiSurface`      | 是  | 否    | 唯讀取`gml:Polygon`成員。 寫為`gml.MultiPolygon`                        |
| `gml:name`              | 是  | 是   |                                                                                        |
| `gml:outerBoundaryIs`   | 是  | 否    | 使用`gml.exterior`編寫。                                                          |
| `gml:Point`             | 是  | 是   |                                                                                        |
| `gml:pointMember`       | 是  | 是   |                                                                                        |
| `gml:pointMembers`      | 是  | 否    |                                                                                        |
| `gml:Polygon`           | 是  | 是   |                                                                                        |
| `gml:polygonMember`     | 是  | 是   |                                                                                        |
| `gml:polygonMembers`    | 是  | 否    |                                                                                        |
| `gml:pos`               | 是  | 是   |                                                                                        |
| `gml:posList`           | 是  | 是   |                                                                                        |
| `gml:surfaceMember`     | 是  | 是   |                                                                                        |

#### <a name="additional-notes"></a>附加說明

- 將搜索成員元素，查找可能埋在子項目中的幾何體。 此搜索操作是必需的，因為許多從 GML 擴展的 XML 格式可能不會將幾何體作為成員元素的直接子級放置。
- `srsName`WGS84 座標和以下代碼部分支援[：EPSG：4326](https://epsg.io/4326)）和 Web Mercator[（EPSG：3857](https://epsg.io/3857)或其替代代碼之一）。 任何其他坐標系將解析為 WGS84。
- 除非在讀取 XML 源時指定，否則軸順序將根據 XML 源中的提示確定。 對"緯度、經度"軸順序給予首選項。
- 除非在寫入 GML 檔時為屬性指定了自訂 GML 命名空間，否則不會添加其他屬性資訊。

### <a name="gpx-elements"></a>GPX 元素

空間 IO 模組支援以下 GPX 元素。

| 元素名稱             | 讀取    | 寫入   | 注意                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | 是     | 是     |                                                                                             |
| `gpx:author`             | 是     | 是     |                                                                                             |
| `gpx:bounds`             | 是     | 是     | 讀取時轉換為位置重新使用。                                                    |
| `gpx:cmt`                | 是     | 是     |                                                                                             |
| `gpx:copyright`          | 是     | 是     |                                                                                             |
| `gpx:desc`               | 是     | 是     | 讀取時複製到描述屬性以與其他 XML 格式對齊。               |
| `gpx:dgpsid`             | 是     | 是     |                                                                                             |
| `gpx:ele`                | 是     | 是     |                                                                                             |
| `gpx:extensions`         | 部分 | 部分 | 讀取時，將提取樣式資訊。 所有其他擴展都將拼合成一個簡單的 JSON 物件。 只寫入形狀樣式資訊。 |
| `gpx:geoidheight`        | 是     | 是     |                                                                                             |
| `gpx:gpx`                | 是     | 是     |                                                                                             |
| `gpx:hdop`               | 是     | 是     |                                                                                             |
| `gpx:link`               | 是     | 是     |                                                                                             |
| `gpx:magvar`             | 是     | 是     |                                                                                             |
| `gpx:metadata`           | 是     | 是     |                                                                                             |
| `gpx:name`               | 是     | 是     |                                                                                             |
| `gpx:pdop`               | 是     | 是     |                                                                                             |
| `gpx:rte`                | 是     | 是     |                                                                                             |
| `gpx:rtept`              | 是     | 是     |                                                                                             |
| `gpx:sat`                | 是     | 是     |                                                                                             |
| `gpx:src`                | 是     | 是     |                                                                                             |
| `gpx:sym`                | 是     | 是     | 值被捕獲，但它不用於更改圖釘圖示。                               |
| `gpx:text`               | 是     | 是     |                                                                                             |
| `gpx:time`               | 是     | 是     |                                                                                             |
| `gpx:trk`                | 是     | 是     |                                                                                             |
| `gpx:trkpt`              | 是     | 是     |                                                                                             |
| `gpx:trkseg`             | 是     | 是     |                                                                                             |
| `gpx:type`               | 是     | 是     |                                                                                             |
| `gpx:vdop`               | 是     | 是     |                                                                                             |
| `gpx:wpt`                | 是     | 是     |                                                                                             |
| `gpx_style:color`        | 是     | 是     |                                                                                             |
| `gpx_style:line`         | 部分 | 部分 | `color``opacity` `width` `lineCap`                                           |
| `gpx_style:opacity`      | 是     | 是     |                                                                                             |
| `gpx_style:width`        | 是     | 是     |                                                                                             |
| `gpxx:DisplayColor`      | 是     | 否      | 用於指定形狀的顏色。 書寫時，`gpx_style:line`將改為使用顏色。  |
| `gpxx:RouteExtension`    | 部分 | 否      | 所有屬性都讀入`properties`。 僅使用 `DisplayColor`。                     |
| `gpxx:TrackExtension`    | 部分 | 否      | 所有屬性都讀入`properties`。 僅使用 `DisplayColor`。                     |
| `gpxx:WaypointExtension` | 部分 | 否      | 所有屬性都讀入`properties`。 僅使用 `DisplayColor`。                     |
| `gpx:keywords`           | 是     | 是     |                                                                                             |
| `gpx:fix`                | 是     | 是     |                                                                                             |

#### <a name="additional-notes"></a>附加說明

寫作時;

- 多點將分為單獨的航點。
- 多邊形和多多邊形將寫入軌道。 
  
## <a name="supported-well-known-text-geometry-types"></a>支援的已知文本幾何類型

| 幾何類型 | 讀取 | 寫入 |
|--------------|:----:|:-----:|
| 點 | x | x |
| 點 Z | x | x | 
| 點 M | x | x<sup>[2]</sup> |
| 點 ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| 線串 | x | x |
| 線林斯林茨 | x | x | 
| 線林斯林 M | x | x<sup>[2]</sup> |
| 線林茨林茲姆 | x<sup>[1]</sup><sup>[2]</sup> | | 
| 多邊形 | x | x |
| POLYGON Z | x | x |
| POLYGON M | x | x<sup>[2]</sup> |
| POLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| 點 | x | x |
| 多點 Z | x | x | 
| 多點 M | x | x<sup>[2]</sup> |
| 波多點丁茨姆 | x<sup>[1]</sup><sup>[2]</sup> | | 
| 多線字串 | x | x |
| 多林斯林茨茨 | x | x | 
| 多林斯特林格 M | x | x<sup>[2]</sup> |
| 多林斯林茨 | x<sup>[1]</sup><sup>[2]</sup> | | 
| 多多隆 | x | x |
| 多波利貢 Z | x | x | 
| 多波利貢 M | x | x<sup>[2]</sup> |
| 多波利貢茲姆 | x<sup>[1]</sup><sup>[2]</sup> | | 
| 幾何 | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1\]在"位置"值中僅捕獲 Z 參數並作為第三個值添加。

\[2\] M 參數未捕獲。

## <a name="delimited-spatial-data-support"></a>空間資料有限

分隔空間資料（如逗號分隔的值檔 （CSV））通常具有包含空間資料的列。 例如，可能有包含緯度和經度資訊的列。 在"已知文本"格式中，可能有一個列包含空間幾何資料。

### <a name="spatial-data-column-detection"></a>空間資料列檢測

讀取包含空間資料的分隔檔時，將分析標頭以確定哪些列包含位置欄位。 如果標頭包含類型資訊，則它將用於將儲存格值轉換為適當的類型。 如果未指定標頭，則將分析第一行並用於生成標頭。 分析第一行時，將執行一項檢查，以不區分大小寫的方式將列名稱與以下名稱匹配。 名稱的順序是優先順序，如果檔中存在兩個或多個名稱。

#### <a name="latitude"></a>緯度

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>經度

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>Elevation

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>[地理位置]

第一行資料將掃描為已知文本格式的字串。 

### <a name="delimited-data-column-types"></a>分隔的資料列類型

掃描標題列時，將提取列名稱中的任何類型資訊，並用於在該行中強制轉換儲存格。 下面是具有類型值的列名稱的示例："列名（類型名稱）"。 支援以下不區分大小寫的類型名稱：

#### <a name="numbers"></a>數字

- edm.int64
- int
- long
- edm.double
- FLOAT
- double
- number

#### <a name="booleans"></a>布林值

- 埃德姆.布林
- bool
- boolean

#### <a name="dates"></a>日期

- edm.日期時間
- date
- Datetime

#### <a name="geography"></a>[地理位置]

- edm.地理
- geography

#### <a name="strings"></a>字串

- edm.字串
- varchar
- text
- 字串

如果無法從標頭中提取類型資訊，並且讀取時啟用了動態鍵入選項，則將單獨分析每個儲存格以確定最適合將其轉換為哪種資料類型。

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [讀取和寫入空間資料](spatial-io-read-write-spatial-data.md)
