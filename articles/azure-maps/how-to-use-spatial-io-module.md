---
title: 如何使用 Azure 地圖服務空間 IO 模組 |Microsoft Azure 對應
description: 瞭解如何使用 Azure 地圖服務 Web SDK 所提供的空間 IO 模組。 此模組提供強大的功能，可讓開發人員輕鬆地將空間資料與 Azure 地圖服務 web sdk 整合。
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: c309473529666d369e8accd1617021249867fb19
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78371034"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>如何使用 Azure 地圖服務空間 IO 模組

Azure 地圖服務 Web SDK 提供**空間 IO 模組**，它會使用 JavaScript 或 TypeScript，將空間資料與 AZURE 地圖服務 Web SDK 整合。 此課程模組中的強大功能可讓開發人員：

- [讀取資料，並將其寫入一般空間](spatial-io-read-write-spatial-data.md)檔案。 支援的檔案格式包括： KML、KMZ、.GPX、GeoRSS、GML 和 CSV 檔案，其中包含具有空間資訊的資料行。
- [連接到開放地理空間協會（OGC）服務，並與 Azure 地圖服務 WEB SDK 整合。將 Web 對應服務（WMS）和 Web 地圖磚服務（WMTS）重迭為地圖上的圖層](spatial-io-add-ogc-map-layer.md)。
- [查詢 Web 功能服務（工作流程）中的資料](spatial-io-connect-wfs-service.md)。
- [包含樣式資訊並自動轉譯的複雜資料集](spatial-io-add-simple-data-layer.md)。
- [利用高速 XML 和分隔的檔案讀取器和寫入器類別](spatial-io-core-operations.md)。

在本指南中，我們將瞭解如何在 web 應用程式中整合和使用空間 IO 模組。

## <a name="prerequisites"></a>必要條件

在您可以使用空間 IO 模組之前，您必須先[建立 Azure 地圖服務帳戶](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-account-with-azure-maps)，並[取得帳戶的主要訂](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account)用帳戶金鑰。

## <a name="installing-the-spatial-io-module"></a>安裝空間 IO 模組

您可以使用下列兩個選項的其中一個來載入 Azure 地圖服務空間 IO 模組：

* Azure 地圖服務空間 IO 模組的全域託管 Azure CDN。 在此選項中，您會在 HTML 檔案的 `<head>` 元素中加入 JavaScript 的參考。

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* [Azure 地圖服務-空間-io](https://www.npmjs.com/package/azure-maps-spatial-io)的原始程式碼可以在本機載入，然後裝載于您的應用程式。 此套件也包含 TypeScript 定義。 針對此選項，請使用下列命令來安裝套件：

    ```sh
    npm install azure-maps-spatial-io
    ```

    然後，在 HTML 檔案的 `<head>` 元素中新增 JavaScript 的參考：

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>使用空間 IO 模組

1. 建立新的 HTML 檔案。

2. 載入 Azure 地圖服務 Web SDK，並初始化地圖控制項。 如需詳細資訊，請參閱[Azure 地圖服務地圖控制項](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)指南。 當您完成此步驟之後，您的 HTML 檔案看起來應該像這樣：

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

2. 載入 Azure 地圖服務空間 IO 模組。 針對此練習，請使用適用于 Azure 地圖服務空間 IO 模組的 CDN。 將下列參考新增至 HTML 檔案的 `<head>` 元素：

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. 初始化 `datasource`，並將資料來源加入至對應。 初始化 `layer`，並將資料來源加入至地圖圖層。 然後，呈現資料來源和圖層。 在下一個步驟中向下移動以查看完整的程式碼之前，請先考慮放入資料來源和分層程式碼片段的最佳位置。 回想一下，在以程式設計方式操作對應之前，我們應該等到地圖資源準備就緒。

    ```javascript
    var datasource, layer;
    ```

    和

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. 將它全部放在一起，您的 HTML 程式碼看起來應該像下面這段程式碼。 這個範例會示範如何從 URL 讀取 XML 檔案。 然後，將檔案的功能資料載入並顯示在對應上。 

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

5. 請記得以您的主要金鑰取代 `<Your Azure Maps Key>`。 開啟您的 HTML 檔案，您會看到類似下圖的結果：

    <center>

    ![空間資料範例](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>後續步驟

我們在此示範的功能只是空間 IO 模組中眾多可用功能的其中之一。 閱讀下列指南，以瞭解如何使用空間 IO 模組中的其他功能：

> [!div class="nextstepaction"]
> [新增簡單的資料層](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [讀取和寫入空間資料](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [新增 OGC 地圖圖層](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [連接到工作流程服務](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [利用核心作業](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支援的資料格式詳細資料](spatial-io-supported-data-format-details.md)

請參閱 Azure 地圖服務空間 IO 檔：

> [!div class="nextstepaction"]
> [Azure 地圖服務空間 IO 封裝](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)
