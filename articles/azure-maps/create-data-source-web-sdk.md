---
title: 在 Microsoft Azure Maps 中建立地圖的資料來源
description: 瞭解如何建立對應的資料來源。 瞭解 Azure 地圖服務 Web SDK 使用的資料來源： GeoJSON 來源和向量圖格。
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9964c99ddfb59811fc67df634b41cede5847ede0
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678853"
---
# <a name="create-a-data-source"></a>建立資料來源

Azure 地圖服務 Web SDK 會將資料儲存在資料來源中。 使用資料來源可將查詢和轉譯的資料作業優化。 目前有兩種類型的資料來源：

* **GeoJSON 來源**：在本機管理 GeoJSON 格式的原始位置資料。 適用于小型至中型的資料集 () 的上百個圖形。
* **向量圖格來源**：根據地圖並排顯示系統，將格式化為目前地圖視圖之向量磚的資料載入。 適用于大型至大型資料集 (上百萬或數十億個圖形) 。

## <a name="geojson-data-source"></a>GeoJSON 資料來源

以 GeoJSON 為基礎的資料來源會使用類別在本機載入和儲存資料 `DataSource` 。 您可以使用 GeoJSON [資料命名空間中的 helper](/javascript/api/azure-maps-control/atlas.data) 類別，以手動方式建立或建立資料。 `DataSource`類別提供匯入本機或遠端 GeoJSON 檔案的函式。 遠端 GeoJSON 檔案必須裝載于已啟用 CORs 的端點上。 `DataSource`類別會提供群集點資料的功能。 而且，您可以使用類別輕鬆地新增、移除和更新資料 `DataSource` 。 下列程式碼顯示如何在 Azure 地圖服務中建立 GeoJSON 資料。

```javascript
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

//Create GeoJSON using helper classes (less error prone and less typing).
var geoJsonClass = new atlas.data.Feature(new atlas.data.Point([-100, 45]), {
    "custom-property": "value"
}); 
```

一旦建立之後，就可以透過 `map.sources` 屬性（也就是 [SourceManager](/javascript/api/azure-maps-control/atlas.sourcemanager)）將資料來源新增至地圖。 下列程式碼會示範如何建立 `DataSource` ，並將它加入至對應。

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);
```

下列程式碼顯示 GeoJSON 資料可新增至的不同方式 `DataSource` 。

```javascript
//GeoJsonData in the following code can be a single or array of GeoJSON features or geometries, a GeoJSON feature colleciton, or a single or array of atlas.Shape objects.

//Add geoJSON object to data source. 
source.add(geoJsonData);

//Load geoJSON data from URL. URL should be on a CORs enabled endpoint.
source.importDataFromUrl(geoJsonUrl);

//Overwrite all data in data source.
source.setShapes(geoJsonData);
```

> [!TIP]
> 假設您想要覆寫中的所有資料 `DataSource` 。 如果您對 then 函式進行呼叫 `clear` `add` ，對應可能會重新轉譯兩次，而這可能會造成一些延遲。 相反 `setShapes` 地，請使用函式，這會移除並取代資料來源中的所有資料，而且只會觸發對應的單一重呈現。

## <a name="vector-tile-source"></a>向量圖格來源

向量圖格來源說明如何存取向量圖格圖層。 使用 [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource) 類別，將向量圖格來源具現化。 向量圖格圖層類似于圖格圖層，但它們並不相同。 圖格圖層是點陣影像。 向量圖格圖層是 **PBF** 格式的壓縮檔案。 這個壓縮檔案包含向量地圖資料，以及一或多個圖層。 您可以根據每個圖層的樣式，在用戶端上轉譯和樣式檔。 向量圖格中的資料會以點、線條和多邊形的形式包含地理特徵。 使用向量圖格圖層有幾個優點，而不是使用點陣圖格圖層：

* 向量磚的檔案大小通常遠小於相等的點陣磚。 因此，使用的頻寬較少。 這表示較低的延遲、更快速的地圖，以及更好的使用者體驗。
* 由於向量圖格會在用戶端上轉譯，因此會調整為其顯示所在裝置的解析度。 因此，轉譯的地圖會以 crystal clear 標籤更妥善地定義。
* 變更向量地圖中的資料樣式不需要再次下載資料，因為新的樣式可以套用至用戶端。 相反地，變更點陣磚圖層的樣式通常需要從伺服器載入磚，然後套用新的樣式。
* 因為資料是以向量形式傳遞，所以準備資料需要較少的伺服器端處理。 如此一來，較新的資料可以更快地提供使用。

Azure 地圖服務遵守 [Mapbox 向量圖格規格](https://github.com/mapbox/vector-tile-spec)，也就是開放式標準。 Azure 地圖服務提供下列向量圖格服務作為平臺的一部分：

- 道路磚[檔](/rest/api/maps/renderv2/getmaptilepreview)  |  [資料格式詳細資料](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile)
- 流量事件[檔](/rest/api/maps/traffic/gettrafficincidenttile)  |  [資料格式詳細資料](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- 交通流程[檔](/rest/api/maps/traffic/gettrafficflowtile)  |  [資料格式詳細資料](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles)
- Azure 地圖服務 Creator (Preview) 也可讓您透過[取得磚轉譯 V2](/rest/api/maps/renderv2/getmaptilepreview)來建立和存取自訂向量磚

> [!TIP]
> 使用 Azure 地圖服務轉譯服務的向量或點陣影像磚搭配 web SDK 時，您可以取代 `atlas.microsoft.com` 為預留位置 `{azMapsDomain}` 。 此預留位置將會取代為對應所使用的相同網域，且也會自動附加相同的驗證詳細資料。 這可大幅簡化使用 Azure Active Directory authentication 時，轉譯服務的驗證。

若要在地圖上顯示向量圖格來源的資料，請將來源連接到其中一個資料轉譯圖層。 使用向量來源的所有圖層都必須 `sourceLayer` 在選項中指定值。 下列程式碼會將 Azure 地圖服務交通流程向量圖格服務載入為向量圖格來源，然後使用線條圖層將它顯示在地圖上。 此向量圖格來源在來源層中有一組稱為「流量流程」的資料。 此資料集中的行資料具有一個名為 `traffic_level` 的屬性，此程式碼會使用此屬性來選取色彩並調整線條大小。

```javascript
//Create a vector tile source and add it to the map.
var source = new atlas.source.VectorTileSource(null, {
    tiles: ['https://{azMapsDomain}/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}'],
    maxZoom: 22
});
map.sources.add(source);

