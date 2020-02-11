---
title: 教學課程：使用 Azure 地圖服務建立商店定位器應用程式 | Microsoft Azure 地圖服務
description: 在本教學課程中，您將了解如何使用 Microsoft Azure 地圖服務 Web SDK 來建立商店定位器 Web 應用程式。
author: walsehgal
ms.author: v-musehg
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 063f085de875272a7b1ba4f52aeceb8f36114cca
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987000"
---
# <a name="tutorial-create-a-store-locator-by-using-azure-maps"></a>教學課程：使用 Azure 地圖服務建立商店定位器

本教學課程將引導您完成使用 Azure 地圖服務建立簡單商店定位器的程序。 商店定位器是通用的。 這類應用程式中使用的概念大多也都適用於許多其他類型的應用程式。 大多數直接對消費者進行銷售的企業，都有需要為客戶提供商店定位器。 在本教學課程中，您會了解如何：
    
> [!div class="checklist"]
> * 使用 Azure 地圖控制項 API 建立新的網頁。
> * 從檔案載入自訂資料，並顯示在地圖上。
> * 使用 Azure 地圖服務搜尋服務來尋找地址或輸入查詢。
> * 從瀏覽器取得使用者的位置，並將其顯示在地圖上。
> * 結合多個圖層以建立地圖上的自訂符號。  
> * 叢集資料點。  
> * 將縮放控制項新增至地圖。

<a id="Intro"></a>

往前跳至[虛擬體驗商店定位器範例](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator)或[原始程式碼](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)。 

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程中的步驟，您必須先建立 Azure 地圖服務帳戶，並取得主要金鑰 (訂用帳戶金鑰)。 請遵循[建立帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)中的指示，建立使用 S1 定價層的 Azure 地圖服務帳戶訂用帳戶，並遵循[取得主要金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的步驟來取得適用於您帳戶的主要金鑰。 如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](how-to-manage-authentication.md)。

## <a name="design"></a>設計

在跳到程式碼之前，先進行設計是個不錯的做法。 商店定位器可依據您的需要調整複雜或簡單程度。 在本教學課程中，我們會建立簡單的商店定位器。 我們在過程中提供了一些秘訣，可協助您依個人需求擴充某些功能。 我們會為名為 Contoso Coffee 的虛構公司建立商店定位器。 下圖顯示我們在本教學課程中建置的商店定位器所採用的一般配置框線：

<center>

![Contoso Coffee 商店位置的商店定位器應用程式框線](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)</center>

為了充分發揮此商店定位器的實用性，我們加入了回應式配置，可在使用者的螢幕寬度小於 700 個像素時進行調整。 回應式配置可方便您在小型螢幕上使用商店定位器，例如行動裝置。 以下是小型螢幕配置的框線：  

<center>

