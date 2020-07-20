---
title: 建立地圖的資料來源 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Web SDK 來建立資料來源，並將其新增至地圖。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: d0334e03f2d4f34913f2f96610868b5ffe169013
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86242554"
---
# <a name="create-a-data-source"></a>建立資料來源

Azure 地圖服務 Web SDK 會將資料儲存在資料來源中。 使用資料來源可將資料作業優化以進行查詢和呈現。 目前有兩種類型的資料來源：

**GeoJSON 資料來源**

以 GeoJSON 為基礎的資料來源會載入，並使用類別在本機儲存資料 `DataSource` 。 您可以使用 GeoJSON 資料，以[資料](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data)命名空間中的 helper 類別手動建立或建立。 `DataSource`類別會提供用來匯入本機或遠端 GeoJSON 檔案的函式。 遠端 GeoJSON 檔必須裝載在啟用 CORs 的端點上。 `DataSource`類別會提供群集點資料的功能。 而且，您可以使用類別輕鬆地新增、移除和更新資料 `DataSource` 。 下列程式碼會示範如何在 Azure 地圖服務中建立 GeoJSON 資料。

```Javascript
//Create raw GeoJSON object.
var rawGeoJson = {
     "type": "Feature",
     "geometry": {
         "type": "Point",
         "coordinates": [-100, 45]
     },
     "properties": {
         "custom-property": "value"
     }
};

//Create GeoJSON using helper classes (less error prone).
var geoJsonClass = new atlas.data.Feature(new atlas.data.Point([-100, 45]), {
    "custom-property": "value"
}); 
```

建立之後，您可以透過 `map.sources` 屬性（ [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager)）將資料來源加入至對應。 下列程式碼示範如何建立 `DataSource` ，並將它加入至對應。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

下列程式碼顯示 GeoJSON 資料可以加入至的不同方式 `DataSource` 。

```Javascript
//GeoJsonData in the following code can be a single or array of GeoJSON features or geometries, a GeoJSON feature colleciton, or a single or array of atlas.Shape objects.

//Add geoJSON object to data source. 
dataSource.add(geoJsonData);

//Load geoJSON data from URL. URL should be on a CORs enabled endpoint.
dataSource.importDataFromUrl(geoJsonUrl);

//Overwrite all data in data source.
dataSource.setShapes(geoJsonData);
```

> [!TIP]
> 讓我們假設您想要覆寫中的所有資料 `DataSource` 。 如果您對 then 函式進行呼叫 `clear` `add` ，對應可能會重新轉譯兩次，這可能會造成一些延遲。 請改用函式，此函式 `setShapes` 會移除並取代資料來源中的所有資料，而且只會觸發地圖的單一重新呈現。

**向量圖格來源**

向量圖格來源會說明如何存取向量磚圖層。 使用[VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource)類別來具現化向量圖格來源。 向量圖格圖層與磚圖層相似，但它們並不相同。 圖格圖層是一個點陣影像。 向量圖格圖層是壓縮檔案，格式為**PBF** 。 此壓縮檔案包含向量對應資料，以及一或多個圖層。 檔案可以根據每個圖層的樣式，在用戶端上呈現和樣式化。 [向量] 磚中的資料會以點、線條和多邊形的形式包含地理特徵。 使用向量圖格圖層，而不是點陣磚圖層有數個優點：

 - [向量] 磚的檔案大小通常會比對等的 [點陣] 磚小很多。 因此，會使用較少的頻寬。 這表示較低的延遲、更快速的對應，以及更佳的使用者體驗。
 - 由於向量圖格會在用戶端上呈現，因此會根據其顯示的裝置解析度進行調整。 如此一來，呈現的對應會以 crystal clear 標籤的方式更妥善定義。
 - 變更向量對應中的資料樣式不需要再次下載資料，因為新的樣式可以套用至用戶端。 相反地，變更點陣磚圖層的樣式通常需要從伺服器載入磚，然後套用新的樣式。
 - 因為資料是以向量形式傳遞，所以準備資料需要較少的伺服器端處理。 因此，較新的資料可以更快速地提供。

