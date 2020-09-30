---
title: 教學課程：從 Google Maps 遷移 Web 應用程式 | Microsoft Azure 地圖服務
description: 如何將 Web 應用程式從 Google Maps 遷移至 Microsoft Azure 地圖服務。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-javascript
ms.openlocfilehash: bc5f10e34b929110763b53fe1016334ce9bfddd6
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090749"
---
# <a name="migrate-a-web-app-from-google-maps"></a>從 Google Maps 遷移 Web 應用程式

使用 Google Maps 的 Web 應用程式大多使用 Google Maps V3 JavaScript SDK。 Azure 地圖服務 Web SDK 是適合作為遷移目的地的 Azure 型 SDK。 Azure 地圖服務 Web SDK 可讓您以自己的內容和圖像自訂互動式地圖。 您可以在 Web 或行動應用程式上執行應用程式。 此控制項使用 WebGL，可讓您以高效能轉譯大型資料集。 您可以使用 JavaScript 或 TypeScript 以此 SDK 進行開發。

如果要遷移現有的 Web 應用程式，請檢查其是否使用開放原始碼地圖控制項程式庫。 開放原始碼地圖控制項程式庫的範例如下：Cesium、Leaflet 和 OpenLayers。 如果您的應用程式使用開放原始碼地圖控制項程式庫，而且您不想使用 Azure 地圖服務 Web SDK，您仍然可以遷移應用程式。 在這類情況下，請將您的應用程式連線至 Azure 地圖服務的地圖底圖服務 ([道路地圖底圖](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [衛星地圖底圖](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile))。 以下幾點會詳細說明如何在某些常用的開放原始碼地圖控制項程式庫中使用 Azure 地圖服務。

- Cesium - 適用於 Web 的3D 地圖控制項。 [程式碼範例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [文件](https://cesiumjs.org/)
- Leaflet – 適用於 Web 的輕量型 2D 地圖控制項。 [程式碼範例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [文件](https://leafletjs.com/)
- OpenLayers - 適用於 Web 且支援投影的 2D 地圖控制項。 [程式碼範例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [文件](https://openlayers.org/)

如果使用 JavaScript 架構進行開發，下列其中一個開放原始碼專案可能會很有用：

- [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps) - Azure 地圖服務的 Angular 10 包裝函式。
- [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) - Azure 地圖服務 Blazor 元件。
- [Azure 地圖服務 React 元件](https://github.com/WiredSolutions/react-azure-maps) - Azure 地圖服務控制項的反應包裝函式。
- [Vue Azure 地圖服務](https://github.com/rickyruiz/vue-azure-maps) - Vue 應用程式的 Azure 地圖服務元件。

## <a name="key-features-support"></a>主要功能支援

資料表列出 Google Maps V3 JavaScript SDK 中的主要 API 功能，以及在 Azure 地圖服務 Web SDK 中的支援 API 功能。

| Google 地圖功能     | Azure 地圖服務 Web SDK 支援 |
|-------------------------|:--------------------------:|
| 標記                 | ✓                          |
| 標記群集       | ✓                          |
| 聚合線條和多邊形    | ✓                          |
| 資料圖層             | ✓                          |
| 地面覆蓋         | ✓                          |
| 熱度圖               | ✓                          |
| 地圖底圖圖層             | ✓                          |
| KML 圖層               | ✓                          |
| 繪圖工具           | ✓                          |
| Geocoder 服務        | ✓                          |
| 路線服務      | ✓                          |
| 距離矩陣服務 | ✓                          |
| 海拔服務       | 已規劃                     |

## <a name="notable-differences-in-the-web-sdks"></a>這兩種 Web SDK 的顯著差異

以下是 Google Maps 與 Azure 地圖服務 Web SDK 之間需要注意的一些主要差異：

- 除了提供主控端點以供存取 Azure 地圖服務 Web SDK 外，也可使用 NPM 套件。 將 Web SDK 套件內嵌至應用程式。 如需詳細資訊，請參閱[這份文件](how-to-use-map-control.md)。 此套件也包含 TypeScript 定義。
- 您必須先在 Azure 地圖服務中建立地圖類別的實例。 必須先等候地圖 `ready` 或 `load` 事件引發，才能以程式設計方式與地圖互動。 此順序可確保所有地圖資源都已載入，並可供存取。
- 這兩個平台在基底地圖上都使用類似的地圖底圖系統。 Google Maps 中的地圖底圖維度是 256 像素；不過，Azure 地圖服務中的地圖底圖維度是 512 像素。 若要在 Azure 地圖服務中獲得和 Google Maps 相同的地圖檢視，可在 Azure 地圖服務中將 Google Maps 縮放層級減去數字 1。
- Google Maps 中的座標是 `latitude,longitude`，而 Azure 地圖服務使用 `longitude,latitude`。 此 Azure 地圖服務格式與大多數 GIS 平台所遵循的標準 `[x, y]` 相符。
- Azure 地圖服務 Web SDK 中的圖形依據的是 GeoJSON 結構描述。 協助程式類別則會透過 [*atlas.data* namespace](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) 來公開。 另外還有 [*atlas.Shape*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) 類別。 您可以使用此類別來包裝 GeoJSON 物件，以便以資料可繫結的方式來更新和維護這些物件。
- Azure 地圖服務中的座標會定義為位置物件。 座標會指定為 `[longitude,latitude]` 格式的數字陣列。 或者，使用新的 atlas.data.Position(經度, 緯度) 來指定。
    > [!TIP]
    > Position 類別具有靜態協助程式方法，可用於匯入「緯度,經度」格式的座標。 [atlas.data.Position.fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position) 方法通常會取代 Google Maps 程式碼中的 `new google.maps.LatLng` 方法。
- Azure 地圖服務並不會在每個新增至地圖的圖形上指定樣式資訊，而是會將樣式與資料分開。 資料會儲存在資料來源中，並連線到呈現圖層。 Azure 地圖服務程式碼會使用資料來源來呈現資料。 這種方法可提供增強的效能優勢。 此外，許多圖層都支援可將商務邏輯加入圖層樣式選項的資料驅動樣式。 這項支援根據圖形中定義的屬性，變更了個別圖形在圖層中呈現的方式。

## <a name="web-sdk-side-by-side-examples"></a>Web SDK 對照範例

此集合具有適用於每個平台的程式碼範例，且每個範例都包含了常見的使用案例。 其目的是協助您將 Web 應用程式從 Google Maps V3 JavaScript SDK 遷移至 Azure 地圖服務 Web SDK。 與 Web 應用程式相關的程式碼範例會以 JavaScript 提供。 不過，Azure 地圖服務也會透過 [NPM 模組](how-to-use-map-control.md)，將 TypeScript 定義提供為額外的選項。


**主題**

- [載入地圖](#load-a-map)
- [將地圖當地語系化](#localizing-the-map)
- [設定地圖檢視](#setting-the-map-view)
- [新增標記](#adding-a-marker)
- [新增自訂標記](#adding-a-custom-marker)
- [新增聚合線條](#adding-a-polyline)
- [新增多邊形](#adding-a-polygon)
- [顯示資訊視窗](#display-an-info-window)
- [匯入 GeoJSON 檔案](#import-a-geojson-file)- 
- [標記群集](#marker-clustering)
- [新增熱度圖](#add-a-heat-map)
- [覆蓋地圖底圖圖層](#overlay-a-tile-layer)
- [顯示流量資料](#show-traffic-data)
- [新增地面覆蓋](#add-a-ground-overlay)
- [將 KML 資料新增至地圖](#add-kml-data-to-the-map)


### <a name="load-a-map"></a>載入地圖

這兩個 SDK 都使用相同步驟來載入地圖：

- 在地圖 SDK 中新增參考。
- 在頁面主體中新增 `div` 標籤以作為地圖的預留位置。
- 建立會在頁面載入時加以呼叫的 JavaScript 函式。
- 建立個別 Map 類別的執行個體。

**一些主要差異**

- Google Maps 需要在 API 的指令碼參考中指定帳戶金鑰。 Azure 地圖服務的驗證認證會指定為 Map 類別的選項。 此認證可以是訂用帳戶金鑰或 Azure Active Directory 資訊。
- Google Maps 會接受 API 指令碼參考中的回呼函式，以便用來呼叫初始化函式以載入地圖。 在使用 Azure 地圖服務時，則應該使用頁面的 onload 事件。
- 在參考將於其中呈現地圖的 `div` 元素時，Azure 地圖服務中的 `Map` 類別只需要 `id` 值，而 Google Maps 則需要 `HTMLElement` 物件。
- Azure 地圖服務中的座標會定義為 Position 物件，並可指定為 `[longitude, latitude]` 格式的簡單數字陣列。
- Azure 地圖服務中的縮放層級比 Google Maps 中的縮放層級低一層級。 這項差異是因為兩個平台的地圖底圖系統大小不同。
- Azure 地圖服務不會將任何導覽控制項新增至地圖畫布。 因此，根據預設，地圖不會有 [縮放] 按鈕和 [地圖樣式] 按鈕。 不過，會有控制選項可用來新增地圖樣式選擇器、縮放按鈕、羅盤或旋轉控制項，以及傾斜角度控制項。
- Azure 地圖服務中會新增事件處理常式，以監視地圖執行個體的 `ready` 事件。 當地圖完成載入 WebGL 內容和所有需要的資源時，就會引發此事件。 在地圖完成載入之後，將您要執行的任何程式碼新增至此事件處理常式。

下列基本範例會使用 Google Maps 載入以紐約為座標中心的地圖。 經度：-73.985、緯度：40.747，而地圖的縮放層級為 12。

**之前：Google 地圖**

顯示以某個位置作為中心點並進行縮放的 Google 地圖。

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
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

在瀏覽器中執行此程式碼會顯示如下圖所示的地圖：

<center>

![簡單的 Google Maps](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**之後：Azure 地圖服務**

在 Azure 地圖服務中載入具有相同檢視的地圖，並且包含地圖的樣式控制項和縮放按鈕。

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

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
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

![簡單的 Azure 地圖服務](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

如需如何在 Web 應用程式中設定及使用 Azure 地圖服務地圖控制項的詳細文件，請按一下[這裡](how-to-use-map-control.md)。

> [!NOTE]
> 不同於 Google Maps，Azure 地圖服務在載入地圖時並不需要初始中心和縮放層級。 如果在載入地圖時未提供此資訊，Azure 地圖服務會嘗試判斷使用者的城市。 然後以該位置作為地圖中心並進行縮放。

**其他資源：**

- Azure 地圖服務也會提供導覽控制項以供您旋轉地圖和傾斜地圖檢視角度，如[這裡](map-add-controls.md)所述。

### <a name="localizing-the-map"></a>將地圖當地語系化

如果您的受眾分散在多個國家/區域或使用不同語言，當地語系化便很重要。

**之前：Google 地圖**

若要將 Google Maps 當地語系化，請新增語言和區域參數。

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

以下是語言設定為 "fr-FR" 的 Google Maps 範例。

<center>

![Google Maps 當地語系化](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**之後：Azure 地圖服務**

Azure 地圖服務提供兩種不同的方式供您設定地圖的語言和區域檢視。 第一個選項是將此資訊新增至全域 atlas 命名空間。 這會導致您應用程式中的所有地圖控制項執行個體皆以這些設定作為預設值。 以下範例會將語言設定為法文 ("fr-FR")，並將區域檢視設為「自動」：

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

第二個選項是在載入地圖時，將此資訊傳入地圖選項中。 例如：

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
> 在使用 Azure 地圖服務時，您可以在相同頁面上使用不同語言和區域設定載入多個地圖執行個體。 在地圖載入後，您也可以在地圖中更新這些設定。 

查看 Azure 地圖服務[所支援語言](supported-languages.md)的詳細清單。

以下範例會示範語言設為 "fr" 且使用者區域設為 "fr-FR" 的 Azure 地圖服務。

<center>

![Azure 地圖服務當地語系化](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>設定地圖檢視

您可以透過程式設計方式將 Azure 地圖服務和 Google Maps 中的動態地圖移至新的地理位置。 若要這麼做，請在 JavaScript 中呼叫適當的函式。 範例將說明如何讓地圖顯示衛星空照圖影像，將地圖置中於某個位置，以及在 Google Maps 中將縮放層級變更為 15。 使用的位置座標如下：經度：-111.0225 和緯度：35.0272。

> [!NOTE]
> Google 地圖所使用的地圖底圖維度為 256 像素，而 Azure 地圖服務則使用較大的 512 像素地圖底圖。 因此，在載入和 Google Maps 相同的地圖區域時，Azure 地圖服務需要較少的網路要求數目。 由於地圖底圖金字塔在地圖控制項中的運作方式所致，您在使用 Azure 地圖服務時，必須將 Google Maps 中所用的縮放層級減去數字 1。 此算數運算可確保 Azure 地圖服務中較大的地圖底圖能和 Google Maps 呈現相同的地圖區域。

**之前：Google 地圖**

使用 `setOptions` 方法移動 Google Maps 地圖控制項。 此方法可讓您指定地圖的中心和縮放層級。

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Google 地圖設定的檢視](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**之後：Azure 地圖服務**

在 Azure 地圖服務中，使用 `setCamera` 方法變更地圖位置，並使用 `setStyle` 方法變更地圖樣式。 Azure 地圖服務中的座標採用「經度,緯度」格式，而且縮放層級值會減一。

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

<center>

![Azure 地圖服務設定的檢視](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**其他資源：**

- [選擇地圖樣式](choose-map-style.md)
- [支援的地圖樣式](supported-map-styles.md)

### <a name="adding-a-marker"></a>新增標記

Azure 地圖服務有多種方式可在地圖上呈現位置點資料：

- **HTML 標記** – 使用傳統 DOM 元素來呈現位置點。 HTML 標記支援拖曳。
- **符號圖層** – 會在 WebGL 內容中以圖示或文字呈現位置點。
- **泡泡圖層** – 在地圖上以圓圈呈現位置點。 圓圈半徑可根據資料中的屬性來縮放。

在 WebGL 內容中呈現符號圖層和泡泡圖層。 這兩個層級可以在地圖上呈現大型的點集合。 這些圖層會要求將資料儲存在資料來源中。 在 `ready` 事件引發之後，就應該將資料來源和呈現圖層新增至地圖中。 HTML 標記會在頁面中呈現為 DOM 元素，而且不會使用資料來源。 頁面所擁有的 DOM 元素越多，頁面就會變得越慢。 如果要在地圖上呈現超過幾百個位置點，建議您改用其中一個呈現圖層。

讓我們在地圖上新增一個標記，並以數字 10 作為標籤重疊在上面。 使用經度：-0.2 和緯度：51.5。

**之前：Google 地圖**

使用 Google Maps 時，可使用 `google.maps.Marker` 類別將標記新增至地圖，也可以將地圖指定為其中一個選項。

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

![Google 地圖標記](media/migrate-google-maps-web-app/google-maps-marker.png)</center>

**之後：使用 HTML 標記的 Azure 地圖服務**

在 Azure 地圖服務中，HTML 標記可用來顯示地圖上的位置點。 建議您將 HTML 標記用於只需要在地圖上顯示少量位置點的應用程式。 若要使用 HTML 標記，請建立 `atlas.HtmlMarker` 類別的實例。 設定文字和位置選項，然後使用 `map.markers.add` 方法將標記新增至地圖。

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Azure 地圖服務的 HTML 標記](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**之後：使用符號圖層的 Azure 地圖服務**

針對符號圖層，請將資料加入至資料來源。 將資料來源附加至圖層。 此外，還應該在 `ready` 事件引發之後，將資料來源和圖層新增至地圖中。 若要在符號上方呈現唯一文字值，文字資訊必須儲存為資料點的屬性。 而且必須在圖層的 `textField` 選項中參考該屬性。 相較於使用 HTML 標記，這種方式的工作量較多，但效能較高。

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

![Azure 地圖服務的符號圖層](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**其他資源：**

- [建立資料來源](create-data-source-web-sdk.md)
- [新增符號圖層](map-add-pin.md)
- [新增泡泡圖層](map-add-bubble-layer.md)
- [群集位置點資料](clustering-point-data-web-sdk.md)
- [新增 HTML 標記](map-add-custom-html.md)
- [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)
- [符號圖層圖示選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)
- [符號圖層文字選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions)
- [HTML 標記類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker)
- [HTML 標記選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-marker"></a>新增自訂標記

您可以使用自訂影像來代表地圖上的位置點。 下方地圖會使用自訂影像在地圖上顯示位置點。 點顯示在緯度：51.5 及經度：-0.2。 錨點會位移標記位置，讓圖釘圖示的尖端對準地圖上的正確位置。

<center>

![黃色圖釘影像](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png</center>

**之前：Google 地圖**

藉由指定包含影像 `url` 的 `Icon` 物件來建立自訂標記。 指定 `anchor` 點，將圖釘影像的位置點與地圖上的座標對齊。 Google Maps 中的錨點值會相對於影像左上角。

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```

<center>

![Google 地圖的自訂標記](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**之後：使用 HTML 標記的 Azure 地圖服務**

若要自訂 HTML 標記，可將 HTML `string` 或 `HTMLElement` 傳遞至標記的 `htmlContent` 選項。 使用 `anchor` 選項，即可指定標記的相對位置 (相對於位置座標)。 將其中一個已定義的參考點 (共九個) 指派給 `anchor` 選項。 這些定義的參考點包括：「中心」、「頂端」、「底部」、「左側」、「右側」、「左上角」、「右上角」、「左下角」、「右下角」。 根據預設，內容會錨定到 html 內容的底部中心點。 為了讓您更輕鬆地從 Google Maps 遷移程式碼，請將 `anchor` 設定為 [左上角]，然後搭配使用 `pixelOffset` 選項與 Google Maps 中使用的相同位移。 Azure 地圖服務中的位移會與 Google Maps 中的位移方向相反。 因此，請將位移乘上負一。

> [!TIP]
> 將 `pointer-events:none` 新增為 html 內容上的樣式，以在 Microsoft Edge 中停用會顯示不必要圖示的預設拖曳行為。

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Azure 地圖服務的自訂 HTML 標記](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**之後：使用符號圖層的 Azure 地圖服務**

Azure 地圖服務中的符號圖層也支援自訂影像。 首先，將影像載入至地圖資源，並將其指派為唯一的識別碼。 參考符號圖層中的影像。 使用 `offset` 選項將影像對齊地圖上的正確位置點。 使用 `anchor` 選項，即可指定符號的相對位置 (相對於位置座標)。 使用九個已定義參考點的其中一個。 這些參考點包括：「中心」、「頂端」、「底部」、「左側」、「右側」、「左上角」、「右上角」、「左下角」、「右下角」。 根據預設，內容會錨定到 html 內容的底部中心點。 為了讓您更輕鬆地從 Google Maps 遷移程式碼，請將 `anchor` 設定為 [左上角]，然後搭配使用 `offset` 選項與 Google Maps 中使用的相同位移。 Azure 地圖服務中的位移會與 Google Maps 中的位移方向相反。 因此，請將位移乘上負一。

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

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

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

![Azure 地圖服務的自訂圖示符號圖層](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> 若要呈現進階的自訂位置點，請同時使用多個呈現圖層。 例如，假設您想要在不同顏色的圓圈上放置多個圖示相同的圖釘。 您不必為每個顏色重疊建立一堆影像，而是可以在泡泡圖層上方加入符號圖層。 讓圖釘參考相同的資料來源。 此方法會比建立及維護一堆不同影像來得有效率。

**其他資源：**

- [建立資料來源](create-data-source-web-sdk.md)
- [新增符號圖層](map-add-pin.md)
- [新增 HTML 標記](map-add-custom-html.md)
- [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)
- [符號圖層圖示選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)
- [符號圖層文字選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions)
- [HTML 標記類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker)
- [HTML 標記選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>新增聚合線條

使用聚合線條來代表地圖上的一條線或路徑。 讓我們在地圖上建立以虛線呈現的聚合線條。

**之前：Google 地圖**

聚合線條類別會接受一組選項。 將座標陣列傳遞至聚合線條的 `path` 選項中。

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

<center>

![Google 地圖的聚合線條](media/migrate-google-maps-web-app/google-maps-polyline.png)</center>

**之後：Azure 地圖服務**

聚合線條稱為 `LineString` 或 `MultiLineString` 物件。 這些物件可新增至資料來源，並使用線條圖層來加以呈現。 將 `LineString` 新增至資料來源，然後將資料來源新增至 `LineLayer` 來加以呈現。

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

![Azure 地圖服務的聚合線條](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**其他資源：**

- [將線條新增至地圖](map-add-line-layer.md)
- [線條圖層選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions)
- [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>新增多邊形

Azure 地圖服務和 Google Maps 針對多邊形提供了類似的支援。 多邊形可用來代表地圖上的區域。 下列範例說明如何根據地圖的中心座標，建立會形成三角形的多邊形。

**之前：Google 地圖**

多邊形類別會接受一組選項。 將座標陣列傳遞至多邊形的 `paths` 選項中。

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

<center>

![Google 地圖的多邊形](media/migrate-google-maps-web-app/google-maps-polygon.png)</center>

**之後：Azure 地圖服務**

將 `Polygon` 或 `MultiPolygon` 物件新增至資料來源。 使用圖層在地圖上呈現物件。 使用多邊形圖層呈現多邊形的區域。 此外，使用線條圖層來呈現多邊形的外框。

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

![Azure 地圖服務的多邊形](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**其他資源：**

- [將多邊形新增至地圖](map-add-shape.md)
- [將圓形新增至地圖](map-add-shape.md#add-a-circle-to-the-map)
- [多邊形圖層選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
- [線條圖層選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions)
- [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>顯示資訊視窗

在 Google Maps 中，實體的其他資訊可在地圖上以 `google.maps.InfoWindow` 類別顯示。 在 Azure 地圖服務中，此功能則可以使用 `atlas.Popup` 類別來實現。 下一個範例會將標記新增至地圖。 當您按一下標記時，資訊視窗或快顯視窗就會隨即顯示。

**之前：Google 地圖**

使用 `google.maps.InfoWindow` 建構函式來具現化資訊視窗。

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```

<center>

![Google Maps 的快顯視窗](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**之後：Azure 地圖服務**

讓我們使用快顯來顯示位置的其他資訊。 將 HTML 的 `string` 或 `HTMLElement` 物件傳遞至快顯視窗的 `content` 選項。 如有需要，快顯視窗可獨立於任何圖形之外來顯示。 因此，快顯視窗需要指定 `position` 值。 指定 `position` 值。 若要顯示快顯視窗，請呼叫 `open` 方法，並傳入要在其中顯示快顯視窗的 `map`。

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
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

![Azure 地圖服務的快顯視窗](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> 若要使用符號、泡泡、線條或多邊形圖層來進行相同操作，您可以將所選的圖層傳遞至地圖事件程式碼 (而不是標記)。

**其他資源：**

- [新增快顯](map-add-popup.md)
- [具有媒體內容的快顯視窗](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [圖形上的快顯視窗](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [重複使用具有多個圖釘的快顯視窗](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Popup 類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)
- [快顯視窗選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="import-a-geojson-file"></a>匯入 GeoJSON 檔案

Google Maps 支援透過 `google.maps.Data` 類別來載入 GeoJSON 資料和動態設定其樣式。 這個類別的功能會更加貼齊 Azure 地圖服務的資料驅動樣式。 但還是有一個重要的差異。 使用 Google Maps 時，您可以指定回呼函式。 商務邏輯可用來為 UI 執行緒中個別處理的每項功能設定樣式。 但是，在 Azure 地圖服務中，圖層則支援將資料驅動運算式指定為樣式選項。 這些運算式會在呈現時於個別執行緒上進行處理。 Azure 地圖服務方法可改善呈現的效能。 顯而易見的優勢是，這可讓更大的資料集以更快的速度呈現。

下列範例會從 USGS 載入過去七天內所有地震的 GeoJSON 摘要。 地震資料會在地圖上以經過縮放的圓形來呈現。 每個圓形的顏色和縮放大小取決於每個地震的規模，而規模的資料則會儲存在資料集內每項功能的 `"mag"` 屬性中。 如果規模大於或等於 5，則圓形會是紅色。 如果大於或等於 3，但小於5，則圓形會是橘色。 如果小於 3，則圓形會是綠色。 每個圓形的半徑會是規模的指數乘以 0.1。

**之前：Google 地圖**

在 `map.data.setStyle` 方法中指定單一回呼函式。 在回呼函式內，將商務邏輯套用至每項功能。 使用 `map.data.loadGeoJson` 方法載入 GeoJSON 摘要。

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
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps 的 GeoJSON](media/migrate-google-maps-web-app/google-maps-geojson.png)</center>

**之後：Azure 地圖服務**

GeoJSON 是 Azure 地圖服務中的基底資料類型。 使用 `datasource.importFromUrl` 方法將其匯入資料來源。 使用泡泡圖層。 泡泡圖層會提供根據資料來源中的功能屬性呈現已縮放圓形的功能。 商務邏輯不會使用回呼函數，而是會轉換成運算式，並傳遞至樣式選項。 運算式會定義商務邏輯的運作方式。 運算式可傳遞至另一個執行緒，並根據功能資料進行評估。 可以將多個資料來源和層級新增至 Azure 地圖服務，每個都有不同的商務邏輯。 這項功能可讓您以不同的方式在地圖上呈現多個資料集。

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
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
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

![Azure 地圖服務的 GeoJSON](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**其他資源：**

- [新增符號圖層](map-add-pin.md)
- [新增泡泡圖層](map-add-bubble-layer.md)
- [群集位置點資料](clustering-point-data-web-sdk.md)
- [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>標記群集

視覺化地圖上許多資料點時，資料點可能會彼此重疊。 重疊會使地圖看起來雜亂，而且地圖會變得難以閱讀和使用。 群集位置點資料的程序會結合彼此接近的資料點，並在地圖上以單一群集資料點的方式加以表示。 當使用者放大地圖時，群集便會分解成個別的資料點。 群集資料點可改善使用者體驗和地圖效能。

在下列範例中，程式碼會載入過去一週地震資料的 GeoJSON 摘要，並將其新增至地圖。 群集會呈現為已縮放和有顏色的圓形。 圓形的縮放和色彩取決於其所包含的點數目。

> [!NOTE]
> Google Maps 和 Azure 地圖服務會使用稍微不同的群集演算法。 因此，群集內的位置點分佈有時會不同。

**之前：Google 地圖**

使用 MarkerCluster 程式庫來群集標記。 群集圖示僅限於影像，並以一到五的數字作為名稱。 並且裝載在相同的目錄中。

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
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps 的群集](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**之後：Azure 地圖服務**

新增和管理資料來源中的資料。 連結資料來源和層級，然後呈現資料。 Azure 地圖服務中的 `DataSource` 類別會提供數個群集選項。

- `cluster` - 讓資料來源群集位置點資料。
- `clusterRadius` - 用來群集位置點的半徑 (以像素為單位)。
- `clusterMaxZoom` - 要執行群集的最大縮放層級。 如果您放大的倍數超過此層級，所有位置點都會呈現為符號。
- `clusterProperties` - 定義會使用運算式針對每個群集內的所有位置點進行計算，並新增至每個群集位置點屬性的自訂屬性。

啟用群集時，資料來源會將已群集和未群集的資料點傳送到圖層來呈現。 資料來源能夠群集數十萬個資料點。 已群集的資料點具有下列屬性：

| 屬性名稱             | 類型    | 描述   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | 指出功能是否代表群集。 |
| `cluster_id`              | 字串  | 群集的唯一識別碼，可與 DataSource 的 `getClusterExpansionZoom`、`getClusterChildren` 和 `getClusterLeaves` 方法搭配使用。 |
| `point_count`             | number  | 群集包含的位置點數目。  |
| `point_count_abbreviated` | 字串  | 會將較長的 `point_count` 值縮寫的字串。 (例如，4,000 變成 4K)  |

`DataSource` 類別具有下列協助程式函式，可使用 `cluster_id` 來存取關於群集的其他資訊。

| 方法 | 傳回類型 | 描述 |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 擷取給定群集在下一個縮放層級上的子系。 這些子系可以是圖形和子群集的組合。 子群集會是屬性符合 ClusteredProperties 的功能。 |
| `getClusterExpansionZoom(clusterId: number)` | Promise&lt;number&gt; | 計算群集將開始擴大或分解的臨界縮放層級。 |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 擷取群集中的所有位置點。 設定 `limit` 可傳回位置點的子集，使用 `offset` 則可逐頁查看位置點。 |

在地圖上呈現已群集的資料時，最好的方式往往是使用兩個以上的圖層。 下列範例會使用三個圖層。 泡泡圖層會根據群集大小繪製經過縮放的彩色圓形。 符號圖層會以文字呈現群集大小。 此外，其會使用第二個符號圖層來呈現未群集的位置點。 還有許多其他方式可呈現群集資料。 如需詳細資訊，請參閱[群集位置點資料](clustering-point-data-web-sdk.md)文件。

在 Azure 地圖服務中，您可以在 `DataSource` 類別上使用 `importDataFromUrl` 函式來直接匯入 GeoJSON 資料。

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

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
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

![Azure 地圖服務的群集](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**其他資源：**

- [新增符號圖層](map-add-pin.md)
- [新增泡泡圖層](map-add-bubble-layer.md)
- [群集位置點資料](clustering-point-data-web-sdk.md)
- [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>新增熱度圖

熱度圖 (也稱為點密度圖) 是一種資料視覺效果。 其用來以色彩範圍表示資料密度。 而且這些圖通常用來顯示地圖上的資料「作用點」。 熱度圖十分適合用來呈現大型的位置點資料集。

下列範例會從 USGS 載入過去一個月所有地震的 GeoJSON 摘要，並以經過加權的熱度圖來呈現。 `"mag"` 屬性會用來作為權數。

**之前：Google 地圖**

若要建立熱度圖，可藉由將 `&libraries=visualization` 新增至 API 指令碼 URL 來載入「視覺效果」程式庫。 Google Maps 中的熱度圖圖層不會直接支援 GeoJSON 資料。 您必須先下載資料，並將其轉換成加權資料點的陣列：

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

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps 熱度圖](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**之後：Azure 地圖服務**

將 GeoJSON 資料載入至資料來源，並將資料來源連線至熱度圖圖層。 將作為權數的屬性可使用運算式傳遞至 `weight` 選項。 您可以在 `DataSource` 類別上使用 `importDataFromUrl` 函式，直接將 GeoJSON 資料匯入 Azure 地圖服務。

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
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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
                    weight: ['get', 'mag'],
                    intensity: 0.005,
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

![Azure 地圖服務的熱度圖](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**其他資源：**

- [新增熱度圖圖層](map-add-heat-map-layer.md)
- [熱度圖圖層類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
- [熱度圖圖層選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
- [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>覆蓋地圖底圖圖層

Azure 地圖服務中的地圖底圖圖層在 Google 地圖中稱為影像覆蓋。 地圖底圖圖層可讓您覆蓋已分解成較小地圖底圖影像的大型影像，並能夠與地圖底圖系統契合。 這種方式通常用來覆蓋大型影像或大型資料集。

下列範例會覆蓋來自愛荷華州立大學 Iowa Environmental Mesonet 的氣象雷達地圖底圖圖層。

**之前：Google Maps**

在 Google Maps 中，您可以使用 `google.maps.ImageMapType` 類別來建立地圖底圖圖層。

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

<center>

![Google Maps 的地圖底圖圖層](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**之後：Azure 地圖服務**

將地圖底圖圖層新增至地圖中，其方式與任何其他圖層大致相同。 使用具有 x、y、縮放預留位置 (`{x}`、`{y}`、`{z}`) 的格式化 URL 來讓圖層知道要於何處存取地圖底圖。 Azure 地圖服務的地圖底圖圖層也支援 `{quadkey}`、`{bbox-epsg-3857}` 和 `{subdomain}` 預留位置。

> [!TIP]
> 在 Azure 地圖服務中，您可以輕鬆地將圖層放到其他圖層下面來呈現，其中也包括基底地圖圖層。 地圖底圖圖層通常最好是放到地圖標籤下面來呈現，以方便辨識。 `map.layers.add` 方法會採用第二個參數，也就是要在其下面插入新圖層的目標圖層所具有的識別碼。 若要在地圖標籤下方插入地圖底圖圖層，請使用此程式碼：`map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Azure 地圖服務的地圖底圖圖層](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> 您可以使用地圖的 `transformRequest` 選項來擷取地圖底圖要求。 這可讓您視需要在要求中修改或新增標頭。

**其他資源：**

- [新增地圖底圖圖層](map-add-tile-layer.md)
- [地圖底圖圖層類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer)
- [地圖底圖圖層選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>顯示流量資料

Azure 地圖服務和 Google 地圖都可以覆蓋交通資料。

**之前：Google 地圖**

使用交通圖層來覆蓋地圖上的交通資料。

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![Google 地圖的交通](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**之後：Azure 地圖服務**

Azure 地圖服務提供數個不同的選項來顯示交通。 在地圖上將交通事件 (例如道路封閉和事故) 顯示為圖示。 覆蓋地圖上的交通流量並以色彩編碼道路。 色彩可以根據發佈的速度限制來修改，並且相對於正常預期的延遲或絕對延遲。 Azure 地圖服務中的事件資料會每分鐘更新一次，車流資料則會每兩分鐘更新一次。

為 `setTraffic` 選項指派所需的值。

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Azure 地圖服務的交通](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

如果您在 Azure 地圖服務中按一下其中一個交通圖示，則會在快顯視窗中顯示其他資訊。

<center>

![Azure 地圖服務的交通事件](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**其他資源：**

- [在地圖上顯示路況](map-show-traffic.md)
- [交通的覆蓋選項](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>新增地面覆蓋

Azure 地圖服務和 Google Maps 都支援在地圖上覆蓋有地理參考的影像。 有地理參考的影像可在您平移和縮放地圖時，隨之移動和縮放。 在 Google Maps 中，有地理參考的影像稱為地面覆蓋，而在 Azure 地圖服務中則稱為影像圖層。 這些項目非常適合用來建置樓層平面圖、覆蓋舊地圖或來自無人機的影像。

**之前：Google 地圖**

指定您要覆蓋的影像 URL，以及用來繫結地圖上影像的周框方塊。 這個範例會在地圖上覆蓋 [1922 年紐澤西紐瓦克](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg)的地圖影像。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

在瀏覽器中執行此程式碼會顯示如下圖所示的地圖：

<center>

![Google Maps 的影像覆蓋](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**之後：Azure 地圖服務**

使用 `atlas.layer.ImageLayer` 類別來覆蓋有地理參考的影像。 此類別需要影像的 URL 和影像四個角落所組成的一組座標。 影像必須裝載於相同網域上或啟用 CORS。

> [!TIP]
> 如果您只有北側、南側、東側、西側和旋轉資訊，而且您沒有影像各個角落的座標，則可以使用靜態的 [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-) 方法。

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

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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

![Azure 地圖服務的影像覆蓋](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**其他資源：**

- [覆蓋影像](map-add-image-layer.md)
- [影像圖層類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>將 KML 資料新增至地圖

Azure 和 Google 地圖都可以在地圖上匯入和轉譯 KML、KMZ 和 GeoRSS 資料。 Azure 地圖服務也支援 GPX、GML、空間 CSV 檔案、GeoJSON、Well Known Text (WKT)、Web Mapping Services (WMS)、Web-Mapping Tile Services (WMTS) 和 Web Feature Services (WFS)。 Azure 地圖服務會在本機將檔案讀取到記憶體中，而且在大部分的情況下可以處理更大的 KML 檔案。 

**之前：Google 地圖**


```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(0, 0),
                zoom: 1
            });

             var layer = new google.maps.KmlLayer({
              url: 'https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml',
              map: map
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

在瀏覽器中執行此程式碼會顯示如下圖所示的地圖：

<center>

![Google Maps KML](media/migrate-google-maps-web-app/google-maps-kml.png)</center>

**之後：Azure 地圖服務**

在 Azure 地圖服務中，GeoJSON 是 Web SDK 中使用的主要資料格式，您可使用[空間 IO 模組](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)輕鬆地將其他空間資料格式整合在其中。 此模組具有可供讀取和寫入空間資料的函式，也包含可輕鬆地從這些空間資料格式轉譯資料的簡單資料層。 若要讀取空間資料檔案中的資料，以字串或 Blob 的形式將 URL 或原始資料傳入 `atlas.io.read` 函式。 這會從檔案傳回所有已剖析的資料，然後再將該檔案新增至地圖。 相較於大部分的空間資料格式，KML 稍微複雜一點，因為其包含更多樣式資訊。 `SpatialDataLayer` 類別支援轉譯大多數的樣式，不過，在載入特徵資料之前，必須先將圖示影像載入地圖中，且地面覆蓋必須分別新增為地圖的圖層。 透過 URL 載入資料時，應將其裝載於已啟用 COR 的端點上，或應將 Proxy 服務當作選項傳入讀取函式。 

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

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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
                atlas.io.read('https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml').then(async r => {
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

![Azure 地圖服務 KML](media/migrate-google-maps-web-app/azure-maps-kml.png)</center>

**其他資源：**

- [atlas.io.read 函式](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
- [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
- [SimpleDataLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

## <a name="additional-code-samples"></a>其他程式碼範例

以下是一些與 Google Maps 移轉相關的其他程式碼範例：

- [繪圖工具](map-add-drawing-toolbar.md)
- [將地圖限制為兩指平移](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [限制滾輪縮放](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [建立全螢幕控制項](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**服務：**

- [使用 Azure 地圖服務的服務模組](how-to-use-services-module.md)
- [搜尋興趣點](map-search-location.md)
- [從座標取得資訊 (反向地理編碼)](map-get-information-from-coordinate.md)
- [顯示從甲地到乙地的指示](map-route.md)
- [使用 JQuery UI 的搜尋自動建議](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Google Maps V3 與 Azure 地圖服務 Web SDK 類別的對應

下列附錄會提供 Google Maps V3 中常用類別和 Azure 地圖服務 Web SDK 對等項目的交互參照。

### <a name="core-classes"></a>核心類別

| Google Maps   | Azure 地圖服務  |
|---------------|-------------|
| `google.maps.Map` | [atlas.Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)  |
| `google.maps.InfoWindow` | [atlas.Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)  |
| `google.maps.InfoWindowOptions` | [atlas.PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [atlas.data.Position](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position)  |
| `google.maps.LatLngBounds` | [atlas.data.BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox) |
| `google.maps.MapOptions`  | [atlas.CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions)<br/>[atlas.CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions)<br/>[atlas.ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions)<br/>[atlas.StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)<br/>[atlas.UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions) |
| `google.maps.Point`  | [atlas.Pixel](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel)   |

## <a name="overlay-classes"></a>覆蓋類別

| Google Maps  | Azure 地圖服務  |
|--------------|-------------|
| `google.maps.Marker` | [atlas.HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker)<br/>[atlas.data.Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point)  |
| `google.maps.MarkerOptions`  | [atlas.HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)<br/>[atlas.layer.SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer)<br/>[atlas.SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions)<br/>[atlas.IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)<br/>[atlas.TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions)<br/>[atlas.layer.BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer)<br/>[atlas.BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions) |
| `google.maps.Polygon`  | [atlas.data.Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon)               |
| `google.maps.PolygonOptions` |[atlas.layer.PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer)<br/> [atlas.PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions)<br/> [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer)<br/> [atlas.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions)|
| `google.maps.Polyline` | [atlas.data.LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring)         |
| `google.maps.PolylineOptions` | [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer)<br/>[atlas.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions) |
| `google.maps.Circle`  | 請參閱[將圓形新增至地圖](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [atlas.TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer)         |
| `google.maps.ImageMapTypeOptions` | [atlas.TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions) |
| `google.maps.GroundOverlay`  | [atlas.layer.ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer)<br/>[atlas.ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions) |

## <a name="service-classes"></a>服務類別

Azure 地圖服務 Web SDK 包含可以個別載入的服務模組。 此模組會使用 Web API 來包裝 Azure 地圖服務 REST 服務，並可在 JavaScript、TypeScript 和 Node.js 應用程式中使用。

| Google Maps | Azure 地圖服務  |
|-------------|-------------|
| `google.maps.Geocoder` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl)  |
| `google.maps.GeocoderRequest`  | [atlas.SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions)<br/>[atlas.SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions)<br/>[atlas.SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions)<br/>[atlas.SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions)<br/>[atlas.SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions)<br/>[atlas.SearchFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions)<br/>[atlas.SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions)<br/>[atlas.SearchNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions)<br/>[atlas.SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions)<br/>[atlas.SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions) |
| `google.maps.DirectionsService`  | [atlas.service.RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl)  |
| `google.maps.DirectionsRequest`  | [atlas.CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions) |
| `google.maps.places.PlacesService` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl)  |

## <a name="libraries"></a>程式庫

程式庫會將其他功能新增至地圖。 其中有許多程式庫會在 Azure 地圖服務的核心 SDK 中。 以下是一些用來取代這些 Google Maps 程式庫的對等類別

| Google Maps           | Azure 地圖服務   |
|-----------------------|--------------|
| 繪圖程式庫       | [繪圖工具模組](set-drawing-options.md) |
| 幾何程式庫      | [atlas.math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math)   |
| 視覺效果程式庫 | [熱度圖圖層](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>後續步驟

深入了解 Azure 地圖服務 Web SDK。

> [!div class="nextstepaction"]
> [如何使用地圖控制項](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [如何使用服務模組](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [如何使用繪圖工具模組](set-drawing-options.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)

> [!div class="nextstepaction"]
> [Azure 地圖服務 Web SDK 服務 API 參考文件](https://docs.microsoft.com/javascript/api/azure-maps-control/)