![Contoso Coffee 商店定位器應用程式在行動裝置上的框線](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</center>

框線可直觀地顯示應用程式。 應用程式具有搜尋方塊、鄰近商店清單，和具有一些標記 (例如符號) 的地圖。 此外也有一個快顯視窗，會在使用者選取標記時顯示額外的資訊。 以下詳細列出我們在本教學課程中建置到此商店定位器的功能：

* 從匯入的 Tab 鍵分隔資料檔案匯入的所有位置，都會載入至地圖上。
* 使用者可以移動瀏覽和縮放地圖、執行搜尋，以及選取 [我的位置 GPS] 按鈕。
* 頁面配置會根據裝置螢幕的寬度進行調整。  
* 標頭會顯示商店標誌。  
* 使用者可以使用搜尋方塊和搜尋按鈕來搜尋位置，例如地址、郵遞區號或城市。 
* 新增至搜尋方塊的 `keypress` 事件會在使用者按下 Enter 鍵時觸發搜尋。 這項功能常被忽略，但可產生較理想的使用者體驗。
* 當地圖移動時，系統會計算從地圖中心點到各個位置的距離。 結果清單會進行更新，並在地圖頂端顯示最接近的位置。  
* 當您選取結果清單中的結果時，地圖將會以選取的位置作為中心點，並且在快顯視窗中顯示該位置的相關資訊。  
* 選取地圖上的特定位置時，也會觸發快顯視窗。
* 使用者縮小地圖時，位置會分組到叢集中。 叢集以圓圈及其內含的數字表示。 叢集會隨著使用者變更縮放層級而形成和分隔。
* 選取某個叢集時，會將地圖放大兩個層級，並以該叢集的位置作為中心點。

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>建立商店位置資料集

在開發商店定位器應用程式之前，我們必須先為要顯示在地圖上的商店建立資料集。 在本教學課程中，我們將使用虛構咖啡廳 Contoso Coffee 的資料集。 我們以 Excel 活頁簿來管理此簡單商店定位器的資料集。 資料集中包含 10,213 個遍布於九個國家/地區的 Contoso Coffee 咖啡廳所在位置：美國、加拿大、英國、法國、德國、義大利、荷蘭、丹麥和西班牙。 其資料如下列螢幕擷取畫面所示：

<center>

![Excel 活頁簿中商店定位器資料的螢幕擷取畫面](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)</center>

您可以[下載 Excel 活頁簿](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data)。 

我們可以查看資料的螢幕擷取畫面，以進行下列觀察：
    
* 位置資訊可使用 **AddressLine**、**City**、**Municipality** (國家/地區)、**AdminDivision** (縣/市)、**PostCode** (郵遞區號) 和 **Country** 等資料行來儲存。  
* **Latitude** 和 **Longitude** 資料行包含每個 Contoso Coffee 咖啡廳所在位置的座標。 如果您沒有座標資訊，您可以使用 Azure 地圖服務中的搜尋服務來決定位置座標。
* 此外還有一些包含咖啡廳相關中繼資料的資料行：電話號碼、布林值資料行，以及 24 小時格式的開店和關店時間。 布林值資料行用於 Wi-Fi 和殘障人士協助工具。 您可以建立的資料行，並納入與您的位置資料更為相關的中繼資料。

> [!Note]
> Azure 地圖服務會以球面麥卡托投影 "EPSG:3857" 呈現資料，但是會讀取 "EPSG:4325" 中採用 WGS84 數據的資料。 

有許多方式可將資料集公開給應用程式。 其中一個方法是將資料載入至資料庫，然後公開會查詢資料的 Web 服務。 然後，您可以將結果傳送至使用者的瀏覽器。 此選項非常適用於大型資料集或經常更新的資料集。 不過，此選項需要高於一般的開發工作，且成本較高。 

另一種方法是將此資料集轉換成瀏覽器可輕易地剖析的一般文字檔案。 檔案本身可隨著應用程式的其餘部分而裝載。 此選項較為單純，但僅適用於較小的資料集，因為使用者會下載所有資料。 我們將為此資料集使用一般文字檔案，因為其資料檔案大小不到 1 MB。  

若要將活頁簿轉換成一般文字檔案，請將活頁簿儲存為 Tab 鍵分隔檔案。 每個資料行都會以 Tab 字元分隔，以便在我們的程式碼中剖析資料行。 您可以使用逗號分隔值 (CSV) 格式，但該選項需要較多剖析邏輯。 周圍有逗號的任何欄位都會在兩側加上引號。 若要將此資料匯出為 Excel 中的 Tab 鍵分隔檔案，請選取 [另存新檔]  。 在 [存檔類型]  下拉式清單中，選取 [文字 (Tab 鍵分隔) (*.txt)]  。 將檔案命名為 *ContosoCoffee.txt*。 

<center>

![[存檔類型] 對話方塊的螢幕擷取畫面](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)</center>

如果您在「記事本」中開啟文字檔，檔案會如下圖所示：

<center>

![顯示 Tab 字元分隔資料集的記事本檔案螢幕擷取畫面](./media/tutorial-create-store-locator/StoreDataTabFile.png)</center>


## <a name="set-up-the-project"></a>設定專案

若要建立專案，您可以使用 [Visual Studio](https://visualstudio.microsoft.com) 或您選擇的程式碼編輯器。 請在您的專案資料夾中建立三個檔案：*index.html*、*index.css* 和 *index.js*。 這些檔案會定義應用程式的版面配置、樣式和邏輯。 建立名為 *data* 的資料夾，並將 *ContosoCoffee.txt* 新增至該資料夾。 建立名為 *images* 的另一個資料夾。 我們在此應用程式中使用 10 個影像來表示地圖上的圖示、按鈕和標記。 您可以[下載這些影像](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data)。 您的專案資料夾此時會如下圖所示︰

<center>

![簡單商店定位器專案資料夾的螢幕擷取畫面](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)</center>

## <a name="create-the-user-interface"></a>建立使用者介面

若要建立使用者介面，請將程式碼新增至 *index.html*：

1. 將下列 `meta` 標記新增至 *index.html* 的 `head`。 `charset` 標籤會定義字元集 (UTF-8)。 `http-equiv` 的值會向 Internet Explorer 和 Microsoft Edge 指出應使用最新的瀏覽器版本。 此外，最後一個 `meta` 標籤會指定一個適用於回應式配置的檢視區。

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

1. 新增 Azure 地圖服務 Web 控制項 JavaScript 和 CSS 檔案的參考：

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

1. 新增 Azure 地圖服務模組的參考。 此模組是一個 JavaScript 程式庫，其中包覆了 Azure 地圖服務 REST 服務，並可使其更容易在 JavaScript 中使用。 此模組可用於加強搜尋功能。

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
    ```

1. 新增 *index.js* 和 *index.css* 的參考：

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

1. 在文件本文中，新增 `header` 標記。 在 `header` 標記內，新增標誌和公司名稱。

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

1. 新增 `main` 標記，並建立具有文字對話方塊和搜尋按鈕的搜尋面板。 此外，新增地圖、清單面板和 [我的位置 GPS] 按鈕的 `div` 參考。

    ```HTML
    <main>
        <div class="searchPanel">
            <div>
                <input id="searchTbx" type="search" placeholder="Find a store" />
                <button id="searchBtn" title="Search"></button>
            </div>
        </div>
        <div id="listPanel"></div>
        <div id="myMap"></div>
        <button id="myLocationBtn" title="My Location"></button>
    </main>
    ```

完成之後，*index.html* 應會如[這個範例 index.html 檔案](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html)所示。

下一個步驟是定義 CSS 樣式。 CSS 樣式會定義應用程式元件的配置方式，和應用程式的外觀。 請開啟 *index.css*，並對其新增下列程式碼。 `@media` 樣式會定義螢幕寬度小於 700 個像素時所要使用的替代樣式選項。  

   ```CSS
    html, body {
        padding: 0;
        margin: 0;
        font-family: Gotham, Helvetica, sans-serif;
        overflow-x: hidden;
    }

    header {
        width: calc(100vw - 10px);
        height: 30px;
        padding: 15px 0 20px 20px;
        font-size: 25px;
        font-style: italic;
        font-family: "Comic Sans MS", cursive, sans-serif;
        line-height: 30px;
        font-weight: bold;
        color: white;
        background-color: #007faa;
    }

    header span {
        vertical-align: middle;
    }

    header img {
        height: 30px;
        vertical-align: middle;
    }

    .searchPanel {
        position: relative;
        width: 350px;
    }

    .searchPanel div {
        padding: 20px;
    }

    .searchPanel input {
        width: calc(100% - 50px);
        font-size: 16px;
        border: 0;
        border-bottom: 1px solid #ccc;
    }

    #listPanel {
        position: absolute;
        top: 135px;
        left: 0px;
        width: 350px;
        height: calc(100vh - 135px);
        overflow-y: auto;
    }

    #myMap { 
        position: absolute;
        top: 65px;
        left: 350px;
        width: calc(100vw - 350px);
        height: calc(100vh - 65px);
    }

    .statusMessage {
        margin: 10px;
    }

    #myLocationBtn, #searchBtn {
        margin: 0;
        padding: 0;
        border: none;
        border-collapse: collapse;
        width: 32px;
        height: 32px; 
        text-align: center;
        cursor: pointer;
        line-height: 32px;
        background-repeat: no-repeat;
        background-size: 20px;
        background-position: center center;
        z-index: 200;
    }

    #myLocationBtn {
        position: absolute;
        top: 150px;
        right: 10px;
        box-shadow: 0px 0px 4px rgba(0,0,0,0.16);
        background-color: white;
        background-image: url("images/GpsIcon.png");
    }

    #myLocationBtn:hover {
        background-image: url("images/GpsIcon-hover.png");
    }

    #searchBtn {
        background-color: transparent;
        background-image: url("images/SearchIcon.png");
    }

    #searchBtn:hover {
        background-image: url("images/SearchIcon-hover.png");
    }

    .listItem {
        height: 50px;
        padding: 20px;
        font-size: 14px;
    }

    .listItem:hover {
        cursor: pointer;
        background-color: #f1f1f1;
    }

    .listItem-title {
        color: #007faa;
        font-weight: bold;
    }

    .storePopup {
        min-width: 150px;
    }

    .storePopup .popupTitle {
        border-top-left-radius: 4px;
        border-top-right-radius: 4px;
        padding: 8px;
        height: 30px;
        background-color: #007faa;
        color: white;
        font-weight: bold;
    }

    .storePopup .popupSubTitle {
        font-size: 10px;
        line-height: 12px;
    }

    .storePopup .popupContent {
        font-size: 11px;
        line-height: 18px;
        padding: 8px;
    }

    .storePopup img {
        vertical-align:middle;
        height: 12px;
        margin-right: 5px;
    }

    /* Adjust the layout of the page when the screen width is less than 700 pixels. */
    @media screen and (max-width: 700px) {
        .searchPanel {
            width: 100vw;
        }

        #listPanel {
            top: 385px;
            width: 100%;
            height: calc(100vh - 385px);
        }

        #myMap {
            width: 100vw;
            height: 250px;
            top: 135px;
            left: 0px;
        }

        #myLocationBtn {
            top: 220px;
        }
    }

    .mapCenterIcon {
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background: orange;
        border: 2px solid white;
        cursor: pointer;
        box-shadow: 0 0 0 rgba(0, 204, 255, 0.4);
        animation: pulse 3s infinite;
    }

    @keyframes pulse {
        0% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0.4);
        }

        70% {
            box-shadow: 0 0 0 50px rgba(0, 204, 255, 0);
        }

        100% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0);
        }
    }
   ```

立即執行應用程式，您會看到標頭、搜尋方塊和搜尋按鈕。 但地圖尚未載入，因此不會顯示。 如果您嘗試執行搜尋，將不會有任何動作。 我們必須設定下一節所說明的 JavaScript 邏輯。 此邏輯會存取商店定位器的所有功能。

## <a name="wire-the-application-with-javascript"></a>將應用程式與 JavaScript 連線

此時，使用者介面中的一切設定皆已完成。 我們還需要新增 JavaScript 以載入和剖析資料，然後在地圖上轉譯資料。 為此，請先開啟 *index.js* 並為其新增程式碼，如下列步驟所說明。

1. 新增全域選項以便更新設定。 請定義地圖的變數、快顯視窗、資料來源、圖示圖層、顯示搜尋區域中心點的 HTML 標記，以及 Azure 地圖服務搜尋服務用戶端的執行個體。

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. 將程式碼新增至 *index.js*。 下列程式碼會初始化地圖。 我們已新增[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)，以等待頁面載入完成。 然後，我們會連結事件以監視地圖的載入，並將功能提供給搜尋按鈕和「我的位置」按鈕。

   當使用者選取搜尋按鈕時，或是當使用者在搜尋方塊中輸入位置之後按下 Enter 鍵時，就會起始對使用者查詢的模糊搜尋。 將國家/地區 ISO 2 值的陣列傳入 `countrySet` 選項，可將搜尋結果限制在這些國家/地區。 限制要搜尋的國家/地區，有助於提高傳回結果的精確度。 
  
   搜尋完成後，請取用第一項結果，並設定該區域的地圖相機。 當使用者選取 [我的位置] 按鈕時，請使用瀏覽器中內建的 HTML5 地理位置 API 來擷取使用者的位置，並以其位置作為地圖的中心點。  

   > [!Tip]
   > 當您使用的快顯視窗時，建議您最好建立單一 `Popup` 執行個體，並藉由更新內容和位置重複使用該執行個體。 您新增至程式碼的每個 `Popup` 執行個體，會有多個 DOM 元素新增至頁面。 頁面上的 DOM 元素越多，瀏覽器所須追蹤的項目就越多。 如果項目過多，瀏覽器可能會變慢。

    ```JavaScript
    function initialize() {
        //Initialize a map instance.
        map = new atlas.Map('myMap', {
            center: [-90, 40],
            zoom: 2,

            //Add your Azure Maps primary subscription key to the map SDK.
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });

        //Create a pop-up window, but leave it closed so we can update it and display it later.
        popup = new atlas.Popup();

        //Use SubscriptionKeyCredential with a subscription key
        const subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

        //Use subscriptionKeyCredential to create a pipeline
        const pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
            retryOptions: { maxTries: 4 } // Retry options
        });

        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in.
        document.getElementById('searchBtn').onclick = performSearch;

        //If the user presses Enter in the search box, perform a search.
        document.getElementById('searchTbx').onkeyup = function(e) {
            if (e.keyCode === 13) {
                performSearch();
            }
        };

        //If the user selects the My Location button, use the Geolocation API to get the user's location. Center and zoom the map on that location.
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation;

        //Wait until the map resources are ready.
        map.events.add('ready', function() {

            //Add your post-map load functionality.

        });
    }

    //Create an array of country ISO 2 values to limit searches to. 
    var countrySet = ['US', 'CA', 'GB', 'FR','DE','IT','ES','NL','DK'];

    function performSearch() {
        var query = document.getElementById('searchTbx').value;

        //Perform a fuzzy search on the users query.
        searchURL.searchFuzzy(atlas.service.Aborter.timeout(3000), query, {
            //Pass in the array of country ISO2 for which we want to limit the search to.
            countrySet: countrySet
        }).then(results => {
            //Parse the response into GeoJSON so that the map can understand.
            var data = results.geojson.getFeatures();

            if (data.features.length > 0) {
                //Set the camera to the bounds of the results.
                map.setCamera({
                    bounds: data.features[0].bbox,
                    padding: 40
                });
            } else {
                document.getElementById('listPanel').innerHTML = '<div class="statusMessage">Unable to find the location you searched for.</div>';
            }
        });
    }

    function setMapToUserLocation() {
        //Request the user's location.
        navigator.geolocation.getCurrentPosition(function(position) {
            //Convert the Geolocation API position to a longitude and latitude position value that the map can interpret and center the map over it.
            map.setCamera({
                center: [position.coords.longitude, position.coords.latitude],
                zoom: maxClusterZoomLevel + 1
            });
        }, function(error) {
            //If an error occurs when the API tries to access the user's position information, display an error message.
            switch (error.code) {
                case error.PERMISSION_DENIED:
                    alert('User denied the request for geolocation.');
                    break;
                case error.POSITION_UNAVAILABLE:
                    alert('Position information is unavailable.');
                    break;
                case error.TIMEOUT:
                    alert('The request to get user position timed out.');
                    break;
                case error.UNKNOWN_ERROR:
                    alert('An unknown error occurred.');
                    break;
            }
        });
    }

    //Initialize the application when the page is loaded.
    window.onload = initialize;
    ```

1. 在地圖的 `ready` 事件接聽程式中新增縮放控制項，以及用來顯示搜尋區域中心點的 HTML 標記。

    ```JavaScript
    //Add a zoom control to the map.
    map.controls.add(new atlas.control.ZoomControl(), {
        position: 'top-right'
    });

    //Add an HTML marker to the map to indicate the center to use for searching.
    centerMarker = new atlas.HtmlMarker({
        htmlContent: '<div class="mapCenterIcon"></div>',
        position: map.getCamera().center
    });

    map.markers.add(centerMarker);
    ```

1. 在地圖的 `ready` 事件接聽程式中，新增資料來源。 然後，進行呼叫以載入和剖析資料集。 啟用資料來源的叢集。 資料來源的叢集會將重疊的資料點分組到同一個叢集中。 當使用者放大地圖時，這些叢集會分成個別的資料點。 這可讓使用者體驗更為流暢，並改善效能。

    ```JavaScript
    //Create a data source, add it to the map, and then enable clustering.
    datasource = new atlas.source.DataSource(null, {
        cluster: true,
        clusterMaxZoom: maxClusterZoomLevel - 1
    });

    map.sources.add(datasource);

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

