---
title: 對應地圖上的點資料 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Web SDK 來叢集點資料，並將其呈現在地圖上。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: e65681aefc047ba540d4ad0d91ef6e4d2af5f3ca
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190270"
---
# <a name="clustering-point-data"></a>群集點資料

視覺化地圖上的許多資料點時，資料點可能會彼此重迭。 重迭可能會導致對應無法讀取且難以使用。 群集位置點資料的程序會結合彼此接近的位置點資料，並在地圖上以單一群集資料點的方式加以表示。 當使用者放大地圖時，群集便會分解成個別的資料點。 當您處理大量的資料點時，請使用叢集處理常式來改善您的使用者體驗。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>在資料來源上啟用叢集

將 `cluster` 選項設定為 true，以啟用 `DataSource` 類別中的叢集。 設定 `ClusterRadius` 以選取附近的點，並將它們結合到叢集。 `ClusterRadius` 的值是以圖元為單位。 使用 `clusterMaxZoom` 來指定要停用群集邏輯的縮放層級。 以下是如何在資料來源中啟用群集的範例。

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
> 如果兩個資料點彼此接近，不論使用者如何放大，叢集都可能永遠不會中斷。 若要解決此情況，您可以設定 `clusterMaxZoom` 選項來停用叢集邏輯，並只顯示所有專案。

以下是 `DataSource` 類別為群集提供的其他方法：

| 方法 | 傳回類型 | 描述 |
|--------|-------------|-------------|
| getClusterChildren （clusterId： number） | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 擷取給定群集在下一個縮放層級上的子系。 這些子系可以是圖形和子群集的組合。 子群集會是屬性符合 ClusteredProperties 的功能。 |
| getClusterExpansionZoom （clusterId： number） | Promise&lt;number&gt; | 計算群集將開始擴大或分解的臨界縮放層級。 |
| getClusterLeaves （clusterId： number，limit： number，offset： number） | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 擷取群集中的所有位置點。 設定 `limit` 可傳回位置點的子集，使用 `offset` 則可逐頁查看位置點。 |

## <a name="display-clusters-using-a-bubble-layer"></a>使用反升層顯示群集

「反升圖層」是呈現叢集點的絕佳方式。 使用運算式來調整半徑，並根據叢集中的點數來變更色彩。 如果您使用反升圖層來顯示叢集，則應該使用個別的圖層來轉譯叢集資料點。

若要在氣泡頂端顯示叢集的大小，請使用符號圖層搭配文字，而不要使用圖示。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="基本反升層叢集" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的畫筆基本反升<a href='https://codepen.io/azuremaps/pen/qvzRZY/'>層</a>叢集。
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>使用符號圖層顯示群集

視覺化資料點時，符號圖層會自動隱藏彼此重迭的符號，以確保使用者介面更整潔。 如果您想要在地圖上顯示資料點密度，則可能不需要這個預設行為。 不過，您可以變更這些設定。 若要顯示所有符號，請將 [符號圖層] `iconOptions` 屬性的 `allowOverlap` 選項設定為 [`true`]。 

使用群集來顯示資料點密度，同時保持乾淨的使用者介面。 下列範例示範如何使用符號層來新增自訂符號，以及表示叢集和個別資料點。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="叢集符號層" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）畫筆叢集<a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>符號層</a>。
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>群集和熱度圖層

熱度圖是在地圖上顯示資料密度的絕佳方式。 這個視覺效果方法可以自行處理大量的資料點。 如果資料點已叢集化，而叢集大小是用來做為熱度圖的權數，則熱度圖可以處理更多資料。 若要達成此選項，請將熱度圖圖層的 [`weight`] 選項設定為 [`['get', 'point_count']`]。 當叢集半徑較小時，熱度圖會看起來幾乎與使用叢集資料點的熱度圖相同，但它的執行效果會更好。 不過，叢集半徑越小，熱度圖的精確度就愈高，但效能優勢較少。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="叢集加權熱度圖" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的畫筆叢集<a href='https://codepen.io/azuremaps/pen/VRJrgO/'>加權熱度圖</a>。
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>叢集資料點上的滑鼠事件

當滑鼠事件發生在包含叢集資料點的圖層上時，叢集資料點會以 GeoJSON 點功能物件的形式傳回事件。 此點功能將具有下列屬性：

| 屬性名稱             | 類型    | 描述   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | 指出功能是否代表群集。 |
| `cluster_id`              | string  | 群集的唯一識別碼，可與 DataSource 的 `getClusterExpansionZoom`、`getClusterChildren` 和 `getClusterLeaves` 方法搭配使用。 |
| `point_count`             | 數字  | 群集包含的位置點數目。  |
| `point_count_abbreviated` | string  | 縮寫 `point_count` 值的字串（如果長度很長）。 (例如，4,000 變成 4K)  |

這個範例會取得呈現叢集點的反升圖層，並加入 click 事件。 當 click 事件觸發程式時，程式碼會計算地圖，並將其縮放至下一個縮放層級，而叢集會中斷。 這項功能是使用 `DataSource` 類別的 `getClusterExpansionZoom` 方法，以及所按下叢集資料點的 `cluster_id` 屬性來執行。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="叢集 getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的 < 畫筆叢集<a href='https://codepen.io/azuremaps/pen/moZWeV/'>getClusterExpansionZoom</a> 。
</iframe>

## <a name="display-cluster-area"></a>顯示叢集區域 

叢集代表的點資料會散佈在某個區域。 在此範例中，當滑鼠停留在叢集上時，會發生兩個主要行為。 首先，會使用包含在叢集中的個別資料點來計算凸殼。 然後，凸殼會顯示在地圖上以顯示區域。  凸殼是一種多邊形，會包裝一組點，例如彈性的寬線，而且可以使用 `atlas.math.getConvexHull` 方法來計算。 您可以使用 `getClusterLeaves` 方法，從資料來源中取出包含在叢集中的所有點。

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="叢集區域凸殼" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的畫筆叢集<a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>區域凸</a>殼。
</iframe>

## <a name="aggregating-data-in-clusters"></a>匯總群集中的資料

通常會使用符號與叢集中的點數來表示叢集。 但有時候，您可能會想要使用額外的計量來自訂群集的樣式。 使用叢集匯總時，您可以使用[匯總運算式](data-driven-style-expressions-web-sdk.md#aggregate-expression)計算來建立和填入自訂屬性。  叢集匯總可以在 `DataSource`的 `clusterProperties` 選項中定義。

下列範例會使用匯總運算式。 程式碼會根據叢集中每個資料點的實體類型屬性來計算計數。 當使用者按一下叢集時，快顯視窗會顯示與叢集相關的其他資訊。

<iframe height="500" style="width: 100%;" scrolling="no" title="叢集匯總" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的畫筆叢集<a href='https://codepen.io/azuremaps/pen/jgYyRL/'>匯總</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [DataSource 類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions 物件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [阿特拉斯. math 命名空間](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

請參閱程式碼範例，將功能新增至您的應用程式：

> [!div class="nextstepaction"]
> [新增泡泡圖層](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [新增符號圖層](map-add-pin.md)

> [!div class="nextstepaction"]
> [新增熱度圖圖層](map-add-heat-map-layer.md)
