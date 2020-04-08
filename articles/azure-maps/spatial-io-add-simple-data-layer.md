---
title: 新增簡單的資料層 |微軟 Azure 地圖
description: 瞭解如何使用 Azure 地圖 Web SDK 提供的空間 IO 模組添加簡單的資料層。
author: philmea
ms.author: philmea
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8862c33b7660b8130f692dc4beea89a7b6b5f5ad
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804481"
---
# <a name="add-a-simple-data-layer"></a>新增簡單的資料層

空間IO模組提供`SimpleDataLayer`類。 此類使在地圖上渲染樣式要素變得容易。 它甚至可以渲染具有樣式屬性的數據集和包含混合幾何類型的數據集。 簡單數據層通過包裝多個呈現圖層並使用樣式運算式來實現此功能。 樣式運算式搜索這些包裝圖層內要素的常見樣式屬性。 函數`atlas.io.read``atlas.io.write`和 函數使用這些屬性將樣式讀取和寫入受支援的檔案格式。 將屬性添加到受支援的檔案格式後,該檔可用於各種用途。 例如,該檔可用於在地圖上顯示樣式特徵。

除了樣式功能外,還提供`SimpleDataLayer`帶有彈出範本的內置彈出視窗功能。 按一下要素時,將顯示彈出視窗。 如果需要,可以禁用默認彈出視窗功能。 此層還支援群集數據。 單擊群集時,地圖將放大到群集中,並將其擴展到單個點和子群集中。

該`SimpleDataLayer`類用於具有許多幾何類型和許多應用於要素樣式的大型數據集。 使用時,此類會增加六個包含樣式表達式的圖層的開銷。 因此,在某些情況下,使用核心呈現圖層的效率更高。 例如,使用核心圖層在要素上呈現幾個幾何類型和幾個樣式

## <a name="use-a-simple-data-layer"></a>使用簡單的資料層

類`SimpleDataLayer`的使用類似於使用其他呈現圖層。 下面的代碼顯示如何在地圖中使用簡單的資料圖層:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

向數據源添加要素。 然後,簡單的數據層將找出如何最好地呈現要素。 單個要素的樣式可以設置為要素上的屬性。 以下代碼顯示屬性設置為`color``red`的 GeoJSON 點要素。 

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

以下代碼使用簡單的數據層呈現上述點要素。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="使用簡單資料層" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true"> 請參考 Azure<a href='https://codepen.io'>CodePen</a><a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 ()<a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>使用簡單資料圖層</a>。
</iframe>

當出現以下情況時,簡單數據層的真正功能就來了:

- 數據源中有幾種不同類型的功能;或
- 數據集中的要素具有多個樣式屬性,分別設置它們;或
- 您不確定資料集包含的內容。

例如,在分析 XML 數據饋送時,您可能不知道要素的確切樣式和幾何類型。 下面的範例透過可顯示 KML 檔的功能來顯示簡單資料層的強大功能。 它還演示了簡單數據層類提供的各種選項。

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="簡單的資料層選項" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"> 在<a href='https://codepen.io'>CodePen</a>上按 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>() 檢視筆<a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>簡單資料圖層選項</a>。
</iframe>