1. 將資料集載入至地圖的 `ready` 事件接聽程式之後，請定義一組用來呈現資料的圖層。 泡泡圖層用來呈現叢集的資料點。 符號圖層用來呈現泡泡圖層之上每個叢集中的點數。 第二個符號圖層會呈現地圖上個別位置的自訂圖示。

   將 `mouseover` 和 `mouseout` 事件新增至泡泡和圖示圖層，可變更使用者將滑鼠停留在地圖上的叢集或圖示上方時的滑鼠游標。 將 `click` 事件新增至叢集泡泡圖層。 此 `click` 事件會在使用者選取任何叢集時將地圖放大兩個層級，並以該叢集作為地圖的中心點。 將 `click` 事件新增至圖示圖層。 此 `click` 事件會在使用者選取個別位置圖示時顯示快顯視窗，以呈現咖啡廳的詳細資料。 在地圖上新增用來監視地圖何時完成移動的事件。 此事件引發時，請更新清單面板中的項目。  

    ```JavaScript
    //Create a bubble layer to render clustered data points.
    var clusterBubbleLayer = new atlas.layer.BubbleLayer(datasource, null, {
        radius: 12,
        color: '#007faa',
        strokeColor: 'white',
        strokeWidth: 2,
        filter: ['has', 'point_count'] //Only render data points that have a point_count property; clusters have this property.
    });

    //Create a symbol layer to render the count of locations in a cluster.
    var clusterLabelLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none' //Hide the icon image.
        },

        textOptions: {
            textField: ['get', 'point_count_abbreviated'],
            size: 12,
            font: ['StandardFont-Bold'],
            offset: [0, 0.4],
            color: 'white'
        }
    });

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]);

    //Load a custom image icon into the map resources.
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function() {

    //Create a layer to render a coffee cup symbol above each bubble for an individual location.
    iconLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            //Pass in the ID of the custom icon that was loaded into the map resources.
            image: 'myCustomIcon',

            //Optionally, scale the size of the icon.
            font: ['SegoeUi-Bold'],

            //Anchor the center of the icon image to the coordinate.
            anchor: 'center',

            //Allow the icons to overlap.
            allowOverlap: true
        },

        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
    });

    map.layers.add(iconLayer);

    //When the mouse is over the cluster and icon layers, change the cursor to a pointer.
    map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'pointer';
    });

    //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab).
    map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'grab';
    });

    //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
    map.events.add('click', clusterBubbleLayer, function(e) {
        map.setCamera({
            center: e.position,
            zoom: map.getCamera().zoom + 2
        });
    });

    //Add a click event to the icon layer and show the shape that was selected.
    map.events.add('click', iconLayer, function(e) {
        showPopup(e.shapes[0]);
    });

    //Add an event to monitor when the map is finished rendering the map after it has moved.
    map.events.add('render', function() {
        //Update the data in the list.
        updateListItems();
    });
    ```

