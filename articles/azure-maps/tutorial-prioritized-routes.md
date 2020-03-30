---
title: 教學課程：依行進模式尋找多條路線 | Microsoft Azure 地圖服務
description: 在本教學課程中，您將了解如何使用 Microsoft Azure 地圖服務尋找不同行進模式的路線。
author: philmea
ms.author: philmea
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: c7ed2421f468dfbb64c635683a7909b517105bc7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333797"
---
# <a name="tutorial-find-routes-for-different-modes-of-travel-using-azure-maps"></a>教學課程：使用 Azure 地圖服務尋找不同行進模式的路線

本教學課程說明如何使用 Azure 地圖服務帳戶和路線規劃服務。 路線規劃服務可尋找景點的路線 (依行進模式設定優先順序)。 您可以在地圖上顯示兩個不同的路線，一個用於車輛，另一個用於卡車。 由於車輛的高度和重量，或車輛可能運送有危險性的貨物，路線規劃服務會考慮相關限制。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 使用地圖控制項 API 建立新的網頁
> * 在地圖上以視覺方式呈現流量
> * 建立會宣告行進模式的路線查詢
> * 在地圖上顯示多條路線

## <a name="prerequisites"></a>Prerequisites
在繼續作業之前，請依照[建立帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)中的指示選取 S1 定價層。 請依照[取得主要金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的步驟取得帳戶的主要金鑰。 如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](how-to-manage-authentication.md)。

## <a name="create-a-new-map"></a>建立新的地圖

下列步驟顯示如何建立內嵌地圖控制項 API 的靜態 HTML 網頁。

1. 在您的本機機器上建立新檔案，並將其命名為 **MapTruckRoute.html**。
2. 在檔案中新增下列 HTML 元件：

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

    請注意，HTML 標頭包含 Azure 地圖控制項程式庫所裝載的 CSS 和 JavaScript 資源檔案。 請注意頁面本文上的 `onload` 事件，此事件在頁面本文載入時將會呼叫 `GetMap` 函式。 此函式會包含內嵌的 JavaScript 程式碼，以便存取 Azure 地圖服務 API。

3. 在 `GetMap` 函式中新增下列 JavaScript 程式碼。 將字串 `<Your Azure Maps Key>` 取代為您從地圖服務帳戶複製的主要金鑰。

    ```JavaScript
    //Instantiate a map object
    var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
            authType: 'subscriptionKey',
            subscriptionKey: '<Your Azure Maps Key>'
        }
    });
    ```

    `atlas.Map` 類別是 Azure 地圖控制項 API 的元件，可供您控制視覺化互動式網路地圖。

