---
title: 支援的資料格式詳細資料 |Microsoft Azure 對應
description: 瞭解如何在空間 IO 模組中剖析分隔空間資料。
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7227813f607ca18ee50f503a30b290414f333e21
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310164"
---
# <a name="supported-data-format-details"></a>支援的資料格式詳細資料

本文提供對所有 XML 標記和已知文字幾何類型的讀取和寫入支援的詳細資訊。 它也會詳細說明如何在空間 IO 模組中剖析分隔空間資料。

## <a name="supported-xml-namespaces"></a>支援的 XML 命名空間

空間 IO 模組支援來自下列命名空間的 XML 標記。

| 命名空間前置詞 | 命名空間 URI   | 注意                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | GeoRSS 檔案中的唯讀支援。           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | GeoRSS 檔案中的唯讀支援。                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | GPX 檔案中的唯讀支援。 剖析並使用 DisplayColor。 新增至圖形中繼資料的所有其他屬性。 |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | 在 GPX 檔案中支援。 使用線條色彩。 |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | 唯讀。 使用 Atom 格式的 GeoRSS 寫入。              |

## <a name="supported-xml-elements"></a>支援的 XML 元素

空間 IO 模組支援下列 XML 元素。 任何不支援的 XML 標記都會轉換成 JSON 物件。 然後，會將每個標記新增為 `properties` 父圖形或圖層之欄位中的屬性。

### <a name="kml-elements"></a>KML 元素

空間 IO 模組支援下列 KML 元素。