1. 咖啡廳資料集載入後，您必須先加以下載。 然後，文字檔必須分成數行。 第一行包含標頭資訊。 為了讓程式碼更易於了解，我們將標頭剖析為物件，而此物件隨後可用來查閱每個屬性的資料格索引。 在第一行後面，請循環使用其餘幾行，並建立資料點功能。 將資料點功能新增至資料來源。 最後，更新清單面板。

    ```JavaScript
    function loadStoreData() {

    //Download the store location data.
    fetch(storeLocationDataUrl)
        .then(response => response.text())
        .then(function(text) {

            //Parse the tab-delimited file data into GeoJSON features.
            var features = [];

            //Split the lines of the file.
            var lines = text.split('\n');

            //Grab the header row.
            var row = lines[0].split('\t');

            //Parse the header row and index each column to make the code for parsing each row easier to follow.
            var header = {};
            var numColumns = row.length;
            for (var i = 0; i < row.length; i++) {
                header[row[i]] = i;
            }

            //Skip the header row and then parse each row into a GeoJSON feature.
            for (var i = 1; i < lines.length; i++) {
                row = lines[i].split('\t');

                //Ensure that the row has the correct number of columns.
                if (row.length >= numColumns) {

                    features.push(new atlas.data.Feature(new atlas.data.Point([parseFloat(row[header['Longitude']]), parseFloat(row[header['Latitude']])]), {
                        AddressLine: row[header['AddressLine']],
                        City: row[header['City']],
                        Municipality: row[header['Municipality']],
                        AdminDivision: row[header['AdminDivision']],
                        Country: row[header['Country']],
                        PostCode: row[header['PostCode']],
                        Phone: row[header['Phone']],
                        StoreType: row[header['StoreType']],
                        IsWiFiHotSpot: (row[header['IsWiFiHotSpot']].toLowerCase() === 'true') ? true : false,
                        IsWheelchairAccessible: (row[header['IsWheelchairAccessible']].toLowerCase() === 'true') ? true : false,
                        Opens: parseInt(row[header['Opens']]),
                        Closes: parseInt(row[header['Closes']])
                    }));
                }
            }

            //Add the features to the data source.
            datasource.add(new atlas.data.FeatureCollection(features));

            //Initially, update the list items.
            updateListItems();
        });
    }
    ```

