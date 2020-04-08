---
title: Azure 地圖 Web SDK 中的影像樣本 ( A) ( Azure )微軟 Azure 地圖
description: 在本文中,您將學習如何在 Microsoft Azure 地圖 Web SDK 中使用帶有 HTML 標記和各種圖層的圖像樣本。
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: ee8e8ee4ca64de0390b6fa34e36fb4d06348a8ac
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804804"
---
# <a name="how-to-use-image-templates"></a>如何使用映像範本

影像可用於 Azure 地圖 Web SDK 中的 HTML 標籤和各種圖層:

 - 符號圖層可以使用圖像圖示渲染地圖上的點。 符號也可以沿線路徑呈現。
 - 可以使用填充圖案圖像渲染多邊形圖層。 
 - HTML 標籤可以使用影像和其他 HTML 元素呈現點。

為了確保圖層性能良好,在渲染之前,將圖像載入到地圖圖像精靈資源中。 默認情況下,SymbolLayer 的[IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)會以少量顏色預載入幾個標記影像到地圖圖像精靈中。 這些標記圖像等可作為 SVG 範本提供。 它們可用於創建具有自定義比例的圖像,或用作客戶主色和次要顏色。 總共提供了 42 個圖像範本:27 個符號圖示和 15 個多邊形填充圖案。

可以使用`map.imageSprite.createFromTemplate`函數將圖像範本添加到地圖圖像精靈資源中。 此功能允許最多傳入五個參數;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

您`id`建立的唯一識別碼。 當`id`圖像添加到地圖圖像精靈時,將分配給該圖像。 在圖層中使用此標識符來指定要呈現的圖像資源。 指定`templateName`要使用的圖像範本。 該`color`選項設定影像的主顏色`secondaryColor`, 選項設置圖像的次色。 在`scale`將圖像範本應用於圖像子畫面之前,該選項將縮放圖像範本。 當圖像應用於圖像精靈時,它將轉換為 PNG。 為了確保清晰的渲染,最好在將圖像範本添加到子畫面之前將其放大,而不是將其放大到圖層中。

此功能非同步地將圖像載入到圖像子畫面中。 因此,它返回一個承諾,您可以等待此函數完成。

以下代碼演示如何從其中一個內置範本創建圖像,並將其與符號圖層一起使用。

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

## <a name="use-an-image-template-with-a-symbol-layer"></a>將影像樣本與符號圖層一起使用

將圖像範本載入地圖影像雪碧中後,可以通過引用選項中的圖像資源`image`ID 將其呈現為符號圖層中的符`iconOptions`號。

以下範例使用具有青色原色和白色次`marker-flat`色的圖像範本渲染符號圖層。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="帶有內建圖示範本的符號圖層" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上,透過 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>地圖 () 檢視<a href='https://codepen.io/azuremaps/pen/VoQMPp/'>具有內建圖示樣本的筆符號圖層</a>。
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>沿線路徑使用影像樣本

將圖像範本載入地圖影像 sprite 中後,可以透過資料來源加入 LineString 並`lineSpacing`使用帶有 選項的符號圖`image``iconOptions`層以及引用 th 選項中圖像資源 ID 來沿線路徑呈現。 

以下示例在地圖上渲染一條粉紅色線,並使用具有道奇藍色原色和白色`car`次要顏色的圖像範本使用符號圖層。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="圖示範本的線圖層" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上,透過 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>地圖 () 查看<a href='https://codepen.io/azuremaps/pen/KOQvJe/'>具有內建圖示樣本的筆線圖層</a>。
</iframe>

> [!TIP]
> 如果圖像範本指向該點,如果希望`rotation`符號圖層指向與線相同的方向,則將符號圖層的圖示選項設置為 90。

## <a name="use-an-image-template-with-a-polygon-layer"></a>將影像樣本與多邊形圖層一起使用

將圖像範本載入地圖影像子畫面中後,可以通過引用圖層`fillPattern`選項中的圖像資源 ID 將其呈現為多邊形圖層中的填充圖案。