| 元素名稱         | 讀取    | 寫入   | 注意                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | partial | 是     | 物件已剖析，但不用於定點陣圖形。                                                                    |
| `AddressDetails`     | partial | 否      | 物件已剖析，但不用於定點陣圖形。                                                                    |
| `atom:author`        | 是     | 是     |                                                                                                                            |
| `atom:link`          | 是     | 是     |                                                                                                                            |
| `atom:name`          | 是     | 是     |                                                                                                                            |
| `BalloonStyle`       | partial | partial | 不支援 `displayMode`。 轉換成 `PopupTemplate` 。 若要寫入，請新增 `popupTemplate` 屬性做為您想要為其寫入之功能的屬性。 |
| `begin`              | 是     | 是     |                                                                                                                            |
| `color`              | 是     | 是     | 包含 `#AABBGGRR` 和 `#BBGGRR` 。 剖析為 CSS 色彩字串                                                           |
| `colorMode`          | 是     | 否      |                                                                                                                            |
| `coordinates`        | 是     | 是     |                                                                                                                            |
| `Data`               | 是     | 是     |                                                                                                                            |
| `description`        | 是     | 是     |                                                                                                                            |
| `displayName`        | 是     | 是     |                                                                                                                            |
| `Document`           | 是     | 是     |                                                                                                                            |
| `drawOrder`          | partial | 否      | 讀取地面重迭並用來排序它們。 
| `east`               | 是     | 是     |                                                                                                                            |
| `end`                | 是     | 是     |                                                                                                                            |
| `ExtendedData`       | 是     | 是     | 支援表單的不具類型 `Data` 、 `SimpleData` 或 `Schema` 和實體取代 `$[dataName]` 。                      |
| `extrude`            | partial | partial | 僅支援多邊形。 具有不同高度之多邊形的 MultiGeometry 會分成個別的功能。 不支援線條樣式。 高度為0的多邊形會轉譯為平面多邊形。 讀取時，會將外部環形中第一個座標的高度新增為多邊形的高度屬性。 然後，第一個座標的高度將用來呈現地圖上的多邊形。 |
| `fill`               | 是     | 是     |                                                                                                                            |
| `Folder`             | 是     | 是     |                                                                                                                            |
| `GroundOverlay`      | 是     | 是     | `color` 不受支援                                                                                                   |
| `heading`            | partial | 否      | 已剖析但未由轉譯 `SimpleDataLayer` 。 只有當資料儲存在圖形的屬性中時，才會寫入。                 |
| `hotSpot`            | 是     | partial | 只有當資料儲存在圖形的屬性中時，才會寫入。 單位只會輸出為「圖元」。                         |
| `href`               | 是     | 是     |                                                                                                                            |
| `Icon`               | partial | partial | 已剖析但未由轉譯 `SimpleDataLayer` 。 只有在包含 URI 資料時，才會寫入圖形的 icon 屬性。 只支援 `href`。 |
| `IconStyle`          | partial | partial | `icon`、 `heading` 、 `colorMode` 和 `hotspots` 值都經過剖析，但不是由 `SimpleDataLayer`         |
| `innerBoundaryIs`    | 是     | 是     |                                                                                                                            |
| `kml`                | 是     | 是     |                                                                                                                            |
| `LabelStyle`         | 否      | 否      |                                                                                                                            |
| `LatLonBox`          | 是     | 是     |                                                                                                                            |
| `gx:LatLonQuad`      | 是     | 是     |                                                                                                                            |
| `LinearRing`         | 是     | 是     |                                                                                                                            |
| `LineString`         | 是     | 是     |                                                                                                                            |
| `LineStyle`          | 是     | 是     | 不支援 `colorMode`。                                                                                         |
| `Link`               | 是     | 否      | `href`網路連結僅支援此屬性。                                                                   |
| `MultiGeometry`      | partial | partial | 可以在讀取時細分為個別功能。                                                                     |
| `name`               | 是     | 是     |                                                                                                                            |
| `NetworkLink`        | 是     | 否      | 連結必須在與檔相同的網域上。                                                                  |
| `NetworkLinkControl` | 否      | 否      |                                                                                                                            |
| `north`              | 是     | 是     |                                                                                                                            |
| `open`               | 是     | 是     |                                                                                                                            |
| `outerBoundaryIs`    | 是     | 是     |                                                                                                                            |
| `outline`            | 是     | 是     |                                                                                                                            |
| `overlayXY`          | 否      | 否      |                                                                                                                            |
| `Pair`               | partial | 否      | 僅 `normal` 支援中的樣式 `StyleMap` 。 不支援 `highlight`。                                   |
| `phoneNumber`        | 是     | 是     |                                                                                                                            |
| `PhotoOverlay`       | 否      | 否      |                                                                                                                            |
| `Placemark`          | 是     | 是     |                                                                                                                            |
| `Point`              | 是     | 是     |                                                                                                                            |
| `Polygon`            | 是     | 是     |                                                                                                                            |
| `PolyStyle`          | 是     | 是     |                                                                                                                            |
| `Region`             | partial | partial | `LatLongBox` 檔層級支援。                                                                      |
| `rotation`           | 否      | 否      |                                                                                                                            |
| `rotationXY`         | 否      | 否      |                                                                                                                            |
| `scale`              | 否      | 否      |                                                                                                                            |
| `Schema`             | 是     | 是     |                                                                                                                            |
| `SchemaData`         | 是     | 是     |                                                                                                                            |
| `schemaUrl`          | partial | 是     | 不支援從不包含在 KMZ 中的外部檔載入樣式。                             |
| `ScreenOverlay`      | 否      | 否      |                                                                                                                            |
| `screenXY`           | 否      | 否      |                                                                                                                            |
| `SimpleData`         | 是     | 是     |                                                                                                                            |
| `SimpleField`        | 是     | 是     |                                                                                                                            |
| `size`               | 否      | 否      |                                                                                                                            |
| `Snippet`            | partial | partial | `maxLines` 忽略屬性。                                                                                  |
| `south`              | 是     | 是     |                                                                                                                            |
| `Style`              | 是     | 是     |                                                                                                                            |
| `StyleMap`           | partial | 否      | 僅支援中的一般樣式 `StyleMap` 。                                                                        |
| `styleUrl`           | partial | 是     | 不支援外部樣式 Url。                                                                         |
| `text`               | 是     | 是     | `$[geDirections]`不支援取代                                                                          |
| `textColor`          | 是     | 是     |                                                                                                                            |
| `TimeSpan`           | 是     | 是     |                                                                                                                            |
| `TimeStamp`          | 是     | 是     |                                                                                                                            |
| `value`              | 是     | 是     |                                                                                                                            |
| `viewRefreshMode`    | partial | 否      |  如果指向 WMS 服務，則只 `onStop` 支援地面重迭。 會附加 `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` 至 URL，並在地圖移動時更新。  |
| `visibility`         | 是     | 是     |                                                                                                                            |
| `west`               | 是     | 是     |                                                                                                                            |
| `when`               | 是     | 是     |                                                                                                                            |
| `width`              | 是     | 是     |                                                                                                                            |

### <a name="georss-elements"></a>GeoRSS 元素

