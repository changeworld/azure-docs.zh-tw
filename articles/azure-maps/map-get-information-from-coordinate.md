---
title: 在地圖上顯示有關座標的資訊 |微軟 Azure 地圖
description: 瞭解如何在使用者選擇座標時在地圖上顯示有關位址的資訊。
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 174bdc496e52a6ac8f2a2d631db92e0f21a819be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371427"
---
# <a name="get-information-from-a-coordinate"></a>從座標取得資訊

本文會示範如何進行反向位址搜尋，顯示已按下的快顯位置本身的位址。

有兩種方式可以進行反向位址搜尋。 第一個方式是透過服務模組查詢 [Azure 地圖反向地址搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)。 另一種方法是使用[Fetch API](https://fetch.spec.whatwg.org/)向[Azure 映射反向位址搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)發出請求以查找位址。 以下簡要說明這兩種方式。

## <a name="make-a-reverse-search-request-via-service-module"></a>透過服務模組提出反向搜尋要求

<iframe height='500' scrolling='no' title='從座標取得資訊 (服務模組)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>從座標取得資訊 (服務模組)</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上面的代碼中，第一個塊構造一個映射物件，並設置身份驗證機制來使用訪問權杖。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個代碼塊創建`TokenCredential`一個用於使用訪問權杖對 Azure 映射的 HTTP 要求進行身份驗證。 然後，它將`TokenCredential`傳遞給`atlas.service.MapsURL.newPipeline()`並創建[管道](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)實例。 `searchURL` 代表 Azure 地圖服務[搜尋](https://docs.microsoft.com/rest/api/maps/search)作業的 URL。

第三個代碼塊將滑鼠游標的樣式更新為指標並創建[一個彈出](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)物件。 如需相關指示，您可以查看[在地圖上新增快顯](./map-add-popup.md)。

第四個代碼塊添加了滑鼠按一下[事件攔截器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)。 觸發時，它會創建具有按一下點的座標的搜索查詢。 然後，它使用[getSearchAddress 反向](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)方法查詢[座標位址的獲取搜索位址反向 API。](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 然後使用回應中`geojson.getFeatures()`的方法提取 GeoJSON 要素集合。

第五個代碼塊設置 HTML 彈出內容以顯示按一下座標位置的回應位址。

游標、彈出物件和按一下事件的更改都在地圖的[載入事件攔截器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)中創建。 此代碼結構可確保在檢索座標資訊之前完全載入地圖。

## <a name="make-a-reverse-search-request-via-fetch-api"></a>通過 Fetch API 發出反向搜索請求

按一下地圖，使用取件對該位置發出反向地理編碼請求。

<iframe height='500' scrolling='no' title='從座標取得資訊' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>從座標取得資訊</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上面的代碼中，第一個代碼塊構造一個映射物件，並設置身份驗證機制來使用訪問權杖。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個代碼塊將滑鼠游標的樣式更新為指標。 它實例[化彈出物件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)。 如需相關指示，您可以查看[在地圖上新增快顯](./map-add-popup.md)。

第三個程式碼區塊會新增滑鼠點選的事件接聽程式。 按一下滑鼠後，它使用[Fetch API](https://fetch.spec.whatwg.org/)查詢[Azure 地圖反向位址搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)以查找按一下的座標位址。 對於成功的回應，它會收集按一下位置的位址。 它使用彈出類的[setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-)函式定義彈出內容和位置。

游標、彈出物件和按一下事件的更改都在地圖的[載入事件攔截器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)中創建。 此代碼結構可確保地圖在檢索座標資訊之前完全載入。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用搜尋服務的最佳做法](how-to-use-best-practices-for-search.md)

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [顯示從甲地到乙地的指示](./map-route.md)

> [!div class="nextstepaction"]
> [顯示流量](./map-show-traffic.md)
