---
title: '使用 Azure 地圖服務提高許可權服務 (預覽來要求提高許可權資料) '
description: 瞭解如何使用 Azure 地圖服務提高許可權服務 (預覽) 來要求提高許可權的資料。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 76232a917e8856a06645fabc0ab4716195c5c0e1
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094194"
---
# <a name="request-elevation-data-using-the-azure-maps-elevation-service-preview"></a>使用 Azure 地圖服務提高許可權服務 (預覽來要求提高許可權資料) 

> [!IMPORTANT]
> Azure 地圖服務提升許可權服務目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 地圖服務提高 [許可權服務](https://docs.microsoft.com/rest/api/maps/elevation) 提供 api，可在地球表面上的任何位置查詢提升許可權資料。 您可以在路徑上、定義的周框方塊內或特定座標要求取樣的提高許可權資料。 此外，您也可以使用轉譯 [V2-Get Map 圖格 API](https://docs.microsoft.com/rest/api/maps/renderv2) ，以磚格式抓取提高許可權的資料。 圖格會以 GeoTIFF 的點陣格式傳遞。 本文說明如何使用 Azure 地圖服務提高許可權服務和「取得地圖磚」 API 來要求提高許可權資料。 GeoJSON 和 GeoTiff 兩種格式都可以要求提高許可權的資料。

## <a name="prerequisites"></a>必要條件

1. [在 S1 定價層中建立 Azure 地圖服務帳戶](quick-demo-map-app.md#create-an-azure-maps-account)
2. [取得主要訂用帳戶金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)，也稱為主要金鑰或訂用帳戶金鑰。

如需 Azure 地圖服務中驗證的詳細資訊，請 [在 Azure 地圖服務中管理驗證](how-to-manage-authentication.md)。

本文使用 [Postman](https://www.postman.com/) 應用程式，但您可以選擇不同的 API 開發環境。

## <a name="request-elevation-data-in-raster-tiled-format"></a>以點陣磚格式要求提高許可權資料

若要以點陣磚格式要求提高許可權資料，請使用轉譯 [V2-取得地圖磚 API](https://docs.microsoft.com/rest/api/maps/renderv2)。 如果可以找到磚，則 API 會以 GeoTIFF 傳回磚。 否則，API 會傳回0。 所有的點陣 DEM 磚都會使用 (海洋) 地球模式的 geoid。 在此範例中，我們會要求 Mt 的提高許可權資料。 珠穆朗瑪峰。

>[!TIP]
>若要在世界地圖上的特定區域抓取磚，您必須在適當的縮放層級找到正確的磚。 另外也請注意，WorldDEM 涵蓋了整個全球 landmass，但未涵蓋海洋。  如需詳細資訊，請參閱[縮放層級和圖格格線](zoom-levels-and-tile-grid.md)。

1. 開啟 Postman 應用程式。 在 Postman 應用程式頂端處，選取 [新增]。 在 [新建] 視窗中，選取 [集合]。  為集合命名，然後選取 [建立] 按鈕。

2. 若要建立要求，請再次選取 [新增]。 在 [新建] 視窗中，選取 [要求]。 輸入要求的 [要求名稱]。 選取您在先前的步驟中建立的集合，然後選取 [儲存]。

3. 在 [建立器] 索引標籤中選取 [ **取得** HTTP 方法]，然後輸入下列 URL 來要求點陣磚。 對於此要求以及本文中提及的其他要求，請將 `{Azure-Maps-Primary-Subscription-key}` 取代為您的主要訂用帳戶金鑰。

    ```http
    https://atlas.microsoft.com/map/tile?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&tilesetId=microsoft.dem&zoom=13&x=6074&y=3432
    ```

4. 按一下 [傳送] 按鈕。 您應該會收到包含 GeoTIFF 格式之提高許可權資料的點陣磚。 點陣磚原始資料中的每個圖元都屬於類型 `float` 。 每個圖元的值代表量值的提高高度。

## <a name="request-elevation-data-in-geojson-format"></a>要求 GeoJSON 格式的提高許可權資料

使用「提高許可權服務」 (預覽版) Api 來要求 GeoJSON 格式的提高許可權資料。 本節將說明這三個 Api 的每一個：

* [取得點數的資料](/rest/api/maps/elevation/getdataforpoints)
* [張貼點資料](/rest/api/maps/elevation/postdataforpoints)
* [取得聚合線條的資料](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline)
* [針對聚合線條張貼資料](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline)
* [取得周框方塊的資料](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox)

>[!IMPORTANT]
> 當無法傳回任何資料時，所有 Api 都會傳回 `0` 。

### <a name="request-elevation-data-for-points"></a>要求點數的提高許可權資料

在此範例中，我們將使用「 [取得資料點」 API](/rest/api/maps/elevation/getdataforpoints) ，在 Mt 要求提升許可權資料。 Everest 和 Chamlang 山脈。 然後，我們會使用 [點 API 的 Post 資料](/rest/api/maps/elevation/postdataforpoints) ，透過相同的兩個點來要求提高許可權的資料。 URL 中的緯度和經度預期會在 WGS84 (World Geodetic System) 小數度。

 >[!IMPORTANT]
 >由於 URL 字元長度限制為2048，因此無法在 URL GET 要求中傳遞100個以上的座標作為管線分隔字串。 如果您想要傳遞100個以上的座標作為管線分隔字串，請使用 POST 資料來取得點。

1. 若要建立要求，請再次選取 [新增]。 在 [新建] 視窗中，選取 [要求]。 輸入要求的 [要求名稱]。 選取您在先前的步驟中建立的集合，然後選取 [儲存]。

2. 在 [建立器] 索引標籤中選取 [ **取得** HTTP 方法]，並輸入下列 URL。 對於此要求以及本文中提及的其他要求，請將 `{Azure-Maps-Primary-Subscription-key}` 取代為您的主要訂用帳戶金鑰。

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&points=-73.998672,40.714728|150.644,-34.397
    ```

3. 按一下 [傳送] 按鈕。  您將會收到下列 JSON 回應：

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 40.714728,
                "longitude": -73.998672
            },
            "elevationInMeter": 12.142355447638208
        },
        {
            "coordinate": {
                "latitude": -34.397,
                "longitude": 150.644
            },
            "elevationInMeter": 384.47041445517846
        }
    ]
    }
    ```

4. 現在，我們將呼叫 [點 API 的 Post 資料](/rest/api/maps/elevation/postdataforpoints) ，以取得相同兩個點的提高許可權資料。 在 [建立器] 索引標籤中選取 **POST** HTTP 方法，然後輸入下列 URL。 對於此要求以及本文中提及的其他要求，請將 `{Azure-Maps-Primary-Subscription-key}` 取代為您的主要訂用帳戶金鑰。

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. 在 **POST** 要求的 [標頭] 中，將 `Content-Type` 設定為 `application/json`。 在 **主體** 中，提供下列座標點資訊。 完成後，按一下 [傳送]。

     ```json
    [
        {
            "lon": -73.998672,
            "lat": 40.714728
        },
        {
            "lon": 150.644,
            "lat": -34.397
        }
    ]
    ```

### <a name="request-elevation-data-samples-along-a-polyline"></a>沿著聚合線條要求提高許可權的資料範例

在此範例中，我們將使用資料行的 [ [取得資料](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline) ]，在 Mt 的座標之間，以直線來要求五個同樣間距的提高許可權資料樣本。 Everest 和 Chamlang 山脈。 這兩個座標都必須以長/Lat 格式來定義。 如果您未指定參數的值 `samples` ，樣本數預設為10。 樣本數上限為2000。

然後，我們將使用「取得資料」的「取得資料」來要求沿著路徑的提高許可權資料的三個平均間距樣本。 我們會以三個長/Lat 座標組傳遞，來定義樣本的精確位置。

最後，我們會使用聚合線條 [API 的 Post 資料](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline) ，在相同的三個同樣間距的範例中要求提高許可權的資料。

URL 中的緯度和經度預期會在 WGS84 (World Geodetic System) 小數度。

 >[!IMPORTANT]
 >由於 URL 字元長度限制為2048，因此無法在 URL GET 要求中傳遞100個以上的座標作為管線分隔字串。 如果您想要傳遞100個以上的座標作為管線分隔字串，請使用 POST 資料來取得點。

1. 選取 [ **新增**]。 在 [新建] 視窗中，選取 [要求]。 輸入 **要求名稱**，然後選取集合。 按一下 [檔案] 。

2. 在 [建立器] 索引標籤中選取 [ **取得** HTTP 方法]，並輸入下列 URL。 對於此要求以及本文中提及的其他要求，請將 `{Azure-Maps-Primary-Subscription-key}` 取代為您的主要訂用帳戶金鑰。

   ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&lines=-73.998672,40.714728|150.644,-34.397&samples=5
    ```

3. 按一下 [傳送] 按鈕。  您將會收到下列 JSON 回應：

    ```JSON
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 40.714728,
                    "longitude": -73.998672
                },
                "elevationInMeter": 12.14236
            },
            {
                "coordinate": {
                    "latitude": 21.936796000000001,
                    "longitude": -17.838003999999998
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": 3.1588640000000012,
                    "longitude": 38.322664000000003
                },
                "elevationInMeter": 598.66943
            },
            {
                "coordinate": {
                    "latitude": -15.619067999999999,
                    "longitude": 94.483332000000019
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": -34.397,
                    "longitude": 150.644
                },
                "elevationInMeter": 384.47041
            }
        ]
    }
    ```

4. 現在，我們會在 Mount Everest、Chamlang 和 Jannu 山脈的座標之間，以路徑來要求提升許可權資料的三個範例。 在 [ **參數** ] 區段中，複製下列座標陣列以取得查詢索引 `lines` 鍵的值。

    ```html
        86.9797222, 27.775|86.9252778, 27.9880556 | 88.0444444, 27.6822222
    ```

5. 將 `samples` 查詢金鑰值變更為 `3` 。  下圖顯示新的值。

     :::image type="content" source="./media/how-to-request-elevation-data/get-elevation-samples.png" alt-text="取出三個提高許可權的資料範例。":::

6. 按一下藍色的 [ **傳送** ] 按鈕。 您將會收到下列 JSON 回應：

    ```json
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 27.775,
                    "longitude": 86.9797222
                },
                "elevationInMeter": 7116.0348851572589
            },
            {
                "coordinate": {
                    "latitude": 27.737403546316028,
                    "longitude": 87.411180791156454
                },
                "elevationInMeter": 1798.6945512521534
            },
            {
                "coordinate": {
                    "latitude": 27.682222199999998,
                    "longitude": 88.0444444
                },
                "elevationInMeter": 7016.9372013588072
            }
        ]
    }
    ```

7. 現在，我們會呼叫聚合 [程式 API 的 Post 資料](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline) ，取得相同三個點的提高許可權資料。 在 [建立器] 索引標籤中選取 **POST** HTTP 方法，然後輸入下列 URL。 對於此要求以及本文中提及的其他要求，請將 `{Azure-Maps-Primary-Subscription-key}` 取代為您的主要訂用帳戶金鑰。

    ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&samples=5
    ```

