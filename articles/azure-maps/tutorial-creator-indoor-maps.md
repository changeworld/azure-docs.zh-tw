---
title: 教學課程：使用 Microsoft Azure Maps Creator (預覽) 建立室內地圖
description: 如何使用 Microsoft Azure Maps Creator (預覽) 建立室內地圖的教學課程
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: dac5a434d7f7c62d7a20e971294992ea91c79d2b
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625010"
---
# <a name="tutorial-use-creator-preview-to-create-indoor-maps"></a>教學課程：使用 Creator (預覽) 建立工具建立室內地圖

> [!IMPORTANT]
> Azure Maps Creator 服務目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本教學課程說明如何建立室內地圖。 在本教學課程中，您將了解如何使用 API 執行下列動作：

> [!div class="checklist"]
> * 上傳您的室內地圖繪圖套件
> * 將繪圖套件轉換成地圖資料
> * 從地圖資料建立資料集
> * 從資料集中的資料建立圖格集
> * 查詢 Azure 地圖服務 Web Feature Service (WFS) API，以了解您的地圖功能
> * 使用您的地圖功能和資料集中的資料建立功能狀態集
> * 更新您的功能狀態集

## <a name="prerequisites"></a>Prerequisites

若要建立室內地圖：

1. [建立 Azure 地圖服務帳戶](quick-demo-map-app.md#create-an-azure-maps-account)
2. [取得主要訂用帳戶金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)，也稱為主要金鑰或訂用帳戶金鑰。
3. [建立 Creator (預覽) 資源](how-to-manage-creator.md)
4. 下載[範例繪圖套件](https://github.com/Azure-Samples/am-creator-indoor-data-examples/blob/master/Sample%20-%20Contoso%20Drawing%20Package.zip)。

本教學課程使用 [Postman](https://www.postman.com/) 應用程式，但您可以選擇不同的 API 開發環境。

>[!IMPORTANT]
> 本文件中的 API URL 可能必須根據您建立者資源的位置進行調整。 如需詳細資訊，請參閱[存取建立工具服務](how-to-manage-creator.md#access-to-creator-services)。

## <a name="upload-a-drawing-package"></a>上傳繪圖套件

使用[資料上傳 API](/rest/api/maps/data/uploadpreview)，將繪圖套件上傳至 Azure 地圖服務資源。

資料上傳 API 是一個長時間執行的交易，會實作此處定義的模式。 作業完成後，我們會使用 `udid` 來存取上傳的套件，以進行轉換。 請依照下列步驟取得 `udid`。

1. 開啟 Postman 應用程式。 在 Postman 應用程式頂端處，選取 [新增]。 在 [新建] 視窗中，選取 [集合]。  為集合命名，然後選取 [建立] 按鈕。

2. 若要建立要求，請再次選取 [新增]。 在 [新建] 視窗中，選取 [要求]。 輸入要求的 [要求名稱]。 選取您在先前的步驟中建立的集合，然後選取 [儲存]。

3. 在 [產生器] 索引標籤中選取 **POST** HTTP 方法，然後輸入下列 URL，以將繪圖套件上傳至 Azure 地圖服務。 對於此要求以及本文中提及的其他要求，請將 `{Azure-Maps-Primary-Subscription-key}` 取代為您的主要訂用帳戶金鑰。

    ```http
    https://atlas.microsoft.com/mapData/upload?api-version=1.0&dataFormat=zip&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. 在 [標頭] 索引標籤中，指定 `Content-Type` 索引鍵的值。 繪圖套件是壓縮的資料夾，因此請使用 `application/octet-stream` 值。 在 [本文] 索引標籤中，選取 [二進位]。 按一下 [選取檔案]，然後選擇繪圖套件。

     ![資料管理](./media/tutorial-creator-indoor-maps/enter-content-type-dialog.png)

5. 按一下藍色的 [傳送] 按鈕，然後等候系統處理要求。 在要求完成後，移至回應的 [標頭] 索引標籤。 複製 [位置] 索引鍵的值，也就是 `status URL`。

6. 若要檢查 API 呼叫的狀態，請在 `status URL` 建立 **GET** HTTP 要求。 您必須將主要訂用帳戶金鑰附加至 URL，以進行驗證。 **GET** 要求應會類似於下列 URL：

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

7. 當 **GET** HTTP 要求成功完成時，會傳回 `resourceLocation`。 `resourceLocation` 也包含已上傳內容的唯一 `udid`。 您也可以在下一個步驟中使用 `resourceLocation` URL 來擷取此資源中的中繼資料。

    ```json
    {
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
    }
    ```

8. 若要擷取內容中繼資料，請對您在步驟 7 中擷取的 `resourceLocation` URL 建立 **GET** HTTP 要求。 務必將主要訂用帳戶金鑰附加至 URL，以進行驗證。 **GET** 要求應會類似於下列 URL：

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. 當 **GET** HTTP 要求成功完成時，回應主體會包含步驟 7 的 `resourceLocation` 中指定的 `udid`、未來存取/下載內容的位置，以及一些關於內容的其他中繼資料，例如建立/更新日期、大小等等。 整體回應的範例如下：

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "2020-02-03T02:32:25.0509366+00:00",
        "updated": "2020-02-11T06:12:13.0309351+00:00",
        "sizeInBytes": 766,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>轉換繪圖套件

 在繪圖套件上傳後，我們將對上傳的套件使用 `udid`，以將套件轉換成地圖資料。 轉換 API 會使用長時間執行的交易，實作[此處](creator-long-running-operation.md)定義的模式。 作業完成後，我們會使用 `conversionId` 來存取已轉換的資料。 請依照下列步驟取得 `conversionId`。

1. 選取 [ **新增**]。 在 [新建] 視窗中，選取 [要求]。 輸入 **要求名稱**，然後選取集合。 按一下 [檔案] 。

2. 在 [產生器] 索引標籤中選取 **POST** HTTP 方法，然後輸入下列 URL，以將您上傳的繪圖套件轉換成地圖資料。 請使用上傳套件的 `udid`。

    ```http
    https://atlas.microsoft.com/conversion/convert?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&udid={udid}&inputType=DWG
    ```

    >[!IMPORTANT]
    > 本文件中的 API URL 可能必須根據您建立者資源的位置進行調整。 如需詳細資訊，請參閱[存取 Creator 服務 (預覽)](how-to-manage-creator.md#access-to-creator-services)。

3. 按一下 [傳送] 按鈕，然後等候系統處理要求。 在要求完成後，移至回應的 [標頭] 索引標籤，並尋找 [位置] 索引鍵。 複製 [位置] 索引鍵的值，此為轉換要求的 `status URL`。 您將會在下個步驟中使用這個。

    :::image type="content" source="./media/tutorial-creator-indoor-maps/copy-location-uri-dialog.png" border="true" alt-text="複製 [位置] 索引鍵的值":::

4. 在 [產生器] 索引標籤中啟動新的 **GET** HTTP 方法。將您的 Azure 地圖服務主要訂用帳戶金鑰附加至 `status URL`。 在您於步驟 3 中複製的 `status URL` 進行 **GET** 要求。 `status URL` 看起來如下列 URL：

    ```http
    https://atlas.microsoft.com/conversion/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

    如果轉換程序尚未完成，您可能會看到類似下列 JSON 回應的內容：

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Running"
    }
    ```

5. 要求順利完成後，您將會在回應本文中看到成功的狀態訊息。  請從已轉換套件的 `resourceLocation` URL 中複製 `conversionId`。 其他 API 會使用 `conversionId` 來存取已轉換的地圖資料。

    ```json
   {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {}
    }
    ```

>[!NOTE]
>Postman 應用程式原本並不支援 HTTP 長時間執行的要求。 因此，對狀態 URL 提出 **GET** 要求時，您可能會發現有很長的延遲。  請等候約 30 秒再嘗試點按 [傳送] 按鈕，直到回應顯示成功或失敗為止。

範例繪圖套件應該會在沒有錯誤或警告的情況下進行轉換。 但若您本身的繪圖套件傳回錯誤或警告，JSON 回應將會提供[繪製錯誤視覺化檢視](drawing-error-visualizer.md)的連結。 繪製錯誤視覺化檢視可讓您檢查錯誤和警告的詳細資料。 如需如何解決轉換錯誤和警告的建議，請參閱[繪製轉換錯誤和警告](drawing-conversion-error-codes.md)。

```json
{
    "operationId": "<operationId>",
    "created": "2020-04-22T19:39:54.9518496+00:00",
    "status": "Failed",
    "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>建立資料集

此資料集是地圖功能的集合，例如建築物、樓層和房間。 若要建立資料集，請使用[資料集建立 API](/rest/api/maps/dataset/createpreview)。 資料集建立 API 會使用已轉換繪圖套件的 `conversionId`，並傳回已建立資料集的 `datasetId`。 下列步驟說明如何建立資料集。

1. 在 Postman 應用程式中，選取 [新增]。 在 [新建] 視窗中，選取 [要求]。 輸入 **要求名稱**，然後選取集合。 按一下 [儲存] 

2. 對 [資料集建立 API](/rest/api/maps/dataset/createpreview) 提出 **POST** 要求，以建立新的資料集。 提交要求之前，請附加您的訂用帳戶金鑰和 `conversionId`，以及在步驟 5 的轉換程序期間取得的 `conversionId`。  要求應會類似於下列 URL：

    ```http
    https://atlas.microsoft.com/dataset/create?api-version=1.0&conversionID={conversionId}&type=facility&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 在回應 **標頭** 的 [位置] 索引鍵中，取得 `statusURL`。

4. 對 `statusURL` 提出 **GET** 要求，以取得 `datasetId`。 請附加您的 Azure 地圖服務主要訂用帳戶金鑰，以進行驗證。 要求應會類似於下列 URL：

    ```http
    https://atlas.microsoft.com/dataset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

5. 在 **GET** HTTP 要求順利完成後，回應標頭將會包含已建立之資料集的 `datasetId`。 複製 `datasetId`。 您必須使用 `datasetId` 來建立圖格集。

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:52:38.9352189+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://azure.microsoft.com/dataset/{datasetiId}?api-version=1.0"
     }
    ```

## <a name="create-a-tileset"></a>建立圖格集

圖格集是在地圖上轉譯的一組向量圖格。 圖格集是從現有資料集建立而來的。 不過，圖格集並不依賴其來源資料集。 如果資料集遭刪除，圖格集將繼續存在。 若要建立圖格集，請遵循下列步驟：

1. 在 Postman 應用程式中，選取 [新增]。 在 [新建] 視窗中，選取 [要求]。 輸入 **要求名稱**，然後選取集合。 按一下 [儲存] 

2. 在 [產生器] 索引標籤中提出 **POST** 要求。要求 URL 應會類似於下列 URL：

    ```http
    https://atlas.microsoft.com/tileset/create/vector?api-version=1.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 對圖格集的 `statusURL` 提出 **GET** 要求。 請附加您的 Azure 地圖服務主要訂用帳戶金鑰，以進行驗證。 要求應會類似於下列 URL：

   ```http
    https://atlas.microsoft.com/tileset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. 在 **GET** HTTP 要求順利完成後，回應標頭將會包含已建立之圖格集的 `tilesetId`。 複製 `tilesetId`。

    ```json
    {
        "operationId": "<operationId>",
        "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/tileset/{tilesetId}?api-version=1.0"
    }
    ```

## <a name="query-datasets-with-wfs-api"></a>使用 WFS API 查詢資料集

 您可以使用 [WFS API](/rest/api/maps/wfs) 來查詢資料集。 您可以使用 WFS API 來查詢功能集合、特定集合，或具有功能 **識別碼** 的特定功能。 功能 **識別碼** 可唯一識別資料集內的功能。 例如，可用來識別特定狀態集中的哪些功能狀態應更新。

1. 在 Postman 應用程式中，選取 [新增]。 在 [新建] 視窗中，選取 [要求]。 輸入 **要求名稱**，然後選取集合。 按一下 [儲存] 

2. 提出 **GET** 要求，以檢視資料集中的集合清單。 請將 `<dataset-id>` 取代為您自己的 `datasetId`。 請使用您的 Azure 地圖服務主要金鑰，而非預留位置。 要求應會類似於下列 URL：

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

3. 回應本文會以 GeoJSON 格式傳遞，且會包含資料集內的所有集合。 為了簡單起見，此處的範例僅顯示 `unit` 集合。 若要查看包含所有集合的範例，請參閱 [WFS 說明集合 API](/rest/api/maps/wfs/collectiondescriptionpreview)。 若要深入了解任何集合，您可以按一下 `link` 元素內的任何 URL。

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

4. 對 `unit` 功能集合提出 **GET** 要求。  請將 `{datasetId}` 取代為您自己的 `datasetId`。 請使用您的 Azure 地圖服務主要金鑰，而非預留位置。 回應本文會包含 `unit` 集合的所有功能。 要求應會類似於下列 URL：

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. 複製有樣式屬性可進行動態修改之單元功能的功能 `id`。  由於單元使用狀態和溫度可以動態更新，我們將在下一節中使用此功能 `id`。 在下列範例中，功能 `id` 為 "UNIT26"。 我們將此功能的樣式屬性稱為狀態，且將使用此功能來建立狀態集。

     ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>建立功能狀態集

1. 在 Postman 應用程式中，選取 [新增]。 在 [新建] 視窗中，選取 [要求]。 輸入 **要求名稱**，然後選取集合。 按一下 [儲存] 

2. 對 [建立狀態集 API](/rest/api/maps/featurestate/createstatesetpreview) 提出 **POST** 要求。 請使用您要修改的狀態所屬資料集的 `datasetId`。 要求應會類似於下列 URL：

    ```http
    https://atlas.microsoft.com/featureState/stateset?api-version=1.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 在 **POST** 要求的 [標頭] 中，將 `Content-Type` 設定為 `application/json`。 在 [本文] 中，提供下列樣式以反映 `occupied` 和 `temperature` *狀態* 的變更。 完成後，按一下 [傳送]。

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          },
          {
             "keyname":"temperature",
             "type":"number",
             "rules":[
                {
                   "range":{
                      "exclusiveMaximum":66
                   },
                   "color":"#00204e"
                },
                {
                   "range":{
                      "minimum":66,
                      "exclusiveMaximum":70
                   },
                   "color":"#0278da"
                },
                {
                   "range":{
                      "minimum":70,
                      "exclusiveMaximum":74
                   },
                   "color":"#187d1d"
                },
                {
                   "range":{
                      "minimum":74,
                      "exclusiveMaximum":78
                   },
                   "color":"#fef200"
                },
                {
                   "range":{
                      "minimum":78,
                      "exclusiveMaximum":82
                   },
                   "color":"#fe8c01"
                },
                {
                   "range":{
                      "minimum":82
                   },
                   "color":"#e71123"
                }
             ]
          }
       ]
    }
    ```

4. 從回應本文中複製 `statesetId`。

5. 建立 **POST** 要求以更新狀態：傳遞 statesetId 和功能 `ID` 以及您的 Azure 地圖服務訂用帳戶金鑰。 要求應會類似於下列 URL：

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID={featureId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. 在 **POST** 要求的 [標頭] 中，將 `Content-Type` 設定為 `application/json`。 在 **POST** 要求的 **BODY** 中，複製並貼上下列範例中的 JSON。

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

    >[!NOTE]
    > 只有在張貼的時間戳記晚於前次要求的時間戳記時，才會儲存更新。 我們可以傳遞先前在建立期間設定的任何索引鍵名稱。

7. 成功更新後，您將會收到 `200 OK` HTTP 狀態碼。 如果您已為室內地圖[實作動態樣式](indoor-map-dynamic-styling.md)，則更新將會依指定的時間戳記顯示於轉譯的地圖中。

[功能取得狀態 API](/rest/api/maps/featurestate/getstatespreview) 可讓您使用功能 `ID` 擷取功能的狀態。 您也可以使用[功能狀態刪除 API](/rest/api/maps/featurestate/deletestatesetpreview) 來刪除狀態集及其資源。

若要深入了解本文中討論的不同 Azure Maps Creator 服務 (預覽)，請參閱 [Creator 室內地圖](creator-indoor-maps.md)。

## <a name="clean-up-resources"></a>清除資源

沒有任何資源需要清除。

## <a name="next-steps"></a>後續步驟

若要了解如何使用室內地圖模組，請參閱

> [!div class="nextstepaction"]
> [使用室內地圖模組](how-to-use-indoor-module.md)