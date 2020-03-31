---
title: 地理JSON地理圍欄資料格式 |微軟 Azure 地圖
description: 在本文中，您將瞭解如何準備可在 Microsoft Azure 地圖 GET 和 POST 地理圍欄 API 中使用的地理圍欄資料。
author: philmea
ms.author: philmea
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7b9860908dd3bdf3dcda727f350578a97b890cac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335610"
---
# <a name="geofencing-geojson-data"></a>地理柵欄 GeoJSON 資料

Azure 地圖服務 [GET Geofence](/rest/api/maps/spatial/getgeofence) 和 [POST Geofence](/rest/api/maps/spatial/postgeofence) API 可讓您擷取與所提供地理柵欄或柵欄集合相對之座標的範圍。 本文會說明如何準備可用於 Azure 地圖服務 GET 和 POST API 中的地理柵欄資料。

地理柵欄或地理柵欄集合的資料，是以 `GeoJSON` 格式的 `Feature` 物件和 `FeatureCollection` 物件來代表，該格式已定義於 [rfc7946](https://tools.ietf.org/html/rfc7946) \(英文\)中。 此外：

* GeoJSON 物件類型可以是 `Feature` 物件或 `FeatureCollection` 物件。
* Geometry 物件類型可以是 `Point`、`MultiPoint`、`LineString`、`MultiLineString`、`Polygon`、`MultiPolygon` 及 `GeometryCollection`。
* 所有特徵屬性都應包含 `geometryId`，它是用來識別地理柵欄。
* 具有 `Point`、`MultiPoint`、`LineString`、`MultiLineString` 的特徵必須在屬性中包含 `radius`。 `radius` 值是以公尺為單位測量，`radius` 值的範圍是 1 至 10000。
* 具有 `polygon` 和 `multipolygon` 幾何類型的特徵並不具有半徑屬性。
* `validityTime` 是選擇性的屬性，它可讓使用者針對地理柵欄資料設定到期時間和有效時間期間。 若未指定，該資料永遠不會到期且永遠有效。
* `expiredTime` 是地理柵欄資料的到期日期和時間。 如果要求中 `userTime` 的值比此值還要晚，系統會將對應的地理柵欄資料視為到期資料，且不會查詢它。 這會使此地理柵欄資料的 geometryId 被包含在地理柵欄回應內的 `expiredGeofenceGeometryId` 陣列中。
* `validityPeriod` 是地理柵欄有效時間期間的清單。 如果要求中 `userTime` 的值落在有效期間之外，系統會將對應的地理柵欄資料視為無效，且不會查詢它。 此地理柵欄資料的 geometryId 被包含在地理柵欄回應內的 `invalidPeriodGeofenceGeometryId` 陣列中。 下表說明 validityPeriod 元素的屬性。

| 名稱 | 類型 | 必要  | 描述 |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datetime  | true | 有效時間期間的開始日期時間。 |
| EndTime   | Datetime  | true |  有效時間期間的結束日期時間。 |
| recurrenceType | 字串 | false |   期間的週期類型。 值可為 `Daily`、`Weekly`、`Monthly`，或 `Yearly`。 預設值為 `Daily`。|
| businessDayOnly | Boolean | false |  指出資料是否僅在工作日有效。 預設值為 `false`。|


* 所有座標值都表示為 [經度、緯度]`WGS84`定義。
* 針對每個 Feature (其包含 `MultiPoint`、`MultiLineString`、`MultiPolygon`，或 `GeometryCollection`)，那些屬性會套用到所有元素上。 例如：中的所有`MultiPoint`點都將使用相同的半徑來形成多個圓地理圍欄。
* 在點-圓形的案例中，圓形幾何可以使用 `Point` 幾何物件來代表，搭配在[擴充 GeoJSON 幾何](https://docs.microsoft.com/azure/azure-maps/extend-geojson)中所述的屬性。      

下面是以使用中心點和半徑的 `GeoJSON` 格式，顯示為圓形地理柵欄幾何之地理柵欄的範例要求本文。 地理柵欄的有效期間從 2018 年 10 月 22 日開始，從上午 9 點到下午 5 點，並於週末之外的每日重複。 `expiredTime` 指出此地理柵欄資料會在要求中的 `userTime` 晚於 `2019-01-01`的情況下被視為失效。  

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "geometryId" : "1",
        "subType": "Circle",
        "radius": 500,
        "validityTime": 
        {
            "expiredTime": "2019-01-01T00:00:00",
            "validityPeriod": [
                {
                    "startTime": "2018-10-22T09:00:00",
                    "endTime": "2018-10-22T17:00:00",
                    "recurrenceType": "Daily",
                    "recurrenceFrequency": 1,
                    "businessDayOnly": true
                }
            ]
        }
    }
}
```