空間 IO 模組支援下列 GeoRSS 元素。

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
| `geo:lat`                | 是     | 否    | 以撰寫為 `georss:point` 。                                                                   |
| `geo:lon`                | 是     | 否    | 以撰寫為 `georss:point` 。                                                                   |
| `geo:long`               | 是     | 否    | 以撰寫為 `georss:point` 。                                                                   |
| `georss:box`             | 是     | 否    | 以多邊形的形式讀取，並指定 `subType` "Rectangle" 的屬性                                |
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
| `geourl:latitude`        | 是     | 否    | 以撰寫為 `georss:point` 。                                                                   |
| `geourl:longitude`       | 是     | 否    | 以撰寫為 `georss:point` 。                                                                   |
| `position`               | 是     | 否    | 某些 XML 摘要會以位置標籤包裝 GML，而不是使用標記來包裝 `georss:where` 。 將會讀取這個標記，但會使用標記來寫入 `georss:where` 。 |
| `rss`                    | 是     | 否    | 以 ATOM 格式撰寫的 GeoRSS。                                                                 |
| `rss:author`             | 是     | partial | 以撰寫為 `atom:author` 。                                                                 |
| `rss:category`           | 是     | partial | 以撰寫為 `atom:category` 。                                                               |
| `rss:channel`            | 是     | 否    |                                                                                                |
| `rss:cloud`              | 是     | 否    |                                                                                                |
| `rss:comments`           | 是     | 否    |                                                                                                |
| `rss:copyright`          | 是     | partial | 撰寫為 `atom:rights` if 圖形尚未有 `rights` `properties` 屬性。       |
| `rss:description`        | 是     | partial | 撰寫為 `atom:content` if 圖形尚未有 `content` `properties` 屬性。      |
| `rss:docs`               | 是     | 否    |                                                                                                |
| `rss:enclosure`          | 是     | 否    |                                                                                                |
| `rss:generator`          | 是     | 否    |                                                                                                |
| `rss:guid`               | 是     | partial | 撰寫為 `atom:id` if 圖形還沒有 `id` `properties` 屬性。         |
| `rss:image`              | 是     | partial | 撰寫為 `atom:logo` if 圖形尚未有 `logo` `properties` 屬性。      |
| `rss:item`               | 是     | partial | 以撰寫為 `atom:entry` 。                                                                  |
| `rss:language`           | 是     | 否    |                                                                                                |
| `rss:lastBuildDate`      | 是     | partial | 撰寫為 `atom:updated` if 圖形還沒有 `updated` `properties` 屬性。     |
| `rss:link`               | 是     | partial | 以撰寫為 `atom:link` 。                                                                   |
| `rss:managingEditor`     | 是     | partial | 以撰寫為 `atom:contributor` 。                                                            |
| `rss:pubDate`            | 是     | partial | 撰寫為 `atom:published` if 圖形尚未有 `published` `properties` 屬性。  |
| `rss:rating`             | 是     | 否    |                                                                                                |
| `rss:skipDays`           | 是     | 否    |                                                                                                |
| `rss:skipHours`          | 是     | 否    |                                                                                                |
| `rss:source`             | 是     | partial | 撰寫為 `atom:source` 包含的 `atom:link` 。                                       |
| `rss:textInput`          | 是     | 否    |                                                                                                |
| `rss:title`              | 是     | partial | 以撰寫為 `atom:title` 。                                                                  |
| `rss:ttl`                | 是     | 否    |                                                                                                |
| `rss:webMaster`          | 是     | 否    |                                                                                                |

### <a name="gml-elements"></a>GML 元素

空間 IO 模組支援下列 GML 元素。 

