---
title: 使用 Azure 地圖服務室內地圖模組
description: 了解如何使用 Microsoft Azure 地圖服務室內地圖模組，藉由內嵌模組的 JavaScript 程式庫來呈現轉譯地圖。
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 6e7b6c7ddb33d42cca66698c87c82477f3e55621
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517462"
---
# <a name="use-the-azure-maps-indoor-maps-module"></a>使用 Azure 地圖服務室內地圖模組

Azure 地圖服務 Web SDK 包括「Azure 室內地圖服務」模組。 「Azure 室內地圖服務」模組可讓您轉譯在 Azure 地圖服務建立工具中建立的室內地圖。

## <a name="prerequisites"></a>Prerequisites

1. [建立 Azure 地圖服務帳戶](quick-demo-map-app.md#create-an-azure-maps-account)
2. [建立一個建立工具資源](how-to-manage-creator.md)
3. [取得主要訂用帳戶金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)，也稱為主要金鑰或訂用帳戶金鑰。
4. 完成[建立室內地圖教學課程](tutorial-creator-indoor-maps.md)，以取得 `tilesetId` 和 `statesetId`。
 您將需要使用這些識別碼，以 Azure 地圖服務室內地圖 模組轉譯室內地圖。

## <a name="embed-the-indoor-maps-module"></a>內嵌室內地圖服務模組

您可以透過兩種方式的其中一種，安裝和內嵌「Azure 室內地圖服務」模組。

若要使用「Azure 室內地圖服務」模組的全球裝載 Azure 內容傳遞網路版本，請參考 HTML 檔案之 `<head>` 元素中的下列 JavaScript 和樣式表參考：

```html
<script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
<script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
<link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
<link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
```

 或者，您可以下載「Azure 室內地圖服務」模組。 「Azure 室內地圖服務」模組包含用來存取 Azure 地圖服務各項服務的用戶端程式庫。 請遵循下列步驟，將「室內」模組安裝並載入至您的 Web 應用程式。  
  
  1. 下載 [azure-maps-indoor package](https://www.npmjs.com/package/azure-maps-indoor)。
  
  2. 安裝 NPM 套件。 請務必在主控台中使用管理員權限：

      ```powershell
      >npm install azure-maps-control
      >npm install azure-maps-indoor
      ```

  3. 在 HTML 檔案的 `<head>` 元素中，參考「Azure 室內地圖服務」模組 JavaScript 和樣式表：

      ```html
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
      ```

## <a name="instantiate-the-map-object"></a>將地圖物件具現化

首先，建立「地圖物件」。 「地圖物件」將在下一個步驟中用來具現化「室內管理工具」物件。  下列程式碼示範如何將「地圖物件」具現化：

```javascript
const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";

const map = new atlas.Map("map-id", {
  //use your facility's location
  center: [-122.13315, 47.63637],
  //or, you can use bounds: [# west, # south, # east, # north] and replace # with your map's bounds
  style: "blank",
  view: 'Auto',
  authOptions: { 
      authType: 'subscriptionKey',
      subscriptionKey: subscriptionKey
  },
  zoom: 19,
});
```

## <a name="instantiate-the-indoor-manager"></a>將室內管理工具具現化

若要載入室內地圖底圖集和地圖底圖的地圖樣式，您必須將「室內管理工具」具現化。 提供「地圖物件」和對應的 `tilesetId`，將「室內管理工具」具現化。 如果要支援[動態地圖樣式](indoor-map-dynamic-styling.md)，必須傳遞 `statesetId`。 `statesetId` 變數名稱會區分大小寫。 您的程式碼應該類似下面的 JavaScript。

```javascript
const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "<tilesetId>",
    statesetId: "<statesetId>" // Optional
});
```

若要啟用您提供的狀態資料輪詢，必須提供 `statesetId` 並呼叫 `indoorManager.setDynamicStyling(true)`。 輪詢狀態資料可讓您以動態方式更新動態屬性狀態或和各種狀態。 例如，空間之類的功能可以有一個動態屬性 (「狀態」)，稱為 `occupancy`。 您的應用程式可能想要輪詢任何「狀態」 變更，以反映視覺效果地圖內的變更。 下列程式碼說明如何啟用狀態輪詢：

```javascript
const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "<tilesetId>",
    statesetId: "<statesetId>" // Optional
});

if (statesetId.length > 0) {
    indoorManager.setDynamicStyling(true);
}
```

## <a name="indoor-level-picker-control"></a>室內層級選擇器控制項

 「室內層級選擇器」控制項可讓您變更已轉譯地圖的層級。 您可以透過「室內管理工具」，選擇性地將「室內層級選擇器」控制項初始化。 用來初始化層級控制項選擇器的程式碼如下：

```javascript
const levelControl = new atlas.control.LevelControl({ position: "top-right" });
indoorManager.setOptions({ levelControl });
```

## <a name="indoor-events"></a>室內事件

 「Azure 室內地圖服務」模組支援「地圖物件」事件。 當層級或設施變更時，系統會叫用「地圖物件」事件接聽程式。 如果您要在層級或設施變更時執行程式碼，請將程式碼放在事件接聽程式內。 下列程式碼顯示如何將事件接聽程式新增至「地圖物件」。

```javascript
map.events.add("levelchanged", indoorManager, (eventData) => {

  //code that you want to run after a level has been changed
  console.log("The level has changed: ", eventData);
});

map.events.add("facilitychanged", indoorManager, (eventData) => {

  //code that you want to run after a facility has been changed
  console.log("The facility has changed: ", eventData);
});
```

`eventData` 變數會分別保存叫用 `levelchanged` 或 `facilitychanged` 事件之層級或設施的相關資訊。 當層級變更時，`eventData` 物件會包含 `facilityId`、新的 `levelNumber` 和其他中繼資料。 當設施變更時，`eventData` 物件會包含新的 `facilityId`、新的 `levelNumber` 和其他中繼資料。

## <a name="example-use-the-indoor-maps-module"></a>範例：使用室內地圖服務模組

此範例會示範如何在 Web 應用程式中使用「Azure 室內地圖服務」模組。 雖然此範例受到範圍限制，但其涵蓋了開始使用「Azure 室內地圖服務」模組所需的基本概念。 完整的 HTML 程式碼會遵循下列步驟。

1. 使用 Azure 內容傳遞網路[選項](#embed-the-indoor-maps-module)來安裝「Azure 室內地圖服務」模組。

2. 建立新的 HTML 檔案

3. 在 HTML 檔案標題中，參考「Azure 室內地圖服務」模組 JavaScript 和樣式表的樣式。

4. 將「地圖物件」初始化。 「地圖物件」支援下列選項：
    - `Subscription key`是您的 Azure 地圖服務主要訂用帳戶金鑰。
    - `center` 定義室內地圖中心位置的緯度和經度。 如果不想提供 `bounds` 值，請提供 `center` 值。 格式應為 `center`: [-122.13315, 47.63637]。
    - `bounds` 是用來括住地圖底圖集地圖資料的最小矩形形狀。 如果不想設定 `center` 值，請設定 `bounds` 值。 您可以呼叫[地圖底圖集清單 API](https://docs.microsoft.com/rest/api/maps/tileset/listpreview)，尋找您的地圖界限。 地圖底圖清單 API 會傳回 `bbox`，您可以剖析並指派給 `bounds`。 格式應顯示為 `bounds` ： [# west，# 南部，# 東部，# 北部]。
    - `style` 可讓您設定背景的色彩。 若要顯示白色背景，請將 `style` 定義為「空白」。
    - `zoom` 可讓您指定地圖的最小和最大縮放層級。

5. 接下來，建立「室內管理工具」模組。 指派「Azure 室內地圖服務」 `tilesetId`，並選擇性地新增 `statesetId`。

6. 將「室內層級選擇器」控制項具現化。

7. 新增「對應物件」事件接聽程式。  

現在，您的檔案看起來應該會類似下列 BTML。

  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, user-scalable=no" />
      <title>Indoor Maps App</title>
      
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>

      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
        
      <style>
        html,
        body {
          width: 100%;
          height: 100%;
          padding: 0;
          margin: 0;
        }

        #map-id {
          width: 100%;
          height: 100%;
        }
      </style>
    </head>

    <body>
      <div id="map-id"></div>
      <script>
        const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";
        const tilesetId = "<your tilesetId>";
        const statesetId = "<your statesetId>";

        const map = new atlas.Map("map-id", {
          //use your facility's location
          center: [-122.13315, 47.63637],
          //or, you can use bounds: [# west, # south, # east, # north] and replace # with your Map bounds
          style: "blank",
          view: 'Auto',
          authOptions: { 
              authType: 'subscriptionKey',
              subscriptionKey: subscriptionKey
          },
          zoom: 19,
        });

        const levelControl = new atlas.control.LevelControl({
          position: "top-right",
        });

        const indoorManager = new atlas.indoor.IndoorManager(map, {
          levelControl, //level picker
          tilesetId,
          statesetId, //optional
        });

        if (statesetId.length > 0) {
          indoorManager.setDynamicStyling(true);
        }

        map.events.add("levelchanged", indoorManager, (eventData) => {
          //put code that runs after a level has been changed
          console.log("The level has changed:", eventData);
        });

        map.events.add("facilitychanged", indoorManager, (eventData) => {
          //put code that runs after a facility has been changed
          console.log("The facility has changed:", eventData);
        });
      </script>
    </body>
  </html>
  ```

若要查看您的室內地圖，請將其載入網頁瀏覽器。 看起來應該如下圖所示。 如果您按一下 [stairwell] 功能，「層級選擇器」會出現在右上角。

  ![室內地圖影像](media/how-to-use-indoor-module/indoor-map-graphic.png)

## <a name="next-steps"></a>後續步驟

閱讀與「Azure 室內地圖服務」模組相關的 API：

> [!div class="nextstepaction"]
> [繪製套件需求](drawing-requirements.md)

>[!div class="nextstepaction"]
> [室內地圖的建立工具](creator-indoor-maps.md)

深入了解如何將更多資料新增至您的地圖：

> [!div class="nextstepaction"]
> [室內地圖動態樣式](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)