Azure 地圖服務遵守[Mapbox Vector 磚規格](https://github.com/mapbox/vector-tile-spec)，這是一種開放標準。 Azure 地圖服務提供下列向量圖格服務作為平臺的一部分：

- 道路磚[檔](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview)  |  [資料格式詳細資料](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile)
- 流量事件[檔](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidenttile)  |  [資料格式詳細資料](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- 交通流量[檔](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficflowtile)  |  [資料格式詳細資料](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles)
- Azure 地圖服務 Creator 也允許透過[Get 磚 Render V2](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview)建立和存取自訂向量圖格

> [!TIP]
> 使用來自 Azure 地圖服務轉譯服務的向量或點陣影像磚搭配 web SDK 時，您可以將取代 `atlas.microsoft.com` 為預留位置 `{azMapsDomain}` 。 此預留位置將會取代為對應所使用的相同網域，而且也會自動附加相同的驗證詳細資料。 這可大幅簡化使用 Azure Active Directory 驗證時，轉譯服務的驗證。

若要在地圖上顯示向量磚來源的資料，請將來源連接至其中一個資料轉譯層。 所有使用向量來源的圖層都必須 `sourceLayer` 在選項中指定值。 定下列程式碼會將 Azure 地圖服務流量向量圖格服務載入為向量磚來源，然後使用線條圖層將它顯示在地圖上。 這個向量磚來源在來源層中有一個稱為「流量流程」的單一資料集。 此資料集內的行資料具有名為 `traffic_level` 的屬性，在此程式碼中會用來選取色彩並調整線條大小。

```javascript
//Create a vector tile source and add it to the map.
var datasource = new atlas.source.VectorTileSource(null, {
    tiles: ['https://{azMapsDomain}/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}'],
    maxZoom: 22
});
map.sources.add(datasource);

//Create a layer for traffic flow lines.
var flowLayer = new atlas.layer.LineLayer(datasource, null, {
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer: 'Traffic flow',

    //Color the roads based on the traffic_level property. 
    strokeColor: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 'red',
        0.33, 'orange',
        0.66, 'green'
    ],

    //Scale the width of roads based on the traffic_level property. 
    strokeWidth: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 6,
        1, 1
    ]
});

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(flowLayer, 'labels');
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="向量磚線圖層" src="https://codepen.io/azuremaps/embed/wvMXJYJ?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在 CodePen 上 Azure 地圖服務 () ，請參閱「畫筆<a href='https://codepen.io/azuremaps/pen/wvMXJYJ'>向量圖層</a>」 <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'> </a>。
</iframe>

<br/>

## <a name="connecting-a-data-source-to-a-layer"></a>將資料來源連接至圖層

資料會使用呈現層在地圖上轉譯。 單一資料來源可由一或多個呈現層參考。 下列呈現層需要資料來源：

- 反升[層](map-add-bubble-layer.md)-將點資料轉譯為地圖上的縮放圓形。
- [符號圖層](map-add-pin.md)-以圖示或文字呈現點資料。
- [熱度圖圖層](map-add-heat-map-layer.md)-以密度熱度圖呈現點資料。
- [線條圖層](map-add-shape.md)-呈現線條和或呈現多邊形的外框。 
- [多邊形圖層](map-add-shape.md)-以純色或影像模式填滿多邊形的區域。

下列程式碼會示範如何建立資料來源、將它加入至地圖，並將它連接到反升圖層。 然後，將 GeoJSON 點資料從遠端位置匯入資料來源。 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

還有其他轉譯層不會連接到這些資料來源，但它們會直接載入資料以進行呈現。 

- [影像圖層](map-add-image-layer.md)-在地圖上將單一影像重迭，並將其角落系結至一組指定的座標。
- 圖格[圖層](map-add-tile-layer.md)-在地圖上方 superimposes 點陣磚圖層。

## <a name="one-data-source-with-multiple-layers"></a>一個具有多個層級的資料來源

多個層級可以連接到單一資料來源。 在許多不同的案例中，此選項很有用。 例如，請考慮使用者繪製多邊形的案例。 當使用者將點新增至地圖時，應該呈現並填滿多邊形區域。 加入樣式的線條以勾勒出多邊形，可讓您在使用者繪製時，更輕鬆地看到多邊形的邊緣。 為了方便編輯多邊形中的個別位置，我們可以在每個位置上方加入一個控點，例如釘選或標記。

![顯示從單一資料來源轉譯資料之多個圖層的地圖](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

在大部分的對應平臺中，您需要多邊形物件、線條物件，以及多邊形中每個位置的釘選。 修改多邊形時，您必須手動更新線條和 pin，這可能很快就會變得複雜。

使用 Azure 地圖服務，您只需要資料來源中的單一多邊形，如下列程式碼所示。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [源](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [新增快顯](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [新增符號圖層](map-add-pin.md)

> [!div class="nextstepaction"]
> [新增泡泡圖層](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [新增線條圖層](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](map-add-shape.md)

> [!div class="nextstepaction"]
> [新增熱度圖](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)