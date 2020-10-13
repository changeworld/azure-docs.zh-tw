---
title: 將熱度圖層新增至地圖 |Microsoft Azure 對應
description: 瞭解如何建立熱度圖。 瞭解如何使用 Azure 地圖服務 Web SDK 將熱度圖層新增至地圖。 瞭解如何自訂熱度圖圖層。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 5008e3b11a7e00e8f831333551c892113f03dd06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310623"
---
# <a name="add-a-heat-map-layer"></a>新增熱度圖層

熱度圖 (也稱為點密度圖) 是一種資料視覺效果。 它們是用來表示使用一系列色彩的資料密度，並在地圖上顯示資料「作用點」。 熱度圖適合用來呈現具有大量點的資料集。 

將數十萬個點轉譯為符號，可以涵蓋大部分的地圖區域。 這種情況可能會導致許多符號重迭。 讓您難以更清楚地瞭解資料。 不過，將這個相同的資料集視覺化為熱度圖，可讓您輕鬆查看每個資料點的密度和相對密度。

您可以在許多不同的案例中使用熱度圖，包括：

- **溫度資料**：為兩個資料點之間的溫度提供近似值。
- **雜訊感應器的資料**：不僅顯示感應器所在的雜訊濃度，還可讓您深入瞭解在距離之間的散熱。 任何一個網站的雜訊等級可能不會太高。 如果多個感應器的噪音涵蓋區域重迭，可能是這個重迭的區域可能會遇到較高的雜訊等級。 因此，重迭的區域會顯示在熱度圖中。
- **GPS 追蹤**：以加權高度地圖的形式包含速度，其中每個資料點的濃度以速度為基礎。 例如，此功能提供一種方式來查看車輛的進行加速。

> [!TIP]
> 熱度圖圖層預設會轉譯資料來源中所有幾何的座標。 若要限制圖層，使其只轉譯點幾何特徵，請將 `filter` 圖層的屬性設定為 `['==', ['geometry-type'], 'Point']` 。 如果您也想要包含 MultiPoint 功能，請將 `filter` 圖層的屬性設定為 `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` 。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>新增熱度圖層

若要將點的資料來源轉譯為熱度圖，請將您的資料來源傳遞至類別的實例 `HeatMapLayer` ，並將其新增至地圖。

在下列程式碼中，每個熱度點的半徑都是所有縮放層級的10圖元。 為了確保更好的使用者體驗，熱度圖位於標籤圖層下方。 標籤會保持清楚可見。 此範例中的資料來自 [USGS 地震風險計畫](https://earthquake.usgs.gov/)。 這是過去30天內發生的重大地震。

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

以下是上述程式碼的完整程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='簡易熱度圖層' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/gQqdQB/'>簡易熱度圖層</a>。
</iframe>

## <a name="customize-the-heat-map-layer"></a>自訂熱度圖圖層

前一個範例已藉由設定半徑和透明度選項來自訂熱度圖。 熱度圖層提供數個自訂選項，包括：

* `radius`：定義要在其中呈現每個資料點的圖元半徑。 您可以將 radius 設定為固定數位或運算式。 藉由使用運算式，您可以根據縮放層級調整半徑，並在地圖上表示一致的空間區域 (例如5英里的半徑) 。
* `color`：指定熱度圖的以色彩標示方式。 色彩漸層是熱度圖的常見功能。 您可以使用運算式來達成效果 `interpolate` 。 您也可以使用 `step` 運算式來標示熱度圖，將密度以視覺化方式細分為類似于輪廓或雷達圖樣式地圖的範圍。 這些色彩調色盤會定義最小密度值到最大密度值的顏色。 

  您可以將熱度圖的色彩值指定為值的運算式 `heatmap-density` 。 在「插補」運算式的索引0定義沒有資料的區域色彩，或是「逐步」運算式的預設色彩。 您可以使用這個值來定義背景色彩。 通常，這個值會設定為透明或半透明的黑色。 
   
  以下是色彩運算式的範例：

  | 插補色彩運算式 | 分階色彩運算式 | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25、' 綠 '、<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50、' 黃色 '、<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75，' red '<br/>\] |   

- `opacity`：指定熱度圖圖層的不透明或透明程度。
- `intensity`：將乘數套用至每個資料點的權數，以增加熱度圖的整體濃度。 它會導致資料點的權數差異，讓您更輕鬆地將其視覺化。
- `weight`：根據預設，所有資料點的權數為1，而且會平均加權。 權數選項可作為乘數，您可以將它設定為數字或運算式。 如果數位設定為權數，則將每個資料點放在地圖上兩次就是相等的。 比方說，如果權數是2，則密度會加倍。 將加權選項設定為數字所呈現的熱度圖，與使用強度選項呈現的熱度圖相似。 

  但是，如果您使用運算式，每個資料點的加權都可以根據每個資料點的屬性。 例如，假設每個資料點都代表地震。 量值對於每個地震資料點而言都是重要的度量。 地震會一直發生，但大部分的情況下都不會察覺到。 使用運算式中的量值，將權數指派給每個資料點。 藉由使用 [量值] 來指派權數，您可以更清楚地表示熱度圖內地震的重要性。
- `source` and `source-layer` ：可讓您更新資料來源。

以下是測試不同熱度圖圖層選項的工具。

<br/>

<iframe height='700' scrolling='no' title='熱度圖層選項' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/WYPaXr/'>熱度圖層選項</a>。
</iframe>

## <a name="consistent-zoomable-heat-map"></a>一致的可熱度圖

根據預設，熱度圖圖層中轉譯的資料點半徑在所有縮放層級中都有固定的圖元半徑。 當您縮放地圖時，資料匯總和熱度圖圖層看起來會不同。 

使用 `zoom` 運算式來調整每個縮放層級的半徑，讓每個資料點都涵蓋地圖的相同實體區域。 此運算式會讓熱度圖圖層看起來更為靜態且一致。 地圖的每個縮放層級都有兩倍垂直和水準的圖元作為先前的縮放層級。 

調整半徑，使其每個縮放層級加倍，以建立在所有縮放層級上都一致的熱度圖。 若要套用這項調整，請使用 `zoom` 和基底 2 `exponential interpolation` 運算式，並針對最小縮放層級設定圖元半徑，並針對所計算的最大縮放層級調整半徑，如 `2 * Math.pow(2, minZoom - maxZoom)` 下列範例所示。 縮放地圖以查看熱度圖如何以縮放層級進行縮放。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="一致的可熱度圖" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
在 >codepen 上 Azure 地圖服務 () ，查看畫筆<a href='https://codepen.io/azuremaps/pen/OGyMZr/'>一致可熱度圖</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

> [!TIP]
> 當您在資料來源上啟用叢集時，接近另一個的點會群組在一起成為一個叢集點。 您可以使用每個叢集的點計數作為熱度圖的加權運算式。 這可以大幅減少要轉譯的點數目。 叢集的點計數會儲存在 `point_count` 點功能的屬性中： 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> 如果群集半徑只有幾個圖元，轉譯中會有較小的視覺差異。 較大的 radius 會將更多的點分組到每個叢集，並提升熱度圖的效能。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

如需更多可新增至地圖的程式碼範例，請參閱下列文章：

> [!div class="nextstepaction"]
> [建立資料來源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)
