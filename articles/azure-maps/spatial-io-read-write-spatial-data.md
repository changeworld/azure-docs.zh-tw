---
title: 讀取和寫入空間資料 |Microsoft Azure 對應
description: 瞭解如何使用 Azure 地圖服務 Web SDK 所提供的空間 IO 模組來讀取及寫入資料。
author: philmea
ms.author: philmea
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4c47335689401ebce98224992c74c3396821a1dd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334162"
---
# <a name="read-and-write-spatial-data"></a>讀取和寫入空間資料

下表列出使用空間 IO 模組進行讀取和寫入作業時支援的空間檔案格式。

| 資料格式       | 讀取 | 寫入 |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| .GPX               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| 空間 CSV       | ✓  |  ✓  |
| 知名文字   | ✓  |  ✓  |

以下幾節將概述使用空間 IO 模組來讀取和寫入空間資料的所有不同工具。

## <a name="read-spatial-data"></a>讀取空間資料

`atlas.io.read`函式是用來讀取一般空間資料格式的主要函式，例如 KML、.Gpx、GeoRSS、GEOJSON 和 CSV 檔案（具有空間資料）。 此函式也可以讀取這些格式的壓縮版本，例如 zip 檔案或 KMZ 檔。 KMZ 檔案格式是 KML 的壓縮版本，也可以包含像是影像的資產。 或者，read 函式可以採用指向其中任何一種格式之檔案的 URL。 Url 應裝載在啟用 CORS 的端點上，或應在讀取選項中提供 proxy 服務。 Proxy 服務用來在未啟用 CORS 的網域上載入資源。 Read 函式會傳回承諾，將影像圖示新增至地圖，並以非同步方式處理資料，以將對 UI 執行緒的影響降至最低。

以 zip 或 KMZ 的形式讀取壓縮檔案時，會將檔案解壓縮並掃描為第一個有效的檔案。 例如，kml 或具有其他有效副檔名的檔案，例如：. kml、.xml、geojson、json、.csv、tsv 或 .txt。 然後，會預先載入 KML 和 GeoRSS 檔案中所參考的影像，以確保它們可以存取。 無法存取的影像資料可能會載入替代的回溯影像，或將從樣式中移除。 從 KMZ 檔案解壓縮的影像將會轉換成資料 Uri。

Read 函式的結果是`SpatialDataSet`物件。 此物件會擴充 GeoJSON FeatureCollection 類別。 您可以輕鬆地依`DataSource`原樣傳遞至，以在地圖上轉譯其功能。 `SpatialDataSet`不僅包含功能資訊，也可能包含 KML 的基礎重迭、處理計量，以及下表中所述的其他詳細資料。

| 屬性名稱 | 類型 | 描述 | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | 資料集中所有資料的周框方塊。 |
| `features` | `Feature[]` | GeoJSON 資料集內的功能。 |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | KML GroundOverlays 的陣列。 |
| `icons` | 記錄&lt;字串，字串&gt; | 一組圖示 Url。 索引鍵 = 圖示名稱，值 = URL。 |
| properties | 任意 | 在空間資料集的檔層級提供的屬性資訊。 |
| `stats` | `SpatialDataSetStats` | 空間資料集之內容和處理時間的相關統計資料。 |
| `type` | `'FeatureCollection'` | 唯讀 GeoJSON 類型值。 |

## <a name="examples-of-reading-spatial-data"></a>讀取空間資料的範例

下列程式碼會示範如何讀取空間資料集，並使用`SimpleDataLayer`類別在地圖上呈現它。 程式碼會使用 URL 所指向的 .GPX 檔案。

<br/>

<iframe height='500' scrolling='no' title='簡單載入空間資料' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱<a href='https://codepen.io'>CodePen</a>上的畫筆透過 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）來<a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>載入空間資料 Simple</a> 。
</iframe>

下一個程式碼示範如何將 KML （或 KMZ）讀取和載入至對應。 KML 可以包含地面重迭，其格式為`ImageLyaer`或。 `OgcMapLayer` 這些重迭必須在對應上與功能分開新增。 此外，如果資料集具有自訂圖示，則在載入功能之前，必須先將這些圖示載入至對應資源。

<br/>

<iframe height='500' scrolling='no' title='將 KML 載入至對應' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上，以 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的方式，在地圖上查看 [畫筆<a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>載入 KML</a> ]。
</iframe>

您可以選擇性地提供 proxy 服務，以存取可能未啟用 CORS 的跨網域資產。 Read 函式會嘗試先使用 CORS 來存取另一個網域上的檔案。 第一次無法使用 CORS 存取另一個網域上的任何資源時，只有在提供 proxy 服務時，才會要求額外的檔案。 Read 函式會將檔案 URL 附加至所提供 proxy URL 的結尾。 這段程式碼會示範如何將 proxy 服務傳遞至 read 函式：

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

下列示範顯示如何讀取分隔的檔案，並將其轉譯在地圖上。 在此情況下，程式碼會使用具有空間資料行的 CSV 檔案。

<br/>