| 元素名稱            | 讀取 | 寫入 | 注意                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | 是  | 否    | 以撰寫為 `gml:posList` 。                                                              |
| `gml:curveMember`       | 是  | 否    |                                                                                        |
| `gml:curveMembers`      | 是  | 否    |                                                                                        |
| `gml:Box`               | 是  | 否    | 以撰寫為 `gml:Envelope` 。                                                             |
| `gml:description`       | 是  | 是   |                                                                                        |
| `gml:Envelope`          | 是  | 是   |                                                                                        |
| `gml:exterior`          | 是  | 是   |                                                                                        |
| `gml:Feature`           | 是  | 否    | 撰寫為圖形。                                                                    |
| `gml:FeatureCollection` | 是  | 否    | 以幾何集合的形式寫入。                                                      |
| `gml:featureMember`     | 是  | 否    | 以幾何集合的形式寫入。                                                      |
| `gml:geometry`          | 是  | 否    | 撰寫為圖形。                                                                    |
| `gml:geometryMember`    | 是  | 是   |                                                                                        |
| `gml:geometryMembers`   | 是  | 是   |                                                                                        |
| `gml:identifier`        | 是  | 是   |                                                                                        |
| `gml:innerBoundaryIs`   | 是  | 否    | 使用撰寫 `gml.interior` 的。                                                          |
| `gml:interior`          | 是  | 是   |                                                                                        |
| `gml:LinearRing`        | 是  | 是   |                                                                                        |
| `gml:LineString`        | 是  | 是   |                                                                                        |
| `gml:lineStringMember`  | 是  | 是   |                                                                                        |
| `gml:lineStringMembers` | 是  | 否    |                                                                                        |
| `gml:MultiCurve`        | 是  | 否    | 唯讀取 `gml:LineString` 成員。 撰寫為 `gml.MultiLineString`                  |
| `gml:MultiGeometry`     | partial  | partial   | 唯讀取為 FeatureCollection。                                              |
| `gml:MultiLineString`   | 是  | 是   |                                                                                        |
| `gml:MultiPoint`        | 是  | 是   |                                                                                        |
| `gml:MultiPolygon`      | 是  | 是   |                                                                                        |
| `gml:MultiSurface`      | 是  | 否    | 唯讀取 `gml:Polygon` 成員。 撰寫為 `gml.MultiPolygon`                        |
| `gml:name`              | 是  | 是   |                                                                                        |
| `gml:outerBoundaryIs`   | 是  | 否    | 使用撰寫 `gml.exterior` 的。                                                          |
| `gml:Point`             | 是  | 是   |                                                                                        |
| `gml:pointMember`       | 是  | 是   |                                                                                        |
| `gml:pointMembers`      | 是  | 否    |                                                                                        |
| `gml:Polygon`           | 是  | 是   |                                                                                        |
| `gml:polygonMember`     | 是  | 是   |                                                                                        |
| `gml:polygonMembers`    | 是  | 否    |                                                                                        |
| `gml:pos`               | 是  | 是   |                                                                                        |
| `gml:posList`           | 是  | 是   |                                                                                        |
| `gml:surfaceMember`     | 是  | 是   |                                                                                        |

#### <a name="additional-notes"></a>其他注意事項

- 成員專案會在子專案內，搜尋是否有可能是隱藏的幾何。 這是必要的搜尋作業，因為從 GML 延伸的 XML 格式可能不會將幾何放置為成員元素的直接子系。
- `srsName` 部分支援 WGS84 座標和下列程式碼：[EPSG： 4326](https://epsg.io/4326)) ，以及 web Mercator ([EPSG： 3857](https://epsg.io/3857) 或其中一個替代程式碼。 任何其他座標系統都會依原樣剖析為 WGS84。
- 除非在讀取 XML 摘要時指定，否則會根據 XML 摘要中的提示來決定軸順序。 「緯度，經度」軸順序會提供喜好設定。
- 除非在寫入 GML 檔案時，指定屬性的自訂 GML 命名空間，否則不會加入其他屬性資訊。

### <a name="gpx-elements"></a>GPX 元素

空間 IO 模組支援下列 GPX 元素。

| 元素名稱             | 讀取    | 寫入   | 注意                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | 是     | 是     |                                                                                             |
| `gpx:author`             | 是     | 是     |                                                                                             |
| `gpx:bounds`             | 是     | 是     | 在讀取時轉換為 LocationRect。                                                    |
| `gpx:cmt`                | 是     | 是     |                                                                                             |
| `gpx:copyright`          | 是     | 是     |                                                                                             |
| `gpx:desc`               | 是     | 是     | 在讀取時複製到 description 屬性，以配合其他 XML 格式。               |
| `gpx:dgpsid`             | 是     | 是     |                                                                                             |
| `gpx:ele`                | 是     | 是     |                                                                                             |
| `gpx:extensions`         | partial | partial | 讀取時，會解壓縮樣式資訊。 所有其他延伸模組都會壓平合併為簡單的 JSON 物件。 只會寫入圖形樣式資訊。 |
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
| `gpx:sym`                | 是     | 是     | 值是已被捕獲的，但不會用來改變圖釘圖示。                               |
| `gpx:text`               | 是     | 是     |                                                                                             |
| `gpx:time`               | 是     | 是     |                                                                                             |
| `gpx:trk`                | 是     | 是     |                                                                                             |
| `gpx:trkpt`              | 是     | 是     |                                                                                             |
| `gpx:trkseg`             | 是     | 是     |                                                                                             |
| `gpx:type`               | 是     | 是     |                                                                                             |
| `gpx:vdop`               | 是     | 是     |                                                                                             |
| `gpx:wpt`                | 是     | 是     |                                                                                             |
| `gpx_style:color`        | 是     | 是     |                                                                                             |
| `gpx_style:line`         | partial | partial | `color``opacity`支援、、、 `width` `lineCap` 。                                           |
| `gpx_style:opacity`      | 是     | 是     |                                                                                             |
| `gpx_style:width`        | 是     | 是     |                                                                                             |
| `gpxx:DisplayColor`      | 是     | 否      | 用來指定圖形的色彩。 寫入時， `gpx_style:line` 將改用色彩。  |
| `gpxx:RouteExtension`    | partial | 否      | 所有屬性都會讀入 `properties` 。 僅使用 `DisplayColor`。                     |
| `gpxx:TrackExtension`    | partial | 否      | 所有屬性都會讀入 `properties` 。 僅使用 `DisplayColor`。                     |
| `gpxx:WaypointExtension` | partial | 否      | 所有屬性都會讀入 `properties` 。 僅使用 `DisplayColor`。                     |
| `gpx:keywords`           | 是     | 是     |                                                                                             |
| `gpx:fix`                | 是     | 是     |                                                                                             |

#### <a name="additional-notes"></a>其他注意事項

撰寫時;

- MultiPoints 將會細分為個別的導航點。
- 多邊形和 MultiPolygons 會以軌跡的形式寫入。 
  
## <a name="supported-well-known-text-geometry-types"></a>支援的知名文字幾何類型

| Geometry 類型 | 讀取 | 寫入 |
|--------------|:----:|:-----:|
| 點 | x | x |
| 點 Z | x | x | 
| 點 M | x | x<sup>[2]</sup> |
| 點 ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| LINESTRING | x | x |
| LINESTRING Z | x | x | 
| LINESTRING M | x | x<sup>[2]</sup> |
| LINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| 多邊形 | x | x |
| 多邊形 Z | x | x |
| 多邊形 M | x | x<sup>[2]</sup> |
| 多邊形 ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| 點 | x | x |
| MULTIPOINT Z | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTILINESTRING | x | x |
| MULTILINESTRING Z | x | x | 
| MULTILINESTRING M | x | x<sup>[2]</sup> |
| MULTILINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOLYGON | x | x |
| MULTIPOLYGON Z | x | x | 
| MULTIPOLYGON M | x | x<sup>[2]</sup> |
| MULTIPOLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| >GEOMETRYCOLLECTION | x | x |
| >GEOMETRYCOLLECTION Z | x | x | 
| >GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> | 
| >GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1 \] ：只會在位置值中捕捉 Z 參數，並將其新增為第三個值。

