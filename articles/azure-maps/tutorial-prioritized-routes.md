---
title: 教學課程：依行進模式尋找多條路線 | Microsoft Azure 地圖服務
description: 此教學課程關於如何使用 Azure 地圖服務來尋找特定旅遊模式的景點路線。 請參閱如何在地圖上顯示多條路線。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 0d57e86088ee472c63b433bde14a0e4316cc20a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91321742"
---
# <a name="tutorial-find-and-display-routes-for-different-modes-of-travel-using-azure-maps"></a>教學課程：使用 Azure 地圖服務尋找並顯示不同移動模式的路線

本教學課程會示範如何使用 Azure 地圖服務[路線規劃服務](https://docs.microsoft.com/rest/api/maps/route)和[地圖控制項](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)，以 `USHazmatClass2` 貨物類型顯示私人車輛和商用車輛 (卡車) 的路線指示。 此外，我們將逐步引導您了解如何在地圖上將即時流量資料視覺化。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 在網頁上建立和顯示地圖控制項
> * 在地圖上呈現即時流量資料
> * 在地圖上要求並顯示私人和商用車輛路線

## <a name="prerequisites"></a>必要條件

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. [建立 Azure 地圖服務帳戶](quick-demo-map-app.md#create-an-azure-maps-account)。

3. [取得主要訂用帳戶金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)，也稱為主要金鑰或訂用帳戶金鑰。 如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](how-to-manage-authentication.md)。

您可以在[這裡](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)取得範例的完整原始程式碼。 您可以在[這裡](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)找到即時範例。

## <a name="create-a-new-web-page-using-the-map-control-api"></a>使用地圖控制項 API 建立新的網頁

下列步驟示範如何在網頁中建立和顯示地圖控制項。

1. 在您的本機機器上建立新檔案，並將其命名為 **MapTruckRoute.html**。
2. 複製下列 HTML 標記並貼到檔案中。

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

     HTML 標頭包含 Azure 地圖控制項程式庫所裝載的 CSS 和 JavaScript 資源檔案。 主體的 `onload` 事件會呼叫 `GetMap` 函式。 在下一個步驟中，我們將新增地圖控制項初始化程式碼。

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

4. 儲存檔案並在瀏覽器中將其開啟。 隨即顯示範例。

    :::image type="content" source="./media/tutorial-prioritized-routes/basic-map.png" alt-text="地圖控制項的基本地圖呈現":::

## <a name="render-real-time-traffic-data-on-a-map"></a>在地圖上呈現即時流量資料

1. 在 `GetMap` 函式中附加下列 JavaScript 程式碼。 此程式碼會實作地圖控制項的 `ready` 事件處理常式。 本教學課程中的其餘程式碼將會放在 `ready` 事件處理常式內。

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    在地圖 `ready` 事件處理常式中，地圖上的流量設定會設為 `relative`，這是相對於自由流量的道路速度。 如需更多流量選項，請參閱 [TrafficOptions 介面](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions?view=azure-maps-typescript-latest&preserve-view=false)。

2. 儲存 **MapTruckRoute.html** 檔案，並重新整理瀏覽器頁面。 如果您放大任何城市 (例如洛杉磯)，您會看到街道顯示目前的交通流量資料。

    :::image type="content" source="./media/tutorial-prioritized-routes/traffic-map.png" alt-text="地圖控制項的基本地圖呈現":::

<a id="queryroutes"></a>

## <a name="define-route-display-rendering"></a>定義路線顯示呈現

在本教學課程中，會在地圖上計算並呈現兩個路線。 第一條路線是針對私人車輛 (汽車) 進行計算。 第二條路線是針對商用車輛 (卡車) 進行計算，以顯示結果之間的差異。 在呈現時，地圖會針對路線的起點和終點顯示符號圖示，並以不同的色彩表示每個路線路徑的路線幾何。 如需新增線條圖層的詳細資訊，請參閱[將線條圖層新增至地圖](map-add-line-layer.md)。 若要深入了解符號圖層，請參閱[將符號圖層新增至地圖](map-add-pin.md)。

1. 在地圖控制項的 `ready` 事件處理常式中，附加下列程式碼。

    ```JavaScript

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

    ```


    在地圖控制項的 `ready` 事件處理常式中，系統會建立資料來源以儲存路線起點和終點。 此外也會使用[運算式](data-driven-style-expressions-web-sdk.md)，從路線特性的屬性中擷取線條的寬度和色彩。 為了確保路線不會涵蓋道路標籤，我們傳遞了第二個參數，其值為 `'labels'`。

    接下來系統會建立符號圖層，並將其附加至資料來源。 此圖層會指定起點和終點的呈現方式，已新增運算式，用以從每個點物件的屬性中取出圖示影像和文字標籤資訊。 若要深入了解運算式，請參閱[資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)一文。

2. 將起點設在西雅圖的一家虛構公司 Fabrikam，並將終點設在 Microsoft 辦公室。  在地圖控制項的 `ready` 事件處理常式中，附加下列程式碼。


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

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });

    ```

    此程式碼會建立兩個 [GeoJSON 點物件](https://en.wikipedia.org/wiki/GeoJSON)，代表起點和終點，此資訊也會新增到資料來源。

    最後一個程式碼區塊會使用起點和終點的經緯度設定相機檢視。 起點和終點會新增至資料來源。 系統會使用 `atlas.data.BoundingBox.fromData` 函式計算起點和終點的週框方塊。 此週框方塊會用來透過 `map.setCamera` 函式設定整個路線的地圖相機檢視。 系統會加入邊框間距，以補償符號圖示的像素維度。 如需地圖控制項的 setCamera 屬性詳細資訊，請參閱 [setCamera(CameraOptions | CameraBoundsOptions & AnimationOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-&preserve-view=false) 屬性。

3. 儲存 **TruckRoute.html** 並重新整理瀏覽器。 現在地圖會以西雅圖作為中心。 淚珠形藍色圖釘會標示起點。 圓形藍色圖釘會標示終點。

   :::image type="content" source="./media/tutorial-prioritized-routes/pins-map.png" alt-text="地圖控制項的基本地圖呈現":::

<a id="multipleroutes"></a>

## <a name="request-and-display-private-and-commercial-vehicle-routes-on-a-map"></a>在地圖上要求並顯示私人和商用車輛路線

本節說明如何使用 Azure 地圖服務路線規劃服務，根據您的運輸模式，取得從某個點到另一個點的方向。 我們將使用兩種運輸模式：卡車和汽車。

>[!TIP]
>路線規劃服務會提供 API，根據距離、交通流量條件和使用的運輸模式，來規劃「最快速」、「最短」、「最環保」或「驚心動魄」的路線。 服務也可讓使用者根據歷程記錄交通流量條件來規劃未來的路由。 使用者可以查看任何指定時間的路由持續期間預測。 如需詳細資訊，請參閱[取得路線方向 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。

1. 在 `GetMap` 函式之控制項的 `ready` 事件處理常式內，將下列內容新增至 JavaScript 程式碼。

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential` 會建立 `SubscriptionKeyCredentialPolicy`，以使用訂用帳戶金鑰驗證對「Azure 地圖服務」的 HTTP 要求。 `atlas.service.MapsURL.newPipeline()` 會採用 `SubscriptionKeyCredential` 原則，並建立[管線](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline)執行個體。 `routeURL` 代表 Azure 地圖服務[路線規劃](https://docs.microsoft.com/rest/api/maps/route)作業的 URL。

2. 設定認證和 URL 之後，請新增下列 JavaScript 程式碼以建構從起點到終點的卡車路線。 此路線是針對裝載 `USHazmatClass2` 類別貨物的卡車而建立和顯示。

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

    上述程式碼會透過 [Azure 地圖服務路線指示 API](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-) 來查詢 Azure 地圖服務路線規劃服務。 接著，系統會從回應中的 GeoJSON 特性集合擷取路線；這些特性是使用 `geojson.getFeatures()` 方法擷取的。 最後，路線會新增至資料來源。 我們會將其新增到 0 的索引處，以確保卡車路線會在資料來源中的任何其他路線之前呈現，因為卡車路線計算通常會比汽車路線計算慢。 如果卡車路線在汽車路線之後新增至資料來源，則會呈現於汽車路線之上。 系統會將兩個屬性新增至卡車路線：一個呈現為藍色的筆觸色彩，和一個寬度為九個像素的筆觸。

    >[!TIP]
    > 若要查看 Azure 地圖服務路線指示 API 的所有可能選項和值，請參閱 [Post 路線指示的 URI 參數](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#uri-parameters)。

3. 現在附加下列 JavaScript 程式碼以建構汽車的行車路線。

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. This will add the car route after the truck route.  
        datasource.add(routeLine);
    });
    ```

    上述程式碼會透過 [Azure 地圖服務路線指示 API](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-) 方法來查詢 Azure 地圖服務路線規劃服務。 接著，系統會從回應中的 GeoJSON 特性集合擷取路線；這些特性是使用 `geojson.getFeatures()` 方法擷取的。 最後，路線會新增至資料來源。 系統會將兩個屬性新增至卡車路線：一個呈現為紫色的筆觸色彩，和一個寬度為五個像素的筆觸。

4. 儲存 **TruckRoute.html** 檔案並重新整理網頁瀏覽器。 地圖現在應該會顯示卡車和汽車路線。

    :::image type="content" source="./media/tutorial-prioritized-routes/prioritized-routes.png" alt-text="地圖控制項的基本地圖呈現":::

    卡車路線會使用粗的藍色線條顯示。 汽車路線會使用細的紫色線條顯示。 汽車路線會透過 I-90 穿越華盛頓湖，期間會經由隧道穿過住宅區底下。 因為隧道接近居住區域，因此不得載運有害廢棄物。 卡車路線 (指定了 `USHazmatClass2` 貨物類型) 則會引導使用不同的高速公路。

您可以在[這裡](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)取得範例的完整原始程式碼。 您可以在[這裡](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)找到即時範例。

您也可以[使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)



## <a name="next-steps"></a>後續步驟

下一個教學課程示範使用 Azure 地圖服務來建立簡單存放區定位器的程序。

> [!div class="nextstepaction"]
> [使用 Azure 地圖服務建立存放區定位器](./tutorial-create-store-locator.md)