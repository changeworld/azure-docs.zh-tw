---
title: 叢集地圖上的點資料 | Microsoft Azure 地圖服務
description: 在本文中，您會了解如何使用 Microsoft Azure 地圖服務 Web SDK 來叢集點資料，並將其呈現在地圖上。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: ce2891201331ee1efd861d2f13cec78c0551b6ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804566"
---
# <a name="clustering-point-data"></a>叢集點資料

當要視覺化地圖上許多的資料點時，資料點可能會彼此重疊。 重疊會導致地圖無法閱讀且難以使用。 群集位置點資料的程序會結合彼此接近的位置點資料，並在地圖上以單一群集資料點的方式加以表示。 當使用者放大地圖時，群集便會分解成個別的資料點。 當處理大量資料點時，請使用叢集處理序來改善使用者體驗。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>對資料來源啟用叢集

將 `cluster` 選項設定為 true，以在 `DataSource` 類別中啟用叢集。 設定 `ClusterRadius` 以選取附近的點，並將這些點結合成叢集。 `ClusterRadius` 的值是以像素為單位。 使用 `clusterMaxZoom` 指定停用叢集邏輯的縮放層級。 以下範例示範如何在資料來源中啟用叢集。

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
> 如果兩個資料點彼此很靠近，不論使用者放大到什麼程度，叢集可能都不會拆分開。 若要解決此問題，您可將 `clusterMaxZoom` 選項設為停用叢集邏輯，只顯示所有項目。

以下是 `DataSource` 類別為叢集提供的其他方法：

| 方法 | 傳回類型 | 描述 |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 擷取給定群集在下一個縮放層級上的子系。 這些子系可以是圖形和子群集的組合。 子群集會是屬性符合 ClusteredProperties 的功能。 |
| getClusterExpansionZoom(clusterId: number) | Promise&lt;number&gt; | 計算群集將開始擴大或分解的臨界縮放層級。 |
| getClusterLeaves(clusterId: number, limit: number, offset: number) | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 擷取群集中的所有位置點。 設定 `limit` 可傳回位置點的子集，使用 `offset` 則可逐頁查看位置點。 |

## <a name="display-clusters-using-a-bubble-layer"></a>使用泡泡圖層顯示叢集

泡泡圖層是呈現叢集資料點的絕佳方式。 使用運算式調整半徑，並根據叢集中的資料點數量變更色彩。 如果使用泡泡圖層顯示叢集，則建議使用其他圖層呈現未叢集化的資料點。

若要在泡泡頂端顯示叢集大小，請使用符號圖層搭配文字，且不要使用圖示。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="基本泡泡圖層叢集" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
查看畫筆的 <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>Basic bubble layer clustering</a> (基本泡泡圖層叢集)，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>使用符號圖層顯示叢集

視覺化資料點時，符號圖層會自動隱藏彼此重疊的符號，以確保使用者介面更整潔。 如果想在地圖上顯示資料點的密度，則可能不需要這種預設行為。 但您可變更這些設定。 若要顯示所有符號，請將符號圖層 `iconOptions` 屬性的 [`allowOverlap`] 選項設為 [`true`]。 

使用叢集顯示資料點密度，同時保持乾淨的使用者介面。 以下範例示範如何使用符號圖層來新增自訂符號，以及表示叢集和個別資料點。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="已叢集化的符號圖層" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
查看畫筆的 <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>Clustered Symbol layer</a> (叢集符號圖層)，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>叢集和熱度圖圖層

熱度圖是在地圖上顯示資料密度的絕佳方式。 這個視覺效果方法可自行處理大量的資料點。 如果資料點已叢集化，且叢集大小作為熱度圖的權數使用，則此熱度圖可處理更多資料。 若要完成此選項，請將熱度圖圖層的 [`weight`] 選項設為 [`['get', 'point_count']`]。 如果叢集半徑很小，則此熱度圖和使用未叢集化資料點的熱度圖看起來幾乎一致，但執行效果更好。 不過，叢集半徑愈小，熱度圖的精確度就愈高，但效能優勢也隨之減少。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="叢集加權熱度圖" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
查看畫筆的 <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>Cluster weighted Heat Map</a> (叢集加權熱度圖)，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>已叢集化資料點的滑鼠事件

當滑鼠事件發生在包含叢集資料點的圖層上時，叢集資料點會以 GeoJSON 點功能物件的形式傳回事件。 此點功能具有下列屬性：

| 屬性名稱             | 類型    | 描述   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | 指出功能是否代表群集。 |
| `cluster_id`              | 字串  | 群集的唯一識別碼，可與 DataSource 的 `getClusterExpansionZoom`、`getClusterChildren` 和 `getClusterLeaves` 方法搭配使用。 |
| `point_count`             | number  | 群集包含的位置點數目。  |
| `point_count_abbreviated` | 字串  | 會縮寫較長 `point_count` 值的字串。 (例如，4,000 變成 4K)  |

此範例使用會呈現叢集點並新增 click 事件的泡池圖圖層。 觸發 click 事件時，程式碼會計算地圖，將其縮放至可拆分叢集的下一個縮放層級。 這項功能是使用 `DataSource` 類別的 `getClusterExpansionZoom` 方法，並按一下叢集資料點的 `cluster_id` 屬性來實作。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="叢集 getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
查看畫筆的 <a href='https://codepen.io/azuremaps/pen/moZWeV/'>Cluster getClusterExpansionZoom</a> (叢集 getClusterExpansionZoom)，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="display-cluster-area"></a>顯示叢集區域 

叢集代表的點資料會散佈在某個區域。 在此範例中，當滑鼠暫留在叢集上時，會發生兩種主要行為。 首先，使用包含在叢集中的個別資料點來計算凸殼。 然後，凸殼會出現在地圖上以顯示某個區域。  凸殼是一個多邊形，其類似橡皮筋一樣包裹住一組可用 `atlas.math.getConvexHull` 方法計算的資料點。 您可使用 `getClusterLeaves` 方法，以從資料來源擷取出所有包含在叢集中的點。

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="叢集區域凸殼" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
查看畫筆的 <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>Cluster area convex hull</a> (叢集區域凸殼)，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="aggregating-data-in-clusters"></a>彙總叢集中的資料

叢集通常會使用一個符號加上叢集中的點數來表示。 但有時候，您會想要使用其他計量來自訂叢集樣式。 使用叢集彙總時，您可使用[彙總運算式](data-driven-style-expressions-web-sdk.md#aggregate-expression)計算來建立與填入自訂的屬性。  叢集彙總可在 `DataSource` 的 `clusterProperties` 選項中定義。

下列範例使用彙總運算式。 程式碼會根據叢集中每個資料點的實體類型屬性來計算資料點計數。 當使用者按一下叢集時，快顯視窗就會顯示與叢集相關的其他資訊。

<iframe height="500" style="width: 100%;" scrolling="no" title="叢集彙總" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
查看畫筆的 <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>Cluster aggregates</a> (叢集彙總)，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [DataSource 類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) (英文)

> [!div class="nextstepaction"]
> [DataSourceOptions 物件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest) (英文)

> [!div class="nextstepaction"]
> [atlas.math 命名空間](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

請參閱程式碼範例，將功能新增至您的應用程式：

> [!div class="nextstepaction"]
> [新增泡泡圖層](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [新增符號圖層](map-add-pin.md)

> [!div class="nextstepaction"]
> [新增熱度圖圖層](map-add-heat-map-layer.md)
