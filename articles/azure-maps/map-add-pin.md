---
title: 向地圖添加符號圖層 |微軟 Azure 地圖
description: 在本文中，您將瞭解如何使用 Symbol 圖層自訂符號，並使用 Microsoft Azure 地圖 Web SDK 在地圖上添加符號。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b8d131dcc798fb2fe1d4bb650cd5b0a68903381b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209693"
---
# <a name="add-a-symbol-layer-to-a-map"></a>將符號圖層新增至地圖

將符號連接到資料來源，並用它來在給定點呈現圖示或文本。 

使用 WebGL 來呈現符號圖層。 使用符號圖層在地圖上呈現較大的點集合。 與 HTML 標籤相比，符號圖層在地圖上呈現大量點資料，性能更好。 但是，符號圖層不支援傳統的 CSS 和 HTML 元素進行樣式設置。  

> [!TIP]
> 根據預設，符號圖層會轉譯資料來源中所有幾何圖形的座標。 要限制圖層，使其僅渲染點幾何要素將圖層`filter`的屬性設置為`['==', ['geometry-type'], 'Point']`或`['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`如果需要，還可以包括 MultiPoint 要素。

地圖圖像精靈管理器載入符號圖層使用的自訂圖像。 它支援以下圖像格式：

- JPEG
- PNG
- SVG
- BMP
- GIF（無動畫）

## <a name="add-a-symbol-layer"></a>新增符號圖層

在將符號圖層添加到地圖之前，需要執行幾個步驟。 首先，創建資料來源，並將其添加到地圖中。 創建符號圖層。 然後，將資料來源傳遞到符號層，以便從資料來源檢索資料。 最後，將資料添加到資料來源中，以便呈現要呈現的內容。 

下面的代碼演示了載入後應添加到地圖的內容。 此示例使用符號圖層渲染地圖上的單個點。 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

有四種不同類型的點資料可以添加到地圖中：

- GeoJSON 點幾何 - 此物件僅包含點的座標，而不包含任何其他座標。 `atlas.data.Point`説明器類可用於輕鬆創建這些物件。
- GeoJSON 多點幾何 - 此物件包含多個點的座標，而沒有其他點。 `atlas.data.MultiPoint`説明器類可用於輕鬆創建這些物件。
- GeoJSON 特徵 - 此物件由任何 GeoJSON 幾何體和一組包含與幾何體關聯的中繼資料的屬性組成。 `atlas.data.Feature`説明器類可用於輕鬆創建這些物件。
- `atlas.Shape`類類似于 GeoJSON 功能。 兩者都由 GeoJSON 幾何體和一組包含與幾何體關聯的中繼資料的屬性組成。 如果將 GeoJSON 物件添加到資料來源中，則可以輕鬆地在圖層中呈現該物件。 但是，如果更新了該 GeoJSON 物件的座標屬性，則資料來源和映射不會更改。 這是因為 JSON 物件中沒有觸發更新的機制。 形狀類提供用於更新其包含的資料的函數。 進行更改時，資料來源和地圖將自動通知和更新。 

以下代碼示例創建 GeoJSON Point 幾何體並將其傳遞到類中`atlas.Shape`，以便輕鬆更新。 地圖的中心最初用於渲染符號。 按一下事件將添加到地圖中，以便當它觸發時，滑鼠的座標將與形狀`setCoordinates`函數一起使用。 滑鼠座標在按一下事件時記錄。 然後，更新`setCoordinates`符號在地圖上的位置。

<br/>

<iframe height='500' scrolling='no' title='切換釘選位置' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>切換釘選位置</a>。
</iframe>

> [!TIP]
> 預設情況下，符號圖層通過隱藏重疊的符號來優化符號的渲染。 放大時，隱藏的符號將變為可見。 要禁用此功能並隨時渲染所有符號，請將`allowOverlap``iconOptions`選項的屬性設置為`true`。

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>將自訂圖示新增至符號圖層

使用 WebGL 來呈現符號圖層。 所有這類資源 (例如圖示影像) 必須載入 WebGL 內容中。 此示例演示如何向地圖資源添加自訂圖示。 然後使用此圖示在地圖上使用自訂符號渲染點資料。 符號圖層的 `textField` 屬性需要指定運算式。 在這種情況下，我們要呈現溫度屬性。 由於溫度是一個數位，它需要轉換為字串。 此外，我們希望附加"+F"到它。 運算式可用於執行此串聯;例如，運算式可用於執行此串聯。`['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='自訂符號影像圖示' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>自訂符號影像圖示</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

> [!TIP]
> Azure 地圖 Web SDK 提供了多個可自訂的圖像範本，可用於符號圖層。 有關詳細資訊，請參閱[如何使用圖像範本](how-to-use-image-templates-web-sdk.md)文檔。

## <a name="customize-a-symbol-layer"></a>自訂符號圖層 

符號圖層有許多可用的樣式選項。 以下是用來測試各種樣式選項的工具。

<br/>

<iframe height='700' scrolling='no' title='符號圖層選項' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/PxVXje/'>符號圖層選項</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

> [!TIP]
> 當您只想渲染帶有符號圖層的文本時，可以通過將圖示選項`image`的屬性設置為`'none'`來隱藏圖示。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [文本選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [建立資料來源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [新增快顯](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [如何使用映像範本](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [新增線條圖層](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](map-add-shape.md)

> [!div class="nextstepaction"]
> [添加氣泡圖層](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [新增 HTML 標記](map-add-bubble-layer.md)
