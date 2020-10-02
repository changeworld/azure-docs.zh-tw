---
title: 從 Google Maps 遷移 Web 服務 | Microsoft Azure 地圖服務
description: 如何將 Web 服務從 Google Maps 遷移至 Microsoft Azure 地圖服務
author: rbrundritt
ms.author: richbrun
ms.date: 08/19/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 57d71d517cb953a2a2c84b7e003fd08541416539
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319566"
---
# <a name="migrate-web-service-from-google-maps"></a>從 Google Maps 遷移 Web 服務

Azure 地圖服務和 Google Maps 都提供透過 REST Web 服務來存取空間 API 的功能。 這些平台的 API 介面會執行類似的功能。 但是，其各自使用不同的命名慣例和回應物件。

下表顯示 Azure 地圖服務的服務 API，其具有與表中所列 Google Maps 服務 API 類似的功能。

| Google Maps 服務 API | Azure 地圖服務的服務 API                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| 方向              | [路由](https://docs.microsoft.com/rest/api/maps/route)                                     |
| 距離矩陣         | [路線矩陣](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)       |
| 地理編碼               | [搜尋](https://docs.microsoft.com/rest/api/maps/search)                                   |
| 地點搜尋           | [搜尋](https://docs.microsoft.com/rest/api/maps/search)                                   |
| 地點自動完成      | [搜尋](https://docs.microsoft.com/rest/api/maps/search)                                   |
| 緊貼道路            | 請參閱[計算路線和方向](#calculate-routes-and-directions)一節。            |
| 速度限制            | 請參閱[對座標進行反向地理編碼](#reverse-geocode-a-coordinate)一節。                  |
| 靜態地圖              | [轉譯](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                       |
| 時區               | [時區](https://docs.microsoft.com/rest/api/maps/timezone)                              |

Azure 地圖服務目前無法使用下列服務 API：

- Elevation
- 地理位置
- 地點詳細資料和相片 - 電話號碼和網站 URL 可於 Azure 地圖服務的搜尋 API 中取得。
- 地圖 URL
- 最接近的道路 - 這可使用 Web SDK 達到 (如[這裡](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic
)所示)，但目前並未以服務的形式提供。
- 靜態街道檢視

Azure 地圖服務有一些可能讓您感興趣的額外 REST Web 服務：

- [空間作業](https://docs.microsoft.com/rest/api/maps/spatial)：將複雜的空間計算和作業 (例如地理柵欄) 卸載至某個服務。
- [交通](https://docs.microsoft.com/rest/api/maps/traffic)：存取即時交通流量和事件資料。

## <a name="geocoding-addresses"></a>地理編碼地址

地理編碼是將地址轉換為座標的程序。 例如，「1 Microsoft way, Redmond, WA」會轉換為「經度：-122.1298，緯度：47.64005。 然後，座標可用於不同的用途，例如，在地圖上置放標記。

Azure 地圖服務提供數種用來地理編碼地址的方法：

- [**自由格式的地址地理編碼**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)：指定單一地址字串，並立即處理要求。 舉例來說，「1 Microsoft way, Redmond, WA」便是單一地址字串。 如果您需要快速地進行個別地址的地理編碼程序，建議使用此 API。
- [**結構化的地址地理編碼**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured)：指定單一地址的各個部分 (例如，街道名稱、城市、國家/區域和郵遞區號)，然後立即處理要求。 如果您需要快速地進行個別地址的地理編碼程序，而且已將資料剖析成個別的地址部分，則建議使用此 API。
- [**批次地址地理編碼**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview)：建立最多包含 10,000 個地址的要求，並讓這些地址進行一段時間的處理。 所有地址會在伺服器上以平行方式進行地理編碼，並可於完成後下載完整的結果集。 若要對大型資料集進行地理編碼，則建議使用此做法。
- [**模糊搜尋**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)：此 API 會結合地址地理編碼與景點搜尋。 此 API 會接受自由格式的字串。 這個字串可以是地址、地點、地標、景點或景點類別。 此 API 會近乎即時地處理要求。 如果應用程式可供使用者在相同文字方塊中搜尋地址或景點，則建議使用此 API。
- [**模糊批次搜尋**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)：建立最多包含 10,000 個地址、地點、地標或景點的要求，並讓這些項目進行一段時間的處理。 所有資料會在伺服器上以平行方式處理，並可於完成後下載完整的結果集。

下表會交互參照 Google Maps API 參數與 Azure 地圖服務中類似的 API 參數。

| Google Maps 的 API 參數 | 類似的 Azure 地圖服務 API 參數  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` 和 `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` - 市/鎮<br/>`municipalitySubdivision` – 鄰近地區、副城市/超級城市<br/>`countrySubdivision` - 州或省<br/>`countrySecondarySubdivision` - 郡/縣<br/>`countryTertiarySubdivision` - 行政區<br/>`countryCode` - 兩個字母的國家/區域代碼 |
| `key`                       | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](azure-maps-authentication.md)文件。 |
| `language`                  | `language` – 請參閱[支援的語言](supported-languages.md)文件。  |
| `region`                    | `countrySet`                       |

如需如何使用搜尋服務的範例，請參閱[這裡](how-to-search-for-address.md)。 請務必檢閱[搜尋的最佳做法](how-to-use-best-practices-for-search.md)。

> [!TIP]
> 在自動完成模式中，您可以將 `&typeahead=true` 新增至要求 URL，以使用自由格式的地址地理編碼和模糊搜尋 API。 這會告訴伺服器輸入文字可能僅有部分，搜尋功能會因此進入預測模式。

## <a name="reverse-geocode-a-coordinate"></a>對座標進行反向地理編碼

反向地理編碼是將地理座標轉換為大致地址的程序。 座標若是「經度：-122.1298，緯度：47.64005」便會轉換為「1 Microsoft way, Redmond, WA」。

Azure 地圖服務提供數種反向地理編碼方法：

- [**地址反向地理編碼器**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)：指定單一地理座標，以取得與此座標對應的大致地址。 近乎即時地處理要求。
- [**十字路口反向地理編碼器**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)：指定單一地理座標以取得附近的十字路口資訊，並立即處理要求。 例如，您可能會收到下列十字路口：1st Ave 和 Main St。
- [**批次地址反向地理編碼器**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview)：建立最多包含 10,000 個座標的要求，並讓這些地址進行一段時間的處理。 所有資料都會在伺服器上平行處理。 當要求完成時，您便可以下載一組完整的結果。

下表會交互參照 Google Maps API 參數與 Azure 地圖服務中類似的 API 參數。

| Google Maps 的 API 參數   | 類似的 Azure 地圖服務 API 參數   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](azure-maps-authentication.md)文件。 |
| `language`                  | `language` – 請參閱[支援的語言](supported-languages.md)文件。  |
| `latlng`                    | `query`  |
| `location_type`             | *N/A*     |
| `result_type`               | `entityType`    |

請檢閱[搜尋的最佳做法](how-to-use-best-practices-for-search.md)。

Azure 地圖服務反向地理編碼 API 有一些無法在 Google Maps 中取得的額外功能。 當您遷移應用程式時，這些功能可能有助於與您的應用程式整合：

- 擷取速限資料
- 擷取道路使用資訊：支線、幹線、限制進入、斜坡等等
- 擷取座標位於街道哪一側

## <a name="search-for-points-of-interest"></a>搜尋景點

您可以使用地點搜尋 API，在 Google Maps 中搜尋景點資料。 此 API 提供了三種不同的方式供您搜尋景點：

- **從文字尋找地點：** 根據景點名稱、地址或電話號碼來搜尋景點。
- **鄰近搜尋**：搜尋位於某位置一定距離內的景點。
- **文字搜尋：** 使用包含景點和位置資訊的自由格式文字來搜尋地點。 例如，「紐約的披薩」或「主街附近的餐廳」。

Azure 地圖服務會提供數個景點搜尋 API：

- [**POI 搜尋**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)：依名稱搜尋景點。 例如，「星巴克」。
- [**POI 類別搜尋**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory)：依類別搜尋景點。 例如，「餐廳」。
- [**鄰近搜尋**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)：搜尋位於某位置一定距離內的景點。
- [**模糊搜尋**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)：此 API 會結合地址地理編碼與景點搜尋。 此 API 會採用自由格式的字串 (內容可以是地址、地點、地標、景點或景點類別)。 其會近乎即時地處理要求。 如果應用程式可供使用者在相同文字方塊中搜尋地址或景點，則建議使用此 API。
- [**在幾何範圍內搜尋**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)：搜尋指定幾何範圍內的景點。 例如，搜尋多邊形內的景點。
- [**沿路線搜尋**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute)：搜尋指定路線路徑沿途的景點。
- [**模糊批次搜尋**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)：建立最多包含 10,000 個地址、地點、地標或景點的要求。 對要求進行一段時間的處理。 所有資料都會在伺服器上平行處理。 當要求處理完成時，您便可以下載一組完整的結果。

Azure 地圖服務目前沒有與 Google Maps 中的文字搜尋 API 類似的 API。

> [!TIP]
> POI 搜尋、POI 類別搜尋和模糊搜尋 API 可在自動完成模式中使用，方法是將 `&typeahead=true` 新增至要求 URL。 這會告訴伺服器輸入文字可能僅有部分。API 會以預測模式進行搜尋。

請檢閱[搜尋的最佳做法](how-to-use-best-practices-for-search.md)文件。

### <a name="find-place-from-text"></a>從文字尋找地點

使用 Azure 地圖服務的 [POI 搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)和[模糊搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)，以便依名稱或地址搜尋景點。

下表會交互參照 Google Maps API 參數與類似的 Azure 地圖服務 API 參數。

| Google Maps 的 API 參數 | 類似的 Azure 地圖服務 API 參數 |
|---------------------------|-------------------------------------|
| `fields`                  | *N/A*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/A*                               |
| `key`                     | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](azure-maps-authentication.md)文件。 |
| `language`                | `language` – 請參閱[支援的語言](supported-languages.md)文件。  |
| `locationbias`            | `lat`、`lon` 及 `radius`<br/>`topLeft` 和 `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>鄰近搜尋

使用[鄰近搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) API，在 Azure 地圖服務中擷取鄰近的景點。

下表會顯示 Google Maps API 參數與類似的 Azure 地圖服務 API 參數。

| Google Maps 的 API 參數 | 類似的 Azure 地圖服務 API 參數  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](azure-maps-authentication.md)文件。 |
| `keyword`                   | `categorySet` 和 `brandSet`        |
| `language`                  | `language` – 請參閱[支援的語言](supported-languages.md)文件。  |
| `location`                  | `lat` 和 `lon`                     |
| `maxprice`                  | *N/A*                               |
| `minprice`                  | *N/A*                               |
| `name`                      | `categorySet` 和 `brandSet`        |
| `opennow`                   | *N/A*                               |
| `pagetoken`                 | `ofs` 和 `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/A*                               |
| `type`                      | `categorySet –` 請參閱[支援的搜尋類別](supported-search-categories.md)文件。   |

## <a name="calculate-routes-and-directions"></a>計算路線和方線

使用 Azure 地圖服務來計算路線和方向。 Azure 地圖服務有許多與 Google Maps 路線服務相同的功能，例如：

- 抵達時間和出發時間。
- 即時型和預測型交通路線。
- 不同運輸模式。 例如，開車、走路、騎腳踏車。

> [!NOTE]
> Azure 地圖服務要求所有導航點都必須是座標。 地址必須先進行地理編碼。

Azure 地圖服務路線服務會提供下列 API 來計算路線：

- [**計算路線**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)：計算路線並立即處理要求。 此 API 同時支援 GET 和 POST 要求。 當您指定大量導航點，或使用許多路線選項來確保 URL 要求不會太長而造成問題時，建議您使用 POST 要求。 Azure 地圖服務中的 POST 路線方向可選擇接受數千個[支援點](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints)，並使用這些支援點來重建其間的邏輯路線路徑 (緊貼道路)。 
- [**批次路線**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview)：建立最多包含 1,000 個路線要求的要求，並讓這些地址進行一段時間的處理。 所有資料會在伺服器上以平行方式處理，並可於完成後下載完整的結果集。
- [**行動服務**](https://docs.microsoft.com/rest/api/maps/mobility)：使用大眾運輸系統來計算路線和方向。

下表會交互參照 Google Maps API 參數與 Azure 地圖服務中類似的 API 參數。

| Google Maps 的 API 參數    | 類似的 Azure 地圖服務 API 參數  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` – 格式為 `"lat0,lon0:lat1,lon1…."` 的座標  |
| `key`                          | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](azure-maps-authentication.md)文件。 |
| `language`                     | `language` – 請參閱[支援的語言](supported-languages.md)文件。   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N/A* – 這是與地理編碼相關的功能。 在使用 Azure 地圖服務的地理編碼 API 時，請使用 countrySet 參數。  |
| `traffic_model`               | *N/A* – 只能指定是否應該讓交通資料與 traffic 參數搭配使用。 |
| `transit_mode`                | 請參閱[行動服務文件](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | 請參閱[行動服務文件](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *N/A* – Azure 地圖服務只會使用公制單位。  |
| `waypoints`                    | `query`                            |

> [!TIP]
> 根據預設，Azure 地圖服務的路線 API 只會傳回摘要。 其會傳回路線路徑的距離和時間以及座標。 使用 `instructionsType` 參數可擷取轉向建議指示。 使用 `routeRepresentation` 參數則可篩選出摘要和路線路徑。

Azure 地圖服務的路線規劃 API 有其他無法在 Google Maps 中取得的功能。 在遷移應用程式時，請考慮使用這些功能，您會發現這些功能很有用。

- 支援路線類型：最短、最快、trilling，以及最省油。
- 支援額外的行進模式：巴士、摩托車、計程車、卡車和貨車。
- 支援 150 個導航點。
- 在單一要求中計算多個行進時間；過往交通情況、即時交通情況、無交通情況。
- 避免額外的道路類型：共乘道路、沒有鋪柏油的道路、已使用的道路。
- 指定要避開的自訂區域。
- 限制路線可能會攀升的海拔高度。
- 根據引擎規格來規劃路線。 根據引擎規格和剩餘的燃料或電量，計算油車或電動車的路線。
- 支援商用車路線參數。 例如，車輛尺寸、重量、車軸數目和貨物類型。
- 指定最大車速。

除此之外，Azure 地圖服務中的路線規劃服務也支援[計算可規劃路線的範圍](https://docs.microsoft.com/rest/api/maps/route/getrouterange)。 計算可規劃路線的範圍也稱為等時線。 其需要產生一個多邊形，以涵蓋可從原點以任何方向行進到達的區域。 這些區域全都在指定的時間長度或是燃料量或電量之下。

請檢閱[路由的最佳做法](how-to-use-best-practices-for-routing.md)文件。

## <a name="retrieve-a-map-image"></a>擷取地圖影像

Azure 地圖服務會提供 API 來呈現已覆蓋資料的靜態地圖影像。 Azure 地圖服務中的[地圖影像呈現](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) API 相當於 Google Maps 中的靜態地圖 API。

> [!NOTE]
> Azure 地圖服務需要中心、所有標記和路徑位置都是採用「緯度,經度」格式的座標。 Google Maps 則會使用「緯度,經度」格式。 地址必須先進行地理編碼。

下表會交互參照 Google Maps API 參數與 Azure 地圖服務中類似的 API 參數。

| Google Maps 的 API 參數 | 類似的 Azure 地圖服務 API 參數  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` – 指定為 URL 路徑的一部分。 目前僅支援 PNG。 |
| `key`                       | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](azure-maps-authentication.md)文件。 |
| `language`                  | `language` – 請參閱[支援的語言](supported-languages.md)文件。  |
| `maptype`                   | `layer` 和 `style` – 請參閱[支援的地圖樣式](supported-map-styles.md)文件。 |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N/A* – 這是與地理編碼有關的功能。 在使用 Azure 地圖服務的地理編碼 API 時，請使用 `countrySet` 參數。  |
| `scale`                     | *N/A*                              |
| `size`                      | `width` 和 `height` – 最高可達 8192x8192 大小。 |
| `style`                     | *N/A*                              |
| `visible`                   | *N/A*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> 在 Azure 地圖服務的地圖底圖系統中，其地圖底圖是 Google Maps 中所用地圖底圖大小的兩倍。 因此，Azure 地圖服務中的縮放層級值會比 Google Maps 中的更近一個縮放層級。 為了補償這一差距，請在您要遷移的要求中減少縮放層級。

如需詳細資訊，請參閱[關於地圖影像呈現 API 的操作指南](how-to-render-custom-data.md)。

除了能夠產生靜態地圖影像之外，Azure 地圖服務的呈現服務也能讓您直接存取點陣 (PNG) 和向量格式的地圖底圖：

- [**地圖底圖**](https://docs.microsoft.com/rest/api/maps/render/getmaptile)：擷取基底地圖 (道路、邊界、背景) 的點陣 (PNG) 和向量地圖底圖。
- [**地圖影像地圖底圖**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)：擷取空照圖影像和衛星影像的地圖底圖。

> [!TIP]
> 幾年前有許多 Google Maps 應用程式從互動式地圖體驗切換成靜態地圖影像。 這麼做是為了節省成本。 在 Azure 地圖服務中，使用 Web SDK 中的互動式地圖控制項通常更符合成本效益。 互動式地圖控制項會根據地圖底圖的載入數量來收費。 Azure 地圖服務中的地圖底圖很大。 通常只需要幾個地圖底圖就能重新建立與靜態地圖相同的地圖檢視。 瀏覽器會自動快取地圖底圖。 因此，互動式地圖控制項在重現靜態地圖檢視時，往往會產生一小部分的交易。 平移和縮放會載入更多地圖底圖，不過地圖控制項中有選項可供停用此行為。 互動式地圖控制項也會提供比靜態地圖服務多更多的視覺效果選項。

### <a name="marker-url-parameter-format-comparison"></a>標記 URL 參數格式比較

**之前：Google 地圖**

在 URL 中使用 `markers` 參數來新增標記。 `markers` 參數會取得樣式，以及要在地圖上以該樣式呈現的位置清單，如下所示：

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

若要新增其他樣式，請使用 URL 的 `markers` 參數並搭配不同樣式和位置集。

使用「緯度,經度」格式來指定標記位置。

使用 `optionName:value` 格式來新增標記樣式，如有多個樣式，則以縱線字元 (\|) 來分隔，例如 "optionName1:value1\|optionName2:value2"。 請注意，選項名稱和值則以冒號 (:) 分隔。 在 Google Maps 中使用下列樣式選項名稱來設定標記樣式：

- `color` – 預設標記圖示的色彩。 可以是 24 位元的十六進位色彩 (`0xrrggbb`) 或下列其中一個值；`black`、`brown`、`green`、`purple`、`yellow`、`blue`、`gray`、`orange`、`red`、`white`。
- `label` – 要顯示在圖示頂端的單一大寫英數位元。
- `size` - 標記的大小。 可以是 `tiny`、`mid` 或 `small`。

針對 Google Maps 中的自訂圖示，請使用下列樣式選項名稱：

- `anchor` – 指定如何將圖示影像對齊座標。 可以是像素 (x,y) 值或下列其中一個值；`top`、`bottom`、`left`、`right`、`center`、`topleft`、`topright`、`bottomleft` 或 `bottomright`。
- `icon` – 指向圖示影像的 URL。

例如，讓我們將中等大小的紅色標記新增至地圖，座標為「經度：-110，緯度：45」：

```
&markers=color:red|size:mid|45,-110
```


![Google 地圖標記](media/migrate-google-maps-web-services/google-maps-marker.png)

**之後：Azure 地圖服務**

藉由在 URL 中指定 `pins` 參數，將標記新增至靜態地圖影像。 如同 Google Maps，請在參數中指定樣式和位置清單。 您可以指定 `pins` 參數多次，以支援具有不同樣式的標記。

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

若要使用其他樣式，請新增 URL 的其他 `pins` 參數並搭配不同樣式和位置集。

在 Azure 地圖服務中，圖釘位置必須採用「經度 緯度」格式。 Google Maps 則會使用「緯度,經度」格式。 空格 (而不是逗號) 會分隔 Azure 地圖服務格式中的經度和緯度。

`iconType` 會指定要建立的圖釘類型。 它具有下列值：

- `default` – 預設的圖釘圖示。
- `none` – 不會顯示任何圖示，只會呈現標籤。
- `custom` - 指定要使用自訂圖示。 指向圖示影像的 URL 可在圖釘位置資訊後面新增至 `pins` 參數的結尾。
- `{udid}` – 儲存在 Azure 地圖服務資料儲存體平台中的圖示所具有的唯一資料識別碼 (UDID)。

新增採用 `optionNameValue` 格式的圖釘樣式。 以縱線 (\|) 字元分隔多個樣式。 例如： `iconType|optionName1Value1|optionName2Value2` 。 選項名稱和值未隔開。 使用下列樣式選項名稱來設定標記樣式：

- `al` – 指定標記的不透明度 (Alpha)。 選取 0 到 1 之間的數字。
- `an` – 指定圖釘錨點。 使用 "x y" 格式指定 X 和 y 像素值。
- `co` – 圖釘的色彩。 指定 24 位元的十六進位色彩：`000000` 到 `FFFFFF`。
- `la` – 指定標籤錨點。 使用 "x y" 格式指定 X 和 y 像素值。
- `lc` – 標籤的色彩。 指定 24 位元的十六進位色彩：`000000` 到 `FFFFFF`。
- `ls` – 標籤的大小 (以像素為單位)。 選擇大於 0 的數字。
- `ro` – 用來旋轉圖示的值 (以度為單位)。 選取 -360 到 360 之間的數字。
- `sc` – 圖釘圖示的縮放值。 選擇大於 0 的數字。

為每個圖釘位置指定標籤值。 這個方法比將單一標籤值套用至位置清單中的所有標記更有效率。 標籤值可以是有多個字元的字串。 請使用單引號括住字串，以確保系統不會將其誤認為樣式或位置值。

讓我們新增紅色 (`FF0000`) 預設圖示，使其具有「太空針塔」標籤，並位於下列位置 (15 50)。 該圖示位於「經度：-122.349300，緯度：47.620180」：

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```


![Azure 地圖服務的標記](media/migrate-google-maps-web-services/azure-maps-marker.png)

新增具有標籤值 '1'、'2' 和 '3' 的三個圖釘：

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```



![Azure 地圖服務的多個標記](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)

### <a name="path-url-parameter-format-comparison"></a>路徑 URL 參數格式比較

**之前：Google 地圖**

在 URL 中使用 `path` 參數，將線條和多邊形新增至靜態地圖影像。 `path` 參數會取得樣式，以及要在地圖上呈現的位置清單，如下所示：

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

藉由新增 URL 的其他 `path` 參數並搭配不同樣式和位置集，來使用其他樣式。

路徑位置會以 `latitude1,longitude1|latitude2,longitude2|…` 格式來指定。 路徑可加以編碼，或包含位置點的地址。

使用 `optionName:value` 格式新增路徑樣式，並以縱線 (\|) 字元分隔多個樣式。 然後，使用冒號 (:) 來分隔選項名稱和值。 如下所示：`optionName1:value1|optionName2:value2`。 在 Google Maps 中，下列樣式選項名稱可用來設定路徑樣式：

- `color` – 路徑或多邊形外框的色彩。 可以是 24 位元的十六進位色彩 (`0xrrggbb`)、32 位元的十六進位色彩 (`0xrrggbbbaa`) 或下列其中一個值：黑色、棕色、綠色、紫色、黃色、藍色、灰色、橘色、紅色、白色。
- `fillColor` – 用來填滿路徑區域的色彩 (多邊形)。 可以是 24 位元的十六進位色彩 (`0xrrggbb`)、32 位元的十六進位色彩 (`0xrrggbbbaa`) 或下列其中一個值：黑色、棕色、綠色、紫色、黃色、藍色、灰色、橘色、紅色、白色。
- `geodesic` – 指出路徑是否應該是遵循地球曲率的線條。
- `weight` – 路徑線條的粗細 (以像素為單位)。

在 URL 參數中，將紅線不透明度和像素粗細新增至地圖的座標之間。 在以下範例中，線條具有 50% 不透明度和四個像素的粗細。 座標為「經度：-110，緯度：45) 和 (經度：-100, 緯度：第 50 號的 IP 通訊協定。

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

![Google 地圖的聚合線條](media/migrate-google-maps-web-services/google-maps-polyline.png)

**之後：Azure 地圖服務**

藉由在 URL 中指定 `path` 參數，將線條和多邊形新增至靜態地圖影像。 如同 Google Maps，請在此參數中指定樣式和位置清單。 指定 `path` 參數多次以呈現多個具有不同樣式的圓形、線條和多邊形。

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

至於路徑位置，Azure 地圖服務會要求座標採用「經度 緯度」格式。 Google Maps 則會使用「緯度,經度」格式。 空格 (而不是逗號) 會分隔 Azure 地圖服務格式中的經度和緯度。 Azure 地圖服務不支援對位置點使用經過編碼的路徑或地址。 請將較大的資料集透過 GeoJSON 檔案的形式上傳至 Azure 地圖服務的資料儲存體 API 中，如[這裡](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)所述。

新增採用 `optionNameValue` 格式的路徑樣式。 使用縱線 (\|) 字元分隔多個樣式，如下所示：`optionName1Value1|optionName2Value2`。 選項名稱和值未隔開。 在 Azure 地圖服務中使用下列樣式選項名稱來設定路徑樣式：

- `fa` - 在呈現多邊形時所使用的填滿色彩不透明度 (Alpha)。 選取 0 到 1 之間的數字。
- `fc` - 用來呈現多邊形區域的填滿色彩。
- `la` – 在繪製線條和多邊形外框時所使用的線條色彩不透明度 (Alpha)。 選取 0 到 1 之間的數字。
- `lc` – 用來呈現線條和多邊形外框的線條色彩。
- `lw` – 線條的寬度 (以像素為單位)。
- `ra` – 指定圓形半徑 (以公尺為單位)。

在 URL 參數中，於座標之間新增紅線不透明度和像素粗細。 在以下範例中，線條具有 50% 不透明度和四個像素的粗細。 座標具有下列值：「經度：-110，緯度：45) 和 (經度：-100, 緯度：第 50 號的 IP 通訊協定。

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

![Azure 地圖服務的聚合線條](media/migrate-google-maps-web-services/azure-maps-polyline.png)

## <a name="calculate-a-distance-matrix"></a>計算距離矩陣

Azure 地圖服務提供距離矩陣 API。 請使用此 API 透過距離矩陣來計算一組位置之間的行進時間和距離。 此 API 相當於 Google Maps 中的距離矩陣 API。

- [**路線矩陣**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)：以非同步方式計算一組起點和終點的行進時間和距離。 每個要求最多可支援 700 個資料格。 此數目就是起點數目與終點數目的乘積。 在考慮到該條件約束的情況下，可能的矩陣維度範例如下：700x1、50x10、10x10、28x25、10x70。

> [!NOTE]
> 針對距離矩陣 API 的要求只能使用 POST 要求來提出，而且要在要求主體中附上起點和終點資訊。 此外，Azure 地圖服務會要求所有起點和終點都必須是座標。 地址必須先進行地理編碼。

下表會交互參照 Google Maps API 參數與 Azure 地圖服務中類似的 API 參數。

| Google Maps 的 API 參數      | 類似的 Azure 地圖服務 API 參數  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` – 在 POST 要求主體中指定為 GeoJSON。 |
| `key`                          | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](azure-maps-authentication.md)文件。 |
| `language`                     | `language` – 請參閱[支援的語言](supported-languages.md)文件。  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` – 在 POST 要求主體中指定為 GeoJSON。  |
| `region`                       | *N/A* – 這是與地理編碼相關的功能。 在使用 Azure 地圖服務的地理編碼 API 時，請使用 `countrySet` 參數。 |
| `traffic_model`                | *N/A* – 只能指定是否應該讓交通資料與 `traffic` 參數搭配使用。 |
| `transit_mode`                 | *N/A* - 目前不支援以運輸系統為基礎的距離矩陣。  |
| `transit_routing_preference`   | *N/A* - 目前不支援以運輸系統為基礎的距離矩陣。  |
| `units`                        | *N/A* – Azure 地圖服務只會使用公制單位。 |

> [!TIP]
> 所有可在 Azure 地圖服務路線規劃 API 中取得的進階路線選項，均可在 Azure 地圖服務的距離矩陣 API 中獲得支援。 進階路線選項包括：卡車路線規劃、引擎規格等等。

請檢閱[路由的最佳做法](how-to-use-best-practices-for-routing.md)文件。

## <a name="get-a-time-zone"></a>取得時區

Azure 地圖服務提供了 API 供您擷取座標的時區。 Azure 地圖服務的時區 API 類似於 Google Maps 中的時區 API：

- [**時區 (依座標)** ](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates)：指定座標並接收座標的時區詳細資料。

下表會交互參照 Google Maps API 參數與 Azure 地圖服務中類似的 API 參數。

| Google Maps 的 API 參數 | 類似的 Azure 地圖服務 API 參數   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](azure-maps-authentication.md)文件。       |
| `language`                  | `language` – 請參閱[支援的語言](supported-languages.md)文件。    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

除了這個 API 之外，Azure 地圖服務還提供一些時區 API。 這些 API 會根據時區的名稱或識別碼來轉換時間：

- [**時區 (依識別碼)** ](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid)：針對指定的 IANA 時區識別碼，傳回目前、以往和未來的時區資訊。
- [**時區列舉 IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana)：傳回 IANA 時區識別碼的完整清單。 IANA 服務的更新會在一天內就反映在系統中。
- [**時區列舉 Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows)：傳回 Windows 時區識別碼的完整清單。
- [**時區 IANA 版本**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion)：傳回 Azure 地圖服務所用的目前 IANA 版本號碼。
- [**Windows 轉 IANA 時區**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana)：在給定有效 Windows 時區識別碼的情況下，傳回對應的 IANA 識別碼。 單一 Windows 識別碼可能會傳回多個 IANA 識別碼。

## <a name="client-libraries"></a>用戶端程式庫

Azure 地圖服務針對下列程式設計語言提供了用戶端程式庫：

- JavaScript、TypeScript、Node.js – [文件](how-to-use-services-module.md) \| [NPM 套件](https://www.npmjs.com/package/azure-maps-rest)

這些開放原始碼用戶端程式庫適用於其他程式設計語言：

- .NET Standard 2.0 – [GitHub 專案](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet 套件](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>其他資源

以下是適用於 Azure 地圖服務 REST 服務的額外文件和資源。

- [搜尋的最佳做法](how-to-use-best-practices-for-search.md)
- [搜尋地址](how-to-search-for-address.md)
- [路由的最佳做法](how-to-use-best-practices-for-routing.md)
- [Azure 地圖服務 REST 服務 API 參考文件](https://docs.microsoft.com/rest/api/maps/)
- [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)
- [如何使用服務模組 (Web SDK)](how-to-use-best-practices-for-routing.md)