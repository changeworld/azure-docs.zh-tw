---
title: Azure 地圖服務 Web SDK 中的影像範本 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何在 Microsoft Azure Maps Web SDK 中，使用 HTML 標籤和各種層級的影像範本。
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 12c407ecacdce6b9052ca70ef70a2ae87928b6c2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87064687"
---
# <a name="how-to-use-image-templates"></a>如何使用映像範本

影像可以搭配 HTML 標籤和 Azure 地圖服務 web SDK 內的各種層級使用：

 - 符號圖層可以使用影像圖示呈現地圖上的點。 符號也可以沿著行路徑呈現。
 - 您可以使用填滿模式影像來轉譯多邊形圖層。 
 - HTML 標籤可以使用影像和其他 HTML 專案來呈現點。

為了確保層級有良好的效能，請先將影像載入地圖影像 sprite 資源，再進行轉譯。 SymbolLayer 的[IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)預設會將幾個色彩中的數個標記影像預先載入地圖影像 sprite。 這些標記影像和更多功能可作為 SVG 範本。 它們可用來建立具有自訂縮放比例的影像，或當做客戶主要和次要色彩使用。 總共提供42個影像範本：27個符號圖示和15個多邊形填滿模式。

您可以使用函式，將影像範本新增至地圖影像 sprite 資源 `map.imageSprite.createFromTemplate` 。 此函式最多允許傳入五個參數;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

`id`是您所建立的唯一識別碼。 `id`新增至地圖影像 sprite 時，會指派給影像。 在圖層中使用此識別碼來指定要呈現的影像資源。 會 `templateName` 指定要使用的映射範本。 `color`選項會設定影像的主要色彩，而選項會 `secondaryColor` 設定影像的次要色彩。 `scale`選項會先調整影像範本，再將它套用至映射 sprite。 當影像套用至影像 sprite 時，它會轉換成 PNG。 為了確保呈現清晰，最好先相應增加影像範本，再將它加入至 sprite，而不是在圖層中相應增加。

此函式會以非同步方式將影像載入影像 sprite。 因此，它會傳回您可以等候此函數完成的承諾。

下列程式碼示範如何從其中一個內建範本建立影像，並將其與符號層搭配使用。

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>使用影像範本搭配符號圖層

將影像範本載入至地圖影像 sprite 之後，您可以在的選項中參考影像資源識別碼，以將其轉譯為符號圖層中的符號 `image` `iconOptions` 。

下列範例會使用 `marker-flat` 具有青色主要色彩和白色次要色彩的影像範本來呈現符號圖層。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="具有內建圖示範本的符號圖層" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在 CodePen 上 Azure 地圖服務（），查看<a href='https://codepen.io/azuremaps/pen/VoQMPp/'>具有內建圖示範本的畫筆符號圖層</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>沿著行路徑使用影像範本

將影像範本載入地圖影像 sprite 後，就可以將 LineString 加入至資料來源，並搭配使用符號圖層與 `lineSpacing` 選項，並在 th 的選項中參考影像資源的識別碼，藉此在行的路徑中轉譯 `image` `iconOptions` 。 

下列範例會在地圖上呈現粉紅色線條，並使用 `car` 具有寶藍藍色主要色彩和白色次要色彩之影像範本的符號圖層。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="具有內建圖示範本的線條圖層" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在 CodePen 上 Azure 地圖服務（），查看<a href='https://codepen.io/azuremaps/pen/KOQvJe/'>具有內建圖示範本的畫筆線條圖層</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

> [!TIP]
> 如果影像範本指向，請將 `rotation` 符號圖層的圖示選項設定為90，如果您想要讓它指向與線條相同的方向。

## <a name="use-an-image-template-with-a-polygon-layer"></a>使用影像範本與多邊形圖層

將影像範本載入地圖影像 sprite 之後，您可以在圖層的選項中參考影像資源識別碼，以將其轉譯為多邊形圖層中的填滿模式 `fillPattern` 。

下列範例會使用 `dot` 具有紅色主要色彩和透明次要色彩的影像範本來呈現多邊形圖層。  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="使用內建圖示範本填滿多邊形" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在 CodePen 上 Azure 地圖服務（），查看<a href='https://codepen.io/azuremaps/pen/WVMEmz/'>使用內建圖示範本的畫筆填滿多邊形</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

