---
title: 為地圖創建資料來源 |微軟 Azure 地圖
description: 在本文中，您將學習如何創建資料來源，以及如何使用 Microsoft Azure 地圖 Web SDK 將其添加到地圖中。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190239"
---
# <a name="create-a-data-source"></a>建立資料來源

Azure 映射 Web SDK 將資料存儲在資料來源中。 使用資料來源可優化用於查詢和呈現的資料操作。 目前有兩種類型的資料來源：

**GeoJSON 資料來源**

基於 GeoJSON 的資料來源使用`DataSource`類在本地載入和存儲資料。 GeoJSON 資料可以使用[atlas.data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data)命名空間中的説明器類手動創建或創建。 該`DataSource`類提供用於導入本地或遠端 GeoJSON 檔的函數。 遠端 GeoJSON 檔必須託管在啟用 COR 的終結點上。 類`DataSource`提供群集點資料的功能。 而且，資料可以輕鬆地添加、刪除和更新類`DataSource`。


> [!TIP]
> 假設您要覆蓋 中的`DataSource`所有資料。 如果調用當時的`clear``add`函數，地圖可能會重新渲染兩次，這可能會導致一些延遲。 而是使用`setShapes`函數，該函數將刪除並替換資料來源中的所有資料，並且僅觸發映射的單個重新渲染。

**向量磁貼源**

向量切片源描述如何訪問向量切片圖層。 使用[VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource)類具現化向量切片源。 向量切片圖層與切片圖層類似，但它們不同。 切片圖層是柵格圖像。 向量切片圖層是壓縮檔，採用 PBF 格式。 此壓縮檔包含向量映射資料以及一個或多個圖層。 檔可以根據每個圖層的樣式在用戶端上呈現和設置樣式。 向量磁貼中的資料以點、線和麵的形式包含地理要素。 使用向量切片圖層而不是柵格切片圖層有幾個優點：

 - 向量磁貼的檔案大小通常比等效柵格磁貼小得多。 因此，使用的頻寬更少。 這意味著更低的延遲、更快的地圖和更好的使用者體驗。
 - 由於向量切片呈現在用戶端上，因此它們會適應顯示它們的設備的解析度。 因此，渲染的地圖看起來更清晰，帶有水晶般清晰的標籤。
 - 更改向量映射中的資料樣式不需要再次下載資料，因為新樣式可以應用於用戶端。 相反，更改柵格切片圖層的樣式通常需要從伺服器載入切片，然後應用新樣式。
 - 由於資料以向量形式傳遞，因此準備資料所需的伺服器端處理更少。 因此，較新的資料可以更快地可用。

使用向量源的所有圖層都必須指定值`sourceLayer`。

創建後，資料來源可以通過`map.sources`屬性（即[SourceManager）](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager)添加到地圖中。 以下代碼演示如何創建 並將其`DataSource`添加到地圖中。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Azure 地圖遵循[地圖框向量磁貼規範](https://github.com/mapbox/vector-tile-spec)，這是一個開放標準。

## <a name="connecting-a-data-source-to-a-layer"></a>將資料來源連接到圖層

使用渲染圖層在地圖上呈現資料。 單個資料來源可以由一個或多個呈現圖層引用。 以下呈現圖層需要資料來源：

- [氣泡圖層](map-add-bubble-layer.md)- 在地圖上以縮放的圓圈渲染點資料。
- [符號圖層](map-add-pin.md)- 將點資料渲染為圖示或文本。
- [熱圖圖層](map-add-heat-map-layer.md)- 渲染點資料作為密度熱圖。
- [線圖層](map-add-shape.md)- 渲染線條和或渲染多邊形的輪廓。 
- [多邊形圖層](map-add-shape.md)- 用純色或圖像圖案填充多邊形區域。

以下代碼演示如何創建資料來源、將其添加到地圖並將其連接到氣泡圖層。 然後，將 GeoJSON 點資料從遠端位置導入資料來源。 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

還有其他呈現圖層不連接到這些資料來源，但它們直接載入資料進行呈現。 

- [圖像圖層](map-add-image-layer.md)- 覆蓋地圖頂部的單個圖像，並將其角綁定到一組指定的座標。
- [切片圖層](map-add-tile-layer.md)- 疊加地圖頂部的柵格切片圖層。

## <a name="one-data-source-with-multiple-layers"></a>具有多層的一個資料來源

多個層可以連接到單個資料來源。 在許多不同的方案中，此選項很有用。 例如，請考慮使用者繪製多邊形的方案。 當使用者向地圖添加點時，我們應該渲染和填充多邊形區域。 添加樣式線以勾勒出多邊形，從而在使用者繪製時更輕鬆地查看多邊形的邊緣。 為了方便地編輯多邊形中的單個位置，我們可以在每個位置上方添加一個控點，如圖釘或標記。

![顯示從單個資料來源呈現資料的多個圖層的地圖](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

在大多數映射平臺中，對於多邊形中的每個位置，都需要一個多邊形物件、一個線物件和一個引腳。 修改多邊形後，需要手動更新線和引腳，這很快就會變得複雜。

使用 Azure 映射，只需資料來源中的單個多邊形，如下所示。

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
> [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [資料來源選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [向量切片源](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [向量切片源選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [新增快顯](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [添加符號圖層](map-add-pin.md)

> [!div class="nextstepaction"]
> [添加氣泡圖層](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [新增線條圖層](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](map-add-shape.md)

> [!div class="nextstepaction"]
> [新增熱度圖](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)