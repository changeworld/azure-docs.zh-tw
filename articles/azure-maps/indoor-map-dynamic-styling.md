---
title: 實作 Azure 地圖服務建立工具室內地圖的動態樣式
description: 了解如何實作建立工具室內地圖的動態樣式
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 6edc114a2d69dfe8f1e6e5d3c0a2d4af26dbad67
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596597"
---
# <a name="implement-dynamic-styling-for-creator-indoor-maps"></a>實作建立工具室內地圖的動態樣式

Azure 地圖服務建立工具的[功能狀態服務](https://docs.microsoft.com/rest/api/maps/featurestate/featurestate)可讓您根據室內地圖資料功能的動態屬性來套用樣式。  例如，您可以使用特定色彩來顯示公用會議室，以反映使用狀態。 在本文中，我們將說明如何使用[功能狀態服務](https://docs.microsoft.com/rest/api/maps/featurestate/featurestate)和[室內 Web 模組](how-to-use-indoor-module.md)，以動態方式顯示室內地圖功能。

## <a name="prerequisites"></a>Prerequisites

1. [建立 Azure 地圖服務帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)
2. [取得主要訂用帳戶金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)，也稱為主要金鑰或訂用帳戶金鑰。
3. [建立一個建立工具資源](how-to-manage-creator.md)
4. 下載[範例繪圖套件](https://github.com/Azure-Samples/am-creator-indoor-data-examples)。
5. [建立室內地圖](tutorial-creator-indoor-maps.md)以取得 `tilesetId` 和 `statesetId`。
6. 依照[如何使用室內地圖模組](how-to-use-indoor-module.md)中的步驟建置 Web 應用程式。

本教學課程使用 [Postman](https://www.postman.com/) 應用程式，但您可以選擇不同的 API 開發環境。

## <a name="implement-dynamic-styling"></a>實作動態樣式

完成必要條件後，您應該會有一個使用您的訂用帳戶金鑰、`tilesetId` 和 `statesetId` 設定的簡易 Web 應用程式。

### <a name="select-features"></a>特性選取

若要實作動態樣式，一項功能 (例如會議或會議室) 必須以其功能 `id` 來參考。 您將使用功能 `id` 來更新該功能的動態屬性或*狀態*。 若要檢視資料集內定義的功能，您可以使用下列其中一種方法：

* WFS API (Web Feature Service)。 資料集可使用 WFS API 來查詢。 WFS 會依循開放地理空間協會 API 功能。 WFS API 有助於查詢資料集內的功能。 例如，您可以使用 WFS 來尋找特定設施和樓層的所有中型會議室。

* 實作自訂的程式碼，讓使用者使用您的 Web 應用程式選取地圖上的功能。 在本文中，我們將使用這個選項。  

下列指令碼會實作滑鼠點擊事件。 程式碼會根據點擊的點來擷取功能 `id`。 在您的應用程式中，您可以將程式碼插入室內管理員程式碼區塊下方。 執行您的應用程式，然後檢查主控台，以取得所點擊之點的功能 `id`。

```javascript
/* Upon a mouse click, log the feature properties to the browser's console. */
map.events.add("click", function(e){

    var features = map.layers.getRenderedShapes(e.position, "indoor")

    var result = features.reduce(function (ids, feature) {
        if (feature.layer.id == "indoor_unit_office") {
            console.log(feature);
        }
    }, []);
});
```

[建立室內地圖](tutorial-creator-indoor-maps.md)教學課程已將功能狀態集設定為接受 `occupancy` 的狀態更新。

在下一節中，我們會將辦公室 `UNIT26` 的使用*狀態*設定為 `true`。 辦公室 `UNIT27` 將會設定為 `false`。

### <a name="set-occupancy-status"></a>設定使用狀態

 現在，我們將更新 `UNIT26` 和 `UNIT27` 這兩個辦公室的狀態：

1. 在 Postman 應用程式中，選取 [新增]。 在 [新建] 視窗中，選取 [要求]。 輸入**要求名稱**，然後選取集合。 按一下 [儲存] 

2. 使用[功能更新狀態 API](https://docs.microsoft.com/rest/api/maps/featurestate/updatestatespreview) 來更新狀態。 傳遞狀態集識別碼，兩個單元其中之一的識別碼為 `UNIT26`。 附加您的 Azure 地圖服務訂用帳戶金鑰。 以下是更新狀態之 **POST** 要求的 URL：

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID=UNIT26&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 在 **POST** 要求的 [標頭] 中，將 `Content-Type` 設定為 `application/json`。 在 **POST** 要求的 **BODY** 中，撰寫下列關於功能更新的 JSON。 只有在張貼時間戳記晚於上次對相同功能 `ID` 的功能狀態更新要求中使用的時間戳記時，才會儲存更新。 傳遞「使用中」`keyName` 以更新其值。

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

4. 使用 `UNIT27` 重複執行步驟 2 和 3，JSON 如下。

    ``` json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": false,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

### <a name="visualize-dynamic-styles-on-a-map"></a>將地圖上的動態樣式視覺化

您先前在瀏覽器中開啟的 Web 應用程式此時應該會反映地圖功能的更新狀態。 `UNIT27`(151) 應該會顯示為綠色，`UNIT26`(157) 應會顯示為紅色。

![閒置的會議室以綠色表示，使用中的會議室以紅色表示](./media/indoor-map-dynamic-styling/room-state.png)

## <a name="next-steps"></a>後續步驟

閱讀下列資訊以深入了解：

> [!div class="nextstepaction"]
> [室內位置對應的建立工具](creator-indoor-maps.md)

請參閱本文所述之 API 的參考：

> [!div class="nextstepaction"]
> [資料上傳](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [資料轉換](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [資料集](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [圖格集](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [功能狀態集](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [WFS 服務](creator-indoor-maps.md#web-feature-service-api)

