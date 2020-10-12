---
title: 新增簡單資料層 |Microsoft Azure 對應
description: 瞭解如何使用 Azure 地圖服務 Web SDK 所提供的空間 IO 模組來新增簡單的資料層。
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 75b193017bbfccbae0f299c0a1db2fe0e34f1810
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91321793"
---
# <a name="add-a-simple-data-layer"></a>新增簡單的資料圖層

空間 IO 模組提供 `SimpleDataLayer` 類別。 此類別可讓您輕鬆地在地圖上呈現樣式的功能。 它甚至可以轉譯具有樣式屬性的資料集，以及包含混合幾何類型的資料集。 簡單的資料層藉由包裝多個轉譯層和使用樣式表達式來達成這項功能。 樣式表達式會搜尋這些包裝圖層內功能的通用樣式屬性。 函式和函式會 `atlas.io.read` `atlas.io.write` 使用這些屬性，將樣式讀取和寫入至支援的檔案格式。 將屬性新增至支援的檔案格式之後，檔案就可以用於各種用途。 例如，檔案可以用來在地圖上顯示樣式的功能。

除了設定樣式功能之外， `SimpleDataLayer` 還提供內建的快顯視窗功能與快顯範本。 按一下功能時，就會顯示快顯視窗。 如有需要，可以停用預設的快顯視窗功能。 這一層也支援叢集資料。 當您按一下叢集時，地圖將會縮放至叢集，並將它擴充到個別的點和子群集。

`SimpleDataLayer`類別的目的是要用於具有許多幾何類型的大型資料集，以及在功能上套用的許多樣式。 使用時，此類別會增加包含樣式表達式的六個層級的額外負荷。 因此，在某些情況下，使用核心呈現圖層會更有效率。 例如，使用核心層轉譯一些幾何類型，以及在功能上呈現一些樣式

## <a name="use-a-simple-data-layer"></a>使用簡單的資料層

使用 `SimpleDataLayer` 類別的方式類似于使用其他轉譯層。 下列程式碼說明如何在地圖中使用簡單的資料層：

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

將功能加入至資料來源。 然後，簡單資料層會找出最能轉譯功能的方式。 個別功能的樣式可以設定為功能的屬性。 下列程式碼顯示內容設為的 GeoJSON 點功能 `color` `red` 。 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

下列程式碼會使用簡單的資料層呈現上述的點特徵。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="使用簡單資料層" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> 在 >codepen 上查看畫筆 Azure 地圖服務 () ，以<a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>使用簡單的資料層</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

簡單資料層的真正威力在於：

- 資料來源中有數種不同類型的功能;或
- 資料集中的功能有個別設定的多個樣式屬性;或
- 您不確定資料集確切包含什麼。

例如，剖析 XML 資料摘要時，您可能不知道功能的確切樣式和幾何類型。 下列範例會呈現 KML 檔案的功能，以顯示簡單資料層的強大功能。 它也會示範簡單資料層級類別所提供的各種選項。

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="簡易資料層選項" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> 在 >codepen 上 Azure 地圖服務 () ，查看畫筆 <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>簡單的資料層選項</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'> </a>。
</iframe>