\[\]未捕獲 2 M 參數。

## <a name="delimited-spatial-data-support"></a>分隔的空間資料支援

分隔的空間資料，例如 (CSV) 的逗點分隔值檔案，通常會有包含空間資料的資料行。 例如，可能會有包含緯度和經度資訊的資料行。 在知名的文字格式中，可能會有包含空間幾何資料的資料行。

### <a name="spatial-data-column-detection"></a>空間資料行偵測

當讀取包含空間資料的分隔檔案時，會分析標頭，以判斷哪些資料行包含位置欄位。 如果標頭包含型別資訊，將會用它來將資料格值轉換成適當的型別。 如果未指定標頭，則會分析第一個資料列，並使用它來產生標頭。 分析第一個資料列時，會執行檢查，以不區分大小寫的方式來比對資料行名稱與下列名稱。 如果檔案中有兩個以上的名稱，則名稱的順序會是優先權。

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

系統會掃描資料的第一個資料列是否有已知文字格式的字串。 

### <a name="delimited-data-column-types"></a>分隔資料行類型

掃描標頭資料列時，會將資料行名稱中的任何類型資訊解壓縮，並用來轉換該資料行中的資料格。 以下是具有類型值的資料行名稱範例： "ColumnName (typeName) "。 以下是支援的不區分大小寫類型名稱：

#### <a name="numbers"></a>數字

- edm。 int64
- int
- long
- edm. double
- FLOAT
- double
- number

#### <a name="booleans"></a>布林值

- edm。布林值
- bool
- boolean

#### <a name="dates"></a>日期

- edm. datetime
- date
- Datetime

#### <a name="geography"></a>[地理位置]

- edm. geography
- geography

#### <a name="strings"></a>字串

- edm。字串
- varchar
- text
- 字串

如果沒有可以從標頭中解壓縮的型別資訊，而且在讀取時啟用動態輸入選項，就會個別分析每個資料格，以判斷最適合轉換成的資料類型。

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

[讀取和寫入空間資料](spatial-io-read-write-spatial-data.md)
