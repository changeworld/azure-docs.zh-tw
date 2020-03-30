---
title: 讀取和寫入空間資料 |微軟 Azure 地圖
description: 瞭解如何使用 Azure 地圖 Web SDK 提供的空間 IO 模組讀取和寫入資料。
author: philmea
ms.author: philmea
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4c47335689401ebce98224992c74c3396821a1dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334162"
---
# <a name="read-and-write-spatial-data"></a>讀取和寫入空間資料

下表列出了空間 IO 模組的讀取和寫入操作所支援的空間檔案格式。

| 資料格式       | 讀取 | 寫入 |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| 地質學            | ✓  |  ✓  |
| Gml               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| Kml               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| 空間 CSV       | ✓  |  ✓  |
| 知名文本   | ✓  |  ✓  |

以下各節概述了使用空間 IO 模組讀取和寫入空間資料的所有不同工具。

## <a name="read-spatial-data"></a>讀取空間資料

該`atlas.io.read`函數是用於讀取常用空間資料格式的主要函數，例如 KML、GPX、GeoRSS、GeoJSON 和包含空間資料的 CSV 檔。 此功能還可以讀取這些格式的壓縮版本，作為 ZIP 檔案或 KMZ 檔。 KMZ 檔案格式是 KML 的壓縮版本，還可以包括圖像等資產。 或者，讀取函數可以採用以任何這些格式指向檔的 URL。 URL 應託管在啟用 CORS 的終結點上，或者應在讀取選項中提供代理服務。 代理服務用於載入未啟用 CORS 的域上的資源。 讀取函數返回將圖像圖示添加到地圖的承諾，並非同步處理資料，以儘量減少對 UI 執行緒的影響。

讀取壓縮檔（作為 zip 或 KMZ）時，將解壓縮並掃描第一個有效檔。 例如，doc.kml 或其他有效副檔名的檔，例如：.kml、.xml、geojson、.json、.csv、.tsv 或 .txt。 然後，預先載入 KML 和 GeoRSS 檔中引用的圖像，以確保它們易於訪問。 無法訪問的圖像資料可能會載入備用回退圖像，或將從樣式中刪除。 從 KMZ 檔中提取的圖像將轉換為數據 URI。

讀取函數的結果是一個`SpatialDataSet`物件。 此物件擴展 GeoJSON 要素集合類。 它可以很容易地傳遞到一個`DataSource`正樣，以呈現其要素在地圖上。 不僅`SpatialDataSet`包含功能資訊，而且還可能包括下表中概述的 KML 地面疊加、處理指標和其他詳細資訊。

| 屬性名稱 | 類型 | 描述 | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | 資料集中所有資料的邊界框。 |
| `features` | `Feature[]` | 資料集中的 GeoJSON 功能。 |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | KML 接地疊加陣列。 |
| `icons` | 記錄&lt;字串，字串&gt; | 一組圖示 URL。 鍵 = 圖示名稱，值 = URL。 |
| properties | 任意 | 在空間資料集的文檔級別提供的屬性資訊。 |
| `stats` | `SpatialDataSetStats` | 有關空間資料集的內容和處理時間的統計資訊。 |
| `type` | `'FeatureCollection'` | 唯讀 GeoJSON 類型值。 |

## <a name="examples-of-reading-spatial-data"></a>讀取空間資料的示例

以下代碼演示如何讀取空間資料集，以及如何使用 類`SimpleDataLayer`在地圖上呈現它。 該代碼使用 URL 指向的 GPX 檔。

<br/>

<iframe height='500' scrolling='no' title='載入空間資料簡單' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上，請參閱按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 顯示筆<a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>載入空間資料簡單</a>。
</iframe>

下一個代碼演示演示如何讀取 KML 或 KMZ 到地圖。 KML 可以包含接地疊加，其形式為`ImageLyaer`或`OgcMapLayer`。 這些疊加必須與要素分開添加到地圖上。 此外，如果資料集具有自訂圖示，則這些圖示需要在載入要素之前載入到地圖資源。

<br/>

<iframe height='500' scrolling='no' title='將 KML 載入到地圖上' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上，請參閱按 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 顯示筆<a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>載入 KML 到地圖</a>。
</iframe>

您可以選擇提供代理服務來訪問可能未啟用 CORS 的跨域資產。 讀取函數將首先嘗試使用 CORS 訪問另一個域上的檔。 首次使用 CORS 無法訪問其他域上的任何資源後，它僅在提供代理服務時才會請求其他檔。 讀取函數將檔 URL 追加到提供的代理 URL 的末尾。 此程式碼片段演示如何將代理服務傳遞到讀取函數：

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

下面的演示演示如何讀取已劃定的檔並在地圖上呈現該檔。 在這種情況下，代碼使用具有空間資料列的 CSV 檔。

<br/>

<iframe height='500' scrolling='no' title='添加已限制的檔' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱在 CodePen 上按 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（）<a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>添加"已限制檔</a>"<a href='https://codepen.io'>的筆</a>。
</iframe>