> [!NOTE]
> 這個簡單的資料層會使用 [popup](map-add-popup.md#add-popup-templates-to-the-map) 樣板類別，以表格形式顯示 KML 的球標或功能屬性。 根據預設，在快顯視窗中轉譯的所有內容都會以安全性功能的形式在 iframe 內進行沙箱化。 不過，有一些限制：
>
> - 已停用所有腳本、表單、指標鎖定和頂端導覽功能。 當您按一下時，可以在新的索引標籤中開啟連結。 
> - 不支援 iframe 上參數的舊版瀏覽器 `srcdoc` 將受限於轉譯少量的內容。
> 
> 如果您信任正在載入至快顯視窗中的資料，而且可能想要載入到快顯視窗的這些腳本能夠存取您的應用程式，您可以將快顯範本選項設定為 false，以停用此 `sandboxContent` 選項。 

## <a name="default-supported-style-properties"></a>預設支援的樣式屬性

如先前所述，簡單資料層會包裝數個核心轉譯圖層：反升、符號、線條、多邊形和拉伸多邊形。 然後，它會使用運算式來搜尋個別功能的有效樣式屬性。

Azure 地圖服務和 GitHub 樣式屬性是兩組支援的屬性名稱。 不同 Azure 地圖服務層選項的大部分屬性名稱都支援做為簡單資料層中功能的樣式屬性。 運算式已新增至某些圖層選項，以支援 GitHub 一般使用的樣式屬性名稱。 這些屬性名稱是由 [GitHub 的 GeoJSON 對應支援](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)所定義，而且是用來為平臺內儲存和轉譯的 GeoJSON 檔案提供樣式。 除了樣式屬性以外，簡單的資料層都支援所有 GitHub 的樣式屬性 `marker-symbol` 。

如果讀取器跨越較不常見的樣式屬性，則會將它轉換成最接近的 Azure 地圖服務樣式屬性。 此外，您可以使用簡單資料層的函式來覆寫預設樣式表達式， `getLayers` 並更新任何圖層上的選項。

下列各節提供簡單資料層所支援之預設樣式屬性的詳細資料。 支援的屬性名稱的順序也是屬性的優先順序。 如果針對相同圖層選項定義了兩個樣式屬性，則清單中的第一個屬性會具有較高的優先順序。 色彩可以是任何 CSS3 色彩值;HEX、RGB、RGBA、HSL、HSLA 或指名的色彩值。

### <a name="bubble-layer-style-properties"></a>氣泡圖層樣式屬性

如果某項功能為 `Point` 或 `MultiPoint` ，而且此功能沒有 `image` 會當做自訂圖示使用的屬性，以將點轉譯為符號，則會使用來呈現此功能 `BubbleLayer` 。

| 圖層選項 | 支援的屬性名稱 (s)  | 預設值 |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup>、 `marker-size` <sup>2</sup>、 `scale` <sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1 \] `size` ：和 `scale` 值會被視為純量值，而且會乘以 `8`

\[2 \] 如果指定了 GitHub `marker-size` 選項，則會將下列值用於 radius。

| 標記大小 | 半徑 |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

您也可以使用反升圖圖層來呈現叢集。 叢集的 radius 預設會設定為 `16` 。 叢集的色彩會根據叢集中的點數數目而有所不同，如下所示：

| 點數 | Color    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt; 10     | `green`  |

### <a name="symbol-style-properties"></a>符號樣式屬性

如果功能是 `Point` 或 `MultiPoint` ，而且功能和具有 `image` 會當做自訂圖示使用的屬性，以將點轉譯為符號，則會使用來呈現此功能 `SymbolLayer` 。

| 圖層選項 | 支援的屬性名稱 (s)  | 預設值 |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`， `marker-size` <sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1 \] 如果指定了 GitHub `marker-size` 選項，則圖示大小選項將會使用下列值。

| 標記大小 | 符號樣式 |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

如果點功能是叢集，則會將 `point_count_abbreviated` 屬性轉譯為文字標籤。 不會呈現任何影像。

### <a name="line-style-properties"></a>線條樣式屬性

如果功能是 `LineString` 、 `MultiLineString` 、 `Polygon` 或 `MultiPolygon` ，則會使用來呈現此功能 `LineLayer` 。

| 圖層選項 | 支援的屬性名稱 (s)  | 預設值 |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>多邊形樣式屬性

如果功能是 `Polygon` 或 `MultiPolygon` ，而且此功能沒有 `height` 屬性，或 `height` 屬性為零，則會使用來呈現此功能 `PolygonLayer` 。

| 圖層選項 | 支援的屬性名稱 (s)  | 預設值 |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>拉伸多邊形樣式屬性

如果功能是 `Polygon` 或 `MultiPolygon` ，而且具有 `height` 值大於0的屬性，則會使用來呈現此功能 `PolygonExtrusionLayer` 。

| 圖層選項 | 支援的屬性名稱 (s)  | 預設值 |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [讀取和寫入空間資料](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [新增 OGC 地圖圖層](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [連接到 WFS 服務](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [利用核心作業](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支援的資料格式詳細資料](spatial-io-supported-data-format-details.md)