> [!TIP]
> 設定填滿模式的次要色彩，可讓您更輕鬆地看到基礎地圖仍會提供主要模式。 

## <a name="use-an-image-template-with-an-html-marker"></a>使用影像範本搭配 HTML 標籤

您可以使用函式來抓取影像範本，並將其當做 `altas.getImageTemplate` HTML 標籤的內容使用。 範本可以傳遞至 `htmlContent` 標記的選項，然後使用 `color` 、 `secondaryColor` 和選項進行自訂 `text` 。

下列範例會使用 `marker-arrow` 具有紅色主要色彩的範本、粉紅色的次要色彩，以及 "00" 的文字值。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="具有內建圖示範本的 HTML 標籤" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在 CodePen 上 Azure 地圖服務（），查看<a href='https://codepen.io/azuremaps/pen/EqQvzq/'>具有內建圖示範本</a>的手寫筆 HTML 標籤 <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>


> [!TIP]
> 影像範本也可以在地圖之外使用。 GetImageTemplate 運作會傳回具有預留位置的 SVG 字串;`{color}`, `{secondaryColor}`, `{scale}`, `{text}`. 取代這些預留位置值，以建立有效的 SVG 字串。 接著，您可以將 SVG 字串直接新增至 HTML DOM，或將它轉換成資料 URI，並將它插入影像標記中。 例如:
> ```JavaScript
> //Retrieve an SVG template and replace the placeholder values.
> var svg = atlas.getImageTemplate('marker').replace(/{color}/, 'red').replace(/{secondaryColor}/, 'white').replace(/{text}/, '').replace(/{scale}/, 1);
>
> //Convert to data URI for use in image tags.
> var dataUri = 'data:image/svg+xml;base64,' + btoa(svg);
> ```

## <a name="create-custom-reusable-templates"></a>建立自訂可重複使用的範本

如果您的應用程式使用不同圖示的相同圖示，或如果您要建立可新增其他影像範本的模組，您可以從 Azure 地圖服務 web SDK 輕鬆地新增和取出這些圖示。 在命名空間上使用下列靜態函式 `atlas` 。

| 名稱 | 傳回類型 | 描述 | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | 將自訂 SVG 影像範本新增至 [阿特拉斯] 命名空間。 |
|  `getImageTemplate(templateName: string, scale?: number)`| 字串 | 依名稱捕獲 SVG 範本。 |
| `getAllImageTemplateNames()` | string[] |  依名稱捕獲 SVG 範本。 |

SVG 影像範本支援下列預留位置值：

| 預留位置 | 描述 |
|-|-|
| `{color}` | 主要色彩。 | 
| `{secondaryColor}` | 次要色彩。 | 
| `{scale}` | SVG 影像在新增至地圖影像 sprite 時，會轉換成 png 影像。 此預留位置可用來在轉換範本之前進行調整，以確保能清楚呈現。 | 
| `{text}` | 與 HTML 標籤搭配使用時，要呈現文字的位置。 |

下列範例示範如何採用 SVG 範本，並將其新增至 Azure 地圖服務 web SDK 作為可重複使用的圖示範本。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="將自訂圖示範本新增至塔命名空間" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱 CodePen 上的 Azure 地圖服務（），<a href='https://codepen.io/azuremaps/pen/NQyvEX/'>以將自訂圖示範本新增至 [塔命名空間</a>] <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="list-of-image-templates"></a>影像範本清單

下表列出 Azure 地圖服務 web SDK 中目前可用的所有映射範本。 範本名稱位於每個影像的上方。 根據預設，主要色彩是藍色，而次要色彩是白色。 為了讓次要色彩在白色背景上更容易看到，下列影像的次要色彩會設定為黑色。

**符號圖示範本**

