---
title: 擴展的 GeoJSON 幾何體 |微軟 Azure 地圖
description: 在本文中，您將瞭解 Microsoft Azure 映射如何擴展 GeoJSON 規範以表示某些幾何體。
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 98db10f0fc7a417f39d4bb00e77af6bdea034a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276396"
---
# <a name="extended-geojson-geometries"></a>擴展的 GeoJSON 幾何體

Azure 地圖提供了強大的 API 清單，用於在地理要素內部和沿線進行搜索。 這些 API 符合表示地理特徵的標準[GeoJSON 規範][1]。  

[GeoJSON 規範][1]僅支援以下幾何體：

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

某些 Azure 地圖 API 接受不屬於[GeoJSON 規範][1]的幾何圖形。例如，[搜索幾何體](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)內部 API 接受圓形和多邊形。

本文詳細說明 Azure 地圖服務如何延伸 [GeoJSON 規格][1]來代表特定的幾何。

## <a name="circle"></a>Circle

`Circle` [GeoJSON 規範][1]不支援幾何體。我們使用物件`GeoJSON Point Feature`來表示一個圓。

使用`Circle`物件表示的`GeoJSON Feature`幾何必須包含__must__以下座標和屬性：

- 中心

    圓的中心使用`GeoJSON Point`物件表示。

- 半徑

    圓形的 `radius` 是以 `GeoJSON Feature` 的屬性表示。 半徑值的單位是_公尺_，而且必須屬於型別 `double`。

- 子類型

    圓形幾何也必須包含 `subType` 屬性。 此屬性必須是 屬性的一部分，`GeoJSON Feature`其值應為_Circle_

#### <a name="example"></a>範例

以下是如何使用`GeoJSON Feature`物件表示圓的方式。 讓我們在緯度處圍繞圓：47.639754和經度：-122.126986，並指定半徑等於100米：

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

`Rectangle` [GeoJSON 規範][1]不支援幾何體。我們使用物件`GeoJSON Polygon Feature`來表示矩形。 矩形擴展主要用於 Web SDK 的繪圖工具模組。

使用`Rectangle`物件表示的`GeoJSON Polygon Feature`幾何必須包含__must__以下座標和屬性：

- 角落

    矩形的角使用`GeoJSON Polygon`物件的座標表示。 應該有五個座標，每個角一個。 並且，第五個座標與第一個座標相同，以關閉多邊形環。 假定這些座標組齊，並且開發人員可以根據需要旋轉它們。

- 子類型

    矩形幾何圖形還必須包含該`subType`屬性。 此屬性必須是`GeoJSON Feature`屬性的一部分，其值應為_矩形_

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

在 Azure 地圖中瞭解有關 GeoJSON 資料的更多內容：

> [!div class="nextstepaction"]
> [地理圍欄地理JSON格式](geofence-geojson.md)

查看與 Azure 地圖和位置智慧應用程式關聯的常見技術術語的詞彙表：

> [!div class="nextstepaction"]
> [Azure 地圖詞彙表](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