1. 當清單面板更新時，會計算距離。 這從地圖中心點到目前地圖檢視中各個資料點特徵的距離。 隨後，這些功能會依距離排序。 系統會產生 HTML 以顯示清單面板中的每個位置。

    ```JavaScript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>';

    function updateListItems() {
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map.
        var camera = map.getCamera();
        var listPanel = document.getElementById('listPanel');

        //Check to see whether the user is zoomed out a substantial distance. If they are, tell the user to zoom in and to perform a search or select the My Location button.
        if (camera.zoom < maxClusterZoomLevel) {
            //Close the pop-up window; clusters might be displayed on the map.  
            popup.close(); 
            listPanel.innerHTML = '<div class="statusMessage">Search for a location, zoom the map, or select the My Location button to see individual locations.</div>';
        } else {
            //Update the location of the centerMarker property.
            centerMarker.setOptions({
                position: camera.center,
                visible: true
            });

            //List the ten closest locations in the side panel.
            var html = [], properties;

            /*
            Generating HTML for each item that looks like this:
            <div class="listItem" onclick="itemSelected('id')">
                <div class="listItem-title">1 Microsoft Way</div>
                Redmond, WA 98052<br />
                Open until 9:00 PM<br />
                0.7 miles away
            </div>
            */

            //Get all the shapes that have been rendered in the bubble layer. 
            var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]);

            //Create an index of the distances of each shape.
            var distances = {};

            data.forEach(function (shape) {
                if (shape instanceof atlas.Shape) {

                    //Calculate the distance from the center of the map to each shape and store in the index. Round to 2 decimals.
                    distances[shape.getId()] = Math.round(atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles') * 100) / 100;
                }
            });

            //Sort the data by distance.
            data.sort(function (x, y) {
                return distances[x.getId()] - distances[y.getId()];
            });

            data.forEach(function(shape) {
                properties = shape.getProperties();
                html.push('<div class="listItem" onclick="itemSelected(\'', shape.getId(), '\')"><div class="listItem-title">',
                properties['AddressLine'],
                '</div>',
                //Get a formatted addressLine2 value that consists of City, Municipality, AdminDivision, and PostCode.
                getAddressLine2(properties),
                '<br />',

                //Convert the closing time to a format that is easier to read.
                getOpenTillTime(properties),
                '<br />',

                //Get the distance of the shape.
                distances[shape.getId()],
                ' miles away</div>');
            });

            listPanel.innerHTML = html.join('');

            //Scroll to the top of the list panel in case the user has scrolled down.
            listPanel.scrollTop = 0;
        }
    }

    //This converts a time that's in a 24-hour format to an AM/PM time or noon/midnight string.
    function getOpenTillTime(properties) {
        var time = properties['Closes'];
        var t = time / 100;
        var sTime;

        if (time === 1200) {
            sTime = 'noon';
        } else if (time === 0 || time === 2400) {
            sTime = 'midnight';
        } else {
            sTime = Math.round(t) + ':';

            //Get the minutes.
            t = (t - Math.round(t)) * 100;

            if (t === 0) {
                sTime += '00';
            } else if (t < 10) {
                sTime += '0' + t;
            } else {
                sTime += Math.round(t);
            }

            if (time < 1200) {
                sTime += ' AM';
            } else {
                sTime += ' PM';
            }
        }

        return 'Open until ' + sTime;
    }

    //Create an addressLine2 string that contains City, Municipality, AdminDivision, and PostCode.
    function getAddressLine2(properties) {
        var html = [properties['City']];

        if (properties['Municipality']) {
            html.push(', ', properties['Municipality']);
        }

        if (properties['AdminDivision']) {
            html.push(', ', properties['AdminDivision']);
        }

        if (properties['PostCode']) {
            html.push(' ', properties['PostCode']);
        }

        return html.join('');
    }
    ```