//Create a layer for traffic flow lines.
var flowLayer = new atlas.layer.LineLayer(source, null, {
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

<iframe height="500" style="width: 100%;" scrolling="no" title="向量圖格線條圖層" src="https://codepen.io/azuremaps/embed/wvMXJYJ?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在 >codepen 上 Azure 地圖服務 () ，查看畫筆 <a href='https://codepen.io/azuremaps/pen/wvMXJYJ'>向量圖格圖層</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'> </a>。
</iframe>

<br/>

## <a name="connecting-a-data-source-to-a-layer"></a>將資料來源連接至圖層

使用轉譯圖層在地圖上呈現資料。 單一資料來源可由一個或多個呈現圖層參考。 下列呈現圖層需要資料來源：

* 反升[圖圖層](map-add-bubble-layer.md)-將點資料呈現為地圖上的縮放圓形。
* [符號圖層](map-add-pin.md) -將點資料呈現為圖示或文字。
* [熱度圖圖層](map-add-heat-map-layer.md) -將點資料呈現為密度熱度圖。
* [線條圖層](map-add-shape.md) -轉譯線條和或轉譯多邊形的外框。 
* [多邊形圖層](map-add-shape.md) -以純色或影像樣式填滿多邊形的區域。

下列程式碼會示範如何建立資料來源、將其新增至地圖，以及將它連接到反升圖圖層。 然後，將 GeoJSON 點資料從遠端位置匯入資料來源。 

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(source));

//Load the earthquake data.
source.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

有其他轉譯層不會連接到這些資料來源，但會直接載入資料以進行轉譯。 

* [影像圖層](map-add-image-layer.md) -在地圖頂端覆迭單一影像，並將其邊角系結至一組指定的座標。
* 圖格[圖層](map-add-tile-layer.md)-在地圖上方但將點陣圖格圖層。

## <a name="one-data-source-with-multiple-layers"></a>一個具有多層的資料來源

多層可以連接到單一資料來源。 在許多不同的情況下，此選項很有用。 例如，請考慮使用者繪製多邊形的案例。 當使用者將點新增至地圖時，我們應轉譯和填滿多邊形區域。 新增已設定樣式的線條來勾勒出多邊形，可讓您在使用者繪製時更輕鬆地查看多邊形的邊緣。 為了方便編輯多邊形中的個別位置，我們可以在每個位置的上方新增一個控點，例如圖釘或標記。

![顯示從單一資料來源轉譯資料之多個圖層的地圖](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

在大部分的對應平臺中，您需要多邊形物件、線條物件，以及多邊形中每個位置的釘選。 修改多邊形時，您需要手動更新線條和釘選，這可能很快就會變得複雜。

使用 Azure 地圖服務，您只需要資料來源中的單一多邊形，如下列程式碼所示。

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(source, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(source, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(source, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     strokeColor: 'white',
     strokeWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

> [!TIP]
> 使用函式將圖層加入至地圖時 `map.layers.add` ，現有圖層的識別碼或實例可以作為第二個參數傳入。 這會告訴 map 要插入新的圖層，並將其新增至現有的圖層下方。 除了傳入圖層識別碼之外，此方法也支援下列值。
>
> * `"labels"` -在地圖標籤圖層下方插入新圖層。
> * `"transit"` -在地圖道路和傳輸層下方插入新的圖層。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [DataSourceOptions](/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](/javascript/api/azure-maps-control/atlas.vectortilesourceoptions)

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
> [程式碼範例](/samples/browse/?products=azure-maps)