<iframe height='500' scrolling='no' title='新增分隔的檔案' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱<a href='https://codepen.io'>CodePen</a>上的透過 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的畫筆<a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>新增分隔的</a>檔案。
</iframe>

## <a name="write-spatial-data"></a>寫入空間資料

空間 IO 模組中有兩個主要寫入功能。 `atlas.io.write`函數會產生字串，而`atlas.io.writeCompressed`函式會產生壓縮的 zip 檔案。 壓縮的 zip 檔案會包含具有空間資料的文字型檔案。 這兩個函式都會傳回將資料新增至檔案的承諾。 而且，它們都可以寫入下列任何資料`SpatialDataSet`：、 `DataSource`、 `ImageLayer`、 `OgcMapLayer`、功能集合、功能、幾何，或這些資料類型的任何組合陣列。 使用其中一個函式撰寫時，您可以指定想要的檔案格式。 如果未指定檔案格式，則會將資料寫入為 KML。

下列工具示範可搭配函式使用`atlas.io.write`的大部分寫入選項。

<br/>

<iframe height='700' scrolling='no' title='空間資料寫入選項' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱<a href='https://codepen.io'>CodePen</a>上 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的畫筆<a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>空間資料寫入選項</a>。
</iframe>

## <a name="example-of-writing-spatial-data"></a>寫入空間資料的範例

下列範例可讓您拖放，然後在地圖上載入空間檔案。 您可以從對應匯出 GeoJSON 資料，並以字串或壓縮檔案的其中一個支援的空間資料格式加以寫入。

<br/>

<iframe height='700' scrolling='no' title='將空間檔案拖放到對應上' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>），請參閱畫筆<a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>將空間檔案拖放到地圖</a>上。
</iframe>

您可以選擇性地提供 proxy 服務，以存取可能未啟用 CORS 的跨網域資產。 這段程式碼顯示您可以合併 proxy 服務：

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

## <a name="read-and-write-well-known-text-wkt"></a>讀取和寫入已知的文字（WKT）

[已知的文字](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry)（WKT）是將空間幾何表示為文字的開放地理空間協會（OGC）標準。 許多地理空間系統都支援 WKT，例如使用 PostGIS 外掛程式的 Azure SQL 和 Azure 于 postgresql。 如同大部分的 OGC 標準，座標會格式化為「經度緯度」，以配合「x y」慣例。 例如，您可以`POINT(-110 45)`使用 WKT 格式，將經度-110 和緯度45的點寫入。

已知的文字可以使用`atlas.io.ogc.WKT.read`函式來讀取，並使用`atlas.io.ogc.WKT.write`函數撰寫。

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>讀取和寫入已知文字的範例（WKT）

下列程式碼示範如何讀取已知的文字字串`POINT(-122.34009 47.60995)` ，並使用反升圖層將其轉譯在地圖上。

<br/>

<iframe height='500' scrolling='no' title='讀取知名文字' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>），手寫筆<a href='https://codepen.io/azuremaps/pen/XWbabLd/'>讀取已知的文字</a>。
</iframe>

下列程式碼示範如何在前後讀取和寫入已知的文字。

<br/>

<iframe height='700' scrolling='no' title='讀取和寫入知名文字' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱在<a href='https://codepen.io'>CodePen</a>上 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的畫筆<a href='https://codepen.io/azuremaps/pen/JjdyYav/'>讀取和寫入已知的文字</a>。
</iframe>

## <a name="read-and-write-gml"></a>讀取和寫入 GML

GML 是空間 XML 檔案規格，通常用來做為其他 XML 規格的延伸。 您可以使用`atlas.io.core.GmlWriter.write`函式，將 GeoJSON 資料寫入為具有 GML 標記的 XML。 包含 GML 的 XML 可以使用`atlas.io.core.GmlReader.read`函數來讀取。 Read 函式有兩個選項：

- `isAxisOrderLonLat`選項-[緯度、經度] 或 [經度，緯度] 座標的軸順序可能會因資料集而異，而且不一定會妥善定義。 根據預設，GML 讀取器會將座標資料讀取為「緯度，經度」，但將此選項設定為 true 會將其讀取為「經度，緯度」。
- `propertyTypes`選項-此選項是索引鍵值查閱資料表，其中索引鍵是資料集中的屬性名稱。 值是剖析時要將值轉換成的物件類型。 支援的類型值為： `string`、 `number`、 `boolean`和`date`。 如果屬性不在查閱資料表中，或未定義類型，則會將屬性剖析為字串。

`atlas.io.read`函式會在偵測到`atlas.io.core.GmlReader.read`輸入資料為 XML 時，預設為函式，但資料不是其他支援空間 XML 格式的其中一個。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [atlas.io 靜態函式](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [SpatialDataSet](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [SpatialDataSetStats](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [GmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [GmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [ogc. WKT 函式](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [新增 OGC 地圖圖層](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [連接到工作流程服務](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [利用核心作業](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支援的資料格式詳細資料](spatial-io-supported-data-format-details.md)