1. 當使用者選取清單面板中的項目時，系統會從資料來源擷取該項目的相關圖形。 此時會根據圖形中儲存的屬性資訊產生快顯視窗。 地圖會以該圖形作為中心點。 如果地圖的寬度少於 700 個像素，地圖檢視就會位移，以顯示快顯視窗。

    ```JavaScript
    //When a user selects a result in the side panel, look up the shape by its ID value and display the pop-up window.
    function itemSelected(id) {
        //Get the shape from the data source by using its ID.  
        var shape = datasource.getShapeById(id);
        showPopup(shape);

        //Center the map over the shape on the map.
        var center = shape.getCoordinates();
        var offset;

        //If the map is less than 700 pixels wide, then the layout is set for small screens.
        if (map.getCanvas().width < 700) {
            //When the map is small, offset the center of the map relative to the shape so that there is room for the popup to appear.
            offset = [0, -80];
        }

        map.setCamera({
            center: center,
            centerOffset: offset
        });
    }

    function showPopup(shape) {
        var properties = shape.getProperties();

        /* Generating HTML for the pop-up window that looks like this:

            <div class="storePopup">
                <div class="popupTitle">
                    3159 Tongass Avenue
                    <div class="popupSubTitle">Ketchikan, AK 99901</div>
                </div>
                <div class="popupContent">
                    Open until 22:00 PM<br/>
                    <img title="Phone Icon" src="images/PhoneIcon.png">
                    <a href="tel:1-800-XXX-XXXX">1-800-XXX-XXXX</a>
                    <br>Amenities:
                    <img title="Wi-Fi Hotspot" src="images/WiFiIcon.png">
                    <img title="Wheelchair Accessible" src="images/WheelChair-small.png">
                </div>
            </div>
        */

         //Calculate the distance from the center of the map to the shape in miles, round to 2 decimals.
        var distance = Math.round(atlas.math.getDistanceTo(map.getCamera().center, shape.getCoordinates(), 'miles') * 100)/100;

        var html = ['<div class="storePopup">'];
        html.push('<div class="popupTitle">',
            properties['AddressLine'],
            '<div class="popupSubTitle">',
            getAddressLine2(properties),
            '</div></div><div class="popupContent">',

            //Convert the closing time to a format that's easier to read.
            getOpenTillTime(properties),

            //Add the distance information.  
            '<br/>', distance,
            ' miles away',
            '<br /><img src="images/PhoneIcon.png" title="Phone Icon"/><a href="tel:',
            properties['Phone'],
            '">',  
            properties['Phone'],
            '</a>'
        );

        if (properties['IsWiFiHotSpot'] || properties['IsWheelchairAccessible']) {
            html.push('<br/>Amenities: ');

            if (properties['IsWiFiHotSpot']) {
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>');
            }

            if (properties['IsWheelchairAccessible']) {
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>');
            }
        }

        html.push('</div></div>');

        //Update the content and position of the pop-up window for the specified shape information.
        popup.setOptions({

            //Create a table from the properties in the feature.
            content:  html.join(''),
            position: shape.getCoordinates()
        });

        //Open the pop-up window.
        popup.open(map);
    }
    ```