以下範例使用紅色原色和透明次色`dot`的圖像範本渲染多邊形圖層。  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="使用內建圖示樣本填充多邊形" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上,透過 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 () 使用<a href='https://codepen.io/azuremaps/pen/WVMEmz/'>內建圖示樣本檢視筆填充多邊形</a>。
</iframe>

> [!TIP]
> 設置填充圖案的輔助顏色可以更輕鬆地查看基礎地圖仍將提供主圖案。 

## <a name="use-an-image-template-with-an-html-marker"></a>使用含有 HTML 標籤的影像樣本

可以使用`altas.getImageTemplate`函數檢索圖像範本,並將其用作 HTML 標記的內容。 樣本可以`htmlContent`傳遞到 標記的選項,然後使用`color`和`secondaryColor``text`選項進行自定義。

以下示例使用紅色原`marker-arrow`色、粉紅色輔助色和文本值"00"的範本。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="含有內建圖示樣本的 HTML 標籤" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上,透過 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 () 查看<a href='https://codepen.io/azuremaps/pen/EqQvzq/'>具有內建圖示範本的</a>筆 HTML 標記。
</iframe>

## <a name="create-custom-reusable-templates"></a>建立自訂可重用樣本

如果應用程式使用不同的圖示使用同一圖示,或者正在創建添加其他圖像範本的模組,則可以輕鬆地從 Azure 地圖 Web SDK 添加和檢索這些圖示。 在`atlas`命名空間上使用以下靜態函數。

| 名稱 | 傳回類型 | 描述 | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | 將自訂 SVG 影像樣本添加到地圖集命名空間。 |
|  `getImageTemplate(templateName: string, scale?: number)`| 字串 | 按名稱檢索 SVG 範本。 |
| `getAllImageTemplateNames()` | string[] |  按名稱檢索 SVG 範本。 |

SVG 影像樣本支援以下占位符值:

| 預留位置 | 描述 |
|-|-|
| `{color}` | 主顏色。 | 
| `{secondaryColor}` | 輔助顏色。 | 
| `{scale}` | SVG 圖像在添加到地圖圖像精靈時轉換為 png 圖像。 此占位符可用於在轉換範本之前縮放範本,以確保範本呈現清晰。 | 
| `{text}` | 與 HTML 標記一起使用時要呈現文本的位置。 |

下面的範例展示如何採用 SVG 樣本,並將其作為可重用圖示樣本添加到 Azure 地圖 Web SDK 中。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="將自訂圖示樣本加入到地圖集命名空間" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參考 CodePen 上按 Azure 地圖<a href='https://codepen.io/azuremaps'>@azuremaps</a>()<a href='https://codepen.io/azuremaps/pen/NQyvEX/'>向地圖集新增自訂圖示樣本</a><a href='https://codepen.io'>的筆</a>。
</iframe>

## <a name="list-of-image-templates"></a>影像樣本清單

此表列出了 Azure 地圖 Web SDK 中當前提供的所有映像範本。 範本名稱在每個圖像上方。 默認情況下,主色為藍色,輔助顏色為白色。 為了使次要顏色更容易在白色背景上看到,以下圖像將次色設置為黑色。

**符號圖示樣本**

