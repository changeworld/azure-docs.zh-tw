---
title: 教學課程：從 Bing 地圖服務遷移 Web 應用程式 | Microsoft Azure 地圖服務
description: 如何從 Bing 地圖服務 將 Web 應用程式遷移至 Microsoft Azure 地圖服務的教學課程。
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: be0b2a3a15c77ae0de303f02be078f115b283eb9
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897138"
---
# <a name="tutorial---migrate-a-web-app-from-bing-maps"></a>教學課程 - 從 Bing 地圖服務遷移 Web 應用程式

使用 Bing 地圖服務的 Web 應用程式通常會使用 Bing 地圖服務 V8 JavaScript SDK。 Azure 地圖服務 Web SDK 是適合作為遷移目的地的 Azure 型 SDK。 Azure 地圖服務 Web SDK 可讓您以自己的內容和圖像，自訂顯示在 Web 或行動應用程式中的互動式地圖。 此控制項使用 WebGL，可讓您以高效能轉譯大型資料集。 您可以使用 JavaScript 或 TypeScript 以此 SDK 進行開發。

如果要遷移現有的 Web 應用程式，請檢查其是否使用開放原始碼地圖控制項程式庫，例如 Cesium、Leaflet 和 OpenLayers。 如果是，而且您想要繼續使用該程式庫，可以將其連線到 Azure 地圖服務地圖底圖服務 ([道路地圖底圖](/rest/api/maps/render/getmaptile) \| [衛星地圖底圖](/rest/api/maps/render/getmapimagerytile))。 以下連結提供詳細說明如何在某些常用的開放原始碼地圖控制項程式庫中使用 Azure 地圖服務。

