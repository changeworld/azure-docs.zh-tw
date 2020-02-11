---
title: 教學課程：尋找前往位置的路線 | Microsoft Azure 地圖服務
description: 本教學課程會示範如何使用 Microsoft Azure 地圖服務的路線服務，在地圖上呈現如何前往某個位置 (景點) 的路線。
author: walsehgal
ms.author: v-musehg
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 3fedb045773cb975d37e2d866862e7863a6232e3
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989632"
---
# <a name="tutorial-route-to-a-point-of-interest-using-azure-maps"></a>教學課程：使用 Azure 地圖服務的景點路線

本教學課程說明如何使用 Azure 地圖服務帳戶和路線規劃服務 SDK，來尋找景點路線。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 使用地圖控制項 API 建立新的網頁
> * 設定地址座標
> * 查詢路線規劃服務，以了解如何前往景點

## <a name="prerequisites"></a>Prerequisites

在繼續作業之前，依照[建立帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)中的指示，您需要 S1 定價層的訂用帳戶。 請依照[取得主要金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的步驟取得帳戶的主要金鑰。 如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](how-to-manage-authentication.md)。

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>建立新的地圖

下列步驟顯示如何建立內嵌地圖控制項 API 的靜態 HTML 網頁。

1. 在您的本機機器上建立新檔案，並將其命名為 **MapRoute.html**。
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

    `atlas.Map` 是 Azure 地圖控制項 API 的元件，可供您控制視覺化互動式網路地圖。

4. 儲存檔案並在瀏覽器中將其開啟。 至此，您已擁有可以進一步發展的基本地圖了。

   ![檢視基本地圖](media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>定義呈現路線的方式

在本教學課程中，將使用路線起點和終點的符號圖示以及路線路徑的線條來呈現簡易路線。

1. 在地圖完成初始化後，請新增下列 JavaScript 程式碼。

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
    
    在地圖 `ready` 事件處理常式中，會建立資料來源以儲存路線以及起點和終點。 系統會建立線條圖層並將其附加至資料來源，以定義呈現路線的方式。 路線會呈現鮮明的藍色。 其寬度為五個像素，並採用圓角的線條聯結和線蓋。 將圖層新增至地圖時，會傳入值為 `'labels'` 的第二個參數，而指定要將此圖層呈現在地圖標籤下方。 這樣可以確保路線不會遮住道路標籤。 系統會建立符號圖層，並將其附加至資料來源。 此圖層會指定起點和終點的呈現方式。 此案例中已新增運算式，用以從每個點物件的屬性中擷取圖示影像和文字標籤資訊。 
    
2. 在此教學課程中，將起點設在 Microsoft，並將終點設在西雅圖的加油站。 在地圖 `ready` 事件處理常式中，新增下列程式碼。

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

    此程式碼會建立兩個 [GeoJSON 點物件](https://en.wikipedia.org/wiki/GeoJSON)以代表路線的起點和終點，且會為資料來源加上點。 `title` 和 `icon` 屬性會新增至每個點。 最後一個區塊會使用起點和終點的經緯度，以地圖 [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 屬性設定相機檢視。

3. 儲存 **MapRoute.html** 檔案並重新整理瀏覽器。 現在地圖會以西雅圖作為中心，而且您會看到以藍色圖釘標示的起點，和以圓形藍色圖釘標示的終點。

   ![在地圖上檢視路線的起點和終點](media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>取得指示

本節說明如何使用 Azure 地圖服務的路線規劃服務 API。 路線規劃服務 API 會尋找從給定起點到終點的路線。 此服務中有相關 API 可用來規劃兩個位置之間「最快速」  、「最短」  、「最環保」  或「驚心動魄」  的路線。 此服務也可讓使用者使用 Azure 廣泛的歷史路況資料庫來規劃日後的路線。 使用者可以查看任何所選日期和時間的路線時間預測。 如需詳細資訊，請參閱[取得路線指示](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。 以下所有功能都應該新增至**地圖備妥 eventListener 內**，以確保它們會在地圖資源可供存取之後載入。

1. 在 GetMap 函式中，將下列內容新增至 JavaScript 程式碼。

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential` 會建立 `SubscriptionKeyCredentialPolicy`，以使用訂用帳戶金鑰驗證對「Azure 地圖服務」的 HTTP 要求。 `atlas.service.MapsURL.newPipeline()` 會採用 `SubscriptionKeyCredential` 原則，並建立[管線](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)執行個體。 `routeURL` 代表 Azure 地圖服務[路線規劃](https://docs.microsoft.com/rest/api/maps/route)作業的 URL。

2. 設定認證和 URL 之後，請新增下列 JavaScript 程式碼以建構從起點到終點的路線。 `routeURL` 會要求 Azure 地圖服務的路線規劃服務計算路線方向。 接著，系統會使用 `geojson.getFeatures()` 方法擷取回應中的 GeoJSON 特性集合，並將其新增至資料來源。

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

3. 儲存 **MapRoute.html** 檔案並重新整理網頁瀏覽器。 如果您成功連線到地圖服務的 API，您應該會看到類似下面的地圖。

    ![Azure 地圖控制項和路線規劃服務](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 使用地圖控制項 API 建立新的網頁
> * 設定地址座標
> * 查詢路線規劃服務以了解如何前往景點

> [!div class="nextstepaction"]
> [檢視完整的原始程式碼](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

> [!div class="nextstepaction"]
> [檢視即時範例](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

下一個教學課程會示範如何建立具有限制 (例如，行進模式或貨物類型) 的路線查詢 ，然後在同一個地圖上顯示多條路線。

> [!div class="nextstepaction"]
> [尋找不同行進模式的路線](./tutorial-prioritized-routes.md)