## <a name="write-spatial-data"></a>寫入空間資料

空間 IO 模組中有兩個主要寫入函數。 函數`atlas.io.write`生成字串，`atlas.io.writeCompressed`而函數生成壓縮 ZIP 檔案。 壓縮的 ZIP 檔案將包含一個基於文本的檔，其中包含空間資料。 這兩個函數都返回將資料添加到檔中的承諾。 並且，它們都可以編寫以下任何資料`SpatialDataSet`：、、、、`DataSource``ImageLayer``OgcMapLayer`特徵、幾何或這些資料類型的任意組合陣列。 使用任一函數寫入時，可以指定想要的檔案格式。 如果未指定檔案格式，則資料將寫入 KML。

下面的工具演示了可用於`atlas.io.write`函數的大多數寫入選項。

<br/>

<iframe height='700' scrolling='no' title='空間資料寫入選項' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上按 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看筆<a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>空間資料寫入選項</a>。
</iframe>

## <a name="example-of-writing-spatial-data"></a>編寫空間資料的示例

以下示例允許您拖放，然後在地圖上載入空間檔。 可以從地圖匯出 GeoJSON 資料，並將其以受支援的空間資料格式之一寫成字串或壓縮檔。

<br/>

<iframe height='700' scrolling='no' title='將空間檔拖放到地圖上' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上，通過 Azure 地圖 （）<a href='https://codepen.io/azuremaps'>@azuremaps</a>查看筆<a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>拖放空間檔到地圖上</a>。
</iframe>

您可以選擇提供代理服務來訪問可能未啟用 CORS 的跨域資產。 此程式碼片段顯示您可以合併代理服務：

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>閱讀和寫入知名文本 （WKT）

[眾所周知的文本](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry)（WKT） 是開放地理空間聯盟 （OGC） 標準，用於將空間幾何表示為文本。 許多地理空間系統都支援 WKT，例如使用 PostGIS 外掛程式的 Azure SQL 和 Azure PostgreSQL。 與大多數 OGC 標準一樣，座標被格式化為"經緯度"，以符合"x y"約定。 例如，經度 -110 和緯度 45 處的點`POINT(-110 45)`可以編寫為使用 WKT 格式。

可以使用 函數讀取`atlas.io.ogc.WKT.read`已知文本，並使用 函數`atlas.io.ogc.WKT.write`編寫。

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>閱讀和編寫知名文本的示例 （WKT）

以下代碼演示如何讀取已知文本字串`POINT(-122.34009 47.60995)`並使用氣泡圖層在地圖上呈現它。

<br/>

<iframe height='500' scrolling='no' title='閱讀已知文本' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在 CodePen 上，請參閱按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （）<a href='https://codepen.io/azuremaps/pen/XWbabLd/'>讀取已知文本</a><a href='https://codepen.io'>的筆</a>。
</iframe>

以下代碼演示了來回閱讀和編寫眾所周知的文本。

<br/>

<iframe height='700' scrolling='no' title='閱讀和寫入知名文本' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在 CodePen 上，請參閱按 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（）<a href='https://codepen.io/azuremaps/pen/JjdyYav/'>讀取和寫入已知文本</a><a href='https://codepen.io'>的筆</a>。
</iframe>

## <a name="read-and-write-gml"></a>讀取和寫入 GML

GML 是一種空間 XML 檔規範，通常用作其他 XML 規範的副檔名。 使用 函數可以使用 GML 標記`atlas.io.core.GmlWriter.write`將 GeoJSON 資料編寫為 XML。 可以使用 函數讀取`atlas.io.core.GmlReader.read`包含 GML 的 XML。 讀取函數有兩個選項：

- 選項`isAxisOrderLonLat`- 座標"緯度、經度"或"經度、緯度"的軸順序可能因資料集而異，並且並不總是定義良好。 預設情況下，GML 讀取器將座標資料讀取為"緯度、經度"，但將此選項設置為 true 會將其讀取為"經度、緯度"。
- 選項`propertyTypes`- 此選項是鍵值查閱資料表，其中鍵是資料集中屬性的名稱。 該值是分析時要將值轉換為的物件類型。 支援的類型值是： `string`、、`boolean``date``number`和 。 如果屬性不在查閱資料表中或未定義類型，則該屬性將作為字串進行解析。

當`atlas.io.read``atlas.io.core.GmlReader.read`函數檢測到輸入資料是 XML 時，它將預設為函數，但資料不是其他支援空間 XML 格式之一。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [atlas.io靜態函數](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [空間資料集](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [空間資料集統計](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [格姆閱讀器](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [格姆作家](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [阿特拉斯.io.ogc.WKT 功能](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [添加 OGC 地圖圖層](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [連接到 WFS 服務](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [利用核心業務](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支援的資料格式詳細資訊](spatial-io-supported-data-format-details.md)
