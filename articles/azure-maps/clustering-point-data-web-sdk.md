---
title: 在地圖上聚類點資料 |微軟 Azure 地圖
description: 在本文中，您將學習如何使用 Microsoft Azure 地圖 Web SDK 對點資料進行群集並在地圖上呈現資料。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: e65681aefc047ba540d4ad0d91ef6e4d2af5f3ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190270"
---
# <a name="clustering-point-data"></a>聚類點資料

在地圖上視覺化許多資料點時，資料點可能會相互重疊。 重疊可能導致地圖變得不可讀且難以使用。 群集位置點資料的程序會結合彼此接近的位置點資料，並在地圖上以單一群集資料點的方式加以表示。 當使用者放大地圖時，群集便會分解成個別的資料點。 處理大量資料點時，請使用群集過程來改善使用者體驗。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>在資料來源上啟用群集

通過將選項設置為 true，`DataSource``cluster`在類中啟用群集。 設置為`ClusterRadius`選擇附近的點並將它們合併到群集中。 的值`ClusterRadius`以圖元為單位。 用於`clusterMaxZoom`指定用於禁用聚類邏輯的縮放級別。 下面是如何在資料來源中啟用群集的示例。

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> 如果兩個資料點在地面上接近，則無論使用者放大有多近，群集都可能永遠不會分崩離析。 為了解決這個問題，`clusterMaxZoom`您可以設置禁用群集邏輯的選項，並僅顯示所有內容。

下面是`DataSource`類為群集提供的其他方法：

| 方法 | 傳回類型 | 描述 |
|--------|-------------|-------------|
| 獲取群集子級（群集 ID：編號） | 承諾&lt;陣列&lt;要素&lt;幾何，任何&gt;\|形狀&gt;&gt; | 擷取給定群集在下一個縮放層級上的子系。 這些子系可以是圖形和子群集的組合。 子群集會是屬性符合 ClusteredProperties 的功能。 |
| 獲取群集擴展（群集 Id：編號） | Promise&lt;number&gt; | 計算群集將開始擴大或分解的臨界縮放層級。 |
| 獲取群集葉（群集 Id：數量、限制：數位、偏移量：數位） | 承諾&lt;陣列&lt;要素&lt;幾何，任何&gt;\|形狀&gt;&gt; | 擷取群集中的所有位置點。 設定 `limit` 可傳回位置點的子集，使用 `offset` 則可逐頁查看位置點。 |

## <a name="display-clusters-using-a-bubble-layer"></a>使用氣泡圖層顯示群集

氣泡圖層是渲染聚類點的好方法。 使用運算式縮放半徑並根據群集中的點數更改顏色。 如果使用氣泡圖層顯示群集，則應使用單獨的圖層來呈現未聚類的資料點。

要在氣泡頂部顯示群集的大小，請使用帶有文本的符號圖層，並且不要使用圖示。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="基本氣泡層聚類" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，請參閱按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 群集的筆<a href='https://codepen.io/azuremaps/pen/qvzRZY/'>基本氣泡圖層</a>。
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>使用符號圖層顯示群集

視覺化資料點時，符號圖層會自動隱藏彼此重疊的符號，以確保使用者介面更簡潔。 如果要在地圖上顯示資料點密度，則此預設行為可能是不可取的。 但是，可以更改這些設置。 要顯示所有符號，將`allowOverlap`"符號"圖層`iconOptions`屬性的選項設置為`true`。 

使用聚類顯示資料點密度，同時保持乾淨的使用者介面。 下面的示例演示如何添加自訂符號，並使用符號圖層表示聚類和單個資料點。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="聚類符號圖層" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure 地圖<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看筆<a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>群集符號圖層</a>。
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>聚類和熱圖圖層

熱圖是顯示地圖上資料密度的好方法。 此視覺化方法可以自行處理大量資料點。 如果資料點是聚類的，並且聚類大小用作熱圖的重量，則熱圖可以處理更多的資料。 要實現此選項，將`weight`熱圖圖層的選項設置為`['get', 'point_count']`。 當聚類半徑較小時，熱圖看起來與使用未聚類資料點的熱圖幾乎相同，但它的性能會好得多。 但是，群集半徑越小，熱圖就越準確，但性能優勢卻越小。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="聚類加權熱圖" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure 地圖<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看筆<a href='https://codepen.io/azuremaps/pen/VRJrgO/'>群集加權熱圖</a>。
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>群集資料點上的滑鼠事件

當滑鼠事件發生在包含群集資料點的圖層上時，群集資料點將作為 GeoJSON 點要素物件返回到事件。 此點功能將具有以下屬性：

| 屬性名稱             | 類型    | 描述   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | 指出功能是否代表群集。 |
| `cluster_id`              | 字串  | 群集的唯一識別碼，可與 DataSource 的 `getClusterExpansionZoom`、`getClusterChildren` 和 `getClusterLeaves` 方法搭配使用。 |
| `point_count`             | number  | 群集包含的位置點數目。  |
| `point_count_abbreviated` | 字串  | 如果值長，則縮寫該`point_count`值的字串。 (例如，4,000 變成 4K)  |

本示例採用一個氣泡圖層，該氣泡圖層呈現聚類點並添加按一下事件。 按一下事件觸發時，代碼將計算地圖並將其縮放到下一個縮放級別，群集將斷開。 此功能使用`getClusterExpansionZoom``DataSource`類的方法和按一下的群集資料點`cluster_id`的屬性實現。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="群集獲取群集擴展縮放" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱在<a href='https://codepen.io'>CodePen</a>上通過 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（）<a href='https://codepen.io/azuremaps/pen/moZWeV/'>獲取群集擴展的筆群集擴展</a>。
</iframe>

## <a name="display-cluster-area"></a>顯示群集區域 

群集表示的點資料分佈在區域上。 在此示例中，當滑鼠懸停在群集上時，會發生兩種主要行為。 首先，群集中包含的單個資料點將用於計算凸包。 然後，凸殼將顯示在地圖上以顯示區域。  凸包是一個多邊形，它包裹一組點，如彈性帶，可以使用 方法`atlas.math.getConvexHull`計算。 可以使用`getClusterLeaves`方法從資料來源檢索群集中包含的所有點。

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="群集區域凸殼" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，請參閱按 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 凸包的筆<a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>群集區域凸包</a>。
</iframe>

## <a name="aggregating-data-in-clusters"></a>聚合群集中的資料

通常，使用具有群集內點數的符號表示群集。 但是，有時最好使用其他指標自訂群集樣式。 使用群集聚合，可以使用[聚合運算式](data-driven-style-expressions-web-sdk.md#aggregate-expression)計算創建和填充自訂屬性。  群集聚合可以在 的選項`clusterProperties``DataSource`中定義。

以下示例使用聚合運算式。 代碼根據群集中每個資料點的實體類型屬性計算計數。 當使用者按一下群集時，將顯示一個快顯視窗，其中包含有關群集的其他資訊。

<iframe height="500" style="width: 100%;" scrolling="no" title="群集聚合" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 查看筆<a href='https://codepen.io/azuremaps/pen/jgYyRL/'>群集聚合</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [資料來源類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [資料來源選項物件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [阿特拉斯.數學命名空間](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

請參閱程式碼範例，將功能新增至您的應用程式：

> [!div class="nextstepaction"]
> [添加氣泡圖層](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [添加符號圖層](map-add-pin.md)

> [!div class="nextstepaction"]
> [新增熱度圖圖層](map-add-heat-map-layer.md)