8. 在 **POST** 要求的 [標頭] 中，將 `Content-Type` 設定為 `application/json`。 在 **主體** 中，提供下列座標點資訊。 完成後，按一下 [傳送]。

     ```json
    [
        {
            "lon": 86.9797222,
            "lat": 27.775
        },
        {
            "lon": 86.9252778,
            "lat": 27.9880556
        },
        {
            "lon": 88.0444444,
            "lat": 27.6822222
        }
    ]
    ```

### <a name="request-elevation-data-by-bounding-box"></a>依周框方塊要求提高許可權資料

現在，我們將使用 [ [取得周框的資料](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox) ] 方塊來要求在 Mt 附近提高許可權的資料。 尼爾山，WA。 提高許可權資料會在周框方塊中的相同位置傳回。  (2) 一組 lat/long 座標所定義的周框區域 (南部緯度、西部經度 |北美洲緯度、東部經度) 會分割成資料列和資料行。 兩個 (2) 資料列的周框方塊，以及兩個 (2) 資料行的邊緣。 針對在資料列和資料行交集處建立的方格頂點，會傳回提升許可權。 在單一要求中可傳回最高2000的提升。

在此範例中，我們會指定 rows = 3，而 columns = 6。 系統會在回應中傳回18個提高許可權的值。 在下圖中，提升許可權值的順序是以西南角為開頭，然後繼續向下西部和南至北。  提高許可權點會依傳回的順序編號。