> [!NOTE]
> 此簡單資料層使用[彈出範本](map-add-popup.md#add-popup-templates-to-the-map)類將 KML 氣球或要素屬性顯示為表。 默認情況下,彈出視窗中呈現的所有內容都將作為安全功能在 iframe 內部裝沙箱。 但是,存在限制:
>
> - 禁用所有腳本、窗體、指標鎖定和頂部導航功能。 按一下時,允許在新選項卡中打開連結。 
> - 不支援 iframe`srcdoc`上 參數的舊瀏覽器將僅限於呈現少量內容。
> 
> 如果您信任載入到彈出視窗中的數據,並且可能希望載入到彈出視窗中的這些文本能夠存取您的應用程式,則可以透過將彈出視窗`sandboxContent`範本 選項設定為 false 來禁用此功能。 

## <a name="default-supported-style-properties"></a>預設支援樣式屬性

如前所述,簡單數據圖層環繞多個核心呈現圖層:氣泡、符號、線、面和拉伸多邊形。 然後,它使用表達式在單個要素上搜索有效的樣式屬性。

Azure 映射和 GitHub 樣式屬性是兩種主要受支援的屬性名稱集。 不同 Azure 地圖圖層選項的大多數屬性名稱都支援為簡單數據圖層中要素的樣式屬性。 運算式已添加到某些圖層選項中,以支援 GitHub 常用的樣式屬性名稱。 這些屬性名稱由[GitHub 的 GeoJSON 地圖支援](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)定義,它們用於設置在平臺中儲存和呈現的 GeoJSON 檔樣式。 除了`marker-symbol`樣式屬性外,簡單數據層中支援 GitHub 的所有樣式屬性。

如果讀取器遇到不太常見的樣式屬性,它將將其轉換為最近的 Azure 地圖樣式屬性。 此外,可以使用`getLayers`簡單數據圖層的功能並更新任何圖層上的選項來重寫預設樣式運算式。

以下各節提供有關簡單數據層支援的預設樣式屬性的詳細資訊。 支援的屬性名稱的順序也是屬性的優先順序。 如果為同一圖層選項定義了兩個樣式屬性,則清單中的第一個樣式屬性具有更高的優先順序。

### <a name="bubble-layer-style-properties"></a>氣泡圖層樣式屬性

如果要素`Point`為`MultiPoint`或 ,並且該要素沒有用作自訂圖示`image`以將 點呈現為符號的屬性,則該要素`BubbleLayer`將使用 呈現 。

| 層次選項 | 支援的屬性名稱 | 預設值 |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup> `marker-size` 1 <sup> </sup> `scale`, 2 , <sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] `size``scale`和 值被視為標量值,它們將乘以`8`

\[2\]如果指定`marker-size`了 GitHub 選項,則半徑將使用以下值。

| 標記大小 | 半徑 |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

群集也使用氣泡圖層呈現。 預設情況下,叢集的半徑設定為`16`。 叢集的顏色因群集中的點數而異,如下所示:

| 積分 | Color    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt;10     | `green`  |

### <a name="symbol-style-properties"></a>符號樣式屬性

如果要素`Point`為`MultiPoint`或 ,並且要素具有用作自訂圖`image`示以 將點呈現為符號的屬性,則該要素`SymbolLayer`將使用 呈現 。

| 層次選項 | 支援的屬性名稱 | 預設值 |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size` <sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\]如果指定`marker-size`了 GitHub 選項,則以下值將用於圖示大小選項。

| 標記大小 | 符號樣式 |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

如果點要素是群集,則`point_count_abbreviated`該屬性將呈現為文本標籤。 不會渲染任何圖像。

### <a name="line-style-properties"></a>線條樣式屬性

如果要素`LineString`為`MultiLineString``Polygon`、、 或`MultiPolygon`,則該要素`LineLayer`將使用 呈現。

| 層次選項 | 支援的屬性名稱 | 預設值 |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>多邊形樣式屬性

`Polygon`如果要素為`MultiPolygon`或 ,並且`height`要素沒有`height`屬性,或者 該屬性為零,則該要素`PolygonLayer`將使用 呈現。

| 層次選項 | 支援的屬性名稱 | 預設值 |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>拉伸多邊形樣式屬性

如果要素`Polygon`為`MultiPolygon`或 ,並且具有值`height`大於 0 的屬性,則該`PolygonExtrusionLayer`要素將使用 呈現 。

| 層次選項 | 支援的屬性名稱 | 預設值 |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [簡單資料層](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [簡單資料清單選項](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [讀取與寫入空間資料](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [新增 OGC 地圖層次](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [連線到 WFS 服務](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [利用核心業務](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支援的資料格式詳細資訊](spatial-io-supported-data-format-details.md)
