---
title: 將符號圖層新增至地圖 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用符號圖層來自訂符號，並使用 Microsoft Azure Maps Web SDK 在地圖上新增符號。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b8d131dcc798fb2fe1d4bb650cd5b0a68903381b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77209693"
---
# <a name="add-a-symbol-layer-to-a-map"></a>將符號圖層新增至地圖

將符號連接至資料來源，並使用它在指定的點呈現圖示或文字。 

使用 WebGL 來呈現符號圖層。 使用符號圖層，在地圖上呈現大量的點集合。 相較于 HTML 標籤，符號圖層會在地圖上呈現大量的點資料，效能較佳。 不過，符號圖層不支援傳統的 CSS 和 HTML 專案來進行樣式設定。  

> [!TIP]
> 根據預設，符號圖層會轉譯資料來源中所有幾何圖形的座標。 若要限制圖層，使其只轉譯點幾何特徵 `filter` ，請將圖層的屬性設為 `['==', ['geometry-type'], 'Point']` 或 `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` ，如果您想要，您也可以包含 MultiPoint 功能。

地圖影像 sprite 管理員會載入符號圖層所使用的自訂影像。 它支援下列影像格式：

- JPEG
- PNG
- SVG
- BMP
- GIF （沒有動畫）

## <a name="add-a-symbol-layer"></a>新增符號圖層

在您可以將符號圖層新增至地圖之前，您必須採取幾個步驟。 首先，建立資料來源，並將它加入至對應。 建立符號圖層。 然後，將資料來源傳入符號層，以從資料來源中取出資料。 最後，將資料加入資料來源中，以便呈現內容。 

下列程式碼示範在對應載入之後應新增至地圖的內容。 這個範例會使用符號圖層，在地圖上呈現單一點。 

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

有四種不同類型的點資料可以新增至地圖：

- GeoJSON 點幾何-這個物件只包含某個點的座標，其他則沒有。 `atlas.data.Point`Helper 類別可以用來輕鬆地建立這些物件。
- GeoJSON MultiPoint geometry-此物件包含多個點的座標，而不是任何其他。 `atlas.data.MultiPoint`Helper 類別可以用來輕鬆地建立這些物件。
- GeoJSON 功能-此物件是由任何 GeoJSON 幾何和一組屬性所組成，其中包含與幾何相關聯的中繼資料。 `atlas.data.Feature`Helper 類別可以用來輕鬆地建立這些物件。
- `atlas.Shape`類別與 GeoJSON 功能類似。 兩者都是由 GeoJSON geometry 和一組屬性所組成，其中包含與幾何相關聯的中繼資料。 如果將 GeoJSON 物件加入至資料來源，就可以輕鬆地在圖層中轉譯。 不過，如果已更新該 GeoJSON 物件的座標屬性，則資料來源和對應不會變更。 這是因為 JSON 物件中沒有任何機制可觸發更新。 Shape 類別提供用來更新其包含之資料的功能。 進行變更時，會自動通知和更新資料來源和對應。 

下列程式碼範例會建立 GeoJSON 點幾何，並將它傳遞至 `atlas.Shape` 類別，以便輕鬆地進行更新。 地圖的中心一開始是用來呈現符號。 [Click] 事件會加入至地圖中，因此當它引發時，滑鼠的座標會與 shapes 函式一起使用 `setCoordinates` 。 滑鼠座標會在 click 事件時錄製。 然後，會 `setCoordinates` 更新地圖上符號的位置。

<br/>

<iframe height='500' scrolling='no' title='切換釘選位置' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>切換釘選位置</a>。
</iframe>

> [!TIP]
> 根據預設，符號圖層會藉由隱藏重迭的符號來優化符號的呈現。 當您放大時，隱藏的符號就會變成可見。 若要停用此功能並隨時轉譯所有符號，請將 `allowOverlap` 選項的屬性設定 `iconOptions` 為 `true` 。

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>將自訂圖示新增至符號圖層

使用 WebGL 來呈現符號圖層。 所有這類資源 (例如圖示影像) 必須載入 WebGL 內容中。 這個範例示範如何將自訂圖示新增至地圖資源。 此圖示接著會用來呈現地圖上具有自訂符號的點資料。 符號圖層的 `textField` 屬性需要指定運算式。 在此情況下，我們想要呈現溫度屬性。 因為溫度是數位，所以必須轉換成字串。 此外，我們想要將 "° F" 附加至該檔案。 運算式可以用來執行這種串連;`['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='自訂符號影像圖示' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>自訂符號影像圖示</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

> [!TIP]
> Azure 地圖服務 Web SDK 提供數個可自訂的影像範本，以供作為符號圖層使用。 如需詳細資訊，請參閱[如何使用影像範本](how-to-use-image-templates-web-sdk.md)文件。

## <a name="customize-a-symbol-layer"></a>自訂符號圖層 

符號圖層有許多可用的樣式選項。 以下是用來測試各種樣式選項的工具。

<br/>

<iframe height='700' scrolling='no' title='符號圖層選項' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/PxVXje/'>符號圖層選項</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

> [!TIP]
> 當您只想要轉譯具有符號圖層的文字時，可以將 `image` 圖示選項的屬性設為來隱藏圖示 `'none'` 。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [建立資料來源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [新增快顯](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [如何使用影像範本](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [新增線條圖層](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](map-add-shape.md)

> [!div class="nextstepaction"]
> [新增泡泡圖層](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [新增 HTML 標記](map-add-bubble-layer.md)
