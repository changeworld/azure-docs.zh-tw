---
title: Microsoft Azure 地圖服務的概觀
description: 了解 Microsoft Azure 地圖服務中的服務和功能，以及如何在您的應用程式中加以使用。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/31/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, references_regions
ms.openlocfilehash: 6933321ad402104fa5529af2b7fdf38e329e0168
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830910"
---
# <a name="what-is-azure-maps"></a>什麼是 Azure 地圖服務？

Azure 地圖服務是地理空間服務和 SDK 的集合，採用最新的地圖資料為 Web 和行動裝置應用程式提供地理內容。 Azure 地圖服務提供以下功能與服務：

* 用於以多種樣式和衛星影像呈現向量和點陣地圖的 REST API。
* 根據私人的室內地圖資料來建立和呈現地圖的建立者服務。
* 尋找全世界地址、地點及熱門景點的搜尋服務。
* 各種路線規劃選項：例如點對點、多點、多點最佳化、等時線、電動車、商用車輛、交通影響，以及矩陣路線規劃。
* 交通流量檢視和事件檢視，適用於需要即時交通資訊的應用程式。
* 用來要求大眾運輸資訊的行動服務，藉由混合不同的行進模式和即時抵達來規劃路線。
* 時區和地理位置服務。
* 建立地理圍欄服務及儲存地圖資料，位置資訊則由 Azure 代管。
* 能夠分析地理空間的高智慧定位能力。

此外，也可以透過 Web SDK 和 Android SDK 來使用 Azure 地圖服務。 這些工具可協助讓開發人員快速開發和調整解決方案，以將位置資訊整合到 Azure 解決方案中。

