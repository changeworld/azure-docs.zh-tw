---
title: 如何使用 Azure 地圖服務空間 IO 模組 |Microsoft Azure 對應
description: 瞭解如何使用 Azure 地圖服務 Web SDK 所提供的空間 IO 模組。 此課程模組提供強大的功能，可讓開發人員輕鬆地將空間資料與 Azure 地圖服務 web sdk 整合。
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/28/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: cd64c80acceb1542c080fc45efbce59f287d448a
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680692"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>如何使用 Azure 地圖服務空間 IO 模組

Azure 地圖服務 Web SDK 提供 **空間 IO 模組**，使用 JavaScript 或 TypeScript 將空間資料與 AZURE 地圖服務 Web SDK 整合。 此課程模組中的健全功能可讓開發人員：

- [讀取和寫入一般空間資料檔](spatial-io-read-write-spatial-data.md)。 支援的檔案格式包括： KML、KMZ、GPX、GeoRSS、GML、GeoJSON 和 CSV 檔案，其中包含具有空間資訊的資料行。 也支援 Well-Known 文字 (WKT) 。
- [連接至開放地理空間協會 (OGC) services，並與 Azure 地圖服務 WEB SDK 整合。重迭 Web 地圖服務 (WMS) 和 Web 地圖磚服務 (WMTS) 為地圖上的圖層](spatial-io-add-ogc-map-layer.md)。
- [查詢 Web 功能服務中的資料 (WFS) ](spatial-io-connect-wfs-service.md)。
- [包含樣式資訊並使用基本程式碼自動轉譯的複雜資料集](spatial-io-add-simple-data-layer.md)。
- [利用高速 XML 和分隔的檔案讀取器和寫入器類別](spatial-io-core-operations.md)。

在本指南中，我們將瞭解如何在 web 應用程式中整合和使用空間 IO 模組。

這段影片提供 Azure 地圖服務 Web SDK 中空間 IO 模組的總覽。

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Easily-integrate-spatial-data-into-the-Azure-Maps/player?format=ny]

> [!WARNING]
> 只使用來自您信任來源的資料和服務，特別是從另一個網域參考資料時。 空間 IO 模組會採取步驟來將風險降至最低，但是最安全的方法也不允許任何 danagerous 的資料進入您的應用程式。 

## <a name="prerequisites"></a>必要條件

在您可以使用空間 IO 模組之前，您必須先 [建立 Azure 地圖服務帳戶](./quick-demo-map-app.md#create-an-azure-maps-account) ，並 [取得您帳戶的主要訂](./quick-demo-map-app.md#get-the-primary-key-for-your-account)用帳戶金鑰。

## <a name="installing-the-spatial-io-module"></a>安裝空間 IO 模組

您可以使用下列兩個選項的其中一個來載入 Azure 地圖服務空間 IO 模組：

* 全域裝載的 Azure CDN，適用于 Azure 地圖服務空間 IO 模組。 針對此選項，您可以在 HTML 檔案的元素中加入 JavaScript 的參考 `<head>` 。

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* [Azure 地圖服務-空間-io](https://www.npmjs.com/package/azure-maps-spatial-io)的原始程式碼可以在本機載入，然後與您的應用程式一起裝載。 此套件也包含 TypeScript 定義。 針對此選項，請使用下列命令來安裝套件：

    ```sh
    npm install azure-maps-spatial-io
    ```

    然後，在 HTML 檔案的元素中加入 JavaScript 的參考 `<head>` ：

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>使用空間 IO 模組

1. 建立新的 HTML 檔案。

2. 載入 Azure 地圖服務 Web SDK 並初始化地圖控制項。 如需詳細資料，請參閱 [Azure 地圖服務地圖控制項](./how-to-use-map-control.md) 指南。 完成此步驟之後，您的 HTML 檔案看起來應該像這樣：

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <script type='text/javascript'>

            var map;

            function GetMap() {
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
                map.events.add('ready', function() {

                    // Write your code here to make sure it runs once the map resources are ready

                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

2. 載入 Azure 地圖服務空間 IO 模組。 針對此練習，請針對 Azure 地圖服務空間 IO 模組使用 CDN。 將下列參考新增至 HTML 檔案的 `<head>` 元素：

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. 初始化 `datasource` ，並將資料來源加入至對應。 初始化 `layer` ，並將資料來源加入至地圖圖層。 然後，轉譯資料來源和圖層。 當您在下一個步驟中向下移動以查看完整的程式碼之前，請先思考放置資料來源和分層程式碼片段的最佳位置。 回想一下，在以程式設計方式操作地圖之前，我們應該先等待地圖資源就緒。

    ```javascript
    var datasource, layer;
    ```

    及

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. 將它們全部放在一起，您的 HTML 程式碼看起來應該像下列程式碼。 這個範例會示範如何從 URL 讀取 XML 檔案。 然後，將檔案的功能資料載入和顯示在地圖上。 

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Spatial IO Module Example</title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <!-- Add reference to the Azure Maps Spatial IO module. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

        <script type='text/javascript'>
            var map, datasource, layer;

            function GetMap() {
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
                map.events.add('ready', function() {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Add a simple data layer for rendering the data.
                    layer = new atlas.layer.SimpleDataLayer(datasource);
                    map.layers.add(layer);

                    //Read an XML file from a URL or pass in a raw XML string.
                    atlas.io.read('superCoolKmlFile.xml').then(r => {
                        if (r) {
                            //Add the feature data to the data source.
                            datasource.add(r);

                            //If bounding box information is known for data, set the map view to it.
                            if (r.bbox) {
                                map.setCamera({
                                    bounds: r.bbox,
                                    padding: 50
                                });
                            }
                        }
                    });
                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

5. 請記得將取代為 `<Your Azure Maps Key>` 您的主要金鑰。 開啟您的 HTML 檔案，您會看到類似下圖的結果：

    <center>

    ![空間資料範例](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>後續步驟

我們在這裡示範的功能只是空間 IO 模組中可用的許多功能之一。 閱讀下列指南，以瞭解如何使用空間 IO 模組中的其他功能：

> [!div class="nextstepaction"]
> [新增簡單的資料圖層](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [讀取和寫入空間資料](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [新增 OGC 地圖圖層](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [連接到 WFS 服務](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [利用核心作業](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支援的資料格式詳細資料](spatial-io-supported-data-format-details.md)

請參閱 Azure 地圖服務空間 IO 檔：

> [!div class="nextstepaction"]
> [Azure 地圖服務空間 IO 套件](/javascript/api/azure-maps-spatial-io/)