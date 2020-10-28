---
title: 擴充 GeoJSON 幾何 |Microsoft Azure 對應
description: 瞭解 Azure 地圖服務如何擴充 GeoJSON 規格，以包含其他幾何圖形。 查看設定要在地圖中使用之圓形和矩形的範例。
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 87a5dec2582796c6703bfb37b76e26e53a1192c0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895778"
---
# <a name="extended-geojson-geometries"></a>擴充 GeoJSON 幾何

Azure 地圖服務提供可在地理功能內外搜尋的強大 Api 清單。 這些 Api 符合代表地理特徵的標準 [GeoJSON 規格][1] 。  

[GeoJSON 規格][1]僅支援下列幾何：

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

某些 Azure 地圖服務 Api 接受不屬於 [GeoJSON 規格][1]的幾何。例如，在 [幾何 API 內搜尋](/rest/api/maps/search/postsearchinsidegeometry) 會接受圓形和多邊形。

本文詳細說明 Azure 地圖服務如何延伸 [GeoJSON 規格][1]來代表特定的幾何。

## <a name="circle"></a>Circle

`Circle` [GeoJSON 規格][1]不支援幾何。我們會使用 `GeoJSON Point Feature` 物件來代表圓形。

`Circle`使用物件表示的幾何 `GeoJSON Feature` __必須__ 包含下列座標和屬性：

- Center

    圓形的中心會使用物件來表示 `GeoJSON Point` 。

- 半徑

    圓形的 `radius` 是以 `GeoJSON Feature` 的屬性表示。 半徑值的單位是 _公尺_ ，而且必須屬於型別 `double`。

- 子類型

    圓形幾何也必須包含 `subType` 屬性。 這個屬性必須是的屬性的一部分 `GeoJSON Feature` ，而且其值應為 _圓形_

#### <a name="example"></a>範例

以下是使用物件表示圓形的方式 `GeoJSON Feature` 。 讓我們將圓形置中的圓形：47.639754 和經度：-122.126986，並為其指派等於100計量的半徑：

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

## <a name="rectangle"></a>矩形

`Rectangle` [GeoJSON 規格][1]不支援幾何。我們會使用 `GeoJSON Polygon Feature` 物件來表示矩形。 矩形擴充功能主要是由 Web SDK 的繪圖工具模組所使用。

`Rectangle`使用物件表示的幾何 `GeoJSON Polygon Feature` __必須__ 包含下列座標和屬性：

- 角落

    矩形的邊角是使用物件的座標來表示 `GeoJSON Polygon` 。 應該有五個座標，每個角落各一個。 和第五個座標，與第一個座標相同，以關閉多邊形環形。 系統會假設這些座標一致，而且開發人員可以依需要來旋轉它們。

- 子類型

    矩形幾何也必須包含 `subType` 屬性。 這個屬性必須是的屬性的一部分 `GeoJSON Feature` ，而且其值應為 _矩形_

### <a name="example"></a>範例

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>後續步驟

深入瞭解如何在 Azure 地圖服務中 GeoJSON 資料：

> [!div class="nextstepaction"]
> [地理柵欄 GeoJSON 格式](geofence-geojson.md)

複習與 Azure 地圖服務和定位智慧應用程式相關聯的常見技術術語詞彙：

> [!div class="nextstepaction"]
> [Azure 地圖服務字彙](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946