現在，您已有功能完整的商店定位器。 在網頁瀏覽器中，開啟商店定位器的 *index.html* 檔案。 當叢集出現在地圖上時，您可以藉由使用搜尋方塊、選取 [我的位置] 按鈕、選取叢集，或藉由放大地圖以查看個別位置來搜尋位置。

在使用者第一次選取 [我的位置] 按鈕時，瀏覽器會顯示安全性警告，要求提供存取使用者所在位置的權限。 如果使用者同意共用其位置，地圖就會放大使用者的位置，並顯示附近的咖啡廳。 

<center>

![瀏覽器要求存取使用者所在位置的螢幕擷取畫面](./media/tutorial-create-store-locator/GeolocationApiWarning.png)</center>

當您將咖啡廳所在位置的區域放大足夠的程度時，叢集就會分成個別的位置。 選取地圖上的其中一個圖示，或選取側邊面板中的項目，就會顯示一個快顯視窗，內含該位置的相關資訊。

<center>

![已完成之商店定位器的螢幕擷取畫面](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)</center>

如果您將瀏覽器視窗的大小調整為少於 700 個像素的寬度，或是在行動裝置上開啟應用程式，版面配置將會變更為適用於較小螢幕的設定。 

<center>

![小型螢幕版商店定位器的螢幕擷取畫面](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)</center>

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用 Azure 地圖服務建立基本的商店定位器。 您在本教學課程中建立的商店定位器可能已有您所需的所有功能。 您可以在商店定位器中新增功能，或使用更進階的功能以滿足自訂使用者體驗： 