|||||
|:-:|:-:|:-:|:-:|
| marker | 標記厚 | 標記圓 | 標記平面 |
|![標籤圖示](./media/image-templates/marker.png)|![標籤厚圖示](./media/image-templates/marker-thick.png)|![標籤圖示](./media/image-templates/marker-circle.png)|![標記平面圖示](./media/image-templates/marker-flat.png)|
||||
| 標記方 | 標記-方形群集 | 標記箭號 | 標記-球針 | 
|![標記方形圖示](./media/image-templates/marker-square.png)|![標記方形叢集圖示](./media/image-templates/marker-square-cluster.png)|![標記箭號圖示](./media/image-templates/marker-arrow.png)|![標記-球針圖示](./media/image-templates/marker-ball-pin.png)|
||||
| 標記-平方圓舍五入 | 標記-平方圓-聚類 | 旗標 | 標誌三角形 |
| ![標記方形圓角圖示](./media/image-templates/marker-square-rounded.png) | ![標記-方形圓角-群集圖示](./media/image-templates/marker-square-rounded-cluster.png) | ![標誌圖示](./media/image-templates/flag.png) | ![標誌三角形圖示](./media/image-templates/flag-triangle.png) |
||||
| 三角形 | 三角形厚 | 三角形箭頭向上 | 三角形箭頭-左側 |
| ![三角圖示](./media/image-templates/triangle.png) | ![三角形厚的圖示](./media/image-templates/triangle-thick.png) | ![三角箭頭圖示](./media/image-templates/triangle-arrow-up.png) | ![三角箭頭左方圖示](./media/image-templates/triangle-arrow-left.png) |
||||
| 六 角 | 六邊形厚 | 六邊形圓潤 | 六邊形-圓形厚 |
| ![六邊形圖示](./media/image-templates/hexagon.png) | ![六邊形厚圖示](./media/image-templates/hexagon-thick.png) | ![六邊形圓角圖示](./media/image-templates/hexagon-rounded.png) | ![六邊形四捨五入厚圖示](./media/image-templates/hexagon-rounded-thick.png) |
||||
| 固定 | 針圓 | 圓方形 | 圓方形厚 |
| ![釘選圖示](./media/image-templates/pin.png) | ![針圓圖示](./media/image-templates/pin-round.png) | ![圓形方形圖示](./media/image-templates/rounded-square.png) | ![圓形方形厚圖示](./media/image-templates/rounded-square-thick.png) |
||||
| 箭號 | 箭頭向上薄 | 汽車 ||
| ![箭號圖示](./media/image-templates/arrow-up.png) | ![箭頭向上精簡圖示](./media/image-templates/arrow-up-thin.png) | ![汽車圖示](./media/image-templates/car.png) | |

**多邊形填充圖案範本**

|||||
|:-:|:-:|:-:|:-:|
| 檢查 | 跳棋旋轉 | 圓形 | 圓間距 |
| ![檢查器圖示](./media/image-templates/checker.png) | ![跳棋旋轉圖示](./media/image-templates/checker-rotated.png) | ![圓圈圖示](./media/image-templates/circles.png) | ![圓間距圖示](./media/image-templates/circles-spaced.png) |
|||||
| 對角線 | 對角線向下 | 對角條紋向上 | 對角條紋向下 |
| ![對角線排列圖示](./media/image-templates/diagonal-lines-up.png) | ![對角線向下圖示](./media/image-templates/diagonal-lines-down.png) | ![對角條紋向上圖示](./media/image-templates/diagonal-stripes-up.png) | ![對角條紋向下圖示](./media/image-templates/diagonal-stripes-down.png) |
|||||
| 網線線 | 旋轉格線線 | 旋轉格線條紋 | x 填滿 |
| ![格線圖示](./media/image-templates/grid-lines.png) | ![旋轉格線圖示](./media/image-templates/rotated-grid-lines.png) | ![旋轉格線條紋圖示](./media/image-templates/rotated-grid-stripes.png) | ![x 填滿圖示](./media/image-templates/x-fill.png) |
|||||
| 鋸齒形 | 鋸齒形垂直 | 點 |  |
| ![鋸齒圖示](./media/image-templates/zig-zag.png) | ![鋸齒形垂直圖示](./media/image-templates/zig-zag-vertical.png) | ![點圖示](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>立即試用工具

使用以下工具,您可以通過多種方式呈現不同的內置圖像範本,並自定義主顏色和次要顏色和比例。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="圖示樣本選項" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure 映射 () 檢視筆<a href='https://codepen.io/azuremaps/pen/NQyaaO/'>圖示樣本選項</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [影像 Sprite 管理員](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [地圖集命名空間](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

有關可以使用影像範本的更多代碼範例,請參閱以下文章:

> [!div class="nextstepaction"]
> [新增符號層次](map-add-pin.md)

> [!div class="nextstepaction"]
> [新增線條圖層](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](map-add-shape.md)

> [!div class="nextstepaction"]
> [新增 HTML 標記](map-add-bubble-layer.md)
