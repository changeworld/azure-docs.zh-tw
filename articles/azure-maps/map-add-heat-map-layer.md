---
title: 向地圖添加熱圖圖層 |微軟 Azure 地圖
description: 在本文中，您將瞭解如何使用 Microsoft Azure 地圖 Web SDK 將熱圖圖層添加到地圖中。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 4a853871ef5f66881235e5a6ffec0886b81f5a92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77208534"
---
# <a name="add-a-heat-map-layer"></a>新增熱度圖層

熱度圖 (也稱為點密度圖) 是一種資料視覺效果。 它們用於使用一系列顏色表示資料密度，並在地圖上顯示資料"熱點"。 熱圖是渲染具有大量點的資料資料集的好方法。 

將數以萬計的點渲染為符號可以覆蓋地圖區域的大部分。 這種情況可能會導致許多符號重疊。 使資料難以更好地瞭解。 但是，將此資料集與熱圖視覺化，便於查看每個資料點的密度和相對密度。

您可以在許多不同的方案中使用熱圖，包括：

- **溫度資料**：提供兩個資料點之間的溫度的近似值。
- **雜訊感應器的資料**：不僅顯示感應器所在位置的雜訊強度，還可以提供對遠距離耗散情況的洞察。 任何一個網站的雜訊級別可能並不高。 如果來自多個感應器的雜訊覆蓋區域重疊，則此重疊區域可能會遇到更高的雜訊級別。 因此，重疊區域將在熱圖中可見。
- **GPS 跟蹤**：包括速度作為加權高度圖，其中每個資料點的強度基於速度。 例如，此功能提供了一種查看車輛超速位置的方法。

> [!TIP]
> 預設情況下，熱貼圖圖層呈現資料來源中所有幾何體的座標。 要限制圖層以便僅渲染點幾何要素，將圖層的屬性設置為`filter``['==', ['geometry-type'], 'Point']`。 如果還希望也包括 MultiPoint 要素，請將`filter`圖層的屬性設置為`['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>新增熱度圖層

要將點的資料來源呈現為熱圖，請將資料來源傳遞到`HeatMapLayer`類的實例中，並將其添加到地圖中。

在以下代碼中，每個熱點在所有縮放級別上都有 10 圖元的半徑。 為了確保更好的使用者體驗，熱圖位於標籤圖層下方。 標籤保持清晰可見。 此示例中的資料來自[美國地質調查局地震危害計畫](https://earthquake.usgs.gov/)。 這是針對過去30天發生的大地震。

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

下面是前面代碼的完整運行代碼示例。

<br/>

<iframe height='500' scrolling='no' title='簡易熱度圖層' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/gQqdQB/'>簡易熱度圖層</a>。
</iframe>

## <a name="customize-the-heat-map-layer"></a>自訂熱圖圖層

前一個範例已藉由設定半徑和透明度選項來自訂熱度圖。 熱圖圖層提供了多種自訂選項，包括：

* `radius`： 定義用於渲染每個資料點的圖元半徑。 您可以將半徑設置為固定數位或運算式。 通過使用運算式，可以根據縮放級別縮放半徑，並表示地圖上的一致空間區域（例如，5 英里半徑）。
* `color`：指定熱圖的著色方式。 色彩坡形是熱圖的常見特徵。 您可以使用`interpolate`運算式實現效果。 您還可以使用`step`運算式對熱圖進行著色，將密度直觀地分解為類似于輪廓或雷達樣式圖的範圍。 這些色彩調色盤會定義最小密度值到最大密度值的顏色。 

  將熱圖的顏色值指定為值上的`heatmap-density`運算式。 沒有資料的區域的顏色在"插值"運算式的索引 0 或"步進"運算式的預設顏色中定義。 可以使用此值定義背景顏色。 通常，此值設置為透明或半透明黑色。 
   
  下面是顏色運算式的示例：

  | 插值顏色運算式 | 階梯顏色運算式 | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25，"綠色"<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50，"黃色"，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75，"紅色"<br/>\] |   

- `opacity`：指定熱圖圖層的不透明程度或透明度。
- `intensity`：對每個資料點的重量應用乘數，以增加熱圖的總體強度。 它會導致資料點重量的差異，從而更易於視覺化。
- `weight`： 預設情況下，所有資料點的權重為 1，並且權重相等。 權重選項充當乘數，您可以將其設置為數字或運算式。 如果將數位設置為權重，則是將每個資料點放在地圖上兩次的等效。 例如，如果權重為 2，則密度加倍。 將加權選項設定為數字所呈現的熱度圖，與使用強度選項呈現的熱度圖相似。 

  但是，如果使用運算式，則每個資料點的權重可以基於每個資料點的屬性。 例如，假設每個資料點表示地震。 震級值是每個地震資料點的重要指標。 地震一直發生，但大多數地震的震級較低，沒有引起注意。 使用運算式中的量級值將權重分配給每個資料點。 通過使用震級值來分配權重，您可以在熱圖中更好地表示地震的重要性。
- `source`和`source-layer`： 使您能夠更新資料來源。

下面是一個用於測試不同熱圖圖層選項的工具。

<br/>

<iframe height='700' scrolling='no' title='熱度圖層選項' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/WYPaXr/'>熱度圖層選項</a>。
</iframe>

## <a name="consistent-zoomable-heat-map"></a>一致的可縮放熱圖

預設情況下，熱貼圖圖層中呈現的資料點的半徑為所有縮放級別的固定圖元半徑。 縮放地圖時，資料聚合在一起，熱圖圖層看起來不同。 

使用`zoom`運算式縮放每個縮放級別的半徑，以便每個資料點覆蓋地圖的相同物理區域。 此運算式使熱圖圖層看起來更靜態且一致。 地圖的每個縮放級別垂直和水準的圖元數是上一個縮放級別的兩倍。 

縮放半徑，使其與每個縮放級別加倍，可創建在所有縮放級別上看起來一致的熱圖。 要應用此縮放，請使用`zoom`基 2`exponential interpolation`運算式，如以下示例所示。 縮放地圖以查看熱圖如何與縮放級別一起縮放。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="一致的可縮放熱圖" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，請參閱按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>地圖 （） 提供的"筆<a href='https://codepen.io/azuremaps/pen/OGyMZr/'>一致可縮放熱圖</a>"。
</iframe>

> [!TIP]
> 在資料來源上啟用聚類時，彼此接近的點將分組為聚類點。 您可以將每個群集的點計數用作熱圖的重量運算式。 這可以顯著減少要呈現的點數。 群集的點計數存儲在點要素的屬性中`point_count`： 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> 如果聚類半徑只有幾圖元，則渲染中會有很小的視覺差異。 半徑越大，將更多點分組到每個群集中，並提高了熱圖的性能。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

如需更多可新增至地圖的程式碼範例，請參閱下列文章：

> [!div class="nextstepaction"]
> [建立資料來源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)