> [!div class="checklist"]
> * 啟用[在搜尋方塊中鍵入時提供建議](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI)的功能。  
> * 新增[支援多種語言](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization)。 
> * 允許使用者[沿路線篩選位置](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route)。 
> * 新增[設定篩選](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property)的功能。 
> * 新增使用查詢字串指定初始搜尋值的支援。 當您在商店定位器中納入此選項時，使用者可以將搜尋加入書籤和共用搜尋。 此外也可讓您輕鬆地將其他頁面上的搜尋傳至此頁面。  
> * 將商店定位器部署為 [Azure App Service Web 應用程式](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html)。 
> * 將資料儲存在資料庫中和搜尋附近的位置。 若要深入了解，請參閱 [SQL Server 空間資料類型概觀](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview?view=sql-server-2017)和[查詢最接近像素的空間資料](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?view=sql-server-2017)。

> [!div class="nextstepaction"]
> [檢視完整的原始程式碼](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)

> [!div class="nextstepaction"]
> [檢視即時範例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator)

若要深入了解 Azure 地圖服務的涵蓋範圍和功能：

> [!div class="nextstepaction"]
> [縮放層級和圖格格線](zoom-levels-and-tile-grid.md)

若要查看更多程式碼範例和互動式編碼體驗：

> [!div class="nextstepaction"]
> [如何使用地圖控制項](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)
