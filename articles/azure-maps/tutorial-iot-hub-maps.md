---
title: 教學課程：執行 IoT 空間分析 | Microsoft Azure 地圖服務
description: 如何整合 IoT 中樞與 Microsoft Azure 地圖服務 API 的教學課程
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: cdbc972d230988420a066c4b927388b885f99a17
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896740"
---
# <a name="tutorial-implement-iot-spatial-analytics-by-using-azure-maps"></a>教學課程：使用 Azure 地圖服務執行 IoT 空間分析

IoT 案例通常會捕捉及追蹤在空間和時間中發生的相關事件。 範例包括車隊管理、資產追蹤、行動和智慧城市應用程式。 本教學課程會引導您了解使用 Azure 地圖服務 API 追蹤二手車租賃動向的解決方案。

在本教學課程中，您將：

> [!div class="checklist"]
> * 建立 Azure 儲存體帳戶記錄車輛以追蹤資料。
> * 使用 Data Upload API 在 Azure 地圖服務資料服務中上傳地理柵欄。
> * 在 Azure IoT 中樞中建立中樞，然後註冊裝置。
> * 在 Azure Functions 中建立函式，並根據 Azure 地圖服務空間分析來實作商務邏輯。
> * 透過 Azure 事件方格從 Azure 函式訂閱 IoT 裝置遙測事件。
> * 使用 IoT 中樞訊息路由來篩選遙測事件。

## <a name="prerequisites"></a>必要條件

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. [建立 Azure 地圖服務帳戶](quick-demo-map-app.md#create-an-azure-maps-account)。

3. [取得主要訂用帳戶金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)，也稱為主要金鑰或訂用帳戶金鑰。 如需詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](how-to-manage-authentication.md)。

4. [建立資源群組](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)。 在本教學課程中，我們會將資源群組命名為 ContosoRental，但您可以選擇任何您喜歡的名稱。

