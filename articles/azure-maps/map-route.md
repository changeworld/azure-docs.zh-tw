---
title: 在地圖上顯示路線方向 |微軟 Azure 地圖
description: 在本文中，您將瞭解如何使用 Microsoft Azure 地圖 Web SDK 在地圖上的兩個位置之間顯示方向。
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: dde9264d0cb65726b624b918982cfa01985b63ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371416"
---
# <a name="show-directions-from-a-to-b"></a>顯示從甲地到乙地的指示

本文說明如何提出路線要求，並在地圖上顯示路線。

有兩種方式可以這麼做。 第一個方式是透過服務模組查詢 [Azure 地圖服務路線 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。 第二種方法是使用[Fetch API](https://fetch.spec.whatwg.org/)向[Azure 地圖路由 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)發出搜索請求。 下文將討論這兩種方式。

## <a name="query-the-route-via-service-module"></a>透過服務模組查詢路線

<iframe height='500' scrolling='no' title='在地圖上顯示從甲地到乙地的指示 (服務模組)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/RBZbep/'>在地圖上顯示從甲地到乙地的指示 (服務模組)</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述代碼中，第一個塊構造一個映射物件，並將身份驗證機制設置為使用訪問權杖。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個代碼塊創建一`TokenCredential`個用於使用訪問權杖對 Azure 映射的 HTTP 要求進行身份驗證。 然後，它將`TokenCredential`傳遞給`atlas.service.MapsURL.newPipeline()`並創建[管道](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)實例。 `routeURL` 代表 Azure 地圖服務[路線規劃](https://docs.microsoft.com/rest/api/maps/route)作業的 URL。

第三個代碼塊創建[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)物件並將其添加到地圖中。

代碼的第四個塊創建起始點和結束[點](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)物件，並將它們添加到 DataSource 物件。

線[是線](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)弦的功能。 [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 會將 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包裝的線條物件轉譯為地圖上的線條。 第四個程式碼區塊會建立線條圖層，並將其新增至地圖。 請在 [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) 查看線條圖層的屬性。

[符號圖層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)使用文本或圖示來呈現資料[源](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)中包裝的基於點的資料。 文本或圖示在地圖上呈現為符號。 第五個代碼塊創建符號圖層並將其添加到地圖中。

第六個代碼塊查詢 Azure 映射路由服務，該服務是[服務模組](how-to-use-services-module.md)的一部分。 RouteURL 的計算[路由方向](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods)方法用於獲取起點和終點之間的路由。 然後使用`geojson.getFeatures()`方法提取回應中的 GeoJSON 要素集合，並將其添加到資料來源中。 然後，它會將回應呈現為地圖上的路線。 如需有關將線條新增至地圖的詳細資訊，請參閱[在地圖上新增線條](map-add-line-layer.md)。

最後一個代碼塊使用地圖的[setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)屬性設置地圖的邊界。

路由查詢、資料來源、符號、線圖層和攝像機邊界在[事件攔截器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)中創建。 此代碼結構可確保僅在地圖完全載入後顯示結果。

## <a name="query-the-route-via-fetch-api"></a>通過 Fetch API 查詢路由

<iframe height='500' scrolling='no' title='在地圖上顯示從甲地到乙地的指示' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>在地圖上顯示從甲地到乙地的指示</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上面的代碼中，第一個代碼塊構造一個映射物件，並設置身份驗證機制來使用訪問權杖。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會建立 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 物件並將其新增至地圖。

第三個代碼塊創建路由的開始點和目標點。 然後，它將它們添加到資料來源。 如需使用 [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 的指示，您可以查看[在地圖上新增圖釘](map-add-pin.md)。

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 會將 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包裝的線條物件轉譯為地圖上的線條。 第四個程式碼區塊會建立線條圖層，並將其新增至地圖。 請在 [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) 查看線條圖層的屬性。

[符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的點式資料轉譯為地圖上的符號。 第五個代碼塊創建符號圖層並將其添加到地圖中。 請在 [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 上查看符號圖層的屬性。

下一個程式碼區塊會從起點與目的地點建立 `SouthWest` 和 `NorthEast` 點，並使用地圖的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 屬性來設定地圖的範圍。

最後一個代碼塊使用[Fetch API](https://fetch.spec.whatwg.org/)向[Azure 地圖路由 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)發出搜索請求。 然後分析回應。 如果回應成功，則緯度和經度資訊用於通過連接這些點來創建陣列。 然後，將線資料添加到資料來源以呈現地圖上的路由。 如需相關指示，您可以查看[在地圖上新增線條](map-add-line-layer.md)。

路由查詢、資料來源、符號、線圖層和攝像機邊界在[事件攔截器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)中創建。 同樣，我們希望確保在地圖完全載入後顯示結果。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用路由服務的最佳做法](how-to-use-best-practices-for-search.md)

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [在地圖上顯示路況](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [與地圖交互 - 滑鼠事件](./map-events.md)