:::row:::
   :::column span="":::
      marker
   :::column-end:::
   :::column span="":::
      標記-粗
   :::column-end:::
   :::column span="":::
      標記-圓形
   :::column-end:::
   :::column span="":::
      標記-一般
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![標記圖示](./media/image-templates/marker.png)
   :::column-end:::
   :::column span="":::
      ![標記-粗圖示](./media/image-templates/marker-thick.png)
   :::column-end:::
   :::column span="":::
      ![標記-圓形圖示](./media/image-templates/marker-circle.png)
   :::column-end:::
   :::column span="":::
      ![標記-平面圖標](./media/image-templates/marker-flat.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      標記-正方形
   :::column-end:::
   :::column span="":::
      標記-方形-叢集
   :::column-end:::
   :::column span="":::
      標記-箭號
   :::column-end:::
   :::column span="":::
      標記-球-圖釘
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![標記-正方形圖示](./media/image-templates/marker-square.png)
   :::column-end:::
   :::column span="":::
      ![標記-方形-叢集圖示](./media/image-templates/marker-square-cluster.png)
   :::column-end:::
   :::column span="":::
      ![標記-箭號圖示](./media/image-templates/marker-arrow.png)
   :::column-end:::
   :::column span="":::
      ![標記-球形釘選圖示](./media/image-templates/marker-ball-pin.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      標記-方形圓角
   :::column-end:::
   :::column span="":::
      標記-方形-進位-叢集
   :::column-end:::
   :::column span="":::
      flag
   :::column-end:::
   :::column span="":::
      旗標-三角形
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![標記-方形圓角圖示](./media/image-templates/marker-square-rounded.png)
   :::column-end:::
   :::column span="":::
      ![標記-方形-進位-叢集圖示](./media/image-templates/marker-square-rounded-cluster.png)
   :::column-end:::
   :::column span="":::
      ![旗標圖示](./media/image-templates/flag.png)
   :::column-end:::
   :::column span="":::
      ![旗標-三角形圖示](./media/image-templates/flag-triangle.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      三角形
   :::column-end:::
   :::column span="":::
      三角形-粗
   :::column-end:::
   :::column span="":::
      三角形-向上箭號
   :::column-end:::
   :::column span="":::
      三角形-箭號-左
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![三角形圖示](./media/image-templates/triangle.png)
   :::column-end:::
   :::column span="":::
      ![三角形-粗圖示](./media/image-templates/triangle-thick.png)
   :::column-end:::
   :::column span="":::
      ![三角形-向上箭號圖示](./media/image-templates/triangle-arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![三角形-箭號左圖示](./media/image-templates/triangle-arrow-left.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      六邊形
   :::column-end:::
   :::column span="":::
      六邊形-粗
   :::column-end:::
   :::column span="":::
      六邊形-圓角
   :::column-end:::
   :::column span="":::
      六邊形-進位-粗
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![六邊形圖示](./media/image-templates/hexagon.png)
   :::column-end:::
   :::column span="":::
      ![六邊形-粗圖示](./media/image-templates/hexagon-thick.png)
   :::column-end:::
   :::column span="":::
      ![六邊形-圓角圖示](./media/image-templates/hexagon-rounded.png)
   :::column-end:::
   :::column span="":::
      ![六邊形-進位-粗圖示](./media/image-templates/hexagon-rounded-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      釘選
   :::column-end:::
   :::column span="":::
      圖釘-round
   :::column-end:::
   :::column span="":::
      圓角-正方形
   :::column-end:::
   :::column span="":::
      進位-正方形-粗
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![釘選圖示](./media/image-templates/pin.png)
   :::column-end:::
   :::column span="":::
      ![圖釘-round 圖示](./media/image-templates/pin-round.png)
   :::column-end:::
   :::column span="":::
      ![圓角-正方形圖示](./media/image-templates/rounded-square.png)
   :::column-end:::
   :::column span="":::
      ![圓角-方形-粗圖示](./media/image-templates/rounded-square-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      向上箭號
   :::column-end:::
   :::column span="":::
      向上箭號-細
   :::column-end:::
   :::column span="":::
      car
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![向上箭號圖示](./media/image-templates/arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![向上箭號-精簡型圖示](./media/image-templates/arrow-up-thin.png)
   :::column-end:::
   :::column span="":::
      ![汽車圖示](./media/image-templates/car.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::


**多邊形填滿模式範本**

:::row:::
   :::column span="":::
      棋盤
   :::column-end:::
   :::column span="":::
      檢查程式-旋轉
   :::column-end:::
   :::column span="":::
      圓形
   :::column-end:::
   :::column span="":::
      圓圈-間距
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![檢查工具圖示](./media/image-templates/checker.png)
   :::column-end:::
   :::column span="":::
      ![檢查器-旋轉圖示](./media/image-templates/checker-rotated.png)
   :::column-end:::
   :::column span="":::
      ![圓形圖示](./media/image-templates/circles.png)
   :::column-end:::
   :::column span="":::
      ![圓圈-間距圖示](./media/image-templates/circles-spaced.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      對角線-向上
   :::column-end:::
   :::column span="":::
      對角線-行-向下
   :::column-end:::
   :::column span="":::
      對角線-向上
   :::column-end:::
   :::column span="":::
      對角線-向下
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![對角線-線條-向上圖示](./media/image-templates/diagonal-lines-up.png)
   :::column-end:::
   :::column span="":::
      ![對角線-行向下圖示](./media/image-templates/diagonal-lines-down.png)
   :::column-end:::
   :::column span="":::
      ![對角線-向上圖示](./media/image-templates/diagonal-stripes-up.png)
   :::column-end:::
   :::column span="":::
      ![對角線-向下鍵](./media/image-templates/diagonal-stripes-down.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      格線
   :::column-end:::
   :::column span="":::
      旋轉格線
   :::column-end:::
   :::column span="":::
      旋轉格線-條紋
   :::column-end:::
   :::column span="":::
      x-填滿
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![格線圖示](./media/image-templates/grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![旋轉格線圖示](./media/image-templates/rotated-grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![旋轉格線-條紋圖示](./media/image-templates/rotated-grid-stripes.png)
   :::column-end:::
   :::column span="":::
      ![x-填滿圖示](./media/image-templates/x-fill.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      鋸齒-紋
   :::column-end:::
   :::column span="":::
      鋸齒-紋-垂直
   :::column-end:::
   :::column span="":::
      句點
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![鋸齒-紋圖示](./media/image-templates/zig-zag.png)
   :::column-end:::
   :::column span="":::
      ![鋸齒-紋-垂直圖示](./media/image-templates/zig-zag-vertical.png)
   :::column-end:::
   :::column span="":::
      ![點圖示](./media/image-templates/dots.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
<br>

**預先載入的影像圖示**

對應會使用 `marker` 、和範本，將一組圖示預先載入至地圖影像 sprite `pin` `pin-round` 。 下表列出這些圖示名稱及其色彩值。

| 圖示名稱 | color | secondaryColor |
|-----------|-------|----------------|
| `marker-black` | `#231f20` | `#ffffff` |
| `marker-blue` | `#1a73aa` | `#ffffff` |
| `marker-darkblue` | `#003963` | `#ffffff` |
| `marker-red` | `#ef4c4c` | `#ffffff` |
| `marker-yellow` | `#f2c851` | `#ffffff` |
| `pin-blue` | `#2072b8` | `#ffffff` |
| `pin-darkblue` | `#003963` | `#ffffff` |
| `pin-red` | `#ef4c4c` | `#ffffff` |
| `pin-round-blue` | `#2072b8` | `#ffffff` |
| `pin-round-darkblue` | `#003963` | `#ffffff` |
| `pin-round-red` | `#ef4c4c` | `#ffffff` |


## <a name="try-it-now-tool"></a>立即試用工具

使用下列工具，您可以透過各種方式轉譯不同的內建影像範本，並自訂主要和次要色彩和縮放比例。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="圖示範本選項" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱 CodePen 上的 Azure 地圖服務（），以查看畫筆<a href='https://codepen.io/azuremaps/pen/NQyaaO/'>圖示範本選項</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'> </a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [塔命名空間](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

如需可使用影像範本的更多程式碼範例，請參閱下列文章：

> [!div class="nextstepaction"]
> [新增符號圖層](map-add-pin.md)

> [!div class="nextstepaction"]
> [新增線條圖層](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](map-add-shape.md)

> [!div class="nextstepaction"]
> [新增 HTML 標記](map-add-bubble-layer.md)
