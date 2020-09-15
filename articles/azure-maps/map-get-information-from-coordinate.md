---
title: 在地圖上顯示座標資訊 | Microsoft Azure 地圖服務
description: 了解如何在使用者選取座標時於地圖上顯示地址的資訊。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: 0026b3714be4cf0a59d348f6708a3840aaddf11b
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90085326"
---
# <a name="get-information-from-a-coordinate"></a>從座標取得資訊

本文會示範如何進行反向位址搜尋，顯示已按下的快顯位置本身的位址。

有兩種方式可以進行反向位址搜尋。 第一個方式是透過服務模組查詢 [Azure 地圖反向地址搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)。 另一種方式，則是使用[擷取 API](https://fetch.spec.whatwg.org/) 向 [Azure 地圖服務反向位址搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 提出要求以尋找位址。 以下簡要說明這兩種方式。

## <a name="make-a-reverse-search-request-via-service-module"></a>透過服務模組提出反向搜尋要求

<iframe height='500' scrolling='no' title='從座標取得資訊 (服務模組)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>從座標取得資訊 (服務模組)</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，第一個區塊會建構地圖物件，並將驗證機制設為使用存取權杖。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會建立 `TokenCredential`，以使用存取權杖來驗證對 Azure 地圖服務的 HTTP 要求。 然後會將 `TokenCredential` 傳遞至 `atlas.service.MapsURL.newPipeline()`，並建立[管線](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline)執行個體。 `searchURL` 代表 Azure 地圖服務[搜尋](https://docs.microsoft.com/rest/api/maps/search)作業的 URL。

第三個程式碼區塊會將滑鼠游標的樣式更新為指標，並建立[快顯](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup#open)物件。 如需相關指示，您可以查看[在地圖上新增快顯](./map-add-popup.md)。

第四個程式碼區塊會新增滑鼠點擊的[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#events)。 觸發時，會使用點擊處的座標來建立搜尋查詢。 然後，會使用 [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) 方法來查詢[取得搜尋位址反向 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 以取得座標位址。 接著，會從回應中使用 `geojson.getFeatures()` 方法來擷取 GeoJSON 功能集合。

第五個程式碼區塊會設定 HTML 快顯視窗內容，以顯示點擊座標位置的回應位址。

指標、快顯物件和點擊事件其變更都會在地圖的[載入事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#events)中建立。 此程式碼結構可確保在擷取座標資訊之前完全載入地圖。

## <a name="make-a-reverse-search-request-via-fetch-api"></a>透過擷取 API 提出反向搜尋要求

按一下地圖以使用擷取功能對該位置提出反向地理編碼要求。

<iframe height='500' scrolling='no' title='從座標取得資訊' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>從座標取得資訊</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，第一個程式碼區塊會建構地圖物件，並將驗證機制設為使用存取權杖。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會將滑鼠游標的樣式更新為指標。 其會將[快顯視窗](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup#open)物件具現化。 如需相關指示，您可以查看[在地圖上新增快顯](./map-add-popup.md)。

第三個程式碼區塊會新增滑鼠點選的事件接聽程式。 按一下滑鼠後，會將[擷取 API](https://fetch.spec.whatwg.org/) 傳送至 [Azure 地圖服務反向地址搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 以取得點擊的座標位址。 若為成功的回應，則其即會收集點擊位置的位址。 其會使用快顯類別的 [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup#setoptions-popupoptions-) 函式來定義快顯內容和位置。

指標、快顯物件和點擊事件其變更都會在地圖的[載入事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#events)中建立。 此程式碼結構可確保在擷取座標資訊之前完全載入地圖。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用搜尋服務的最佳做法](how-to-use-best-practices-for-search.md)

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [顯示從甲地到乙地的指示](./map-route.md)

> [!div class="nextstepaction"]
> [顯示流量](./map-show-traffic.md)
