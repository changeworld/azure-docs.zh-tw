---
title: 顯示地圖上的路線指示 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Web SDK，在地圖上顯示兩個位置之間的指示。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 9d0197a16c8074ce961c2b403724149929f566f7
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890712"
---
# <a name="show-directions-from-a-to-b"></a>顯示從甲地到乙地的指示

本文說明如何提出路線要求，並在地圖上顯示路線。

有兩種方式可以這麼做。 第一個方式是透過服務模組查詢 [Azure 地圖服務路線 API](/rest/api/maps/route/getroutedirections)。 第二種方式是使用 [FETCH api](https://fetch.spec.whatwg.org/) 向 [Azure 地圖服務 Route api](/rest/api/maps/route/getroutedirections)提出搜尋要求。 下文將討論這兩種方式。

## <a name="query-the-route-via-service-module"></a>透過服務模組查詢路線

<iframe height='500' scrolling='no' title='在地圖上顯示從甲地到乙地的指示 (服務模組)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/RBZbep/'>在地圖上顯示從甲地到乙地的指示 (服務模組)</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，第一個區塊會建立地圖物件，並將驗證機制設定為使用存取權杖。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會建立 `TokenCredential`，以使用存取權杖來驗證對 Azure 地圖服務的 HTTP 要求。 然後會將 `TokenCredential` 傳遞至 `atlas.service.MapsURL.newPipeline()`，並建立[管線](/javascript/api/azure-maps-rest/atlas.service.pipeline)執行個體。 `routeURL` 代表 Azure 地圖服務[路線規劃](/rest/api/maps/route)作業的 URL。

程式碼的第三個區塊會建立 [資料來源](/javascript/api/azure-maps-control/atlas.source.datasource) 物件，並將它加入至對應。

程式碼的第四個區塊會建立開始和結束 [點](/javascript/api/azure-maps-control/atlas.data.point) 物件，並將它們加入至 dataSource 物件。

線條是 LineString 的 [功能](/javascript/api/azure-maps-control/atlas.data.feature) 。 [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) 會將 [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) 中包裝的線條物件轉譯為地圖上的線條。 第四個程式碼區塊會建立線條圖層，並將其新增至地圖。 請在 [LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) 查看線條圖層的屬性。

[符號圖層](/javascript/api/azure-maps-control/atlas.layer.symbollayer)會使用文字或圖示來轉譯資料[源](/javascript/api/azure-maps-control/atlas.source.datasource)中包裝的以點為基礎的資料。 文字或圖示會轉譯為地圖上的符號。 第五個程式碼區塊會建立符號圖層，並將其新增至地圖。

第六個程式碼區塊會查詢 Azure 地圖服務路由服務，這是 [服務模組](how-to-use-services-module.md)的一部分。 RouteURL 的 [calculateRouteDirections](/javascript/api/azure-maps-rest/atlas.service.routeurl#methods) 方法是用來取得起點和終點之間的路線。 然後會使用方法來解壓縮回應中的 GeoJSON 功能集合 `geojson.getFeatures()` ，並將它加入至 datasource。 然後，它會將回應呈現為地圖上的路線。 如需有關將線條新增至地圖的詳細資訊，請參閱[在地圖上新增線條](map-add-line-layer.md)。

最後一個程式碼區塊會使用地圖的 [>setcamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 屬性來設定地圖的界限。

路線查詢、資料來源、符號、線條圖層和相機界限會建立在 [事件](/javascript/api/azure-maps-control/atlas.map#events)接聽程式內。 此程式碼結構可確保只有在地圖完全載入後才會顯示結果。

## <a name="query-the-route-via-fetch-api"></a>透過 Fetch API 查詢路由

<iframe height='500' scrolling='no' title='在地圖上顯示從甲地到乙地的指示' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>在地圖上顯示從甲地到乙地的指示</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，第一個程式碼區塊會建構地圖物件，並將驗證機制設為使用存取權杖。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會建立 [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) 物件並將其新增至地圖。

第三個程式碼區塊會建立路由的起點和終點。 然後，它會將其新增至資料來源。 如需使用 [addPins](/javascript/api/azure-maps-control/atlas.map) 的指示，您可以查看[在地圖上新增圖釘](map-add-pin.md)。

[LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) 會將 [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) 中包裝的線條物件轉譯為地圖上的線條。 第四個程式碼區塊會建立線條圖層，並將其新增至地圖。 請在 [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) 查看線條圖層的屬性。

[符號層](/javascript/api/azure-maps-control/atlas.layer.symbollayer)會使用文字或圖示，將包裝在 [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) 中的點式資料轉譯為地圖上的符號。 第五個程式碼區塊會建立符號圖層，並將其新增至地圖。 請在 [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions) 上查看符號圖層的屬性。

下一個程式碼區塊會從起點與目的地點建立 `SouthWest` 和 `NorthEast` 點，並使用地圖的 [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 屬性來設定地圖的範圍。

最後一個程式碼區塊會使用 [FETCH api](https://fetch.spec.whatwg.org/) 向 [Azure 地圖服務 Route api](/rest/api/maps/route/getroutedirections)提出搜尋要求。 然後會剖析回應。 如果回應成功，則會使用緯度和經度資訊，藉由連接這些點來建立線條陣列。 然後，會將資料行資料新增至資料來源，以轉譯地圖上的路線。 如需相關指示，您可以查看[在地圖上新增線條](map-add-line-layer.md)。

路線查詢、資料來源、符號、線條圖層和相機界限會建立在 [事件](/javascript/api/azure-maps-control/atlas.map#events)接聽程式內。 同樣地，我們想要確保在地圖完全載入後顯示結果。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用路由服務的最佳做法](how-to-use-best-practices-for-search.md)

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](/javascript/api/azure-maps-control/atlas.map)

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [在地圖上顯示路況](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [與地圖互動-滑鼠事件](./map-events.md)