:::image type="content" source="./media/how-to-request-elevation-data/bounding-box.png" border="false" alt-text="周框方塊座標位於 NE 和 SE 角落。":::

1. 選取 [ **新增**]。 在 [新建] 視窗中，選取 [要求]。 輸入 **要求名稱**，然後選取集合。 按一下 [檔案] 。

2. 在 [建立器] 索引標籤中選取 [ **取得** HTTP 方法]，並輸入下列 URL。 對於此要求以及本文中提及的其他要求，請將 `{Azure-Maps-Primary-Subscription-key}` 取代為您的主要訂用帳戶金鑰。

    ```http
    https://atlas.microsoft.com/elevation/lattice/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&bounds=-121.66853362143818, 46.84646479863713,-121.65853362143818, 46.85646479863713&rows=2&columns=3
    ```

3. 按一下藍色的 [ **傳送** ] 按鈕。 18個提高許可權的資料範例（格線的每個頂點各一個）會在回應中傳回。

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2298.6581875651746
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2306.3980756609963
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2279.3385479564113
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2233.1549264690366
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2196.4485923541492
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2133.1756767157253
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2345.3227848228803
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2292.2449195443587
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2270.5867788258074
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2296.8311427390604
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2266.0729430891065
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2242.216346631234
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2378.460838833359
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2327.6761137260387
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2208.3782743402949
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2106.9526472760981
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2054.3270174034078
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2030.6438331110671
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2318.753153399402
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2253.88875188271
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2136.6145845357587
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2073.6734467948486
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2042.994055784251
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 1988.3631481900356
        }
    ]
    }
    ```

## <a name="samples-use-elevation-service-preview-apis-in-azure-maps-control"></a>範例：使用 Azure 地圖服務控制項中的提高許可權服務 (預覽版) Api

### <a name="get-elevation-data-by-coordinate-position"></a>依座標位置取得提高許可權資料

下列範例網頁會示範如何使用地圖控制項，在座標點顯示提高許可權的資料。 當使用者拖曳標記時，地圖會在快顯中顯示提高許可權的資料。

<br/>

<iframe height="500" style="width:100%;" scrolling="no" title="在位置取得提高許可權" src="https://codepen.io/azuremaps/embed/c840b510e113ba7cb32809591d5f96a2?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
在 >codepen 上，查看畫筆在位置上的 Azure 地圖服務 () <a href='https://codepen.io/azuremaps/pen/c840b510e113ba7cb32809591d5f96a2'>取得提升許可權</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'> </a>。
</iframe>

### <a name="get-elevation-data-by-bounding-box"></a>依周框方塊取得提高許可權資料

下列範例網頁會示範如何使用地圖控制項來顯示包含在周框方塊內的提高許可權資料。 使用者可以按一下 `square` 左上角的圖示，然後在地圖上的任意位置繪製正方形，藉以定義周框方塊。 然後，地圖控制項會根據位於右上角的索引鍵所指定的色彩，來轉譯提高許可權的資料。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="依周框方塊的提升" src="https://codepen.io/azuremaps/embed/619c888c70089c3350a3e95d499f3e48?height=500&theme-id=default&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
在 >codepen 上 Azure 地圖服務 () ，<a href='https://codepen.io/azuremaps/pen/619c888c70089c3350a3e95d499f3e48'>以依周框</a>方塊查看畫筆提升 <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'></a>
</iframe>

### <a name="get-elevation-data-by-polyline-path"></a>依聚合線條路徑取得提升許可權資料

下列範例網頁會示範如何使用地圖控制項，沿著路徑顯示提高許可權資料。 使用者可以按一下 `PolyLine` 左上角的圖示，然後在地圖上繪製聚合線條來定義路徑。 然後，地圖控制項會以在右上角的索引鍵中指定的色彩轉譯提高許可權的資料。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="提高許可權路徑漸層" src="https://codepen.io/azuremaps/embed/7bee08e5cb13d05cb0a11636b60f14ca?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
請參閱 >codepen 上的 Azure 地圖服務 () ，查看畫筆提高<a href='https://codepen.io/azuremaps/pen/7bee08e5cb13d05cb0a11636b60f14ca'>許可權路徑</a>漸層 <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'></a>
</iframe>


## <a name="next-steps"></a>後續步驟

若要進一步探索 Azure 地圖服務提升 (Preview) Api 的許可權，請參閱：

> [!div class="nextstepaction"]
> [提高許可權 (預覽) -取得 Lat 長座標的資料](/rest/api/maps/elevation/getdataforpoints)

> [!div class="nextstepaction"]
> [提高許可權 (預覽) -取得周框方塊的資料](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox)

> [!div class="nextstepaction"]
> [提高許可權 (預覽) -取得聚合線條的資料](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline)

> [!div class="nextstepaction"]
> [轉譯 V2 –取得地圖底圖](https://docs.microsoft.com/rest/api/maps/renderv2)

如需 Azure 地圖服務 REST API 的完整清單，請參閱：

> [!div class="nextstepaction"]
> [Azure 地圖服務 REST API](https://docs.microsoft.com/rest/api/maps/)
