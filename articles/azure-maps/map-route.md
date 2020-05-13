---
title: 在地圖上顯示路線指示 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Web SDK，在地圖上顯示兩個位置之間的指示。
author: Philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 1cde1aaa7c9dba3e28407439a46b0e0a3326e4fc
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123967"
---
# <a name="show-directions-from-a-to-b"></a>顯示從甲地到乙地的指示

本文說明如何提出路線要求，並在地圖上顯示路線。

有兩種方式可以這麼做。 第一個方式是透過服務模組查詢 [Azure 地圖服務路線 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。 第二種方式是使用[FETCH api](https://fetch.spec.whatwg.org/)向[AZURE 地圖服務的路由 api](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)提出搜尋要求。 下文將討論這兩種方式。

## <a name="query-the-route-via-service-module"></a>透過服務模組查詢路線

<iframe height='500' scrolling='no' title='在地圖上顯示從甲地到乙地的指示 (服務模組)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/RBZbep/'>在地圖上顯示從甲地到乙地的指示 (服務模組)</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，第一個區塊會建立地圖物件，並將驗證機制設定為使用存取權杖。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會建立 `TokenCredential` ，以使用存取權杖來驗證 Azure 地圖服務的 HTTP 要求。 然後，它會將傳遞 `TokenCredential` 至 `atlas.service.MapsURL.newPipeline()` ，並建立[管線](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)實例。 `routeURL` 代表 Azure 地圖服務[路線規劃](https://docs.microsoft.com/rest/api/maps/route)作業的 URL。

第三個程式碼區塊會建立[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)物件，並將其新增至對應。

第四個程式碼區塊會建立開始和結束[點](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)物件，並將它們加入至 dataSource 物件。

「行」是 LineString 的[功能](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)。 [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 會將 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包裝的線條物件轉譯為地圖上的線條。 第四個程式碼區塊會建立線條圖層，並將其新增至地圖。 請在 [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) 查看線條圖層的屬性。

[符號圖層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示來轉譯資料[源](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)中所包裝的以點為基礎的資料。 文字或圖示會轉譯為地圖上的符號。 第五個程式碼區塊會建立符號圖層，並將其新增至地圖。

第六個程式碼區塊會查詢 Azure 地圖服務路由服務，這是[服務模組](how-to-use-services-module.md)的一部分。 RouteURL 的[calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods)方法是用來取得起點和終點之間的路線。 接著會使用方法來解壓縮回應中的 GeoJSON 功能集合 `geojson.getFeatures()` ，並將它加入至資料來源。 然後，它會將回應呈現為地圖上的路線。 如需有關將線條新增至地圖的詳細資訊，請參閱[在地圖上新增線條](map-add-line-layer.md)。

程式碼的最後一個區塊會使用地圖的[setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)屬性來設定地圖的界限。

路由查詢、資料來源、符號、線條圖層和相機界限會建立在[事件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)接聽程式內。 這個程式碼結構可確保只有在對應完全載入之後，才會顯示結果。

## <a name="query-the-route-via-fetch-api"></a>透過 Fetch API 查詢路由

<iframe height='500' scrolling='no' title='在地圖上顯示從甲地到乙地的指示' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>在地圖上顯示從甲地到乙地的指示</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建立地圖物件，並將驗證機制設定為使用存取權杖。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會建立 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 物件並將其新增至地圖。

第三個程式碼區塊會建立路由的起點和目的地點。 然後，它會將它們加入至資料來源。 如需使用 [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 的指示，您可以查看[在地圖上新增圖釘](map-add-pin.md)。

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 會將 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包裝的線條物件轉譯為地圖上的線條。 第四個程式碼區塊會建立線條圖層，並將其新增至地圖。 請在 [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) 查看線條圖層的屬性。

[符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的點式資料轉譯為地圖上的符號。 第五個程式碼區塊會建立符號圖層，並將其新增至地圖。 請在 [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 上查看符號圖層的屬性。

下一個程式碼區塊會從起點與目的地點建立 `SouthWest` 和 `NorthEast` 點，並使用地圖的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 屬性來設定地圖的範圍。

程式碼的最後一個區塊會使用[FETCH api](https://fetch.spec.whatwg.org/)向[AZURE 地圖服務的路由 api](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)提出搜尋要求。 然後會剖析回應。 如果回應成功，就會使用緯度和經度資訊，藉由連接這些點來建立一條陣列。 然後，將程式程式碼資料新增至資料來源，以轉譯地圖上的路線。 如需相關指示，您可以查看[在地圖上新增線條](map-add-line-layer.md)。

路由查詢、資料來源、符號、線條圖層和相機界限會建立在[事件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)接聽程式內。 同樣地，我們想要確保在地圖完全載入後顯示結果。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用路由服務的最佳做法](how-to-use-best-practices-for-search.md)

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [對應](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [在地圖上顯示路況](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [與地圖互動-滑鼠事件](./map-events.md)
