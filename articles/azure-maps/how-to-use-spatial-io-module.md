---
title: 如何使用 Azure 地圖空間 IO 模組 |微軟 Azure 地圖
description: 瞭解如何使用 Azure 地圖 Web SDK 提供的空間 IO 模組。 此模組提供了強大的功能,使開發人員可以輕鬆地將空間數據與 Azure 地圖 Web sdk 整合。
author: philmea
ms.author: philmea
ms.date: 02/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 1de9dd9721700418b1aa9ba661fc070db1dbedcc
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804634"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>如何使用 Azure 地圖空間 IO 模組

Azure 地圖 Web SDK 提供**空間 IO 模組**,該模組使用 JavaScript 或 TypeScript 將空間數據與 Azure 地圖 Web SDK 整合。 這個模組中的強大功能允許開發人員:

- [讀取與寫入常見的空間資料檔](spatial-io-read-write-spatial-data.md)。 支援的檔案格式包括:KML、KMZ、GPX、GeoRSS、GML、GeoJSON 和 CSV 檔,其中包含包含空間資訊的列。 還支援已知文本 (WKT)。
- [連接到開放地理空間聯盟 (OGC) 服務並與 Azure 地圖 Web SDK 整合。將 Web 地圖服務 (WMS) 與 Web 地圖磁貼服務 (WMTS) 疊加為地圖上的圖層](spatial-io-add-ogc-map-layer.md)。
- [查詢 Web 要素服務 (WFS) 中的資料](spatial-io-connect-wfs-service.md)。
- [覆寫包含樣式資訊的複雜資料集,並讓他們使用最小程式碼自動呈現](spatial-io-add-simple-data-layer.md)。
- [利用高速XML與分隔檔案讀取器和編寫器類別](spatial-io-core-operations.md)。

在本指南中,我們將瞭解如何在 Web 應用程式中整合和使用空間 IO 模組。

此視訊概述了 Azure 地圖 Web SDK 中的空間 IO 模組。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Easily-integrate-spatial-data-into-the-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0" title="輕鬆將空間資料整合到 Azure 地圖中 - 微軟頻道 9 影片"></iframe>


> [!WARNING]
> 僅使用來自您信任的源的數據和服務,尤其是在從其他域引用數據和服務時。 空間 IO 模組確實採取措施將風險降至最低,但最安全的方法是不允許從應用程式開始任何淫人的數據。 

## <a name="prerequisites"></a>Prerequisites

在使用 Spatial IO 模組之前,需要[建立 Azure 地圖帳戶](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-account-with-azure-maps)並[取得帳戶的主訂閱金鑰](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account)。

## <a name="installing-the-spatial-io-module"></a>安裝空間 IO 模組

可以使用以下兩個選項之一載入 Azure 地圖空間 IO 模組:

* Azure 地圖空間 IO 模組的全域託管 Azure CDN。 對於這個選項,在 HTML`<head>`檔的元素中添加對 JavaScript 的引用。

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* [Azure 地圖空間 io](https://www.npmjs.com/package/azure-maps-spatial-io)的原始碼可以在本地載入,然後隨應用一起託管。 此套件也包含 TypeScript 定義。 對此選項,請使用以下指令安裝套件:

    ```sh
    npm install azure-maps-spatial-io
    ```

    然後,在 HTML 文件的`<head>`元素中 加入對 JavaScript 的參考:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>使用空間 IO 模組

1. 建立新的 HTML 檔案。

2. 載入 Azure 映射 Web SDK 並初始化地圖控制項。 有關詳細資訊,請參閱[Azure 地圖地圖控件](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)指南。 完成此步驟後,HTML 檔應如下所示:

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

2. 載入 Azure 地圖空間 IO 模組。 在本練習中,使用 Azure 地圖空間 IO 模組的 CDN。 將下面的參考加入 HTML`<head>`檔的元素:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. 初始化`datasource`, 並將資料源添加到地圖中。 初始化`layer`,並將數據源添加到地圖圖層。 然後,呈現數據源和圖層。 在下一步向下滾動查看完整代碼之前,請考慮放置數據源和層代碼段的最佳位置。 回想一下,在以程式設計方式操作地圖之前,我們應該等到地圖資源準備就緒。

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

4. 將所有代碼放在一起,您的 HTML 代碼應類似於以下代碼。 此範例簡報如何從網址讀取 XML 檔。 然後,在地圖上載入並顯示檔的要素數據。 

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

5. 請記住用主`<Your Azure Maps Key>`鍵替換。 開啟 HTML 檔案,您會看到類似於下圖的結果:

    <center>

    ![空間資料範例](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>後續步驟

我們在這裡演示的功能只是空間 IO 模組中可用的眾多功能之一。 閱讀以下指南,瞭解如何在空間 IO 模組中使用其他功能:

> [!div class="nextstepaction"]
> [新增簡單的資料層](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [讀取與寫入空間資料](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [新增 OGC 地圖層次](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [連線到 WFS 服務](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [利用核心業務](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支援的資料格式詳細資訊](spatial-io-supported-data-format-details.md)

請參考 Azure 地圖空間 IO 文件:

> [!div class="nextstepaction"]
> [Azure 地圖空間 IO 套件](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)