4. 儲存檔案並在瀏覽器中將其開啟。 至此，您已擁有可以進一步發展的基本地圖了。

   ![檢視基本地圖](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>以視覺方式呈現流量

1. 在地圖上新增流量顯示。 地圖 `ready` 事件會等待地圖資源已載入，並且已可安全地與其互動。

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    在地圖 `ready` 事件處理常式中，地圖上的流量設定會設為 `relative`，這是相對於自由流量的道路速度。 您也可以將其設定為道路的 `absolute` 速度，或設定為 `relative-delay`，以顯示不同於自由流量的相對速度。

2. 儲存 **MapTruckRoute.html** 檔案，並重新整理瀏覽器頁面。 如果您與地圖互動並將洛杉磯放大，您應該會看到街道與目前的路況資料。

   ![在地圖上檢視交通情況](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>定義呈現路線的方式

在本教學課程中，會在地圖上計算並呈現兩個路線。 一個路線使用供汽車行駛的道路，另一個路線則使用供卡車行駛的道路。 在呈現時，我們將為路線的起點和終點顯示符號圖示，並以不同的彩色線條表示每個路線的路徑。

1. 在初始化地圖後，請在地圖 `ready` 事件處理常式中新增下列 JavaScript 程式碼。

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('ready', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round'
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
            },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```
    
    在地圖 `ready` 事件處理常式中，會建立資料來源以儲存路線以及起點和終點。 系統會建立線條圖層並將其附加至資料來源，以定義呈現路線的方式。 此外也會使用運算式，從路線特性的屬性中擷取線條的寬度和色彩。 將圖層新增至地圖時，會傳入值為 `'labels'` 的第二個參數，而指定要將此圖層呈現在地圖標籤下方。 如此可確保路線不會遮住道路標籤。 系統會建立符號圖層，並將其附加至資料來源。 此圖層會指定起點和終點的呈現方式。 此案例中已新增運算式，用以從每個點物件的屬性中擷取圖示影像和文字標籤資訊。 
    
2. 在此教學課程中，將起點設在西雅圖的一家虛構公司 Fabrikam，並將終點設在 Microsoft 辦公室。 在地圖 `ready` 事件處理常式中，新增下列程式碼。

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
    });
    ```

    此程式碼會建立兩個 [GeoJSON 物件](https://en.wikipedia.org/wiki/GeoJSON)，代表路線的起點和終點。 `title` 和 `icon` 屬性會新增至每個點。

3. 接著，請新增下列 JavaScript 程式碼，以在地圖中新增起點和終點的圖釘：

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```

    起點和終點會新增至資料來源。 系統會使用 `atlas.data.BoundingBox.fromData` 函式計算起點和終點的週框方塊。 此週框方塊會用來透過 `map.setCamera` 函式設定整個路線的地圖相機檢視。 系統會加入邊框間距，以補償符號圖示的像素維度。

4. 儲存檔案並重新整理瀏覽器，以查看地圖上顯示的圖釘。 現在地圖會以西雅圖作為中心。 您會看到以圓形藍色圖釘標示的起點，和以藍色圖釘標示的終點。

   ![檢視有起點和終點的地圖](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>轉譯依行進模式設定優先順序的路線

本節說明如何使用地圖服務的路線規劃服務 API。 路線規劃服務 API 可根據運輸模式，尋找從給定起點到終點的多條路線。 路線規劃服務會提供 API 來規劃「最快速」  、「最短」  、「最環保」  或「驚心動魄」  的路線。 API 不只會在兩個位置之間規劃路由，還會考慮目前的交通狀況。 

路線規劃 API 也可讓使用者使用 Azure 廣泛的歷史路況資料庫來規劃日後的路線。 API 可針對指定的日期和時間預測某段時間的路線狀況。 如需詳細資訊，請參閱 [GetRoute 指示](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。 

以下所有程式碼區塊都應該新增至**地圖載入 eventListener 內**，以確保會在地圖完整載入之後載入。

1. 在 GetMap 函式中，將下列內容新增至 Javascript 程式碼。

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential` 會建立 `SubscriptionKeyCredentialPolicy`，以使用訂用帳戶金鑰驗證對「Azure 地圖服務」的 HTTP 要求。 `atlas.service.MapsURL.newPipeline()` 會採用 `SubscriptionKeyCredential` 原則，並建立[管線](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)執行個體。 `routeURL` 代表 Azure 地圖服務[路線規劃](https://docs.microsoft.com/rest/api/maps/route)作業的 URL。

2. 設定認證和 URL 之後，請新增下列 JavaScript 程式碼，為載運 USHazmatClass2 類貨物的卡車建構從起點到終點的路線，並顯示結果。

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    上方的程式碼片段會透過 [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) 方法來查詢 Azure 地圖服務的路線規劃服務。 接著，系統會從回應中的 GeoJSON 特性集合擷取路線；這些特性是使用 `geojson.getFeatures()` 方法擷取的。 路線隨後會新增至資料來源。 指數 0 可確保該路線會在資料來源中的任何其他路線之前先呈現。 之所以這麼做，是因為卡車路線計算的速度通常比汽車路線計算慢。 如果卡車路線在汽車路線之後新增至資料來源，則會呈現於汽車路線之上。 系統會將兩個屬性新增至卡車路線，一個呈現為鮮明藍色的筆觸色彩，和一個寬度為九個像素的筆觸。

3. 新增下列 JavaScript 程式碼以建構行車路線，並顯示結果。

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.  
        datasource.add(routeLine);
    });
    ```

    上方的程式碼片段會透過 [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) 方法來查詢 Azure 地圖服務的路線規劃服務。 接著，系統會從回應中的 GeoJSON 特性集合擷取路線；這些特性是使用 `geojson.getFeatures()` 方法擷取的。 路線隨後會新增至資料來源。 系統會將兩個屬性新增至汽車路線，一個紫色的筆觸色彩，和一個寬度為五個像素的筆觸。  

4. 儲存 **MapTruckRoute.html** 檔案並重新整理瀏覽器，以觀察結果。 如果您成功連線到地圖服務的 API，您應該會看到類似下面的地圖。

    ![使用 Azure 路線規劃服務設定優先順序的路線](./media/tutorial-prioritized-routes/prioritized-routes.png)

    卡車路線為藍色粗線，而汽車路線為紫色細線。 汽車路線會透過 I-90 穿越華盛頓湖，期間會經由隧道穿過住宅區底下。 因為隧道接近居住區域，因此不得載運有害廢棄物。 卡車路線 (指定了 USHazmatClass2 貨物類型) 則會引導使用不同的高速公路。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 使用地圖控制項 API 建立新的網頁
> * 在地圖上以視覺方式呈現流量
> * 建立會宣告行進模式的路線查詢
> * 在地圖上顯示多條路線

> [!div class="nextstepaction"]
> [檢視完整的原始程式碼](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [檢視即時範例](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

下一個教學課程示範使用 Azure 地圖服務來建立簡單存放區定位器的程序。

> [!div class="nextstepaction"]
> [使用 Azure 地圖服務建立存放區定位器](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)