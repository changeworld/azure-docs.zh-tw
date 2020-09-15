---
title: 在地圖上顯示搜尋結果 | Azure 地圖服務
description: 在本文中，您將了解如何使用 Microsoft Azure 地圖服務 Web SDK 來執行搜尋要求，並在地圖上顯示結果。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: be6d508da15b7c403259bd66c86c3b3e72ff2f12
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90089268"
---
# <a name="show-search-results-on-the-map"></a>在地圖上顯示搜尋結果

本文說明如何搜尋景點，並在地圖上顯示搜尋結果。

有兩種方式可搜尋景點。 其中一個方式是使用服務模組發出搜尋要求。 第二個方式是透過[擷取 API](https://fetch.spec.whatwg.org/) 向 [Azure 地圖服務模糊搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 提出搜尋要求。 下文將討論這兩種方式。

## <a name="make-a-search-request-via-service-module"></a>透過服務模組提出搜尋要求

<iframe height='500' scrolling='no' title='在地圖上顯示搜尋結果 (服務模組)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/zLdYEB/'> (英文) 在地圖上顯示搜尋結果 (服務模組)</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a> (英文)。
</iframe>

在上述程式碼中，第一個區塊會建構地圖物件，並將驗證機制設為使用存取權杖。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會建立 `TokenCredential`，以使用存取權杖來驗證對 Azure 地圖服務的 HTTP 要求。 然後會將 `TokenCredential` 傳遞至 `atlas.service.MapsURL.newPipeline()`，並建立[管線](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline)執行個體。 `searchURL` 代表 Azure 地圖服務[搜尋](https://docs.microsoft.com/rest/api/maps/search)作業的 URL。

第三個程式碼區塊會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource) 類別來建立資料來源物件，以及搜尋其結果。 [符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource) 中的點式資料轉譯為地圖上的符號。  然後就會建立符號圖層。 資料來源會新增至符號層，然後新增至地圖。

第四個程式碼區塊會使用[服務模組](how-to-use-services-module.md)中的 [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) 方法。 此方法可供透過[取得搜尋模糊 Rest API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 來執行自由格式文字搜尋，以搜尋景點。 搜尋模糊 API 的取得要求可處理任何模糊輸入組合。 接著會使用 `geojson.getFeatures()` 方法，從回應中擷取 GeoJSON 功能集合並新增至資料來源，而自動使資料透過符號圖層呈現在地圖上。

程式碼最後一個區塊會使用地圖的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 屬性來為地圖調整觀景窗界限。

搜尋要求、資料來源、符號圖層和觀景窗界限位於地圖的[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#events)內。 我們要確保在地圖完全載入後顯示結果。


## <a name="make-a-search-request-via-fetch-api"></a>透過擷取 API 提出搜尋要求

<iframe height='500' scrolling='no' title='在地圖上顯示搜尋結果' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>在地圖上顯示搜尋結果</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構對應物件。 其會將驗證機制設為使用存取權杖。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會建立可向其提出搜尋要求的 URL。 也會建立兩個陣列，以儲存搜尋結果的界限和釘選。

第三個程式碼區塊會使用[擷取 API](https://fetch.spec.whatwg.org/)。 [擷取 API](https://fetch.spec.whatwg.org/)用於向 [Azure 地圖服務模糊搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 提出要求，以搜尋景點。 模糊搜尋 API 可以處理任何模糊輸入組合。 然後，其會處理搜尋回應並加以剖析，並將結果釘選至 searchPins 陣列。

第四個程式碼區塊會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource) 類別來建立資料來源物件。 我們會在程式碼中將搜尋結果新增至來源物件。 [符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource) 中的點式資料轉譯為地圖上的符號。 然後即會建立符號圖層。 資料來源會新增至符號層，然後新增至地圖。

程式碼的最後一個區塊會建立 [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox) 物件。 其會使用結果的陣列，然後使用地圖其 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 來調整地圖的觀景窗界限。 然後會轉譯結果圖釘。

路線查詢、資料來源、符號圖層及觀景窗界限會設定於地圖的[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#events)內，以確保在地圖完全載入後顯示結果。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用搜尋服務的最佳做法](how-to-use-best-practices-for-search.md)

深入了解**模糊搜尋**：

> [!div class="nextstepaction"]
> [Azure 地圖服務模糊搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [從座標取得資訊](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [顯示從甲地到乙地的指示](map-route.md)
