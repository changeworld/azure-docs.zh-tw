---
title: 在柵格地圖上渲染自訂資料 |微軟 Azure 地圖
description: 在本文中，您將瞭解如何使用 Microsoft Azure 映射靜態圖像服務在柵格地圖上呈現自訂資料。
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b8d47b69b4aba14c86fb09176b662aee7d5482d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335508"
---
# <a name="render-custom-data-on-a-raster-map"></a>在柵格地圖上渲染自訂資料

本文介紹如何使用具有圖像合成功能的[靜態圖像服務](https://docs.microsoft.com/rest/api/maps/render/getmapimage)，以允許在柵格貼圖頂部疊加。 圖像合成包括返回柵格磁貼的能力，以及自訂圖釘、標籤和幾何疊加等其他資料。

要渲染自訂圖釘、標籤和幾何疊加，可以使用 Postman 應用程式。 可以使用 Azure 地圖[資料服務 API](https://docs.microsoft.com/rest/api/maps/data)來存儲和呈現疊加。

> [!Tip]
> 使用 Azure 地圖 Web SDK 在網頁上顯示簡單地圖通常比使用靜態圖像服務更具成本效益。 Web SDK 使用地圖切片，除非使用者平移和縮放地圖，否則它們通常僅生成每個地圖負載的事務的一小部分。 請注意，Azure 地圖 Web SDK 具有禁用平移和縮放的選項。 此外，Azure 地圖 Web SDK 提供了比靜態地圖 Web 服務更豐富的資料視覺化選項組。  

## <a name="prerequisites"></a>Prerequisites

### <a name="create-an-azure-maps-account"></a>建立 Azure 地圖服務帳戶

要完成本文中的過程，首先需要創建 Azure 地圖帳戶並獲取地圖帳戶金鑰。 按照[創建帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)中的說明創建 Azure 地圖帳戶訂閱，然後按照[獲取主鍵](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的步驟獲取帳戶的主金鑰。 如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>使用標籤和自訂圖像渲染圖釘

> [!Note]
> 本節中的過程要求在定價層 S0 或 S1 中使用 Azure 地圖帳戶。

Azure 映射帳戶 S0 層僅支援`pins`參數的單個實例。 它允許您使用自訂圖像渲染最多五個在 URL 請求中指定的圖釘。

要使用標籤和自訂圖像渲染圖釘，完成以下步驟：

1. 創建用於存儲請求的集合。 在"郵遞員"應用中，選擇 **"新建**"。 在 **"創建新"** 視窗中，選擇 **"集合**"。 命名集合併選擇"**創建**"按鈕。 

2. 要創建請求，請再次選擇 **"新建**"。 在 **"創建新"** 視窗中，選擇 **"請求**"。 輸入圖釘**的請求名稱**。 選擇您在上一步中創建的集合，作為保存請求的位置。 然後，選擇 **"保存**"。
    
    ![在郵遞員中創建請求](./media/how-to-render-custom-data/postman-new.png)

3. 選擇產生器選項卡上的 GET HTTP 方法，然後輸入以下 URL 以創建 GET 請求。

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    下面是生成的圖像：

    ![帶有標籤的自訂圖釘](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>從 Azure 地圖服務資料儲存體取得資料

> [!Note]
> 本節中的過程要求在定價層 S1 中使用 Azure 地圖帳戶。

您還可以使用[資料上傳 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)獲取路徑和引腳位置資訊。 遵循下列步驟來上傳路徑和圖釘資料。

1. 在 Postman 應用中，在上一節中創建的集合中打開一個新選項卡。 在產生器選項卡上選擇 POST HTTP 方法，然後輸入以下 URL 以發出 POST 請求：

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. 在**Params**選項卡上，輸入以下用於 POST 請求 URL 的鍵/值對。 將`subscription-key`該值替換為 Azure 地圖訂閱金鑰。
    
    ![郵遞員中的關鍵/值參數](./media/how-to-render-custom-data/postman-key-vals.png)

3. 在 **"正文"** 選項卡上，選擇原始輸入格式，並從下拉清單中選擇 JSON 作為輸入格式。 提供此 JSON 作為要上載的資料：
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. 選擇 **"發送**並查看回應標頭"。 要求成功時，[位置] 標頭將會包含狀態 URI，用以查看上傳要求目前的狀態。 狀態 URI 將採用以下格式。  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. 複製狀態 URI 並將訂閱鍵參數追加到其使用 Azure 地圖帳戶訂閱金鑰的值。 使用用於上載資料的相同帳戶訂閱金鑰。 狀態 URI 格式應如下所示：

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. 要獲取 udId，在 Postman 應用中打開一個新選項卡。 在產生器選項卡上選擇 GET HTTP 方法。在狀態 URI 發出 GET 請求。 如果資料上傳成功，您將在回應正文中收到 udId。 複製 udId。

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. 使用從`udId`資料上傳 API 接收的值在地圖上呈現要素。 為此，在上一節中創建的集合中打開一個新選項卡。 選擇產生器選項卡上的 GET HTTP 方法，將 [訂閱金鑰] 和 [udId] 替換為您的值，然後輸入此 URL 以發出 GET 請求：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    下面是回應圖像：

    ![從 Azure 地圖服務資料儲存體取得資料](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>渲染具有顏色和不透明的多邊形

> [!Note]
> 本節中的過程要求在定價層 S1 中使用 Azure 地圖帳戶。


您可以使用樣式修飾詞搭配[路徑參數](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)來修改多邊形的外觀。

1. 在 Postman 應用中，在之前創建的集合中打開一個新選項卡。 選擇產生器選項卡上的 GET HTTP 方法，然後輸入以下 URL 以配置 GET 請求以呈現具有顏色和不透明的多邊形：
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    下面是回應圖像：

    ![渲染不透明多邊形](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>使用自訂標籤渲染圓和圖釘

> [!Note]
> 本節中的過程要求在定價層 S1 中使用 Azure 地圖帳戶。


您可以通過添加樣式修改器來修改引腳的外觀。 例如，要使圖釘及其標籤更大或更小，請使用`sc`"縮放樣式"修改器。 此修改器獲取大於零的值。 1 的值是標準比例。 大於 1 的值會使圖釘變大，小於 1 的值會使它們變小。 有關樣式修改器的詳細資訊，請參閱[靜態圖像服務路徑參數](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)。


按照以下步驟使用自訂標籤渲染圓和圖釘：

1. 在 Postman 應用中，在之前創建的集合中打開一個新選項卡。 選擇產生器選項卡上的 GET HTTP 方法，然後輸入此 URL 以發出 GET 請求：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    下面是回應圖像：

    ![使用自訂圖釘渲染圓](./media/how-to-render-custom-data/circle-custom-pins.png)

2. 要更改上一步圖釘的顏色，更改"co"樣式修改器。 看`pins=default|la15+50|al0.66|lc003C62|co002D62|`，使用中色彩將在 CSS 中指定為#002D62。 假設您要將其更改為#41d42a。 在"co"指定器之後寫入新的顏色值，如下所示： `pins=default|la15+50|al0.66|lc003C62|co41D42A|`。 發出新的 GET 請求：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    以下是更改引腳顏色後的回應圖像：

    ![使用更新的圖釘渲染圓](./media/how-to-render-custom-data/circle-updated-pins.png)

同樣，您可以更改、添加和刪除其他樣式修飾符。

## <a name="next-steps"></a>後續步驟


* 探索 [Azure 地圖服務 Get Map Image API](https://docs.microsoft.com/rest/api/maps/render/getmapimage) \(英文\) 文件。
* 要瞭解有關 Azure 地圖資料服務的更多內容，請參閱[服務文件](https://docs.microsoft.com/rest/api/maps/data)。

