---
title: 在地圖上顯示搜尋結果 |微軟 Azure 地圖
description: 在本文中，您將瞭解如何使用 Microsoft Azure 地圖 Web SDK 執行搜索請求，並在地圖上顯示結果。
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e82a1daee381c7bad19c83fa735d0028bef2010e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371387"
---
# <a name="show-search-results-on-the-map"></a>在地圖上顯示搜尋結果

本文說明如何搜尋景點，並在地圖上顯示搜尋結果。

有兩種方式可搜尋景點。 其中一個方式是使用服務模組發出搜尋要求。 另一種方法是通過 Fetch API 向[Azure 映射模糊搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) [API](https://fetch.spec.whatwg.org/)發出搜索請求。 下文將討論這兩種方式。

## <a name="make-a-search-request-via-service-module"></a>透過服務模組提出搜尋要求

<iframe height='500' scrolling='no' title='在地圖上顯示搜尋結果 (服務模組)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/zLdYEB/'> (英文) 在地圖上顯示搜尋結果 (服務模組)</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a> (英文)。
</iframe>

在上面的代碼中，第一個塊構造一個映射物件，並設置身份驗證機制來使用訪問權杖。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個代碼塊創建一`TokenCredential`個用於使用訪問權杖對 Azure 映射的 HTTP 要求進行身份驗證。 然後，它將`TokenCredential`傳遞給`atlas.service.MapsURL.newPipeline()`並創建[管道](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)實例。 `searchURL` 代表 Azure 地圖服務[搜尋](https://docs.microsoft.com/rest/api/maps/search)作業的 URL。

第三個程式碼區塊會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件，以及搜尋其結果。 [符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的點式資料轉譯為地圖上的符號。  然後創建符號圖層。 資料來源會新增至符號層，然後新增至地圖。

第四個代碼塊在[服務模組](how-to-use-services-module.md)中使用[SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams)方法。 它允許您通過[獲取搜索模糊休息 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)執行自由形式文本搜索，以搜索興趣點。 獲取對搜索模糊 API 的請求可以處理模糊輸入的任意組合。 接著會使用 `geojson.getFeatures()` 方法，從回應中擷取 GeoJSON 功能集合並新增至資料來源，而自動使資料透過符號圖層呈現在地圖上。

最後一個代碼塊使用地圖的[SetCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)屬性調整地圖的攝像機邊界。

搜索請求、資料來源、符號圖層和攝像機邊界位於地圖[的事件攔截器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)內。 我們希望確保在地圖完全載入後顯示結果。


## <a name="make-a-search-request-via-fetch-api"></a>通過 Fetch API 發出搜索請求

<iframe height='500' scrolling='no' title='在地圖上顯示搜尋結果' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>在地圖上顯示搜尋結果</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構對應物件。 它將身份驗證機制設置以使用訪問權杖。 如需相關指示，您可以查看[建立對應](./map-create.md)。

代碼的第二個塊創建一個 URL 以發出搜索請求。 它還創建兩個數組來存儲搜尋結果的邊界和引腳。

第三個代碼塊使用[Fetch API](https://fetch.spec.whatwg.org/)。 [Fetch API](https://fetch.spec.whatwg.org/)用於向[Azure 映射模糊搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)發出請求以搜索感興趣的點。 模糊搜尋 API 可以處理任何模糊輸入組合。 然後，它處理並分析搜索回應，並將結果引腳添加到搜索引腳陣列中。

第四個代碼塊使用[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)類創建資料來源物件。 在代碼中，我們將搜尋結果添加到源物件。 [符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的點式資料轉譯為地圖上的符號。 然後創建符號圖層。 資料來源會新增至符號層，然後新增至地圖。

最後一個代碼塊創建[邊界框](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest)物件。 它使用結果陣列，然後使用地圖的[SetCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)調整地圖的攝像機邊界。 然後，它呈現結果引腳。

搜索請求、資料來源、符號圖層和攝像機邊界設置在地圖[的事件攔截器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)中，以確保在地圖完全載入後顯示結果。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用搜尋服務的最佳做法](how-to-use-best-practices-for-search.md)

深入了解**模糊搜尋**：

> [!div class="nextstepaction"]
> [Azure 地圖服務模糊搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [從座標取得資訊](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [顯示從甲地到乙地的指示](map-route.md)