-   Cesium - 適用於 Web 的3D 地圖控制項。 [程式碼範例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [文件](https://cesiumjs.org/)
-   Leaflet – 適用於 Web 的輕量型 2D 地圖控制項。 [程式碼範例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [文件](https://leafletjs.com/)
-   OpenLayers - 適用於 Web 且支援投影的 2D 地圖控制項。 [程式碼範例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [文件](https://openlayers.org/)

如果使用 JavaScript 架構進行開發，下列其中一個開放原始碼專案可能會很有用：

- [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps) - Azure 地圖服務的 Angular 10 包裝函式。
- [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) - Azure 地圖服務 Blazor 元件。
- [Azure 地圖服務 React 元件](https://github.com/WiredSolutions/react-azure-maps) - Azure 地圖服務控制項的反應包裝函式。
- [Vue Azure 地圖服務](https://github.com/rickyruiz/vue-azure-maps) - Vue 應用程式的 Azure 地圖服務元件。

## <a name="key-features-support"></a>主要功能支援

下表列出 Bing 地圖服務 V8 JavaScript SDK 中的主要 API 功能，以及類似 API 在 Azure 地圖服務 Web SDK 中的支援。

| Bing 地圖服務功能        | Azure 地圖服務 Web SDK 支援                                                             |
|--------------------------|:--------------------------------------------------------------------------------------:|
| 圖釘                 | ✓                                                                                      |
| 圖釘叢集       | ✓                                                                                      |
| 聚合線條和多邊形     | ✓                                                                                      |
| 地面覆蓋          | ✓                                                                                      |
| 熱度圖                | ✓                                                                                      |
| 地圖底圖圖層              | ✓                                                                                      |
| KML 圖層                | ✓                                                                                      |
| 等高線圖層            | [範例](https://azuremapscodesamples.azurewebsites.net/?search=contour)              |
| 資料收納圖層       | [範例](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)       |
| 動畫地圖底圖圖層      | 包含在開放原始碼 Azure 地圖服務[動畫模組](https://github.com/Azure-Samples/azure-maps-animations)中 |
| 繪圖工具            | ✓                                                                                      |
| Geocoder 服務         | ✓                                                                                      |
| 路線服務       | ✓                                                                                      |
| 距離矩陣服務  | ✓                                                                                      |
| 空間資料服務     | N/A                                                                                    |
| 衛星/空照圖影像 | ✓                                                                                      |
| 鳥瞰影像         | 已規劃                                                                                |
| 街邊影像       | 已規劃                                                                                |
| GeoJSON 支援          | ✓                                                                                      |
| GeoXML 支援           | ✓                                                                                      |
| 已知文字支援  | ✓                                                                                      |
| 自訂地圖樣式        | Partial                                                                                |

Azure 地圖服務也有許多[適用於 web SDK 的其他開放原始碼模組](open-source-projects.md#open-web-sdk-modules)，可延伸其功能。

## <a name="notable-differences-in-the-web-sdks"></a>這兩種 Web SDK 的顯著差異

以下是 Bing 地圖服務與 Azure 地圖服務 Web SDK 之間需要注意的一些主要差異：

-   除了提供主控端點以供存取 Azure 地圖服務 Web SDK 外，如果您想要的話，也可使用 NPM 套件來將 Web SDK 內嵌至應用程式。 如需詳細資訊，請參閱[文件](./how-to-use-map-control.md)。 此套件也包含 TypeScript 定義。
-   Bing 地圖服務提供兩個其 SDK 的主控分支；「發行」和「實驗性」。 「實驗性」分支在進行新的開發時，可能會一天收到多個更新。 Azure 地圖服務只會主控發行分支，不過，實驗性功能會建立為開放原始碼 Azure 地圖服務程式碼範例專案中的自訂模組。 Bing 地圖服務也用來擁有已凍結的分支，但較不頻繁地更新，因而降低因發行而中斷變更的風險。 在 Azure 地圖服務中，您可以使用 NPM 模組並指向任何先前的次要版本發行。

> [!TIP]
> Azure 地圖服務會同時發行 SDK 的縮製和解除縮製版本。 簡單移除檔案名稱中的 `.min`。 解除縮製版本在偵錯問題時很有用，但請務必使用生產環境中的縮製版本以利用較小的檔案大小。

-   在 Azure 地圖服務中建立 Map 類別的執行個體之後，您的程式碼應該先等待地圖的 `ready` 或 `load` 事件引發，然後再與地圖互動。 這些事件會確保所有地圖資源都已載入，並可供存取。
-   這兩種平台都會針對基底地圖使用類似的地圖底圖系統，不過，Bing 地圖服務中的地圖底圖維度是 256 像素，而 Azure 地圖服務中的地圖底圖維度則是 512 像素。 因此，若要在 Azure 地圖服務中獲得和 Bing 地圖服務相同的地圖檢視，則 Bing 地圖服務中所使用的縮放層級，在 Azure 地圖服務中就必須減一。
-   Bing 地圖服務中的座標是 `latitude, longitude`，而 Azure 地圖服務是使用 `longitude, latitude`。 此格式與大多數 GIS 平台所遵循的標準 `[x, y]` 相符。

-   Azure 地圖服務 Web SDK 中的圖形依據的是 GeoJSON 結構描述。 協助程式類別則會透過 [atlas.data namespace](/javascript/api/azure-maps-control/atlas.data) 來公開。 另外還有 [atlas.Shape](/javascript/api/azure-maps-control/atlas.shape) 類別可用來包裝 GeoJSON 物件，這可讓您以資料可繫結的方式來更新和維護這些物件。
-   Azure 地圖服務中的座標會定義為 Position 物件，並可指定為 `[longitude, latitude]` 或 `new atlas.data.Position(longitude, latitude)` 格式的簡單數字陣列。

> [!TIP]
> Position 類別具有靜態協助程式函式，可用於匯入 `latitude, longitude` 格式的座標。 [atlas.data.Position.fromLatLng](/javascript/api/azure-maps-control/atlas.data.position) 函式通常會取代 Bing 地圖服務程式碼中的 `new Microsoft.Maps.Location` 函式。

-   Azure 地圖服務並不會在每個新增至地圖的圖形上指定樣式資訊，而是會將樣式與資料分開。 資料會儲存在資料來源中，並連結至 Azure 地圖服務程式碼用來呈現資料的呈現圖層。 這種方法可提供增強的效能優勢。 此外，許多圖層都支援資料驅動的樣式，也就是可將商務邏輯新增至圖層樣式選項，以根據圖形中定義的屬性來變更圖層內個別圖形的呈現方式。
-   Azure 地圖服務在 `atlas.math` 命名空間中提供了許多實用的空間數學函式，不過這些函式與 Bing 地圖服務空間數學模組中的不同。 主要的差異在於，Azure 地圖服務不會提供二進位作業的內建函式 (例如聯合與交集)，不過，由於 Azure 地圖服務是以開放式標準的 GeoJSON 為基礎，因此有許多開放原始碼程式庫可供使用。 一個適用於 Azure 地圖服務並提供大量空間數學功能的熱門選項為 [turf js](http://turfjs.org/)。

另請參閱 [Azure 地圖服務詞彙](./glossary.md)，以取得與 Azure 地圖服務相關聯術語的深入清單。

## <a name="web-sdk-side-by-side-examples"></a>Web SDK 對照範例

以下是每個平台的程式碼範例集合，集合內涵蓋了常見的使用案例，可協助您將 Web 應用程式從 Bing 地圖服務 V8 JavaScript SDK 遷移至 Azure 地圖服務 Web SDK。 與 Web 應用程式相關的程式碼範例會以 JavaScript 提供；不過，Azure 地圖服務也會透過 [NPM 模組](./how-to-use-map-control.md)來提供 TypeScript 定義作為額外的選項。

**主題**

- [載入地圖](#load-a-map)
- [將地圖當地語系化](#localizing-the-map)
- [設定地圖檢視](#setting-the-map-view)
- [新增圖釘](#adding-a-pushpin)
- [新增自訂圖釘](#adding-a-custom-pushpin)
- [新增聚合線條](#adding-a-polyline)
- [新增多邊形](#adding-a-polygon)
- [顯示 InfoBox](#display-an-infobox)
- [圖釘叢集](#pushpin-clustering)
- [新增熱度圖](#add-a-heat-map)
- [覆蓋地圖底圖圖層](#overlay-a-tile-layer)
- [顯示流量資料](#show-traffic-data)
- [新增地面覆蓋](#add-a-ground-overlay)
- [將 KML 資料新增至地圖](#add-kml-data-to-the-map)
- [新增繪製工具](#add-drawing-tools)


### <a name="load-a-map"></a>載入地圖

在這兩種 SDK 中，載入地圖時都會遵循同一組步驟；

-   在地圖 SDK 中新增參考。
-   在頁面主體中新增 `div` 標籤以作為地圖的預留位置。
-   建立會在頁面載入時加以呼叫的 JavaScript 函式。
-   建立個別 Map 類別的執行個體。

**一些主要差異**

-   Bing 地圖服務需要在 API 的指令碼參考或地圖選項中指定帳戶金鑰。 Azure 地圖服務的驗證認證會指定為 Map 類別的選項。 這可以是訂用帳戶金鑰或 Azure Active Directory 資訊。
-   Bing 地圖服務會在 API 的指令碼參考中採用回呼函式，以便用來呼叫初始化函式以載入地圖。 在使用 Azure 地圖服務時，則應該使用頁面的 onload 事件。
-   使用識別碼來參考地圖將轉譯的 `div` 元素時，Bing 地圖服務會使用 HTML 選取器 (也就是 `#myMap`)，而 Azure 地圖服務只會使用識別碼值 (也就是 `myMap`)。
-   Azure 地圖服務中的座標會定義為 Position 物件，並可指定為 `[longitude, latitude]` 格式的簡單數字陣列。
-   Azure 地圖服務的縮放層級比 Bing 地圖服務範例低一層，其原因是這兩種平台的地圖底圖系統有差異。
-   根據預設，Azure 地圖服務不會將任何導覽控制項新增至地圖畫布，例如縮放按鈕和地圖樣式按鈕。 不過，會有控制項可用來新增地圖樣式選擇器、縮放按鈕、羅盤或旋轉控制項，以及傾斜角度控制項。
-   Azure 地圖服務中會新增事件處理常式，以監視地圖執行個體的 `ready` 事件。 當地圖所需的 WebGL 內容和所有資源載入完成時，就會引發此事件。 您可以在此事件處理常式中新增任何後續載入程式碼。

下列範例示範如何載入基本地圖，該地圖的中心位於紐約，座標為 (經度：-73.985、緯度：40.747)，在 Bing 地圖服務中的縮放層級為 12。

**之前：Bing 地圖服務**

下列程式碼示範如何顯示在某個位置置中和縮放的 Bing 地圖服務。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
          center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

在瀏覽器中執行此程式碼會顯示如下圖所示的地圖：

<center>

![Bing 地圖服務地圖](media/migrate-bing-maps-web-app/bing-maps-load-map.jpg)</center>

**之後：Azure 地圖服務**

下列程式碼示範如何在 Azure 地圖服務中載入具有相同檢視的地圖，以及地圖的樣式控制項和縮放按鈕。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom and map style controls to top right of map.
                map.controls.add([
                    new atlas.control.StyleControl(),
                    new atlas.control.ZoomControl()
                ], {
                    position: 'top-right'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

在瀏覽器中執行此程式碼會顯示如下圖所示的地圖：

<center>

![Azure 地圖服務地圖](media/migrate-bing-maps-web-app/azure-maps-load-map.jpg)</center>

如需如何在 Web 應用程式中設定及使用 Azure 地圖服務地圖控制項的詳細文件，請參閱[這裡](./how-to-use-map-control.md)。

> [!TIP]
> Azure 地圖服務會同時發行 SDK 的縮製和解除縮製版本。 移除檔案名稱中的 `.min`。 解除縮製版本在偵錯問題時很有用，但請務必使用生產環境中的縮製版本以利用較小的檔案大小。

**其他資源**

-   Azure 地圖服務也會提供導覽控制項以供您旋轉地圖和傾斜地圖檢視角度，如[這裡](./map-add-controls.md)所述。

### <a name="localizing-the-map"></a>將地圖當地語系化

如果您的受眾分散在多個國家/地區或使用不同語言，當地語系化便很重要。

**之前：Bing 地圖服務**

若要當地語系化 Bing 地圖服務，會使用 `setLang` 來指定語言和地區，並將 `UR` 參數新增至 API `<script>` 標記參考。 Bing 地圖服務中的某些功能僅適用於特定市場，因此會使用 `setMkt` 參數來指定使用者的市場。

```html
<script type="text/javascript" src="https://www.bing.com/api/maps/mapcontrol?callback=initMap&setLang=[language_code]&setMkt=[market]&UR=[region_code]" async defer></script>
```

以下是語言設定為 "fr-FR" 的 Bing 地圖服務範例。

<center>

![當地語系化的 Bing 地圖服務地圖](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)</center>

**之後：Azure 地圖服務**

Azure 地圖服務僅提供設定地圖語言和區域檢視的選項。 市場參數不會用來限制功能。 提供兩種不同的方式供您設定地圖的語言和區域檢視。 第一個選項是將這項資訊新增至全球 `atlas` 命名空間，這會導致應用程式中的所有地圖控制項執行個體預設為這些設定。 以下範例會將語言設定為法文 ("fr-FR")，並將區域檢視設為 `"auto"`：

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

第二個選項是在載入地圖時，將此資訊傳入地圖選項中，如下所示：

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> 在使用 Azure 地圖服務時，您可以在相同頁面上使用不同語言和區域設定載入多個地圖執行個體。 此外，在地圖載入後，也可以在地圖中更新這些設定。 如需 Azure 地圖服務所支援語言的詳細清單，請參閱[這裡](./supported-languages.md)。

以下範例會示範語言設為 "fr" 且使用者區域設為 "fr-FR" 的 Azure 地圖服務。

<center>

![當地語系化的 Azure 地圖服務地圖](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)</center>

### <a name="setting-the-map-view"></a>設定地圖檢視

您可以透過在 JavaScript 中呼叫適當的函式，以程式設計方式將 Bing 地圖服務和 Azure 地圖服務中的動態地圖移至新的地理位置。 下列範例示範如何讓地圖顯示衛星空照圖影像，並將地圖置中於某個位置，其座標為 (經度：-111.0225、緯度：35.0272)，並在 Bing 地圖服務中將縮放層級變更為 15。

> [!NOTE]
> Bing 地圖服務所使用的地圖底圖維度為 256 像素，而 Azure 地圖服務則使用較大的 512 像素地圖底圖。 在載入和 Bing 地圖服務相同的地圖區域時，這種設定會讓 Azure 地圖服務減少所需的網路要求數目。 不過，由於地圖底圖金字塔在地圖控制項中的運作方式所致，如果在 Azure 地圖服務中使用較大的地圖底圖，則表示若要達到與 Bing 地圖服務中的地圖相同的可檢視區域，您就必須在使用 Azure 地圖服務時，將 Bing 地圖服務中所用的縮放層級減 1。

**之前：Bing 地圖服務**

您可以使用 `setView` 函式來以程式設計方式移動 Bing 地圖服務地圖控制項，這種方法可讓您指定地圖的中心點和縮放層級。

```javascript
map.setView({
    mapTypeId: Microsoft.Maps.MapTypeId.aerial,
    center: new Microsoft.Maps.Location(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Bing 地圖服務設定地圖視圖](media/migrate-bing-maps-web-app/bing-maps-set-map-view.jpg)</center>

**之後：Azure 地圖服務**

在 Azure 地圖服務中，您可以使用地圖的 `setCamera` 函式來以程式設計方式變更地圖位置，以及使用 `setStyle` 函式來變更地圖樣式。 請注意，Azure 地圖服務中的座標採用「經度,緯度」格式，而且縮放層級值會減 1。

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite_with_roads'
});
```

<center>

![Azure 地圖服務設定地圖視圖](media/migrate-bing-maps-web-app/azure-maps-set-map-view.jpg)</center>

**其他資源**

-   [選擇地圖樣式](./choose-map-style.md)
-   [支援的地圖樣式](./supported-map-styles.md)

### <a name="adding-a-pushpin"></a>新增圖釘

Azure 地圖服務有多種方式可在地圖上呈現位置點資料；

-   HTML 標記 – 使用傳統 DOM 元素來呈現位置點。 HTML 標記支援拖曳。
-   符號圖層 – 會在 WebGL 內容中以圖示和/或文字呈現位置點。
-   泡泡圖層 – 在地圖上以圓圈呈現位置點。 圓圈半徑可根據資料中的屬性來縮放。

符號和泡泡圖層都會在 WebGL 內容中呈現，而且能在地圖上呈現非常大的一組位置點。 這些圖層會要求將資料儲存在資料來源中。 在 `ready` 事件引發之後，就應該將資料來源和呈現圖層新增至地圖中。 HTML 標記會在頁面中呈現為 DOM 元素，且不會使用資料來源。 頁面所擁有的 DOM 元素越多，頁面就會變得越慢。 如果要在地圖上呈現超過幾百個位置點，建議您改用其中一個呈現圖層。

下列範例會將標記新增至地圖中，其位置為 (經度：-0.2、緯度：51.5)，並以數字 10 覆蓋為標籤。

**之前：Bing 地圖服務**

使用 Bing 地圖服務時，會使用 `Microsoft.Maps.Pushpin` 類別*將標記新增至地圖。 然後使用兩個函式其中之一，將圖釘新增至地圖。

第一個函式是建立圖層、在其中插入圖釘，然後將圖層新增至地圖的 `layers` 屬性。

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

第二個是使用地圖的 `entities` 屬性來加以新增。 此函式在 Bing 地圖服務 V8 的文件中標示為已被取代，但在基本案例中，其仍為部分正常運作的功能。

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

map.entities.add(pushpin);
```

<center>

![Bing 地圖服務新增圖釘](media/migrate-bing-maps-web-app/bing-maps-add-pushpin.jpg)</center>

**之後：使用 HTML 標記的 Azure 地圖服務**

在 Azure 地圖服務中，HTML 標記可用來輕鬆顯示地圖上的某個位置點，且建議用於只需要在地圖上顯示少量位置點的應用程式。 若要使用 HTML 標記，請建立 `atlas.HtmlMarker` 類別的執行個體、設定文字和位置選項，然後使用 `map.markers.add` 函式將標記新增至地圖。

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Azure 地圖服務新增標記](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)</center>

**之後：使用符號圖層的 Azure 地圖服務**

在使用符號圖層時，您必須將資料新增至資料來源，並將資料來源連結至圖層。 此外，還應該在 `ready` 事件引發之後，將資料來源和圖層新增至地圖中。 若要在符號上方呈現唯一文字值，文字資訊必須儲存為資料點的屬性，而且必須在圖層的 `textField` 選項中參考該屬性。 相較於使用 HTML 標記，這種方式的工作量較多，但可提供諸多效能優勢。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure 地圖服務新增符號圖層](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)</center>

**其他資源**

-   [建立資料來源](./create-data-source-web-sdk.md)
-   [新增符號圖層](./map-add-pin.md)
-   [新增泡泡圖層](./map-add-bubble-layer.md)
-   [群集位置點資料](./clustering-point-data-web-sdk.md)
-   [新增 HTML 標記](./map-add-custom-html.md)
-   [使用資料驅動樣式運算式](./data-driven-style-expressions-web-sdk.md)
-   [符號圖層圖示選項](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [符號圖層文字選項](/javascript/api/azure-maps-control/atlas.textoptions)
-   [HTML 標記類別](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [HTML 標記選項](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-pushpin"></a>新增自訂圖釘

自訂影像可用來代表地圖上的位置點。 下列影像會在以下範例中使用，並會使用自訂影像在地圖上顯示某個位置點 (緯度：51.5、經度：-0.2) 並位移標記位置，讓圖釘圖示的尖端對準地圖上的正確位置。

| ![Azure 地圖服務新增圖釘](media/migrate-bing-maps-web-app/yellow-pushpin.png)|
|:-----------------------------------------------------------------------:|
| yellow-pushpin.png                                                        |


**之前：Bing 地圖服務**

在 Bing 地圖服務中，會將影像的 URL 傳遞至圖釘的 `icon` 選項，藉此建立自訂標記。 `anchor` 選項會用來將圖釘影像的位置點與地圖上的座標對齊。 Bing 地圖服務中的 anchor 值會相對於影像的左上角。

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    icon: 'ylw-pushpin.png',
    anchor: new Microsoft.Maps.Point(5, 30)
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

<center>

![Bing 地圖服務新增自訂圖釘](media/migrate-bing-maps-web-app/bing-maps-add-custom-pushpin.jpg)</center>

**之後：使用 HTML 標記的 Azure 地圖服務**

若要在 Azure 地圖服務中自訂 HTML 標記，可將 HTML `string` 或 `HTMLElement` 傳遞至標記的 `htmlContent` 選項。 在 Azure 地圖服務中，`anchor` 選項可用來指定標記的相對位置，作為其相對依據的是使用九個已定義參考點之一的位置座標：中心、頂端、底部、左側、右側、左上角、右上角、左下角、右下角。 根據預設，內容會進行錨定並設定為「底部」，也就是 html 內容的底部中心點。 為了讓您更輕鬆地從 Bing 地圖服務遷移程式碼，請將錨點設定為 [左上角]，然後搭配使用 `offset` 選項與 Bing 地圖服務中使用的相同位移。 Azure 地圖服務中的位移會以和 Bing 地圖服務相反的方向移動，因此請將位移乘上負一。

> [!TIP]
> 將 `pointer-events:none` 新增為 HTML 內容上的樣式，以在 MS Edge 中停用會顯示不必要圖示的預設拖曳行為。

```html
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Azure 地圖服務新增自訂標記](media/migrate-bing-maps-web-app/azure-maps-add-custom-marker.jpg)</center>

**之後：使用符號圖層的 Azure 地圖服務**

Azure 地圖服務中的符號圖層也支援自訂影像，但影像必須先載入至地圖資源，並獲派唯一識別碼。 符號圖層接著便可以參考此識別碼。 您可以使用圖示 `offset` 選項來位移符號，使其對齊影像上的正確位置點。 在 Azure 地圖服務中，`anchor` 選項可用來指定符號的相對位置，作為其相對依據的是使用九個已定義參考點之一的位置座標：中心、頂端、底部、左側、右側、左上角、右上角、左下角、右下角。 根據預設，內容會進行錨定並設定為「底部」，也就是 HTML 內容的底部中心點。 為了讓您更輕鬆地從 Bing 地圖服務遷移程式碼，請將錨點設定為 [左上角]，然後搭配使用 `offset` 選項與 Bing 地圖服務中使用的相同位移。 Azure 地圖服務中的位移會以和 Bing 地圖服務相反的方向移動，因此請將位移乘上負一。

```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Bing 地圖服務新增自訂符號圖層](media/migrate-bing-maps-web-app/azure-maps-add-custom-symbol-layer.jpg)</center>

> [!TIP]
> 若要為位置點建立進階的自訂呈現條件，請一起使用多個呈現圖層。 例如，如果您想要擁有多個圖釘，並讓這些圖釘在不同顏色的圓上具有相同的圖示，則不必為每個顏色建立一堆影像，而是可以在泡泡圖層上方覆蓋符號圖層，並讓這些圖釘參考相同的資料來源。 這會比建立地圖並讓地圖保有一堆不同影像來得有效率。

**其他資源**

-   [建立資料來源](./create-data-source-web-sdk.md)
-   [新增符號圖層](./map-add-pin.md)
-   [新增 HTML 標記](./map-add-custom-html.md)
-   [使用資料驅動樣式運算式](./data-driven-style-expressions-web-sdk.md)
-   [符號圖層圖示選項](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [符號圖層文字選項](/javascript/api/azure-maps-control/atlas.textoptions)
-   [HTML 標記類別](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [HTML 標記選項](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>新增聚合線條

聚合線條可用來代表地圖上的一條線或路徑。 下列範例說明如何在地圖上建立虛線聚合線條。

**之前：Bing 地圖服務**

在 Bing 地圖服務中，聚合線條類別會接受位置陣列和一組選項。

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polyline.
var polyline = new Microsoft.Maps.Polyline([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1)
    ], {
        strokeColor: 'red',
        strokeThickness: 4,
        strokeDashArray: [3, 3]
    });

//Add the polyline to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polyline);
map.layers.insert(layer);
```

<center>

![Bing 地圖服務的聚合線條](media/migrate-bing-maps-web-app/bing-maps-line.jpg)</center>

**之後：Azure 地圖服務**

在 Azure 地圖服務中，聚合線條會稱為更常見的地理空間字詞 `LineString` 或 `MultiLineString` 物件。 這些物件可新增至資料來源，並使用線條圖層來加以呈現。 [筆觸色彩]、[寬度] 和 [虛線陣列] 選項在平台之間幾乎完全相同。

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

<center>

![Azure 地圖服務線條](media/migrate-bing-maps-web-app/azure-maps-line.jpg)</center>

**其他資源**

-   [將線條新增至地圖](./map-add-line-layer.md)
-   [線條圖層選項](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [使用資料驅動樣式運算式](./data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>新增多邊形

多邊形可用來代表地圖上的區域。 Azure 地圖服務和 Bing 地圖服務針對多邊形提供了非常類似的支援。 下列範例說明如何根據地圖的中心座標，建立會形成三角形的多邊形。

**之前：Bing 地圖服務**

在 Bing 地圖服務中，多邊形類別會採用座標或座標通道的陣列，以及一組選項。

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polygon.
var polygon = new Microsoft.Maps.Polygon([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1),
        center
    ], {
        fillColor: 'rgba(0, 255, 0, 0.5)',
        strokeColor: 'red',
        strokeThickness: 2
    });

//Add the polygon to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polygon);
map.layers.insert(layer);
```

<center>

![Bing 地圖服務的多邊形](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)</center>

**之後：Azure 地圖服務**

在 Azure 地圖服務中，MultiPolygon 物件可新增至資料來源，並使用圖層在地圖上加以呈現。 多邊形的區域可在多邊形圖層中呈現。 多邊形的外框則可使用線條圖層來呈現。

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

<center>

![Azure 地圖服務的多邊形](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)</center>

**其他資源**

-   [將多邊形新增至地圖](./map-add-shape.md#use-a-polygon-layer)
-   [將圓形新增至地圖](./map-add-shape.md#add-a-circle-to-the-map)
-   [多邊形圖層選項](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
-   [線條圖層選項](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [使用資料驅動樣式運算式](./data-driven-style-expressions-web-sdk.md)

### <a name="display-an-infobox"></a>顯示 InfoBox

在 Bing 地圖服務中，實體的其他資訊可在地圖上以 `Microsoft.Maps.Infobox` 類別顯示，在 Azure 地圖服務中，則可以使用 `atlas.Popup` 類別來實現。 下列範例會將圖釘/標記新增至地圖，然後按一下時，會顯示 Infobox/快顯。

**之前：Bing 地圖服務**

使用 Bing 地圖服務時，可使用 `Microsoft.Maps.Infobox` 建構函式來建立 Infobox。

```javascript
//Add a pushpin where we want to display an infobox.
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(47.6, -122.33));

//Add the pushpin to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);

//Create an infobox and bind it to the map.
var infobox = new Microsoft.Maps.Infobox(new Microsoft.Maps.Location(47.6, -122.33), {
    description: '<div style="padding:5px"><b>Hello World!</b></div>',
    visible: false
});
infobox.setMap(map);

//Add a click event to the pushpin to open the infobox.
Microsoft.Maps.Events.addHandler(pushpin, 'click', function () {
    infobox.setOptions({ visible: true });
});
```

<center>

![Bing 地圖服務 Infobox](media/migrate-bing-maps-web-app/bing-maps-infobox.jpg)</center>

**之後：Azure 地圖服務**

在 Azure 地圖服務中，可使用快顯視窗來顯示某個位置的其他資訊。 HTML 的 `string` 或 `HTMLElement` 物件可以傳遞至快顯視窗的 `content` 選項。 如有需要，快顯視窗可獨立於任何圖形之外來顯示，但也因此必須指定 `position` 值。 若要顯示快顯視窗，請呼叫 `open` 函式，並傳入要在其中顯示快顯視窗的地圖。

```javascript
//Add a marker to the map that to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:10px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

<center>

![Azure 地圖服務的快顯視窗](media/migrate-bing-maps-web-app/azure-maps-popup.jpg)</center>

> [!NOTE]
> 若要使用符號、泡泡、線條或多邊形圖層來進行相同操作，請將圖層傳遞至地圖事件程式碼即可 (而不是標記)。

**其他資源**

-   [新增快顯](./map-add-popup.md)
-   [具有媒體內容的快顯視窗](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
-   [圖形上的快顯視窗](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
-   [重複使用具有多個圖釘的快顯視窗](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
-   [Popup 類別](/javascript/api/azure-maps-control/atlas.popup)
-   [快顯視窗選項](/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="pushpin-clustering"></a>圖釘叢集

在地圖上視覺化許多資料點時，資料點會彼此覆蓋，而讓地圖看起來很雜亂，並變得難以看清楚和使用。 位置點資料的群集功能可用來改善此使用者體驗，並改善效能。 群集位置點資料的程序會結合彼此接近的位置點資料，並在地圖上以單一群集資料點的方式加以表示。 當使用者放大地圖時，群集便會分解成個別的資料點。

下列範例會載入過去一週地震資料的 GeoJSON 摘要，並將其新增至地圖。 群集則會根據所包含的位置點數目，以經過縮放且有顏色的圓形來呈現。

> [!NOTE]
> 有數種不同的演算法可用於圖釘叢集。 Bing 地圖服務會使用簡單的方格型函式，而 Azure 地圖服務會使用更先進且更具視覺效果的點型叢集方法。

**之前：Bing 地圖服務**

在 Bing 地圖服務中，您可以使用 GeoJSON 模組來載入 GeoJSON 資料。 圖釘可以藉由在叢集模組中載入並使用其所包含的叢集層進行叢集化。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2
            });

            //Load the GeoJSON and Clustering modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.Clustering'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (pins) {

                    //Create a ClusterLayer with options and add it to the map.
                    clusterLayer = new Microsoft.Maps.ClusterLayer(pins, {
                        clusteredPinCallback: createCustomClusteredPin,
                        gridSize: 100
                    });

                    map.layers.insert(clusterLayer);
                });
            });
        }

        //A function that defines how clustered pins are rendered.
        function createCustomClusteredPin(cluster) {
            //Get the number of pushpins in the cluster
            var clusterSize = cluster.containedPushpins.length;

            var radius = 20;    //Default radius to 20 pixels.
            var fillColor = 'lime';     //Default to lime green.

            if (clusterSize >= 750) {
                radius = 40;   //If point_count >= 750, radius is 40 pixels.
                fillColor = 'red';    //If the point_count >= 750, color is red.
            } else if (clusterSize >= 100) {
                radius = 30;    //If point_count >= 100, radius is 30 pixels.
                fillColor = 'yellow';    //If the point_count >= 100, color is yellow.
            }

            //Create an SVG string of a circle with the specified radius and color.
            var svg = ['<svg xmlns="http://www.w3.org/2000/svg" width="', (radius * 2), '" height="', (radius * 2), '">',
                '<circle cx="', radius, '" cy="', radius, '" r="', radius, '" fill="', fillColor, '"/>',
                '<text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" style="font-size:12px;font-family:arial;fill:black;" >{text}</text>',
                '</svg>'];

            //Customize the clustered pushpin using the generated SVG and anchor on its center.
            cluster.setOptions({
                icon: svg.join(''),
                anchor: new Microsoft.Maps.Point(radius, radius),
                textOffset: new Microsoft.Maps.Point(0, radius - 8) //Subtract 8 to compensate for height of text.
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Bing 地圖服務的叢集](media/migrate-bing-maps-web-app/bing-maps-clustering.jpg)</center>

**之後：Azure 地圖服務**

在 Azure 地圖服務中，資料則會由資料來源新增和管理。 圖層會連線到資料來源，並呈現其中所含的資料。 Azure 地圖服務中的 `DataSource` 類別會提供數個群集選項。

-   `cluster` - 讓資料來源群集位置點資料。 
-   `clusterRadius` - 用來群集位置點的半徑 (以像素為單位)。
-   `clusterMaxZoom` - 要執行叢集的最大縮放層級。 如果您放大的倍數超過此層級，所有位置點都會呈現為符號。
-   `clusterProperties` - 定義會使用運算式針對每個群集內的所有位置點進行計算，並新增至每個群集位置點屬性的自訂屬性。

啟用群集時，資料來源會將已群集和未群集的資料點傳送到圖層來呈現。 資料來源能夠群集數十萬個資料點。 已群集的資料點具有下列屬性：

| 屬性名稱               | 類型    | 描述                                    |
|-----------------------------|---------|------------------------------------------------|
| `cluster`                   | boolean | 指出功能是否代表群集。     |
| `cluster_id`                | 字串  | 叢集的唯一識別碼，可與 `DataSource` 類別 `getClusterExpansionZoom`、`getClusterChildren` 和 `getClusterLeaves` 函式搭配使用。 |
| `point_count`               | number  | 群集包含的位置點數目。     |
| `point_count_abbreviated`   | 字串  | 會將較長的 `point_count` 值縮寫的字串。 (例如，4,000 變成 4K) |

`DataSource` 類別具有下列協助程式函式，可使用 `cluster_id` 來存取關於群集的其他資訊。

| 函式       | 傳回類型        | 描述     |
|----------------|--------------------|-----------------|
| `getClusterChildren(clusterId: number)`                              | `Promise<Feature<Geometry, any> | Shape>` | 擷取給定群集在下一個縮放層級上的子系。 這些子系可以是圖形和子叢集的組合。 子叢集會是屬性符合叢集屬性的功能。 |
| `getClusterExpansionZoom(clusterId: number)`                         | `Promise<number>`                            | 計算叢集將開始擴大或分解的臨界縮放層級。    |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | `Promise<Feature<Geometry, any> | Shape>` | 擷取群集中的所有位置點。 設定 `limit` 可傳回位置點的子集，使用 `offset` 則可逐頁查看位置點。    |

在地圖上呈現已群集的資料時，最簡單的方式往往是使用兩個以上的圖層。 下列範例會使用三個圖層，泡泡圖層會根據叢集大小繪製經過縮放的彩色圓形、符號圖層會以文字呈現叢集大小，第二個符號圖層則用來呈現未叢集的位置點。 [群集位置點資料](./clustering-point-data-web-sdk.md)文件中會重點指出其他許多可在 Azure 地圖服務中呈現已群集資料的方式。

您可以在 `DataSource` 類別上使用 `importDataFromUrl` 函式，於 Azure 地圖服務中直接匯入 GeoJSON 資料。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points that have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure 地圖服務的群集](media/migrate-bing-maps-web-app/azure-maps-clustering.jpg)</center>

**其他資源**

-   [新增符號圖層](./map-add-pin.md)
-   [新增泡泡圖層](./map-add-bubble-layer.md)
-   [群集位置點資料](./clustering-point-data-web-sdk.md)
-   [使用資料驅動樣式運算式](./data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>新增熱度圖

熱度圖 (也稱為點密度圖) 是一種資料視覺效果，用來以色彩範圍表示資料密度。 這些圖通常用來顯示地圖上的資料「作用點」，而且十分適合用來轉譯大型的點資料集。

下列範例會從 USGS 載入過去一個月所有地震的 GeoJSON 摘要，並熱度圖來呈現。

**之前：Bing 地圖服務**

在 Bing 地圖服務中，若要建立熱度圖，請在熱度圖模組中載入。 同樣地，也會載入 GeoJSON 模組來新增 GeoJSON 資料的支援。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2,
                mapTypeId: Microsoft.Maps.MapTypeId.aerial
            });

            //Load the GeoJSON and HeatMap modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.HeatMap'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (shapes) {

                    //Create a heat map and add it to the map.
                    var heatMap = new Microsoft.Maps.HeatMapLayer(shapes, {
                        opacity: 0.65,
                        radius: 10
                    });
                    map.layers.insert(heatMap);
                });
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Bing 地圖服務熱度圖](media/migrate-bing-maps-web-app/bing-maps-heatmap.jpg)</center>

**之後：Azure 地圖服務**

在 Azure 地圖服務中，將 GeoJSON 資料載入至資料來源，並將資料來源連線至熱度圖圖層。 您可以在 `DataSource` 類別上使用 `importDataFromUrl` 函式，於 Azure 地圖服務中直接匯入 GeoJSON 資料。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite_with_roads',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure 地圖服務熱度圖](media/migrate-bing-maps-web-app/azure-maps-heatmap.jpg)</center>

**其他資源**

-   [新增熱度圖圖層](./map-add-heat-map-layer.md)
-   [熱度圖圖層類別](/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
-   [熱度圖圖層選項](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
-   [使用資料驅動樣式運算式](./data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>覆蓋地圖底圖圖層

地圖底圖圖層可讓您將已分解成較小地圖底圖影像、並與地圖底圖系統契合的大型影像覆蓋在一起。 在覆蓋大型影像或非常大型的資料集時，常會使用這種方式。

下列範例會覆蓋來自愛荷華州立大學 Iowa Environmental Mesonet 的氣象雷達地圖底圖圖層，其使用 X、Y、縮放地圖底圖命名結構描述。

**之前：Bing 地圖服務**

在 Bing 地圖服務中，您可以使用 `Microsoft.Maps.TileLayer` 類別來建立地圖底圖圖層。

```javascript
var weatherTileLayer = new Microsoft.Maps.TileLayer({
    mercator: new Microsoft.Maps.TileSource({
        uriConstructor: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{zoom}/{x}/{y}.png'
    })
});
map.layers.insert(weatherTileLayer);
```

<center>

![Bing 地圖服務加權熱度圖](media/migrate-bing-maps-web-app/bing-maps-weighted-heatmap.jpg)</center>

**之後：Azure 地圖服務**

您可在 Azure 地圖服務中將地圖底圖圖層新增至地圖中，其方式與任何其他圖層大致相同。 具有 x、y、縮放預留位置 (分別為 `{x}`、`{y}`、`{z}`) 的格式化 URL 可用來讓圖層知道要於何處存取地圖底圖。 Azure 地圖服務的地圖底圖圖層也支援 `{quadkey}`、`{bbox-epsg-3857}` 和 `{subdomain}` 預留位置。

> [!TIP]
> 在 Azure 地圖服務中，您可以輕鬆地將圖層放到其他圖層下面來呈現，其中也包括基底地圖圖層。 地圖底圖圖層通常最好是放到地圖標籤下面來呈現，以方便辨識。 `map.layers.add` 函式會採用第二個參數，也就是要插入下方新圖層的第二個圖層識別碼。 若要在地圖標籤下面插入地圖底圖圖層，您可以使用下列程式碼：
> 
> `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Azure 地圖服務加權熱度圖](media/migrate-bing-maps-web-app/azure-maps-weighted-heatmap.jpg)</center>

> [!TIP]
> 您可以使用地圖的 `transformRequest` 選項來擷取地圖底圖要求。 這可讓您視需要在要求中修改或新增標頭。

**其他資源**

-   [新增地圖底圖圖層](./map-add-tile-layer.md)
-   [地圖底圖圖層類別](/javascript/api/azure-maps-control/atlas.layer.tilelayer)
-   [地圖底圖圖層選項](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>顯示流量資料

Bing 地圖服務和 Azure 地圖服務都可以覆蓋交通資料。

**之前：Bing 地圖服務**

在 Bing 地圖服務中，交通資料可使用交通模組來覆蓋在地圖上。

```javascript
Microsoft.Maps.loadModule('Microsoft.Maps.Traffic', function () {
    var manager = new Microsoft.Maps.Traffic.TrafficManager(map);
    manager.show();
});
```

<center>

![Bing 地圖服務的交通](media/migrate-bing-maps-web-app/bing-maps-traffic.jpg)</center>

**之後：Azure 地圖服務**

Azure 地圖服務提供數個不同的選項來顯示交通。 交通事件 (例如道路封閉和事故) 可在地圖上顯示為圖示。 車流量 (以色彩編碼的道路) 可以在地圖上覆蓋，且色彩可修改為以相對於速限、相對於平常的預期延遲或絕對延遲作為依據。 Azure 地圖服務中的事件資料會每分鐘更新一次，車流資料則會每 2 分鐘更新一次。

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Azure 地圖服務的交通](media/migrate-bing-maps-web-app/azure-maps-traffic.jpg)</center>

如果您在 Azure 地圖服務中按一下其中一個交通圖示，則會在快顯視窗中顯示其他資訊。

<center>

![Azure 地圖服務的交通快顯視窗](media/migrate-bing-maps-web-app/azure-maps-traffic-popup.jpg)</center>

**其他資源**

-   [在地圖上顯示路況](./map-show-traffic.md)
-   [交通的覆蓋選項](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)
-   [交通控制](https://azuremapscodesamples.azurewebsites.net/?sample=Traffic%20controls)

### <a name="add-a-ground-overlay"></a>新增地面覆蓋

Bing 地圖服務和 Azure 地圖服務都支援在地圖上覆蓋有地理參考的影像，因此影像可在您平移和縮放地圖時隨之移動和縮放。 在 Bing 地圖服務中，這些項目稱為地面覆蓋，而在 Azure 地圖服務中則稱為影像圖層。 這些項目非常適合用來建置樓層平面圖、覆蓋舊地圖或來自無人機的影像。

**之前：Bing 地圖服務**

在 Bing 地圖服務中建立地面覆蓋時，您需要指定所要覆蓋影像的 URL，以及地圖上要用來作為影像系繫結目標的週框方塊。 這個範例會在地圖上覆蓋 [1922 年紐澤西紐瓦克](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg)的地圖影像。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.740, -74.18),
                zoom: 12
            });

            var overlay = new Microsoft.Maps.GroundOverlay({
                //Create a LocationRect from the edges of the bounding box; north, west, south, east.
                bounds: Microsoft.Maps.LocationRect.fromEdges(40.773941, -74.22655, 40.712216, -74.12544),
                imageUrl: 'newark_nj_1922.jpg'
            });
            map.layers.insert(overlay);
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

在瀏覽器中執行此程式碼會顯示如下圖所示的地圖：

<center>

![Bing 地圖服務地面重疊](media/migrate-bing-maps-web-app/bing-maps-ground-overlay.jpg)</center>

**之後：Azure 地圖服務**

在 Azure 地圖服務中，您可以使用 `atlas.layer.ImageLayer` 類別來覆蓋有地理參考的影像。 此類別需要影像的 URL 和影像四個角落所組成的一組座標。 影像必須裝載於相同網域上或啟用 CORS。

> [!TIP]
> 如果您只有北側、南側、東側、西側和旋轉資訊，而沒有影像各個角落的座標，則可以使用靜態的 [atlas.layer.ImageLayer.getCoordinatesFromEdges](/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-) 函式。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure 地圖服務地面重疊](media/migrate-bing-maps-web-app/azure-maps-ground-overlay.jpg)</center>

**其他資源**

-   [覆蓋影像](./map-add-image-layer.md)
-   [影像圖層類別](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>將 KML 資料新增至地圖

Azure 地圖服務和 Bing 地圖服務都可以在地圖上匯入及呈現 KML、KMZ、GeoRSS、GeoJSON 和已知文字 (WKT) 資料。 Azure 地圖服務也支援 GPX、GML、空間 CSV 檔案、Web Mapping Services (WMS)、Web Mapping Tile Services (WMTS) 和 Web Feature Services (WFS)。

**之前：Bing 地圖服務**

在瀏覽器中執行此程式碼會顯示如下圖所示的地圖：

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });

            Microsoft.Maps.loadModule('Microsoft.Maps.GeoXml', function () {
                var callback = function (dataset) {
                    if (dataset.shapes) {
                        var l = new Microsoft.Maps.Layer();
                        l.add(dataset.shapes);
                        map.layers.insert(l);
                    }
                    if (dataset.layers) {
                        for (var i = 0, len = dataset.layers.length; i < len; i++) {
                            map.layers.insert(dataset.layers[i]);
                        }
                    }
                };
                Microsoft.Maps.GeoXml.readFromUrl('myKMLFile.kml', { error: function (msg) { alert(msg); } }, callback);
            });                
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Bing 地圖服務 kml](media/migrate-bing-maps-web-app/bing-maps-kml.jpg)</center>

**之後：Azure 地圖服務**

在 Azure 地圖服務中，GeoJSON 是 Web SDK 中使用的主要資料格式，您可使用[空間 IO 模組](/javascript/api/azure-maps-spatial-io/)輕鬆地將其他空間資料格式整合在其中。 此模組具有可供讀取和寫入空間資料的函式，也包含可輕鬆地從這些空間資料格式轉譯資料的簡單資料層。 若要讀取空間資料檔案中的資料，以字串或 Blob 的形式將 URL 或原始資料傳入 `atlas.io.read` 函式。 這會從檔案傳回所有已剖析的資料，然後再將該檔案新增至地圖。 相較於大部分的空間資料格式，KML 稍微複雜一點，因為其包含更多樣式資訊。 `SpatialDataLayer` 類別支援轉譯大多數的樣式，不過，在載入特徵資料之前，必須先將圖示影像載入地圖中，且地面覆蓋必須分別新增為地圖的圖層。 透過 URL 載入資料時，應將其裝載於已啟用 COR 的端點上，或應將 Proxy 服務當作選項傳入讀取函式。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
            
                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Add a simple data layer for rendering the data.
                layer = new atlas.layer.SimpleDataLayer(datasource);
                map.layers.add(layer);

                //Read a KML file from a URL or pass in a raw KML string.
                atlas.io.read('myKMLFile.kml').then(async r => {
                    if (r) {

                        //Check to see if there are any icons in the data set that need to be loaded into the map resources.
                        if (r.icons) {
                            //For each icon image, create a promise to add it to the map, then run the promises in parrallel.
                            var imagePromises = [];

                            //The keys are the names of each icon image.
                            var keys = Object.keys(r.icons);

                            if (keys.length !== 0) {
                                keys.forEach(function (key) {
                                    imagePromises.push(map.imageSprite.add(key, r.icons[key]));
                                });

                                await Promise.all(imagePromises);
                            }
                        }

                        //Load all features.
                        if (r.features && r.features.length > 0) {
                            datasource.add(r.features);
                        }

                        //Load all ground overlays.
                        if (r.groundOverlays && r.groundOverlays.length > 0) {
                            map.layers.add(r.groundOverlays);
                        }

                        //If bounding box information is known for data, set the map view to it.
                        if (r.bbox) {
                            map.setCamera({ bounds: r.bbox, padding: 50 });
                        }
                    }
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure 地圖服務 KML](media/migrate-bing-maps-web-app/azure-maps-kml.jpg)</center>

**其他資源**

-   [atlas.io.read 函式](/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
-   [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
-   [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

### <a name="add-drawing-tools"></a>新增繪製工具

Bing 地圖服務和 Azure 地圖服務都會提供模組，讓使用者能夠使用滑鼠或其他輸入裝置，在地圖上繪製和編輯圖形。 兩者都支援繪製圖釘、線條和多邊形。 Azure 地圖服務也會提供繪製圓形和矩形的選項。

**之前：Bing 地圖服務**

在 Bing 地圖服務中，會使用 `Microsoft.Maps.loadModule` 函式載入 `DrawingTools` 模組。 載入之後，即可建立 DrawingTools 類別的執行個體，並呼叫 `showDrawingManager` 函式，將工具列新增至地圖。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
    var map, drawingManager;

    function initMap() {
        map = new Microsoft.Maps.Map('#myMap', {
            credentials: '<Your Bing Maps Key>'
        });

        //Load the DrawingTools module
        Microsoft.Maps.loadModule('Microsoft.Maps.DrawingTools', function () {
            //Create an instance of the DrawingTools class and bind it to the map.
            var tools = new Microsoft.Maps.DrawingTools(map);

            //Show the drawing toolbar and enable editting on the map.
            tools.showDrawingManager(function (manager) {
                //Store a reference to the drawing manager as it will be useful later.
                drawingManager = manager;
            });
        });
    }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>

```

<center>

![Bing 地圖服務繪圖工具](media/migrate-bing-maps-web-app/bing-maps-drawing-tools.jpg)</center>

**之後：Azure 地圖服務**

在 Azure 地圖服務中，需要載入「繪圖工具」模組，方法是載入必須在應用程式中參考的 JavaScript 和 CSS 檔案。 載入對應之後，就可以建立 `DrawingManager` 類別的執行個體，並附加 `DrawingToolbar` 執行個體。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add references to the Azure Maps Map Drawing Tools JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
    
    <script type='text/javascript'>
        var map, drawingManager;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',
                
                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an instance of the drawing manager and display the drawing toolbar.
                drawingManager = new atlas.drawing.DrawingManager(map, {
                    toolbar: new atlas.control.DrawingToolbar({ position: 'top-left' })
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

<center>

![Azure 地圖服務繪圖工具](media/migrate-bing-maps-web-app/azure-maps-drawing-tools.jpg)</center>

> [!TIP]
> 在 Azure 地圖服務圖層中，繪圖工具提供多種方式可供使用者繪製圖形。 例如，當繪製多邊形時，使用者可以按一下以新增每個點，或按住滑鼠左鍵並拖曳滑鼠來繪製路徑。 這可以使用 `DrawingManager` 的 `interactionType` 選項進行修改。

**其他資源**

-   [文件集](./set-drawing-options.md)
-   [程式碼範例](https://azuremapscodesamples.azurewebsites.net/#Drawing-Tools-Module)

## <a name="next-steps"></a>下一步

請參閱[開放原始碼 Azure 地圖服務 Web SDK 模組](open-source-projects.md#open-web-sdk-modules)。 這些模組提供大量的額外功能，且完全可自訂。

檢閱與遷移其他 Bing 地圖服務功能相關的程式碼範例：

**資料視覺效果**

> [!div class="nextstepaction"]
> [等高線圖層](https://azuremapscodesamples.azurewebsites.net/?search=contour)

> [!div class="nextstepaction"]
> [資料收納](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)

**服務**

> [!div class="nextstepaction"]
> [使用 Azure 地圖服務的服務模組](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [搜尋興趣點](./map-search-location.md)

> [!div class="nextstepaction"]
> [從座標取得資訊 (反向地理編碼)](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [顯示從甲地到乙地的指示](./map-route.md)

> [!div class="nextstepaction"]
> [使用 JQuery UI 的搜尋自動建議](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

深入了解 Azure 地圖服務 Web SDK。

> [!div class="nextstepaction"]
> [如何使用地圖控制項](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [如何使用服務模組](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [如何使用繪圖工具模組](set-drawing-options.md)

> [!div class="nextstepaction"]
> [程式碼範例](/samples/browse/?products=azure-maps)

> [!div class="nextstepaction"]
> [Azure 地圖服務 Web SDK 服務 API 參考文件](/javascript/api/azure-maps-control/)