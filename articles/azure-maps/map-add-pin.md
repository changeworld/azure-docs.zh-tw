---
title: 將符號圖層新增至地圖 |Microsoft Azure 對應
description: 瞭解如何將自訂的符號（例如文字或圖示）新增至地圖。 請參閱如何使用 Azure 地圖服務 Web SDK 中的資料來源和符號圖層來實現此目的。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: 72ce95373df0a670179424d8e7ea95254941db1a
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90085921"
---
# <a name="add-a-symbol-layer-to-a-map"></a>將符號圖層新增至地圖

將符號連接至資料來源，並使用它在指定點轉譯圖示或文字。 

使用 WebGL 來呈現符號圖層。 使用符號圖層，在地圖上呈現大型的點集合。 相較于 HTML 標籤，符號圖層會在地圖上轉譯大量的點資料，並提供更好的效能。 不過，符號圖層不支援傳統 CSS 和 HTML 元素來進行樣式設定。  

> [!TIP]
> 根據預設，符號圖層會轉譯資料來源中所有幾何圖形的座標。 若要限制圖層，使其只轉譯點幾何特徵 `filter` ，請將圖層的屬性設定為 `['==', ['geometry-type'], 'Point']` 或 `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` 您想要，也可以包含 MultiPoint 功能。

地圖影像 sprite 管理員會載入符號圖層所使用的自訂影像。 它支援下列影像格式：

- JPEG
- PNG
- SVG
- BMP
- GIF (沒有任何動畫) 

## <a name="add-a-symbol-layer"></a>新增符號圖層

您必須採取幾個步驟，才能將符號圖層新增至地圖。 首先，建立資料來源，並將它加入至對應。 建立符號圖層。 然後，將資料來源傳入符號圖層，以從資料來源取得資料。 最後，將資料新增至資料來源，以便有一些要轉譯的內容。 

下列程式碼示範載入對應之後應新增的內容。 此範例會使用符號圖層，在地圖上呈現單一點。 

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

有四種不同類型的 point 資料可新增至地圖：

- GeoJSON 點幾何-此物件只包含點的座標，而不包含任何其他專案的座標。 `atlas.data.Point`Helper 類別可以用來輕鬆建立這些物件。
- GeoJSON MultiPoint geometry-這個物件包含多個點的座標，其他則沒有。 `atlas.data.MultiPoint`Helper 類別可以用來輕鬆建立這些物件。
- GeoJSON 功能-此物件包含任何 GeoJSON 幾何，以及包含與幾何相關聯之中繼資料的一組屬性。 `atlas.data.Feature`Helper 類別可以用來輕鬆建立這些物件。
- `atlas.Shape` 類別類似于 GeoJSON 功能。 兩者都包含 GeoJSON 幾何和一組屬性，其中包含與幾何相關聯的中繼資料。 如果將 GeoJSON 物件新增至資料來源，則可以輕鬆地在圖層中呈現。 但是，如果 GeoJSON 物件的座標屬性已更新，則資料來源和對應不會變更。 這是因為 JSON 物件中沒有任何機制可觸發更新。 Shape 類別提供的函式可更新其包含的資料。 進行變更時，資料來源和對應會自動收到通知並更新。 

下列程式碼範例會建立 GeoJSON 點幾何，並將其傳遞至 `atlas.Shape` 類別，以便輕鬆地進行更新。 地圖的中心一開始會用來呈現符號。 Click 事件會加入至地圖中，以在引發時，將滑鼠的座標與 shapes 函數搭配使用 `setCoordinates` 。 滑鼠座標會在按一下事件時錄製。 然後，會 `setCoordinates` 更新地圖上符號的位置。

<br/>

<iframe height='500' scrolling='no' title='切換釘選位置' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>切換釘選位置</a>。
</iframe>

> [!TIP]
> 根據預設，符號圖層會隱藏重迭的符號來將符號轉譯優化。 當您放大時，隱藏的符號就會變成可見。 若要停用這項功能並隨時轉譯所有符號，請將 `allowOverlap` 選項的屬性設定 `iconOptions` 為 `true` 。

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>將自訂圖示新增至符號圖層

使用 WebGL 來呈現符號圖層。 所有這類資源 (例如圖示影像) 必須載入 WebGL 內容中。 此範例說明如何將自訂圖示新增至地圖資源。 然後，這個圖示會用來在地圖上以自訂符號呈現點資料。 符號圖層的 `textField` 屬性需要指定運算式。 在此情況下，我們想要呈現溫度屬性。 因為溫度是數位，所以必須轉換成字串。 此外，我們還想要將 "° F" 附加至該檔案。 運算式可以用來進行這種串連; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='自訂符號影像圖示' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>自訂符號影像圖示</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

> [!TIP]
> Azure 地圖服務 Web SDK 提供數個可自訂的影像範本，以供作為符號圖層使用。 如需詳細資訊，請參閱[如何使用影像範本](how-to-use-image-templates-web-sdk.md)文件。

## <a name="customize-a-symbol-layer"></a>自訂符號圖層 

符號圖層有許多可用的樣式選項。 以下是用來測試各種樣式選項的工具。

<br/>

<iframe height='700' scrolling='no' title='符號圖層選項' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/PxVXje/'>符號圖層選項</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

> [!TIP]
> 當您只想要轉譯具有符號圖層的文字時，您可以將 `image` 圖示選項的屬性設定為，以隱藏圖示 `'none'` 。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)

> [!div class="nextstepaction"]
> [TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions)

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
