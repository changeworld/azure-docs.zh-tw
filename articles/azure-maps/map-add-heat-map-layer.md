---
title: 將熱度圖層新增至地圖 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Web SDK，將熱度圖圖層新增至地圖。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 4a853871ef5f66881235e5a6ffec0886b81f5a92
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208534"
---
# <a name="add-a-heat-map-layer"></a>新增熱度圖層

熱度圖（也稱為點密度圖）是一種資料視覺效果。 它們是用來代表資料密度，並在地圖上顯示「作用點」資料。 熱度圖是轉譯具有大量點之資料集的絕佳方式。 

將數十個點轉譯為符號可涵蓋大部分的地圖區域。 這種情況可能會導致許多符號重迭。 讓您難以進一步瞭解資料。 不過，將此相同的資料集視覺化為熱度圖，可讓您輕鬆地查看每個資料點的密度和相對密度。

您可以在許多不同的案例中使用熱度圖，包括：

- **溫度資料**：提供兩個資料點之間溫度的近似值。
- **雜訊感應器的資料**：不僅會顯示感應器所處雜訊的濃度，還可讓您深入瞭解一段距離的散熱。 任何一個網站的雜訊層級可能不會太高。 如果多個感應器的噪音涵蓋範圍重迭，可能是這個重迭的區域可能會遇到較高的雜訊層級。 因此，重迭的區域會顯示在熱度圖中。
- **GPS 追蹤**：包含加權高度地圖的速度，其中每個資料點的濃度以速度為基礎。 例如，這種功能可讓您查看車輛的加速位置。

> [!TIP]
> 熱度圖圖層預設會轉譯資料來源中所有幾何的座標。 若要限制圖層，使其只轉譯點幾何特徵，請將圖層的 [`filter`] 屬性設定為 [`['==', ['geometry-type'], 'Point']`]。 如果您也想要包含 MultiPoint 功能，請將圖層的 [`filter`] 屬性設定為 [`['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`]。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>新增熱度圖層

若要將點的資料來源轉譯為熱度圖，請將資料來源傳遞至 `HeatMapLayer` 類別的實例，並將其新增至地圖。

在下列程式碼中，每個熱度點在所有縮放層級都有10圖元的半徑。 為確保更好的使用者體驗，熱度圖位於標籤圖層底下。 標籤會保持清楚可見。 此範例中的資料來自[USGS 地震風險計畫](https://earthquake.usgs.gov/)。 這適用于過去30天內發生的重大地震。

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

以下是上述程式碼的完整執行程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='簡易熱度圖層' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io'>簡易熱度圖層</a>。
</iframe>

## <a name="customize-the-heat-map-layer"></a>自訂熱度圖層

前一個範例已藉由設定半徑和透明度選項來自訂熱度圖。 熱度圖層提供數個自訂選項，包括：

* `radius`：定義要在其中呈現每個資料點的圖元半徑。 您可以將 radius 設定為固定數位或運算式。 藉由使用運算式，您可以根據縮放層級來調整半徑，並代表地圖上的一致空間區域（例如，5英里的半徑）。
* `color`：指定熱度圖的以色彩標示方式。 色彩漸層是熱度圖的常見功能。 您可以使用 `interpolate` 運算式來達到效果。 您也可以使用 `step` 運算式來上色熱度圖，以視覺化方式將密度分解成類似等高線或雷達圖樣式地圖的範圍。 這些色彩調色盤會定義最小密度值到最大密度值的顏色。 

  您可以指定熱度圖的色彩值，做為 `heatmap-density` 值的運算式。 沒有資料的區域色彩定義于「插補」運算式的索引0，或「階」運算式的預設色彩。 您可以使用這個值來定義背景色彩。 這個值通常會設定為透明或半透明的黑色。 
   
  以下是色彩運算式的範例：

  | 插補色彩運算式 | 分階色彩運算式 | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25、「綠色」、<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50、「黃色」、<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75，' red '<br/>\] |   

- `opacity`：指定熱度圖層的不透明或透明程度。
- `intensity`：將乘數套用到每個資料點的權數，以增加熱度圖的整體濃度。 這會造成資料點的權數差異，讓您更輕鬆地將其視覺化。
- `weight`：根據預設，所有資料點的權數為1，且加權為相同。 權數選項會當做乘數，您可以將它設定為數字或運算式。 如果將某個數位設定為權數，這就是將每個資料點放在地圖上兩次的等價。 例如，如果權數為2，則密度會加倍。 將加權選項設定為數字所呈現的熱度圖，與使用強度選項呈現的熱度圖相似。 

  不過，如果您使用運算式，每個資料點的權數可以根據每個資料點的屬性。 例如，假設每個資料點都代表地震。 「大小」值對於每個地震資料點而言是重要的度量。 地震會一直發生，但大部分的情況下都很低，而且也不會注意到。 使用運算式中的 [大小] 值，將權數指派給每個資料點。 藉由使用 [量值] 來指派權數，您可以在熱度圖中獲得更佳的地震意義標記法。
- `source` 和 `source-layer`：可讓您更新資料來源。

以下是用來測試不同熱度圖層選項的工具。

<br/>

<iframe height='700' scrolling='no' title='熱度圖層選項' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io'>熱度圖層選項</a>。
</iframe>

## <a name="consistent-zoomable-heat-map"></a>一致的可縮放熱度圖

根據預設，熱度圖圖層中轉譯之資料點的半徑對於所有縮放層級都有固定圖元半徑。 當您放大地圖時，會將資料匯總在一起，且熱度圖層看起來會不同。 

使用 `zoom` 運算式來調整每個縮放層級的半徑，讓每個資料點都涵蓋對應的相同實體區域。 此運算式會使熱度圖層看起來更為靜態且一致。 地圖的每個縮放層級都會以垂直和水準方式，與上一個縮放層級相同的圖元數倍。 

調整半徑，使其每個縮放層級加倍時，會建立一個在所有縮放層級上看起來一致的熱度圖。 若要套用此調整，請使用 `zoom` 搭配基底 2 `exponential interpolation` 運算式，如下列範例所示。 縮放地圖以查看熱度圖如何隨著縮放層級調整。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="一致的可縮放熱度圖" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的「手寫筆<a href='https://codepen.io/azuremaps/pen/OGyMZr/'>一致可縮放熱度圖</a>」。
</iframe>

> [!TIP]
> 當您在資料來源上啟用叢集時，彼此接近的點會群組在一起，成為一個叢集點。 您可以使用每個叢集的點計數做為熱度圖的權數運算式。 這可能會大幅減少要轉譯的點數目。 叢集的點計數會儲存在 point 功能的 `point_count` 屬性中： 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> 如果群集半徑只是幾個圖元，轉譯中會有一個小型的視覺差異。 較大的半徑會將更多的點分組到每個叢集，並改善熱度圖的效能。

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