您可以註冊免費的 [Azure 地圖服務帳戶](https://azure.microsoft.com/services/azure-maps/)，開始進行開發！

以下影片會深入說明 Azure 地圖服務：

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>地圖控制項

### <a name="web-sdk"></a>Web SDK

Azure 地圖服務 Web SDK 可讓您以自己的內容和圖像自訂互動式地圖。 您可以針對 Web 或行動應用程式使用此互動式地圖。 此地圖控制項使用 WebGL，因此您能夠以高效能轉譯大型資料集。 您可以使用 JavaScript 或 TypeScript 搭配 SDK 進行開發。

:::image type="content" source="./media/about-azure-maps/intro_web_map_control.png" alt-text="使用 Azure 地圖服務 Web SDK 所建立的人口變化範例地圖":::

### <a name="android-sdk"></a>Android SDK

Azure 地圖服務 Android SDK 可讓您建立功能強大的行動地圖應用程式。

:::image type="content" source="./media/about-azure-maps/android_sdk.png" border="false" alt-text="行動裝置上的地圖範例":::

## <a name="services-in-azure-maps"></a>Azure 地圖服務中的服務

Azure 地圖服務由下列服務組成，可以為 Azure 應用程式提供地理內容。

### <a name="data-service"></a>資料服務

對地圖而言，資料是不可或缺的。 請使用資料服務上傳及儲存地理空間資料供空間作業或影像構圖使用。  讓客戶資料更貼近 Azure 地圖服務可減少您應用程式的延遲、提高應用程式生產力，以及在您的應用程式中建立全新的案例。 如需這項服務的詳細資訊，請瀏覽[資料服務文件](https://docs.microsoft.com/rest/api/maps/data) \(英文\)。

### <a name="geolocation-service"></a>地理位置服務

可使用地理位置服務預覽從 IP 位址擷取的兩個字母國碼/區域碼。 此服務可協助您藉由根據地理位置提供自訂應用程式內容，來加強使用者體驗。

如需詳細資訊，請參閱[地理位置服務文件](https://docs.microsoft.com/rest/api/maps/geolocation)。

### <a name="mobility-service"></a>行動服務

Azure 地圖服務行動服務可加速開發出具有大眾運輸功能的應用程式，例如運輸路線和搜尋附近的大眾運輸站點。 使用者可以擷取有關運輸站點、路線和時刻表的詳細資訊。 行動服務也可讓使用者擷取站點和路線幾何圖形、站點提醒、路線和服務區域，以及即時的大眾運輸抵達和服務提醒。 此外，行動服務也會提供具有多模式行程規劃選項的路線功能。 多模式行程規劃將步行、騎腳踏車和大眾運輸選項全都納入到一個行程中。 使用者也可以存取詳細的多模式逐步路線。

若要深入了解該服務，請參閱[行動服務文件](https://docs.microsoft.com/rest/api/maps/mobility) \(英文\)。

### <a name="render-service"></a>轉譯服務

現在為預覽狀態的[轉譯服務 V2](https://docs.microsoft.com/est/api/maps/renderv2) 引進了新版本的[取得地圖底圖 V2 API](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview)。 取得地圖底圖 V2 API 現在允許客戶要求 Azure 地圖服務的道路底圖、氣象底圖，或使用 Azure 地圖服務建立工具建立的地圖底圖。 建議您使用新的取得地圖底圖 V2 API。  

:::image type="content" source="./media/about-azure-maps/intro_map.png" border="false" alt-text="從轉譯服務 V2 建立的地圖範例":::

如需詳細資訊，請參閱[轉譯服務 V2 文件](https://docs.microsoft.com/rest/api/maps/renderv2) \(英文\)。

若要深入了解 GA (正式發行) 中的轉譯服務 V1，請參閱[轉譯服務 V1 文件](https://docs.microsoft.com/rest/api/maps/render)。  

### <a name="route-service"></a>路線規劃服務

路線規劃服務可以用來計算每個要求路線的預估抵達時間 (ETA)。 路線 API 會考慮一些因素，例如即時交通資訊和歷史交通資料，例如在所要求的那天 (星期幾) 某個時間的一般車速。 API 會根據時間或距離，以序列或最佳化順序，一次傳回多個目的地可行的最短或最快路線。 該服務可讓開發人員跨數種行進模式 (例如汽車、貨車、自行車或步行和電動車) 計算方向。 該服務也會考量輸入的資訊，例如起飛時間、重量限制或危險材料運輸。

:::image type="content" source="./media/about-azure-maps/intro_route.png" border="false" alt-text="從路線規劃服務建立的地圖範例":::

路線規劃服務會提供一組進階功能，例如：

* 以批次處理方式處理多個路徑規劃要求。
* 包含一組出發地與目的地之間旅行時間和距離資訊的矩陣。
* 尋找路徑或距離資訊可讓使用者了解所需的旅行時間與燃料需求，據以做好萬全準備。

如需路線規劃功能的詳細資料，請參閱[路線規劃服務文件](https://docs.microsoft.com/rest/api/maps/route) \(英文\)。

### <a name="search-service"></a>搜尋服務

搜尋服務可協助開發人員搜尋地址、位置、依名稱或類別列出的企業清單，以及其他地理資訊。 此外，服務也可根據經度和緯度進行地址與交叉路口的[反向地理編碼](https://en.wikipedia.org/wiki/Reverse_geocoding) \(英文\)。

:::image type="content" source="./media/about-azure-maps/intro_search.png" border="false" alt-text="在地圖上搜尋的範例":::

搜尋服務也提供進階功能，例如：

* 沿路線搜尋。
* 搜尋更廣大區域的內部。
* 以批次處理方式處理一組搜尋要求。
* 依品牌名稱搜尋電動車充電站和景點 (POI) 資料。

如需搜尋功能的詳細資訊，請參閱[搜尋服務文件](https://docs.microsoft.com/rest/api/maps/search) \(英文\)。

### <a name="spatial-service"></a>空間服務

空間服務會快速分析位置資訊，以便通知客戶有關某個時間和空間發生的進行中事件。 藉此達到幾近即時的事件分析和預測性模型化。

服務可讓客戶運用內含常見地理空間數學計算的程式庫，提升他們的智慧定位能力。 一般計算包括最接近點、大圓距離及環域。 若要深入了解服務以及各種功能，請參閱[空間服務文件](https://docs.microsoft.com/rest/api/maps/spatial)。

### <a name="timezone-service"></a>時區服務

時區服務可讓您查詢目前、過去及未來的時區資訊。 您可以使用經緯度組合或 [IANA 識別碼](https://www.iana.org/)來作為輸入。 時區服務也具備下列能力：

* 將 Microsoft Windows 時區識別碼轉換為 IANA 時區。
* 擷取 UTC 時區時差。
* 取得某一選定時區目前的時間。

時區服務查詢的典型 JSON 回應如以下範例所示：

```JSON
{
  "Version": "2020a",
  "ReferenceUtcTimestamp": "2020-07-31T19:15:14.4570053Z",
  "TimeZones": [
    {
      "Id": "America/Los_Angeles",
      "Names": {
        "ISO6391LanguageCode": "en",
        "Generic": "Pacific Time",
        "Standard": "Pacific Standard Time",
        "Daylight": "Pacific Daylight Time"
      },
      "ReferenceTime": {
        "Tag": "PDT",
        "StandardOffset": "-08:00:00",
        "DaylightSavings": "01:00:00",
        "WallTime": "2020-07-31T12:15:14.4570053-07:00",
        "PosixTzValidYear": 2020,
        "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
      }
    }
  ]
}
```

如需這項服務的詳細資訊，請閱讀[時區服務文件](https://docs.microsoft.com/rest/api/maps/timezone) \(英文\)。

### <a name="traffic-service"></a>交通服務

交通服務是一套 Web 服務，可讓開發人員用於需要交通資訊的 Web 或行動應用程式。 服務可提供兩種資料類型：

* 交通流量：針對路網中的所有重要道路提供即時觀察速度和行進時間。
* 交通事故：提供路網中交通阻塞和交通事故的最新檢視。

![含路況資訊的地圖範例](media/about-azure-maps/intro_traffic.png)

如需詳細資訊，請參閱[交通服務文件](https://docs.microsoft.com/rest/api/maps/traffic) \(英文\)。

### <a name="weather-service"></a>天氣服務

氣象服務所提供的 API 可讓開發人員用來擷取特定位置的氣象資訊。 此資訊包含各種詳細資料，例如觀測日期和時間、天氣狀況的簡短說明、氣象圖示、降雨機率指示器旗標、溫度和風速資訊。 此外，也會傳回其他詳細資料，例如 RealFeel™ 溫度和紫外線指數。

開發人員可以使用[取得沿路氣象資訊 API](https://docs.microsoft.com/rest/api/maps/weather/getweatheralongroutepreview) 來擷取特定路線的沿路氣象資訊。 此外，此服務也支援產生受天氣災害 (例如洪水或豪雨) 影響的導航點氣象通知。

[取得地圖底圖 V2 API](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) 可讓您要求過去、現在和未來的雷達底圖和衛星底圖。

![具有即時氣象雷達底圖的地圖範例](media/about-azure-maps/intro_weather.png)

### <a name="maps-creator-service"></a>地圖建立者服務

地圖建立者服務是一套 Web 服務，可讓開發人員根據室內地圖資料來建立具有地圖功能的應用程式。

地圖建立者會提供三項核心服務：

* [資料集服務](https://docs.microsoft.com/rest/api/maps/dataset)。 使用資料集服務，從已轉換的繪圖套件資料建立資料集。 如需有關繪圖套件需求的詳細資訊，請參閱繪圖套件需求。

* [轉換服務](https://docs.microsoft.com/rest/api/maps/dataset)。 使用轉換服務可將 DWG 設計檔案轉換成適用於室內地圖的繪圖套件資料。

* [地圖底圖集服務](https://docs.microsoft.com/rest/api/maps/tileset)。 使用地圖底圖集服務來建立資料集的向量型標記法。 應用程式可以使用地圖底圖集，呈現以視覺效果地圖底圖為基礎的資料集檢視。

* [功能狀態服務](https://docs.microsoft.com/rest/api/maps/featurestate)。 使用功能狀態服務來支援動態地圖樣式。 動態地圖樣式可讓應用程式反映 IoT 系統所提供空間的即時事件。

* [WFS 服務](https://docs.microsoft.com/rest/api/maps/featurestate)。 使用 WFS 服務來查詢您的室內地圖資料。 WFS 服務會遵循[開放地理空間協會 API](http://docs.opengeospatial.org/is/17-069r3/17-069r3.html) 標準來查詢單一資料集。

## <a name="programming-model"></a>程式設計模型

Azure 地圖服務是針對行動性而建置，可以協助您開發跨平台應用程式。 它使用與語言無關的程式設計模型，並且透過 [REST API](https://docs.microsoft.com/rest/api/maps/) 支援 JSON 輸出。

此外，Azure 地圖服務也使用簡單的程式設計模型提供方便的 [JavaScript 地圖控制項](https://docs.microsoft.com/javascript/api/azure-maps-control)。 Web 和行動應用程式都可輕易且快速地進行開發。

## <a name="power-bi-visual"></a>Power BI 視覺效果

Power BI 的 Azure 地圖服務視覺效果會針對地圖頂端的空間資料提供一組豐富的資料視覺效果。 估計超過 80% 的商務資料有位置內容。 Azure 地圖服務視覺效果提供無程式碼的解決方案，可深入了解此位置內容與您的商務資料之間有何關聯和影響。

:::image type="content" source="./media/about-azure-maps/intro-power-bi.png" border="false" alt-text="Power BI 桌面和顯示商務資料的 Azure 地圖服務視覺效果":::

如需詳細資訊，請參閱 [Azure 地圖服務 Power BI 視覺效果](power-bi-visual-getting-started.md)文件的使用者入門。

## <a name="usage"></a>使用量

若要存取 Azure 地圖服務，請移至 [Azure 入口網站](https://portal.azure.com)並建立 Azure 地圖服務帳戶。

Azure 地圖服務會使用金鑰型驗證結構描述。 建立帳戶時會產生兩個金鑰。 若要針對 Azure 地圖服務來進行驗證，則可以使用任一金鑰。

注意 - Azure 地圖服務會與第三方 TomTom 分享客戶提供的位址/位置查詢 (稱為「查詢」)，以用於對應功能。 與 TomTom 分享時，查詢不會連結至任何客戶或終端使用者，也不能用來識別個人。 包含與 Moovit 和 AccuWeather 整合的行動性和天氣服務，目前處於[預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)狀態。

Microsoft 目前正在將 TomTom、Moovit 和 AccuWeather 新增至線上服務轉包商清單。

## <a name="supported-regions"></a>支援區域

Azure 地圖服務目前已推出，但下列國家/地區除外：

* 中國
* 南韓

請確認您目前 IP 位址的位置處於支援的國家/區域中。

## <a name="next-steps"></a>後續步驟

請試用一下展示 Azure 地圖服務的範例應用程式：

> [!div class="nextstepaction"]
> [快速入門：建立 Web 應用程式](quick-demo-map-app.md)

掌握 Azure 地圖服務的最新動態：

> [!div class="nextstepaction"]
> [Azure 地圖服務部落格](https://azure.microsoft.com/blog/topics/azure-maps/)
