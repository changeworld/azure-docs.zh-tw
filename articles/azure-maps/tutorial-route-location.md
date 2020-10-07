---
title: 教學課程：尋找前往位置的路線 | Microsoft Azure 地圖服務
description: 如何尋找路線前往景點的教學課程。 請參閱如何設定地址座標和查詢 Azure 地圖服務路線規劃服務，以取得前往該景點的指示。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 3cb9bee65ab7fa2c29185c40ecb48fd531192187
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321708"
---
# <a name="tutorial-how-to-display-route-directions-using-azure-maps-route-service-and-map-control"></a>教學課程：如何使用 Azure 地圖服務路線規劃服務和地圖控制項顯示路線方向

本教學課程說明如何使用 Azure 地圖服務[路線規劃服務 API](https://docs.microsoft.com/rest/api/maps/route) 以及[地圖控制項](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)顯示從起點到終點的路線方向。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 在網頁上建立和顯示地圖控制項。 
> * 定義[符號圖層](map-add-pin.md)和[線條圖層](map-add-line-layer.md)，以定義路線的顯示呈現。
> * 建立 GeoJSON 物件並新增至地圖，以代表起點和終點。
> * 使用[取得路線指示 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)，取得起點和終點的路線方向。

您可以在[這裡](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)取得範例的完整原始程式碼。 您可以在[這裡](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)找到即時範例。

## <a name="prerequisites"></a>Prerequisites

1. [建立 Azure 地圖服務帳戶](quick-demo-map-app.md#create-an-azure-maps-account)
2. [取得主要訂用帳戶金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)，也稱為主要金鑰或訂用帳戶金鑰。

<a id="getcoordinates"></a>

## <a name="create-and-display-the-map-control"></a>建立和顯示地圖控制項

下列步驟示範如何在網頁中建立和顯示地圖控制項。

1. 在您的本機機器上建立新檔案，並將其命名為 **MapRoute.html**。
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

     :::image type="content" source="./media/tutorial-route-location/basic-map.png" alt-text="地圖控制項的基本地圖呈現":::

## <a name="define-route-display-rendering"></a>定義路線顯示呈現

在本教學課程中，我們將使用線條圖層來呈現路線。 開始和結束點會使用符號圖層呈現。 如需新增線條圖層的詳細資訊，請參閱[將線條圖層新增至地圖](map-add-line-layer.md)。 若要深入了解符號圖層，請參閱[將符號圖層新增至地圖](map-add-pin.md)。

1. 在 `GetMap` 函式中附加下列 JavaScript 程式碼。 此程式碼會實作地圖控制項的 `ready` 事件處理常式。 本教學課程中的其餘程式碼將會放在 `ready` 事件處理常式內。

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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

    在地圖控制項的 `ready` 事件處理常式中，系統會建立資料來源以儲存路線起點和終點。 系統會建立線條圖層並將其附加至資料來源，以定義呈現路線的方式。  為了確保路線不會涵蓋道路標籤，我們傳遞了第二個參數，其值為 `'labels'`。

    接下來系統會建立符號圖層，並將其附加至資料來源。 此圖層會指定起點和終點的呈現方式，已新增運算式，用以從每個點物件的屬性中取出圖示影像和文字標籤資訊。 若要深入了解運算式，請參閱[資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)一文。

2. 將起點設在 Microsoft，並將終點設在西雅圖的加油站。  在地圖控制項的 `ready` 事件處理常式中，附加下列程式碼。

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    此程式碼會建立兩個 [GeoJSON 點物件](https://en.wikipedia.org/wiki/GeoJSON)，代表起點和終點，此資訊也會新增到資料來源。 

    最後一個程式碼區塊會使用起點和終點的經緯度設定相機檢視。 起點和終點會新增至資料來源。 系統會使用 `atlas.data.BoundingBox.fromData` 函式計算起點和終點的週框方塊。 此週框方塊會用來透過 `map.setCamera` 函式設定整個路線的地圖相機檢視。 系統會加入邊框間距，以補償符號圖示的像素維度。 如需地圖控制項的 setCamera 屬性詳細資訊，請參閱 [setCamera(CameraOptions | CameraBoundsOptions & AnimationOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-&preserve-view=false) 屬性。

3. 儲存 **MapRoute.html** 並重新整理瀏覽器。 現在地圖會以西雅圖作為中心。 淚珠形藍色圖釘會標示起點。 圓形藍色圖釘會標示終點。

    :::image type="content" source="./media/tutorial-route-location/map-pins.png" alt-text="地圖控制項的基本地圖呈現":::

<a id="getroute"></a>

## <a name="get-route-directions"></a>取得路線方向

本節說明如何使用 Azure 地圖服務路線指示 API 來取得路線指示，以及從某個點到達另一個點的預估時間。

>[!TIP]
>Azure 地圖服務路線服務提供 API 以根據不同的路線類型 (例如「最快速」、「最短」、「最環保」或「驚心動魄」*thrilling*的路線) 以及根據距離、交通流量條件和使用的運輸模式，來規劃路線。 服務也可讓使用者根據歷程記錄交通流量條件來規劃未來的路由。 使用者可以查看任何指定時間的路由持續期間預測。 如需詳細資訊，請參閱[取得路線方向 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。

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

2. 設定認證和 URL 後，請在控制項的 `ready` 事件處理常式中附加下列程式碼。 此程式碼會從起點到終點來建構路線。 `routeURL` 會要求 Azure 地圖服務路線規劃服務 API 計算路線方向。 接著，系統會使用 `geojson.getFeatures()` 方法擷取回應中的 GeoJSON 特性集合，並將其新增至資料來源。

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. 儲存 **MapRoute.html** 檔案並重新整理網頁瀏覽器。 地圖現在應該會顯示從起點到終點的路線。

     :::image type="content" source="./media/tutorial-route-location/map-route.png" alt-text="地圖控制項的基本地圖呈現":::

您可以在[這裡](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)取得範例的完整原始程式碼。 您可以在[這裡](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)找到即時範例。

## <a name="next-steps"></a>後續步驟

下一個教學課程會示範如何建立具有限制 (例如旅遊模式或貨物類型) 的路線查詢， 然後您可以在同一個地圖上顯示多條路線。

> [!div class="nextstepaction"]
> [尋找不同行進模式的路線](./tutorial-prioritized-routes.md)