5. 下載 [rentalCarSimulation C# 專案](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation)。

本教學課程使用 [Postman](https://www.postman.com/) 應用程式，但您可以選擇不同的 API 開發環境。

## <a name="use-case-rental-car-tracking"></a>使用案例：出租車追蹤

假設汽車出租公司想要記錄其車輛的位置資訊、行駛距離和發動狀態。 若車輛離開正確的授權地理區域時，公司也會希望儲存此資訊。

出租車輛配備了 IoT 裝置，會定期將遙測資料傳送至 IoT 中樞。 這些遙測資料包括目前的位置，且會指出車輛的引擎是否運轉中。 裝置位置結構描述會符合[地理空間資料的 IoT 隨插即用結構描述](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md)。 出租車輛的裝置遙測結構描述顯示為如下的 JSON 程式碼：

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": {
            "location": {
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            }
        }
    }
}
```

在本教學課程中，您只會追蹤一台車輛。 設定 Azure 服務之後，您必須下載 [rentalCarSimulation C# 專案](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation)，才能執行車輛模擬器。 下列步驟摘要說明從事件到函式執行的整個程序：

1. 車輛內裝置會將遙測資料傳送至 IoT 中樞。

2. 如果車輛引擎狀態為發動中，中樞會將遙測資料發佈到事件方格。

3. 由於 Azure 函式的事件訂用帳戶屬於裝置遙測事件，因此會觸發 Azure 函式。

4. 函式會記錄車輛裝置位置座標、事件時間和裝置識別碼。 然後會使用 [Spatial Geofence Get API](/rest/api/maps/spatial/getgeofence) 來判斷車輛是否已行駛到地理柵欄之外。 如果已超出地理柵欄界限，函式會將從事件接收的位置資料儲存到 Blob 容器中。 函式也會查詢[反向位址搜尋](/rest/api/maps/search/getsearchaddressreverse)，將座標位置轉譯為街道位址，並連同其他裝置位置資料加以儲存。

下圖顯示系統的高階概觀。

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="系統概觀的圖表。":::

下圖以藍色標示的是地理柵欄區域。 出租車輛的路線會以綠色線表示。

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="系統概觀的圖表。":::

## <a name="create-an-azure-storage-account"></a>建立 Azure 儲存體帳戶

為了儲存車輛違規追蹤資料，我們將在您的資源群組中建立一個[一般用途 v2 儲存體帳戶](../storage/common/storage-account-overview.md#general-purpose-v2-accounts)。 如果您尚未建立資源群組，請依照[建立資源群組](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)中的指示進行。 在本教學課程中，您會將資源群組命名為 ContosoRental。

若要建立儲存體帳戶，請依照[建立儲存體帳戶](../storage/common/storage-account-create.md?tabs=azure-portal)中的指示進行。 在本教學課程中，將儲存體帳戶命名為 contosorentalstorage，但是一般而言您可以隨意使用任何您喜歡的名稱。

成功建立儲存體帳戶之後，您必須建立容器來儲存記錄資料。

1. 移至您新建立的儲存體帳戶。 在 [基本資訊] 區段中，選取 [容器] 連結。

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="系統概觀的圖表。":::

2. 選取左上角的 [容器]。 面板就會顯示在瀏覽器右側。 將您的容器命名為 contoso-rental-logs 然後選取 [建立]。

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="系統概觀的圖表。":::

3. 在您的儲存體帳戶中移至 [存取金鑰] 窗格，並複製 **儲存體帳戶名稱** 和 [key1] 區段中的 **金鑰** 值。 您在「建立 Azure 函式並新增事件方格訂用帳戶」區段中會需要這兩個值。

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="系統概觀的圖表。":::

## <a name="upload-a-geofence"></a>上傳地理柵欄

接下來，使用 [Postman 應用程式](https://www.getpostman.com)，[將地理柵欄上傳](./geofence-geojson.md)至 Azure 地圖服務。 地理柵欄會定義出租車輛的授權地理區域。 您會在 Azure 函式中使用地理柵欄，以判斷車輛是否已行駛到地理柵欄區域外。

請遵循下列步驟，使用 Azure 地圖服務 Data Upload API 來上傳地理柵欄： 

1. 開啟 Postman 應用程式，然後選取 [新增]。 在 [新建] 視窗中，選取 [集合]。 為集合命名，然後選取 [建立]。

2. 若要建立要求，請再次選取 [新增]。 在 [建立新項目] 視窗中，選取 [要求]，然後輸入要求的要求名稱。 選取您在先前的步驟中建立的集合，然後選取 [儲存]。

3. 在產生器索引標籤中選取 **POST** HTTP 方法，然後輸入下列 URL，以將地理柵欄資料上傳至 Data Upload API。 請務必使用您的主要訂用帳戶金鑰取代 `{subscription-key}`。

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    在 URL 路徑中，`dataFormat` 參數的 `geojson` 值代表上傳資料的格式。

4. 針對輸入格式選取 [主體]  >  [原始]，然後從下拉式清單中選擇 [JSON]。 [開啟 JSON 資料檔案](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)，並將 JSON 複製到主體區段。 選取 [傳送]。

5. 選取 [傳送]，然後等候系統處理要求。 在要求完成後，移至回應的 [標頭] 索引標籤。 複製 [位置] 索引鍵的值，也就是 `status URL`。

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. 若要檢查 API 呼叫的狀態，請在 `status URL` 建立 **GET** HTTP 要求。 您必須將主要訂用帳戶金鑰附加至 URL，以進行驗證。 **GET** 要求應會類似於下列 URL：

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}

7. When the **GET** HTTP request completes successfully, it returns a `resourceLocation`. The `resourceLocation` contains the unique `udid` for the uploaded content. Copy this `udid` for later use in this tutorial.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

IoT 中樞可保障 IoT 應用程式和其所管理裝置之間雙向通訊的安全性及可靠性。 針對本教學課程，您想要從車輛內裝置取得資訊，以判斷出租車輛的位置。 在本節中，您會在 ContosoRental 資源群組中建立 IoT 中樞。 此中樞將負責發佈我們的裝置遙測事件。

> [!NOTE]
> 在事件方格上發佈裝置遙測事件的功能目前處於公開預覽狀態。 除了下列區域以外，所有區域皆可使用此功能：美國東部、美國西部、西歐、Azure Government、Azure China 21Vianet 和 Azure 德國。

若要在 ContosoRental 資源群組中建立 IoT 中樞，請依照[建立 IoT 中樞](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub)中的步驟進行。

## <a name="register-a-device-in-your-iot-hub"></a>在 IoT 中樞註冊您的裝置

請先在 IoT 中樞身分識別登錄中註冊裝置，否則無法連線到 IoT 中樞。 您會在這裡建立名稱為 InVehicleDevice 的單一裝置。 若要在 IoT 中樞內建立和註冊裝置，請依照[在 IoT 中樞中註冊新裝置](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#register-a-new-device-in-the-iot-hub)中的步驟進行。 請務必複製您裝置的主要連接字串。 稍後您將會用到此資訊。

## <a name="create-a-function-and-add-an-event-grid-subscription"></a>建立函式並新增事件方格訂用帳戶

Azure Functions 是無伺服器計算服務，可讓您執行一小段程式碼 (稱為「函式」)，而無須明確佈建或管理計算基礎結構。 若要深入了解，請參閱 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview)。

函式會由特定事件觸發。 您會在這裡建立由事件方格觸發程序觸發的函式。 建立 IoT 中樞裝置遙測事件的事件訂用帳戶，以建立觸發程序和功能之間的關聯性。 發生裝置遙測事件時，系統會將您的函式呼叫為端點，此端點會接收先前在 IoT 中樞中所註冊裝置的相關資料。

這裡是[您的函式中會包含的 C# 指令碼程式碼](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx)。

現在，請設定您的 Azure 函式。

1. 在 Azure 入口網站儀表板中，選取 [建立資源]。 在 [搜尋] 文字方塊中輸入 **函式應用程式** 。 選取 [函數應用程式]  >  [建立]。

1. 在 [函式應用程式建立]  頁面上，為您的函式應用程式命名。 在 [資源群組] 下的下拉式清單中選取 ContosoRental。 選取 [.NET Core] 作為 **執行階段堆疊** 。 選取頁面底部的 [下一步：裝載 >]。

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="系統概觀的圖表。":::

1. 在 **儲存體帳戶** 中，選取您在 [建立 Azure 儲存體帳戶](#create-an-azure-storage-account)中建立的儲存體帳戶。 選取 [檢閱 + 建立]。

1. 檢閱函數應用程式詳細資料，然後選取 [建立]。

1. 建立應用程式後，您要為其新增函式。 返回函數應用程式。 選取 [函式] 窗格。 從頁面頂端選取 [+ 新增]。 函式範本面板隨即顯示。 在面板中向下捲動，然後選取 [Azure 事件方格觸發程序]。

     >[!IMPORTANT]
    > **Azure 事件中樞觸發程序** 和 **Azure 事件方格觸發程序** 範本的名稱十分類似。 請確定您選取了 **Azure 事件方格觸發程序** 範本。

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="系統概觀的圖表。":::

1. 為函式命名。 在本教學課程中，您會使用名稱 GetGeoFunction，但是一般而言您可以使用任何您喜歡的名稱。 選取 [建立函式]。

1. 在左側功能表中，選取 [程式碼 + 測試] 窗格。 複製 [C# 指令碼](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) 並貼入程式碼視窗。

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="系統概觀的圖表。":::

1. 在 C# 程式碼中，取代下列參數：
    * 以您的 Azure 地圖服務帳戶主要訂用帳戶金鑰取代 **SUBSCRIPTION_KEY** 。
    * 以您在 [上傳地理柵欄](#upload-a-geofence)中上傳的地理柵欄 `udid` 取代 **UDID** 。
    * 指令碼中的 `CreateBlobAsync` 函式會在資料儲存體帳戶中為每個事件建立一個 Blob。 將 **ACCESS_KEY** 、 **ACCOUNT_NAME** 和 **STORAGE_CONTAINER_NAME** 取代為您儲存體帳戶的存取金鑰、帳戶名稱和資料儲存體容器。 當您在[建立 Azure 儲存體帳戶](#create-an-azure-storage-account)中建立儲存體帳戶時，會產生這些值 。

1. 在左側功能表中，選取 [整合] 窗格。 選取圖表中的 [事件方格觸發程序]。 輸入觸發程序的名稱 eventGridEvent，然後選取 [建立事件方格訂用帳戶]。

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="系統概觀的圖表。":::

1. 填妥訂用帳戶詳細資料。 為事件訂用帳戶命名。 針對「事件結構描述」，選取 [事件方格結構描述]。 針對 **主題類型** ，請選取 [Azure IoT 中樞帳戶]。 針對 **資源群組** ，選取您一開始為此教學課程建立的資源群組。 針對 **資源** ，請選取您在「建立 Azure IoT 中樞」中所建立的 IoT 中樞。 針對 **事件種類篩選** ，選取 [裝置遙測]。

   選擇這些選項後，您會看到 **主題類型** 自動變更為「IoT 中樞」。 針對 **系統主題名稱** ，您可以使用與資源相同的名稱。 最後，在 [端點詳細資料] 區段中選取 [選取端點]。 接受所有設定，然後選取 [確認選取]。

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="系統概觀的圖表。":::

1. 檢閱您的設定。 請確定端點指定您在本節開頭所建立的函式。 選取 [建立]。

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="系統概觀的圖表。":::

1. 現在您已經回到 **編輯觸發程序** 面板。 選取 [儲存]。

## <a name="filter-events-by-using-iot-hub-message-routing"></a>使用 IoT 中樞訊息路由來篩選事件

當您將事件方格訂用帳戶新增至 Azure 函式時，系統會在指定的 IoT 中樞內自動建立訊息路由。 訊息路由可讓您將不同的資料類型路由至不同的端點。 例如，您可以路由裝置遙測訊息、裝置生命週期事件和裝置對應項變更事件。 如需詳細資訊，請參閱[使用 IoT 中樞訊息路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)。

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="系統概觀的圖表。":::

在範例案例中，您只想要在出租車輛移動時收到訊息。 建立路由查詢，篩選在 `Engine` 屬性等於 **「ON」** 的事件。 若要建立路由查詢，請選取 **RouteToEventGrid** 路由，並將 **路由查詢** 取代為 **"Engine='ON'"** 。 然後選取 [儲存]。 IoT 中樞現在只會發佈引擎已開啟的裝置遙測。

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="系統概觀的圖表。":::

>[!TIP]
>有各種方式可查詢 IoT 裝置到雲端的訊息。 若要深入了解訊息路由語法，請參閱 [IoT 中樞訊息路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax)。

## <a name="send-telemetry-data-to-iot-hub"></a>將遙測資料傳送至 IoT 中樞

當 Azure 函式執行後，您即可將遙測資料傳送至 IoT 中樞，這會將其路由至事件方格。 使用 C# 應用程式為出租車輛的載具內裝置模擬位置資料。 若要執行此應用程式，您的開發電腦上必須要有 .NET Core SDK 2.1.0 或更新版本。 請依照下列步驟，將模擬的遙測資料傳送至 IoT 中樞：

1. 如果您尚未這麼做，請下載 [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C# 專案。

2. 在您選擇的文字編輯器中開啟 `simulatedCar.cs` 檔案，並將 `connectionString` 的值取代為您在註冊裝置時所儲存的值。 儲存檔案的變更。

3. 確定您已在電腦上安裝 .NET Core。 在您的本機終端機視窗中，移至 C# 專案的根目錄，並執行下列命令以安裝模擬裝置應用程式所需的套件：

    ```cmd/sh
    dotnet restore
    ```

4. 在相同的終端機中執行下列命令，以建置並執行出租車輛的模擬應用程式：

    ```cmd/sh
    dotnet run
    ```


  您的本機終端機應會顯示如下。

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="系統概觀的圖表。":::

如果您現在開啟 Blob 儲存體容器，應該可以看到四個位置 Blob，分別顯示車輛已超出地理柵欄範圍。

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="系統概觀的圖表。":::

下圖顯示位於地理柵欄之外的四部車輛位置點。 每個位置都是以固定時間間隔記錄。

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="系統概觀的圖表。":::

## <a name="explore-azure-maps-and-iot"></a>探索 Azure 地圖服務和 IoT

若要探索本教學課程中使用的 Azure 地圖服務 API，請參閱：

* [取得位址反轉搜尋](/rest/api/maps/search/getsearchaddressreverse)
* [取得地理柵欄](/rest/api/maps/spatial/getgeofence)

如需 Azure 地圖服務 REST API 的完整清單，請參閱：

* [Azure 地圖服務 REST API](/rest/api/maps/spatial/getgeofence)

* [IoT 隨插即用](../iot-pnp/index.yml)

若要取得 Azure 認證的 IoT 裝置清單，請造訪：

* [Azure 認證裝置](https://catalog.azureiotsolutions.com/)

## <a name="next-steps"></a>下一步

若要深入了解如何將裝置傳送至雲端遙測或執行反向作業，請參閱：


> [!div class="nextstepaction"]
> [從裝置傳送遙測資料](../iot-hub/quickstart-send-telemetry-dotnet.md)