---
title: 教學課程：從 Bing 地圖服務遷移 Web 服務 | Microsoft Azure 地圖服務
description: 如何從 Bing 地圖服務將 Web 服務遷移至 Microsoft Azure 地圖服務。
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 4520332fbc5040aff682ce52e819fa4a940999cc
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90108030"
---
# <a name="migrate-web-service-from-bing-maps"></a>從 Bing 地圖服務遷移 Web 服務

Azure 地圖服務和 Bing 地圖服務都提供透過 REST Web 服務來存取空間 API 的功能。 這些平台的 API 介面所執行的功能很類似，但所使用的命名慣例和回應物件則不同。

下表所提供 Azure 地圖服務的服務 API，會提供與所列 Bing 地圖服務的服務 API 類似的功能。

| Bing 地圖服務的服務 API                 | Azure 地圖服務的服務 API      |
|---------------------------------------|-----------------------------|
| 自動建議                           | [搜尋](https://docs.microsoft.com/rest/api/maps/search)     |
| 指示 (包括卡車)          | [路線指示](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)                          |
| 距離矩陣                       | [路線矩陣](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)                          |
| 影像 – 靜態地圖                  | [轉譯](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                                   |
| 等時線                            | [路線範圍](https://docs.microsoft.com/rest/api/maps/route/getrouterange)                                    |
| 地點分析                        | [搜尋](https://docs.microsoft.com/rest/api/maps/search) + [路線範圍](https://docs.microsoft.com/rest/api/maps/route/getrouterange)    |
| 本機搜尋                          | [搜尋](https://docs.microsoft.com/rest/api/maps/search)     |
| 位置辨識 (POI)           | [搜尋](https://docs.microsoft.com/rest/api/maps/search)     |
| 位置 (轉寄/反向地理編碼) | [搜尋](https://docs.microsoft.com/rest/api/maps/search)                                               |
| 緊貼道路                          | [張貼路線指示](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)                         |
| 空間資料服務 (SDS)           | [搜尋](https://docs.microsoft.com/rest/api/maps/search) + [路由](https://docs.microsoft.com/rest/api/maps/route) + 其他 Azure 服務 |
| 時區                             | [時區](https://docs.microsoft.com/rest/api/maps/timezone)  |
| 交通事故                     | [交通事故詳細資料](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidentdetail)                     |

Azure 地圖服務目前無法使用下列服務 API：

-   權限提高 - 已規劃
-   已最佳化路線 - 已規劃。 Azure 地圖服務路線 API 支援單一車輛的旅行推銷員最佳化。
-   影像中繼資料 – 主要用於取得 Bing 地圖服務中的底圖 URL。 Azure 地圖服務擁有獨立的服務，可直接存取地圖底圖。

Azure 地圖服務有一些可能讓您感興趣的額外 REST Web 服務；

-   [Azure 地圖服務建立者](https://docs.microsoft.com/azure/azure-maps/creator-indoor-maps) – 建立大樓和空間的自訂私人數位分身。
-   [空間作業](https://docs.microsoft.com/rest/api/maps/spatial) – 將複雜的空間計算和作業 (例如地理柵欄) 卸載至某個服務。
-   [地圖底圖](https://docs.microsoft.com/rest/api/maps/render/getmaptile) –從 Azure 地圖服務作為點陣和向量地圖底圖的存取道路和影像地圖底圖。
-   [批次路線規劃](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview) –允許在一段時間內，在單一批次中執行最多 1,000 個路線要求。 路線會在伺服器上以平行方式計算，以進行更快速的處理。
-   [交通](https://docs.microsoft.com/rest/api/maps/traffic)車流 – 以點陣和向量地圖底圖的形式存取即時交通車流資料。
-   [地理位置 API](https://docs.microsoft.com/rest/api/maps/geolocation/getiptolocationpreview) – 取得 IP 位址的位置。
-   [天氣服務](https://docs.microsoft.com/rest/api/maps/weather) – 取得即時和預測天氣資料的存取權。

請務必同時參閱下列最佳做法指南：

-   [搜尋的最佳做法](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search)
-   [路由的最佳做法](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-routing)

## <a name="geocoding-addresses"></a>地理編碼地址

地理編碼程序可將地址 (例如 `"1 Microsoft way, Redmond, WA"`) 轉換成座標 (例如「經度：-122.1298、緯度：47.64005」)。 然後，座標通常會用來在地圖上定位圖釘或置中地圖。

Azure 地圖服務提供數種用來地理編碼地址的方法；

-   [自由格式的地址地理編碼](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)：指定單一地址字串 (如 `"1 Microsoft way, Redmond, WA"`)，並立即處理要求。 如果您需要快速地進行個別地址的地理編碼程序，建議使用此服務。
-   [結構化的地址地理編碼](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured)：指定單一地址的各個部分 (例如，街道名稱、城市、國家/地區和郵遞區號)，然後立即處理要求。 如果您需要快速地進行個別地址的地理編碼程序，而且已將資料剖析成個別的地址部分，則建議使用此服務。
-   [批次地址地理編碼](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview)：建立最多包含 10,000 個地址的要求，並讓這些地址進行一段時間的處理。 所有地址會在伺服器上以平行方式進行地理編碼，並可於完成後下載完整的結果集。 若要對大型資料集進行地理編碼，則建議使用此服務。
-   [模糊搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)：此 API 會結合地址地理編碼與景點搜尋。 此 API 會採用自由格式的字串 (內容可以是地址、地點、地標、景點或景點類別)，並立即處理要求。 如果應用程式可供使用者從相同文字方塊中搜尋地址或景點，則建議使用此 API。
-   [模糊批次搜尋](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)：建立最多包含 10,000 個地址、地點、地標或景點的要求，並讓這些項目進行一段時間的處理。 所有資料會在伺服器上以平行方式處理，並可於完成後下載完整的結果集。

下表會針對結構化和自由格式地址地理編碼，在 Azure 地圖服務中使用可比較的 API 參數來交叉參考 Bing 地圖服務 API 參數。

**依地址的位置 (結構化地址)**

| Bing 地圖服務的 API 參數              | 類似的 Azure 地圖服務 API 參數                 |
|--------------------------------------|-----------------------------------------------------|
| `addressLine`                      | `streetNumber`、`streetName` 或 `crossStreet` |
| `adminDistrict`                    | `countrySubdivision`                              |
| `countryRegion`                    | `country` 和 `countryCode`                     |
| `locality`                         | `municipality` 或 `municipalitySubdivision`     |
| `postalCode`                       | `postalCode`                                      |
| `maxResults` (`maxRes`)          | `limit`                                           |
| `includeNeighborhood` (`inclnb`) | 不適用 – 一律由 Azure 地圖服務傳回 (如果有的話)。   |
| `include` (`incl`)               | 不適用 – 一律由 Azure 地圖服務傳回國家/地區 ISO2 程式碼。 |
| `key`                              | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文件。 |
| `culture` (`c`)                  | `language` – 請參閱[支援的語言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文件。 |
| `userRegion` (`ur`)              | `view` – 請參閱[支援的檢視](https://aka.ms/AzureMapsLocalizationViews)文件。 |

Azure 地圖服務也支援；

-   `countrySecondarySubdivision` – 國家/地區，區域
-   `countryTertiarySubdivision` - 命名區域；自治鎮、村鎮、地方行政區
-   `ofs` - 使用 `maxResults` 參數結合結果的頁面。

**依查詢的位置 (自由格式地址字串)**

| Bing 地圖服務的 API 參數              | 類似的 Azure 地圖服務 API 參數      |
|--------------------------------------|------------------------------------------|
| `query`                            | `query`                                |
| `maxResults` (`maxRes`)          | `limit`                                |
| `includeNeighborhood` (`inclnb`) | 不適用 – 一律由 Azure 地圖服務傳回 (如果有的話)。  |
| `include` (`incl`)               | 不適用 – 一律由 Azure 地圖服務傳回國家/地區 ISO2 程式碼。  |
| `key`                              | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文件。 |
| `culture` (`c`)                  | `language` – 請參閱[支援的語言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文件。  |
| `userRegion` (`ur`)              | `view` – 請參閱[支援的檢視](https://aka.ms/AzureMapsLocalizationViews)文件。 |

Azure 地圖服務也支援；

-   `typeahead` - 如果查詢將會解讀為部分輸入，且搜尋將會進入預測模式 (自動建議/自動完成)，則為物種。
-   `countrySet` - 用來限制搜尋的 ISO2 國家/地區代碼清單 (以逗號分隔)。
-   `lat`/`lon``topLeft`/`btmRight`，`radius` –指定使用者位置和區域，讓結果的本機相關性更高。
-   `ofs` - 使用 `maxResults` 參數結合結果的頁面。

如需如何使用搜尋服務的範例，請參閱[這裡](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)。 請務必檢閱[搜尋的最佳做法](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search)文件。

## <a name="reverse-geocode-a-coordinate-find-a-location-by-point"></a>反向地理編碼座標 (依點尋找位置)

反向地理編碼程序可將地理座標 (例如「經度：-122.1298、緯度：47.64005) 到其大約的地址 (例如 `"1 Microsoft way, Redmond, WA"`)。

Azure 地圖服務提供數種反向地理編碼方法；

-   [地址反向地理編碼器](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)：指定單一地理座標以取得其大致地址，並立即處理要求。
-   [十字路口反向地理編碼器](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)：指定單一地理座標以取得附近的十字路口資訊 (例如，第 1 街和主街)，並立即處理要求。
-   [批次地址反向地理編碼器](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview)：建立最多包含 10,000 個座標的要求，並讓這些地址進行一段時間的處理。 所有資料會在伺服器上以平行方式處理，並可於完成後下載完整的結果集。

下表會交互參照 Bing 地圖服務 API 參數與 Azure 地圖服務中類似的 API 參數。

| Bing 地圖服務的 API 參數              | 類似的 Azure 地圖服務 API 參數       |
|--------------------------------------|-------------------------------------------|
| `point`                              | `query`                                   |
| `includeEntityTypes`                 | `entityType` – 請參閱下方的實體類型比較表。    |
| `includeNeighborhood` (`inclnb`)     | 不適用 – 一律由 Azure 地圖服務傳回 (如果有的話)。         |
| `include` (`incl`)                   | 不適用 – 一律由 Azure 地圖服務傳回國家/地區 ISO2 程式碼。    |
| `key`                                | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文件。 |
| `culture` (`c`)                      | `language` – 請參閱[支援的語言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文件。   |
| `userRegion` (`ur`)                  | `view` – 請參閱[支援的檢視](https://aka.ms/AzureMapsLocalizationViews)文件。 |

請務必檢閱[搜尋的最佳做法](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search)文件。

Azure 地圖服務反向地理編碼 API 會有一些 Bing 地圖服務並未提供的額外功能，可能有助於遷移應用程式時的整合：

-   擷取速限資料。
-   擷取道路使用資訊；支線、幹線、限制進入、斜坡等等。
-   座標落在街道哪一側。

**實體類型比較表**

下表會在 Azure 地圖服務中，交叉參考 Bing 地圖服務實體類型值與對等的屬性名稱。

| Bing 地圖服務實體類型 | 類似的 Azure 地圖服務實體類型               | 描述                                |
|-----------------------|-------------------------------------------------|--------------------------------------------|
| `Address`             |                                                 | *位址*                                  |
| `Neighborhood`        | `Neighbourhood`                                 | 「鄰近地區」                             |
| `PopulatedPlace`      | `Municipality` 或 `MunicipalitySubdivision`     | 「市」、「鄉鎮或副城」，或「超級城市」     |
| `Postcode1`           | `PostalCodeArea`                                | 「郵政編碼」或「郵遞區號」                |
| `AdminDivision1`      | `CountrySubdivision`                            | 「州」或「省」                      |
| `AdminDivision2`      | `CountrySecondarySubdivison`                    | 「縣」或「區」                    |
| `CountryRegion`       | `Country`                                       | *國家/地區名稱*                             |
|                       | `CountryTertiarySubdivision`                    | 「自治鎮」、「村鎮」、「地方行政區」          |

## <a name="get-location-suggestions-autosuggest"></a>取得位置建議 (自動建議)

數個 Azure 地圖服務搜尋 API 的支援預測模式可用於自動建議案例。 Azure 地圖服務[模糊搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) API 最類似 Bing 地圖服務自動建議 API。 下列 API 也支援預測模式，將 `&typeahead=true` 新增至查詢；

-   [自由格式的地址地理編碼](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)：指定單一地址字串 (如 `"1 Microsoft way, Redmond, WA"`)，並立即處理要求。 如果您需要快速地進行個別地址的地理編碼程序，建議使用此服務。
-   [模糊搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)：此 API 會結合地址地理編碼與景點搜尋。 此 API 會採用自由格式的字串 (內容可以是地址、地點、地標、景點或景點類別)，並立即處理要求。 如果應用程式可供使用者從相同文字方塊中搜尋地址或景點，則建議使用此 API。
-   [POI 搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)：依名稱搜尋景點。 例如；`"starbucks"`。
-   [POI 類別搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory)：依類別搜尋景點。 例如；「餐廳」。

## <a name="calculate-routes-and-directions"></a>計算路線和方線

Azure 地圖服務可用來計算路線和方向。 Azure 地圖服務有許多與 Bing 地圖服務路線規劃服務相同的功能，例如；

-   抵達時間和出發時間
-   即時型和預測型交通路線
-   不同運輸模式；開車、走路、卡車
-   導航點順序最佳化 (旅行推銷員)

> [!NOTE]
> Azure 地圖服務要求所有導航點都必須是座標。 地址必須先進行地理編碼。

Azure 地圖服務路線規劃服務會提供下列 API 來計算路線；

-   [計算路線](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)：計算路線並立即處理要求。 此 API 同時支援 GET 和 POST 要求。 當您指定大量導航點，或使用許多路線選項來確保 URL 要求不會太長而造成問題時，建議您使用 POST 要求。
-   [批次路線](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview)：建立最多包含 1,000 個路線要求的要求，並讓這些地址進行一段時間的處理。 所有資料會在伺服器上以平行方式處理，並可於完成後下載完整的結果集。
-   [行動服務](https://docs.microsoft.com/rest/api/maps/mobility)：使用大眾運輸系統來計算路線和方向。

下表會交互參照 Bing 地圖服務 API 參數與 Azure 地圖服務中類似的 API 參數。

| Bing 地圖服務的 API 參數                                    | 類似的 Azure 地圖服務 API 參數               |
|------------------------------------------------------------|---------------------------------------------------|
| `avoid`                                                    | `avoid`                                           |
| `dateTime` (`dt`)                                          | `departAt` 或 `arriveAt`                          |
| `distanceBeforeFirstTurn` (`dbft`)                         | N/A                                               |
| `distanceUnit` (`du`)                                      | N/A – Azure 地圖服務只會使用公制單位。     |
| `heading` (`hd`)                                           | `vehicleHeading`                                  |
| `maxSolutions` (`maxSolns`)                                | `maxAlternatives`、`alternativeType`、`minDeviationDistance` 和 `minDeviationTime`  |
| `optimize` (`optwz`)                                       | `routeType` 和 `traffic`                         |
| `optimizeWaypoints` (`optWp`)                              | `computeBestOrder`                                |
| `routeAttributes` (`ra`)                                   | `instructionsType`                                |
| `routePathOutput` (`rpo`)                                  | `routeRepresentation`                             |
| `timeType` (`tt`)                                          | `departAt` 或 `arriveAt`                          |
| `tolerances` (`tl`)                                        | N/A                                               |
| `travelMode`                                               | `travelMode`                                      |
| `waypoint.n` (`wp.n`) 或 `viaWaypoint.n` (`vwp.n`)         | `query` – 格式為 `lat0,lon0:lat1,lon1….` 的座標   |
| `key`                                                      | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文件。 |
| `culture` (`c`)                                            | `language` – 請參閱[支援的語言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文件。 |
| `userRegion` (`ur`)                                        | `view` – 請參閱[支援的檢視](https://aka.ms/AzureMapsLocalizationViews)文件。 |

Azure 地圖服務路線規劃 API 也支援相同 API 內的卡車路線規劃。 下表會交互參照其他 Bing 地圖服務卡車路線規劃參數與 Azure 地圖服務中類似的 API 參數。

| Bing 地圖服務的 API 參數                  | 類似的 Azure 地圖服務 API 參數        |
|------------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                 | 不適用 – 僅支援計量中的維度。 |
| `weightUnit` (`wu`)                      | 不適用 – 僅支援以公斤計算的重量。 |
| `vehicleHeight` (`height`)               | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                 | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                   | `vehicleLength`                            |
| `vehicleWeight` (`weight`)               | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                 | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                 | **N/A**                                    |
| `vehicleSemi` (`semi`)                   | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)             | **N/A**                                    |
| `vehicleMinTurnRadius` (`vmtr`)          | **N/A**                                    |
| `vehicleAvoidCrossWind` (`vacw`)         | **N/A**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)     | **N/A**                                    |
| `vehicleHazardousMaterials` (`vhm`)      | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)        | `vehicleLoadType`                          |

> [!TIP]
> 根據預設，Azure 地圖服務的路線 API 只會傳回路線路徑的摘要 (距離和時間) 和座標。 使用 `instructionsType` 參數可擷取轉向建議指示。 `routeRepresentation` 參數可用來篩選出摘要和路線路徑。

請務必同時檢閱[路線規劃的最佳做法](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-routing)文件。

Azure 地圖服務路線規劃 API 會有許多 Bing 地圖服務並未提供的額外功能，可能有助於遷移應用程式時的整合：

-   支援路線類型：最短、最快、trilling，以及最省油。
-   支援額外的行進模式：自行車、巴士、摩托車、計程車、卡車和貨車。
-   支援 150 個導航點。
-   在單一要求中計算多個行進時間；過往交通情況、即時交通情況、無交通情況。
-   避免額外的道路類型：共乘道路、沒有鋪柏油的道路、已使用的道路。
-   以引擎規格為基礎的路線。 根據剩餘的燃料/電量和引擎規格，計算油車或電動車的路線。
-   指定最大車速。

## <a name="snap-coordinates-to-road"></a>將座標貼齊道路

有數種方式可在 Azure 地圖服務中將座標貼齊道路。

-   使用路線指示 API 來貼齊座標，並從道路網路上的邏輯路線貼齊座標。
-   使用 Azure 地圖服務 Web SDK，將個別座標貼齊向量地圖底圖中的最接近道路。
-   直接使用 Azure 地圖服務向量地圖底圖來貼齊個別座標。

**使用路線指示 API 來貼齊座標**

Azure 地圖服務可以使用[路線指示](https://docs.microsoft.com/rest/api/maps/route/postroutedirections) API，將座標貼齊至道路。 這項服務可以用來重建一組座標之間的邏輯路線，且相當於 Bing 地圖服務貼齊道路 API。

有兩種不同的方式可以使用路線指示 API 將座標貼齊至道路。

-   如果座標低於 150 個，則可以在取得路線指示 API 中以導航點的方式傳遞。 使用這種方法可以抓取兩種不同類型的已貼齊資料；路線指示會包含個別的已對齊導航點，而路線路徑則會包含一組插入的座標，以填滿座標之間的完整路徑。
-   如果座標超過 150 個，則可以使用張貼路線指示 API。 座標開始和結束座標必須傳入查詢參數，但所有座標都可以傳遞至 POST 要求主體中的 `supportingPoints` 參數，並將點的 GeoJSON 幾何集合格式化。 使用此方法時，唯一可用的貼齊資料就是路線路徑，這是一組插入的座標，用來填滿座標之間的完整路徑。 以下是使用 Azure 地圖服務 Web SDK 中的服務模組時，此方法的[範例](https://azuremapscodesamples.azurewebsites.net/?sample=Snap%20points%20to%20logical%20route%20path)。

下表會交互參照 Bing 地圖服務 API 參數與 Azure 地圖服務中類似的 API 參數。

| Bing 地圖服務的 API 參數    | 類似的 Azure 地圖服務 API 參數                                 |
|----------------------------|---------------------------------------------------------------------|
| `points`                   | `supportingPoints` – 將這些點傳遞至張貼要求的主體中  |
| `interpolate`              | N/A                                                                 |
| `includeSpeedLimit`        | N/A                                                                 |
| `includeTruckSpeedLimit`   | N/A                                                                 |
| `speedUnit`                | N/A                                                                 |
| `travelMode`               | `travelMode`                                                        |
| `key`                      | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文件。 |
| `culture` (`c`)            | `language` – 請參閱[支援的語言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文件。   |
| `userRegion` (`ur`)        | `view` – 請參閱[支援的檢視](https://aka.ms/AzureMapsLocalizationViews)文件。   |

Azure 地圖服務路線規劃 API 也支援相同 API 內的卡車路線參數，以確保會計算邏輯路徑。 下表會交互參照其他 Bing 地圖服務卡車路線規劃參數與 Azure 地圖服務中類似的 API 參數。

| Bing 地圖服務的 API 參數                 | 類似的 Azure 地圖服務 API 參數        |
|-----------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                | 不適用 – 僅支援計量中的維度。 |
| `weightUnit` (`wu`)                     | 不適用 – 僅支援以公斤為單位的重量。 |
| `vehicleHeight` (`height`)              | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                  | `vehicleLength`                            |
| `vehicleWeight` (`weight`)              | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                | **N/A**                                    |
| `vehicleSemi` (`semi`)                  | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)            | **N/A**                                    |
| `vehicleMinTurnRadius` (`vmtr`)         | **N/A**                                    |
| `vehicleAvoidCrossWind` (`vacw`)        | **N/A**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)    | **N/A**                                    |
| `vehicleHazardousMaterials` (`vhm`)     | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)       | `vehicleLoadType`                          |

由於此方法使用路線指示 API，因此該服務中的完整選項組可用來自訂用來將座標貼齊道路的邏輯。 例如，指定出發時間會導致記錄的車流資料納入考慮。

Azure 地圖服務路線指示 API 目前不會傳回速度限制資料，不過，您可以使用 Azure 地圖服務反向地理編碼 API 進行擷取。

**使用 Web SDK 來貼齊座標**

Azure 地圖服務 Web SDK 會使用向量地圖底圖來轉譯地圖。 這些向量地圖底圖包含原始的道路幾何資訊，可用來計算最接近座標的道路，以簡化個別座標的貼齊。 當您想要讓座標以視覺方式顯示在道路上，且您已經使用 Azure 地圖服務 Web SDK 將資料視覺化時，這會很有用。

不過，這種方法只會貼齊地圖檢視中所載入的道路區段。 在國家/地區層級相應放大時，可能不會有任何道路資料，因此無法完成貼齊，不過，在該縮放層級上，單一像素可以代表數個城市區塊的區域，因此不需要貼齊。 為了解決此情況，您可以在每次地圖完成移動時套用貼齊邏輯。 以下為示範此情況的[程式碼範例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic)。

**直接使用 Azure 地圖服務向量地圖底圖來貼齊座標**

Azure 地圖服務向量地圖底圖包含原始的道路幾何資料，可用來計算從道路到座標的最接近點，以執行個別座標的基本貼齊。 所有道路區段都會出現在縮放層級 15 的磁區中，因此您將要從該處取出地圖底圖。 然後，您可以使用 [quadtree 地圖底圖金字塔數學](https://docs.microsoft.com/azure/azure-maps/zoom-levels-and-tile-grid)來判斷所需的地圖底圖，並將地圖底圖轉換成幾何。 從這裡可以使用空間數學程式庫 (例如 [turf js](http://turfjs.org/) 或 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)) 來計算最接近的線段。

## <a name="retrieve-a-map-image-static-map"></a>擷取地圖影像 (靜態地圖)

Azure 地圖服務會提供 API 來呈現已覆蓋資料的靜態地圖影像。 Azure 地圖服務的[地圖影像呈現](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) API 相當於 Bing 地圖服務中的靜態地圖 API。

> [!NOTE]
> Azure 地圖服務需要中心、所有圖釘和路徑位置都是 `longitude,latitude` 格式的座標，而 Bing 地圖服務則使用 `latitude,longitude` 格式。</p>
<p>地址必須先進行地理編碼。

下表會交互參照 Bing 地圖服務 API 參數與 Azure 地圖服務中類似的 API 參數。

| Bing 地圖服務的 API 參數  | 類似的 Azure 地圖服務 API 參數            |
|--------------------------|------------------------------------------------|
| `centerPoint`            | `center`                                       |
| `format`                 | `format` – 指定為 URL 路徑的一部分。 目前僅支援 PNG。  |
| `heading`                | 不適用 – 不支援街邊。                |
| `imagerySet`             | `layer` 和 `style` – 請參閱[支援的地圖樣式](https://docs.microsoft.com/azure/azure-maps/supported-map-styles)文件。   |
| `mapArea` (`ma`)         | `bbox`                                         |
| `mapLayer` (`ml`)        | N/A                                            |
| `mapSize` (`ms`)         | `width` 和 `height` – 最高可達 8192x8192 大小。 |
| `declutterPins` (`dcl`)  | N/A                                            |
| `dpi`                    | N/A                                            |
| `drawCurve`              | `path`                                         |
| `mapMetadata`            | N/A                                            |
| `pitch`                  | 不適用 – 不支援街邊。                |
| `pushpin` (`pp`)         | `pins`                                         |
| `zoomLevel`              | `zoom`                                         |
| `query`                  | 不適用 – 必須使用中心或周框。     |
| `highlightEntity` (`he`) | N/A                                            |
| `style`                  | N/A                                            |
| 路線參數         | N/A                                            |
| `key`                    | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文件。 |
| `culture` (`c`)          | `language` – 請參閱[支援的語言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文件。   |
| `userRegion` (`ur`)      | `view` – 請參閱[支援的檢視](https://aka.ms/AzureMapsLocalizationViews)文件。 |

> [!NOTE]
> Azure 地圖服務所使用的地圖底圖系統，其地圖底圖大小是 Bing Maps 所用地圖底圖的兩倍。 因此，Azure 地圖服務中的縮放層級值會比 Bing 地圖服務中的更近一個縮放層級。 請在您要遷移的要求中降低 1 個縮放層級，以補償這一差距。

如需詳細資訊，請參閱[關於地圖影像呈現 API 的操作指南](https://docs.microsoft.com/azure/azure-maps/how-to-render-custom-data)。

除了能夠產生靜態地圖影像之外，Azure 地圖服務的呈現服務也能讓您直接存取點陣 (PNG) 和向量格式的地圖底圖；

-   [地圖底圖](https://docs.microsoft.com/rest/api/maps/render/getmaptile) – 擷取基底地圖 (道路、邊界、背景) 的點陣 (PNG) 和向量地圖底圖。
-   [地圖影像地圖底圖](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) – 擷取空照圖影像和衛星影像的地圖底圖。

### <a name="pushpin-url-parameter-format-comparison"></a>圖釘 URL 參數格式比較

**之前：Bing 地圖服務**

在 Bing 地圖服務中，您可以在 URL 中使用 `pushpin` 參數將圖釘新增至靜態地圖影像。 `pushpin` 參數會取得 `latitude,longitude` 格式中的位置、圖示樣式和文字標籤 (最多三個字元)，如下所示：

> `&pushpin=latitude,longitude;iconStyle;label`

您可以將其他 `pushpin` 參數新增至具有一組不同值的 URL，從而新增其他圖釘。 圖釘圖示樣式僅限於 Bing 地圖服務 API 中提供的其中一個預先定義樣式。

例如，在 Bing 地圖服務中，您可以使用下列 URL 參數將具有標籤 "AB" 的紅色圖釘新增至地圖上的座標 (經度：-110、緯度：45)：

> `&pushpin=45,-110;7;AB`

<center>

![Bing 地圖服務靜態地圖釘選](media/migrate-bing-maps-web-service/bing-maps-static-map-pin.jpg)</center>

**之後：Azure 地圖服務**

在 Azure 地圖服務中，您也可以藉由在 URL 中指定 `pins` 參數，將圖釘新增至靜態地圖影像。 Azure 地圖服務中的圖釘是藉由指定圖示樣式和使用該圖示樣式的位置清單進行定義。 然後將此資訊傳遞到可以指定多次的 `pins` 參數，以支援具有不同樣式的圖釘。

> `&pins=iconType|pinStyles||pinLocation1|pinLocation2|...`

您可以將其他 `pins` 參數新增至具有不同樣式和一組位置的 URL，從而使用其他樣式。

至於釘選位置，Azure 地圖服務會要求座標採用 `longitude latitude` 格式，而 Bing 地圖服務則使用 `latitude,longitude` 格式。 另請注意，在 Azure 地圖服務中，經度和緯度會**用空格而不是逗號**來分隔。

`iconType` 值會指定所要建立的圖釘類型，並可具有下列值：

-   `default` – 預設的圖釘圖示。
-   `none` – 不會顯示任何圖示，只會呈現標籤。
-   `custom` - 指定要使用自訂圖示。 指向圖示影像的 URL 可在圖釘位置資訊後面新增至 `pins` 參數的結尾。
-   `{udid}` – 儲存在 Azure 地圖服務資料儲存體平台中的圖示所具有的唯一資料識別碼 (UDID)。

Azure 地圖服務中的圖釘樣式會以 `optionNameValue` 格式來新增，如有多個樣式，則以縱線字元 (`|`) 來分隔，例如 `iconType|optionName1Value1|optionName2Value2`。 請注意，選項名稱和值不會分隔。 在 Azure 地圖服務中，下列樣式選項名稱可用來設定圖釘樣式：

-   `al` – 指定圖釘的不透明度 (Alpha)。 可以是 0 到 1 之間的數字。
-   `an` – 指定圖釘錨點。 以 `x y` 格式指定的 x 和 y 像素值。
-   `co` – 圖釘的色彩。 必須是 24 位元的十六進位色彩：`000000` 到 `FFFFFF`。
-   `la` – 指定標籤錨點。 以 `x y` 格式指定的 x 和 y 像素值。
-   `lc` – 標籤的色彩。 必須是 24 位元的十六進位色彩：`000000` 到 `FFFFFF`。
-   `ls` – 標籤的大小 (以像素為單位)。 可以是大於 0 的數字。
-   `ro` – 用來旋轉圖示的值 (以度為單位)。 可以是 -360 到 360 之間的數字。
-   `sc` – 圖釘圖示的縮放值。 可以是大於 0 的數字。

系統會為每個圖釘位置都指定標籤值，而不是將單一標籤值套用至位置清單中的所有圖釘。 標籤值可以是有多個字元的字串，並以單引號括住，以確保系統不會將其誤認為樣式或位置值。

例如，在 Azure 地圖服務中，若要新增紅色 (`FF0000`) 預設圖示，其標籤為「太空針塔」，位於 (15 50) 下面，則座標 (經度：-122.349300、緯度：47.620180) 處的圖示可使用下列 URL 參數來實現：

> `&pins=default|coFF0000|la15 50||'Space Needle'-122.349300 47.620180`

<center>

![Azure 地圖服務靜態地圖釘選](media/migrate-bing-maps-web-service/azure-maps-static-map-pin.jpg)</center>

下列範例會使用標籤值 '1'、'2' 和 '3' 來新增三個圖釘：

> `&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12`

<center>

![Azure 地圖服務靜態地圖多個釘選](media/migrate-bing-maps-web-service/azure-maps-static-map-multiple-pins.jpg)</center>

### <a name="draw-curve-url-parameter-format-comparison"></a>繪製曲線 URL 參數格式比較

**之前：Bing 地圖服務**

在 Bing 地圖服務中，您可以在 URL 中使用 `drawCurve` 參數將線條和多邊形新增至靜態地圖影像。 `drawCurve` 參數會取得形類型、樣式類型，以及要在地圖上呈現的位置清單，如下所示：

> `&drawCurve=shapeType,styleType,location1,location2...`

您可以將其他 `drawCurve` 參數新增至具有不同樣式和一組位置的 URL，從而使用其他樣式。

Bing 地圖服務中的位置會以 `latitude1,longitude1_latitude2,longitude2_…` 格式來指定。 位置也可以編碼。

Bing 地圖服務中的形狀類型包括線條、多邊形、圓形和曲線。 樣式類型包括線條色彩、線條粗細、外框色彩、填滿色彩、外框粗細和圓形半徑。

例如，在 Bing 地圖服務中，不透明度為 50% 且粗細為 4 個像素的藍色線條可使用下列 URL 參數新增至地圖上的座標 (經度：-110、緯度：45) 和 (經度：-100, 緯度：50) 之間：

`&drawCurve=l,FF000088,4;45,-110_50,-100`

<center>

![Bing 地圖服務靜態地圖線條](media/migrate-bing-maps-web-service/bing-maps-static-map-line.jpg)</center>

**之後：Azure 地圖服務**

在 Azure 地圖服務中，您也可以藉由在 URL 中指定「路徑」參數，將線條和多邊形新增至靜態地圖影像。 如同 Bing 地圖服務，您也可以在這個參數中指定樣式和位置清單，而且可以多次指定「路徑」參數以呈現多個具有不同樣式的圓形、線條和多邊形。

> `&path=pathStyles||pathLocation1|pathLocation2|...`

至於路徑位置，Azure 地圖服務會要求座標採用 `longitude latitude` 格式，而 Bing 地圖服務則使用 `latitude,longitude` 格式。 另請注意，在 Azure 地圖服務中，經度和緯度會用**空格而不是逗號來分隔**。 Azure 地圖服務目前不支援編碼的路徑。 較大的資料集可以透過 GeoJSON 檔案的形式上傳至 Azure 地圖服務的資料儲存體 API 中，如[這裡](https://docs.microsoft.com/azure/azure-maps/how-to-render-custom-data#get-data-from-azure-maps-data-storage)所述。

Azure 地圖服務中的路徑樣式會以 `optionNameValue` 格式來新增，如有多個樣式，則以縱線字元 (`|`) 來分隔，例如 `optionName1Value1|optionName2Value2`。 請注意，選項名稱和值不會分隔。 在 Azure 地圖服務中，下列樣式選項名稱可用來設定路徑樣式：

-   `fa` – 在呈現多邊形時所使用的填滿色彩不透明度 (Alpha)。 可以是 0 到 1 之間的數字。
-   `fc` – 用來呈現多邊形區域的填滿色彩。
-   `la` – 在繪製線條和多邊形外框時所使用的線條色彩不透明度 (Alpha)。 可以是 0 到 1 之間的數字。
-   `lc` – 用來呈現線條和多邊形外框的線條色彩。
-   `lw` – 線條的寬度 (以像素為單位)。
-   `ra` – 指定圓形半徑 (以公尺為單位)。

例如，在 Azure 地圖服務中，不透明度為 50% 且粗細為 4 個像素的藍色線條可使用下列 URL 參數新增至地圖上的座標 (經度：-110、緯度：45) 和 (經度：-100, 緯度：50) 之間：

> `&path=lc0000FF|la.5|lw4||-110 45|-100 50`

<center>

![Azure 地圖服務靜態地圖線條](media/migrate-bing-maps-web-service/azure-maps-static-map-line.jpg)</center>

## <a name="calculate-a-distance-matrix"></a>計算距離矩陣

Azure 地圖服務提供了 API 供您以距離矩陣的形式計算一組位置之間的行進時間和距離。 Azure 地圖服務的距離矩陣 API 與 Bing 地圖服務中的距離矩陣 API 類似；

-   [路線矩陣](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)：以非同步方式計算一組起點和終點的行進時間和距離。 每個要求最多可支援 700 個資料格 (起點數目乘以終點數目)。 在考慮到該條件約束的情況下，可能的矩陣維度範例如下：`700x1`、`50x10`、`10x10`、`28x25`、`10x70`。

> [!NOTE]
> 針對距離矩陣 API 的要求只能使用 POST 要求來提出，而且要在要求主體中附上起點和終點資訊。</p>
<p>此外，Azure 地圖服務會要求所有起點和終點都必須是座標。 地址必須先進行地理編碼。

下表會交互參照 Bing 地圖服務 API 參數與 Azure 地圖服務中類似的 API 參數。

| Bing 地圖服務的 API 參數 | 類似的 Azure 地圖服務 API 參數                         |
|-------------------------|-------------------------------------------------------------|
| `origins`               | `origins` – 在 POST 要求主體中指定為 GeoJSON。    |
| `destinations`          | `destination` – 在 POST 要求主體中指定為 GeoJSON。 |
| `endTime`               | `arriveAt`                                                  |
| `startTime`             | `departAt`                                                  |
| `travelMode`            | `travelMode`                                                |
| `resolution`            | N/A                                                         |
| `distanceUnit`          | 不適用 – 所有距離 (以公尺為單位)。                              |
| `timeUnit`              | 不適用 – 所有時間 (以秒為單位)。                                 |
| `key`                   | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文件。 |
| `culture` (`c`)         | `language` – 請參閱[支援的語言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文件。  |
| `userRegion` (`ur`)     | `view` – 請參閱[支援的檢視](https://aka.ms/AzureMapsLocalizationViews)文件。     |

> [!TIP]
> Azure 地圖服務路線 API 中可用的所有進階路線選項 (卡車路線、引擎規格、避開)，Azure 地圖服務距離矩陣 API 也都會支援。

## <a name="calculate-an-isochrone"></a>計算等時線

Azure 地圖服務提供用來計算等時線的 API，這個多邊形中涵蓋在指定的時間或燃料/費用內，可以從出發點往任何方向行進的區域。 Azure 地圖服務的路線範圍 API 與 Bing 地圖服務中的等時線 API 類似；

-   [路線](https://docs.microsoft.com/rest/api/maps/route/getrouterange)範圍**：計算多邊形中涵蓋在指定的可用時間、距離或燃料/費用內，可以從出發點往任何方向行進的區域。

> [!NOTE]
> Azure 地圖服務要求查詢原點必須是座標。 地址必須先進行地理編碼。</p>
<p>此外，Bing 地圖服務可以根據時間或距離來計算等時線，而 Azure 地圖服務可以根據時間、距離或可用的燃料/費用量來計算等時線。

下表會交互參照 Bing 地圖服務 API 參數與 Azure 地圖服務中類似的 API 參數。

| Bing 地圖服務的 API 參數      | 類似的 Azure 地圖服務 API 參數            |
|------------------------------|------------------------------------------------|
| `waypoint` (`wp`)            | `query`                                        |
| `dateTime` (`dt`)            | `departAt`                                     |
| `maxTime`                    | `timeBudgetInSec`                              |
| `timeUnit` (`tu`)            | 不適用 – 所有時間 (以秒為單位)。                    |
| `travelMode` (`mode`)        | `travelMode`                                   |
| `maxDistance` (`maxDis`)     | `distanceBudgetInMeters`                       |
| `distanceUnit` (`du`)        | 不適用 – 所有距離 (以公尺為單位)。                 |
| `optimize` (`optmz`)         | `routeType`                                    |
| `key`                        | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文件。 |
| `culture` (`c`)              | `language` – 請參閱[支援的語言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文件。  |
| `userRegion` (`ur`)          | `view` – 請參閱[支援的檢視](https://aka.ms/AzureMapsLocalizationViews)文件。 |

> [!TIP]
> Azure 地圖服務路線 API 中可用的所有進階路線規劃選項 (卡車路線規劃、引擎規格、避開...)，Azure 地圖服務等時線 API 也都會支援。

## <a name="search-for-points-of-interest"></a>搜尋景點

您可以使用下列 API，在 Bing 地圖服務中搜尋景點資料：

-   **本機搜尋：** 依名稱或依實體類型 (類別) 搜尋鄰近景點 (星形搜尋)。 Azure 地圖服務 [POI 搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)和 [POI 類別搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) API 最類似此 API。
-   **位置辨識**：搜尋位於某位置一定距離內的景點。 Azure 地圖服務的[鄰近搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) API 與此 API 非常類似。
-   **地點分析：** 搜尋從特定座標的指定駕駛時間或距離上限內的景點。 藉由先計算等時線，然後將其傳遞至 [幾何內搜尋](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) API，即可達成此 Azure 地圖服務。

Azure 地圖服務會提供數個景點搜尋 API：

-   [POI 搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)：依名稱搜尋景點。 例如；`"starbucks"`。
-   [POI 類別搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory)：依類別搜尋景點。 例如；「餐廳」。
-   [鄰近搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)：搜尋位於某位置一定距離內的景點。
-   [模糊搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)：此 API 會結合地址地理編碼與景點搜尋。 此 API 會採用自由格式的字串 (內容可以是地址、地點、地標、景點或景點類別)，並立即處理要求。 如果應用程式可供使用者從相同文字方塊中搜尋地址或景點，則建議使用此 API。
-   [在幾何範圍內搜尋](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)：搜尋指定幾何範圍 (多邊形) 內的景點。
-   [沿路線搜尋](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute)：搜尋指定路線路徑沿途的景點。
-   [模糊批次搜尋](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)：建立最多包含 10,000 個地址、地點、地標或景點的要求，並讓這些項目進行一段時間的處理。 所有資料會在伺服器上以平行方式處理，並可於完成後下載完整的結果集。

請務必檢閱[搜尋的最佳做法](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search)文件。

## <a name="get-traffic-incidents"></a>取得交通事故

Azure 地圖服務提供數個 API 來擷取交通資料。 可用的路況資料類型有兩種；

-   **車流資料** – 提供有關道路路況車流的計量。 這通常用來為道路標色碼。 此資料會每 2 分鐘更新一次。
-   **事故資料** – 提供可能影響車流的施工、道路封閉、事故和其他事故的資料。 此資料每分鐘更新一次。

Bing 地圖服務會在其互動式地圖控制項中提供交通車流和事故資料，同時也會將事故資料當做服務提供。

交通資料也會整合到 Azure 地圖服務的互動式地圖控制項。 Azure 地圖服務也會提供下列交通服務 API；

-   [交通車流區段](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficflowsegment)：提供與給定座標最接近路段的速度和行進時間的相關資訊。
-   [交通車流地圖底圖](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficflowtile)：提供包含交通車流資料的點陣和向量地圖底圖。 這些可以與 Azure 地圖服務控制項或協力廠商地圖控制項 (例如 Leaflet) 搭配使用。 向量地圖底圖也可以用於先進的資料分析。
-   [交通事故詳細資料](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidentdetail)：提供周框、縮放層級和交通模型內的交通事故詳細資料。
-   [交通事故地圖底圖](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidenttile)：提供包含交通事故資料的點陣和向量地圖底圖。
-   [交通事故檢視區](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidentviewport)：擷取要求中所描述檢視區的法律和技術資訊，例如交通模型識別碼。

下表會交互參照 Bing 地圖服務交通 API 參數與 Azure 地圖服務中類似的交通事故詳細資料 API 參數。

| Bing 地圖服務的 API 參數  | 類似的 Azure 地圖服務 API 參數   |
|--------------------------|---------------------------------------|
| `mapArea`                | `boundingBox` 和 `boundingZoom`      |
| `includeLocationCodes`   | N/A                                   |
| `severity` (`s`)         | 不適用 – 所有傳回的資料               |
| `type` (`t`)             | 不適用 – 所有傳回的資料               |
| `key`                    | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文件。 |
| `culture` (`c`)          | `language` – 請參閱[支援的語言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文件。 |
| `userRegion` (`ur`)      | `view` – 請參閱[支援的檢視](https://aka.ms/AzureMapsLocalizationViews)文件。 |

## <a name="get-a-time-zone"></a>取得時區

Azure 地圖服務提供了 API 供您擷取座標所在的時區。 Azure 地圖服務的時區 API 類似於 Bing 地圖服務中的時區 API；

-   [時區 (依座標)](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates)：指定座標並取得其所在時區的詳細資料。

下表會交互參照 Bing 地圖服務 API 參數與 Azure 地圖服務中類似的 API 參數。

| Bing 地圖服務的 API 參數 | 類似的 Azure 地圖服務 API 參數          |
|-------------------------|----------------------------------------------|
| `point`                 | `query`                                      |
| `query`                 | 不適用 – 位置必須先進行地理編碼。      |
| `dateTime`              | `timeStamp`                                  |
| `includeDstRules`       | 不適用 – 一律包含在 Azure 地圖服務的回應中。 |
| `key`                   | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文件。 |
| `culture` (`c`)         | `language` – 請參閱[支援的語言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文件。  |
| `userRegion` (`ur`)     | `view` – 請參閱[支援的檢視](https://aka.ms/AzureMapsLocalizationViews)文件。  |

除此之外，Azure 地圖服務平台也提供了一些額外的時區 API，以協助使用時區名稱和識別碼進行轉換；

-   [時區 (依識別碼)](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid) ：針對指定的 IANA 時區識別碼，傳回目前、以往和未來的時區資訊。
-   [時區列舉 IANA](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana)：傳回 IANA 時區識別碼的完整清單。 IANA 服務的更新會在一天內就反映在系統中。 
-   [時區列舉 Windows](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows)：傳回 Windows 時區識別碼的完整清單。
-   [時區 IANA 版本](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion)：傳回 Azure 地圖服務所用的目前 IANA 版本號碼。 
-   [Windows 轉 IANA 時區](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana)：在給定有效 Windows 時區識別碼的情況下，傳回對應的 IANA 識別碼。 單一 Windows 識別碼可能會傳回多個 IANA 識別碼。

## <a name="spatial-data-services-sds"></a>空間資料服務 (SDS)

Bing 地圖服務中的空間資料服務提供三個主要功能：

-   批次地理編碼 – 使用單一要求處理大量的地址地理編碼批次。
-   擷取系統管理界限資料 – 使用座標並取得指定實體類型的交集界限。
-   裝載及查詢空間商務資料 – 上傳簡單的 2D 資料表，並使用幾個簡單的空間查詢來存取資料。

### <a name="batch-geocode-data"></a>批次地理編碼資料

批次地理編碼是取得大量地址或位置的程序，在單一要求中全部傳遞給服務，並將其全部以平行方式地理編碼，並在單一回應中傳回結果。

Bing 地圖服務最多可在單一批次地理編碼要求中傳遞 200,000 個地址。 這項要求會進入佇列，且通常會在一段時間內 (視資料集的大小和服務的負載而定，從幾分鐘到幾個小時) 進行處裡。 要求中的每個地址都會產生一筆交易。

Azure 地圖服務具有批次地理編碼服務，但其最多允許在單一要求中傳遞 10,000 個地址，且會根據資料集的大小和服務上的負載，處理數秒鐘到數分鐘的時間。 要求中的每個地址都會產生一筆交易。 在 Azure 地圖服務中，批次地理編碼服務僅適用於 S1 階層。

另一個使用 Azure 地圖服務地理編碼大量地址的選項，是對標準搜尋 API 進行平行要求。 這些服務針對每個要求只會接受單一地址，但可與 S0 階層搭配使用，這也會提供免費的使用量限制。 S0 階層允許每秒從單一帳戶傳送最多 50 個要求到 Azure 地圖服務平台。 因此，如果您處理限制這些以維持在該限制內，就可以一小時向上地理編碼 180,000 個地址。 S1 層對於每秒可從帳戶進行的查詢數目沒有記載的限制，因此在使用該定價層時，可以更快速地處理更多資料，但使用批次地理編碼服務有助於減少資料傳輸的總量，並可大幅減少網路流量。

-   [自由格式的地址地理編碼](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)：指定單一地址字串 (如 `"1 Microsoft way, Redmond, WA"`)，並立即處理要求。 如果您需要快速地進行個別地址的地理編碼程序，建議使用此服務。
-   [結構化的地址地理編碼](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured)：指定單一地址的各個部分 (例如，街道名稱、城市、國家/地區和郵遞區號)，然後立即處理要求。 如果您需要快速地進行個別地址的地理編碼程序，而且已將資料剖析成個別的地址部分，則建議使用此服務。
-   [批次地址地理編碼](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview)：建立最多包含 10,000 個地址的要求，並讓這些地址進行一段時間的處理。 所有地址會在伺服器上以平行方式進行地理編碼，並可於完成後下載完整的結果集。 若要對大型資料集進行地理編碼，則建議使用此服務。
-   [模糊搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)：此 API 會結合地址地理編碼與景點搜尋。 此 API 會採用自由格式的字串 (內容可以是地址、地點、地標、景點或景點類別)，並立即處理要求。 如果應用程式可供使用者從相同文字方塊中搜尋地址或景點，則建議使用此 API。
-   [模糊批次搜尋](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)：建立最多包含 10,000 個地址、地點、地標或景點的要求，並讓這些項目進行一段時間的處理。 所有資料會在伺服器上以平行方式處理，並可於完成後下載完整的結果集。

### <a name="get-administrative-boundary-data"></a>取得系統管理界限資料

在 Bing 地圖服務中，國家/地區、州、縣、市和郵遞區號的管理界限可透過 Geodata API 取得。 此 API 會取得座標或查詢進行地理編碼。 如果傳入查詢，則會進行地理編碼，並使用第一個結果的座標。 此 API 會取得座標，並擷取與座標相交的指定實體類型界限。 請注意，此 API 不一定會傳回傳入查詢的界限。 如果傳入 `"Seattle, WA"` 的查詢，但實體類型值設定為國家地區，則會傳回美國的界限。

Azure 地圖服務也會提供系統管理界限 (國家/地區、州、縣、市和郵遞區號) 的存取權。 若要擷取界限，您必須查詢所需界限的其中一個搜尋 API (亦即 `Seattle, WA`)。 如果搜尋結果具有相關聯的界限，則會在結果回應中提供幾何識別碼。 然後，您可以使用搜尋多邊形 API 來擷取一或多個幾何識別碼的確切界限。 這與 Bing 地圖服務有點不同，因為 Azure 地圖服務會傳回所搜尋專案的界限，而 Bing 地圖服務會在指定的座標中傳回指定實體類型的界限。 此外，Azure 地圖服務傳回的界限資料為 GeoJSON 格式。

總結：

1.  將您想要接收的界限查詢傳遞至下列其中一個搜尋 API。
    -   [自由格式的地址地理編碼](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
    -   [結構化的地址地理編碼](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured)
    -   [批次地址地理編碼](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview)
    -   [模糊搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
    -   [模糊批次搜尋](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)
2.  如果想要的結果具有幾何識別碼，請將其傳遞至[搜尋多邊形 API](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)。

### <a name="host-and-query-spatial-business-data"></a>裝載和查詢空間商務資料

Bing 地圖服務中的空間資料服務提供了簡單的空間資料儲存體解決方案，可用於裝載商務資料，並將其公開為空間 REST 服務。 此服務提供四個主要查詢；[依屬性尋找]、[依鄰近地區尋找]、[在周框中尋找] 和 [以 1 英里的路線尋找]。 許多使用此服務的公司，通常已將其商務資料儲存在某處的資料庫中，並已將其一小部分上傳到此服務，以支援商店定位器之類的應用程式。 由於以金鑰為基礎的驗證提供基本的安全性，因此建議您只將此服務與公開的資料搭配使用。

大部分的商務位置資料都是從資料庫中開始。 因此，建議使用現有的 Azure 儲存體解決方案，例如 Azure SQL 或 Azure PostgreSQL (搭配 PostGIS 外掛程式)。 這兩種儲存體解決方案都支援空間資料，並提供一組豐富的空間查詢功能。 一旦您的資料位於適當的儲存體解決方案中，就可以建立自訂 web 服務或使用 ASP.NET 或 Entity Framework 之類的架構，將其整合到您的應用程式中。 使用此方法可提供更多查詢功能，以及更高的安全性選項。

Azure Cosmos DB 也提供一組有限的空間功能，視您的案例而定，可能就已足夠。

以下是有關在 Azure 中裝載和查詢空間資料的一些實用資源。

-   [Azure SQL 空間資料類型概觀](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview)
-   [Azure SQL 空間 – 查詢最近的鄰近地區](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor)
-   [Azure Cosmos DB 地理空間功能概觀](https://docs.microsoft.com/azure/cosmos-db/geospatial)

## <a name="client-libraries"></a>用戶端程式庫

Azure 地圖服務針對下列程式設計語言提供了用戶端程式庫；

-   JavaScript、TypeScript、Node.js – [文件](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module) \| [NPM 套件](https://www.npmjs.com/package/azure-maps-rest)

適用於其他程式設計語言的開放原始碼用戶端程式庫；

-   .NET Standard 2.0 – [GitHub 專案](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet 套件](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="next-steps"></a>後續步驟

深入了解 Azure 地圖服務的 REST 服務。

> [!div class="nextstepaction"]
> [使用搜尋服務的最佳做法](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [使用路由服務的最佳做法](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [如何使用服務模組 (Web SDK)](how-to-use-best-practices-for-routing.md)

> [!div class="nextstepaction"]
> [Azure 地圖服務 REST 服務 API 參考文件](https://docs.microsoft.com/rest/api/maps/)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)