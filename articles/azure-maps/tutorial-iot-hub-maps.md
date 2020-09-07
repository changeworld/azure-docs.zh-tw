---
title: 教學課程：透過 Microsoft Azure 地圖服務實作 IoT 空間分析 | Microsoft Azure 地圖服務
description: 整合 IoT 中樞與 Microsoft Azure 地圖服務 API。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4150464b5c59b631afea0c788b1e351dee5185f9
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299351"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>教學課程：使用 Azure 地圖服務執行 IoT 空間分析

IoT 案例通常會捕捉及追蹤在空間和時間中發生的相關事件。 範例案例包括車隊管理、資產追蹤、行動和智慧城市應用程式。 本教學課程會引導您了解使用 Azure 地圖服務 API 追蹤二手車租賃動向的解決方案。

在本教學課程中，您將：

> [!div class="checklist"]
> * 建立 Azure 儲存體帳戶記錄車輛以追蹤資料。
> * 使用 Data Upload API 在 Azure 地圖服務資料服務中上傳地理柵欄。
> * 建立 IoT 中樞並登錄裝置。
> * 在 Azure Functions 中建立函式，並根據 Azure 地圖服務空間分析來實作商務邏輯。
> * 透過事件方格從 Azure 函式訂閱 IoT 裝置遙測事件。
> * 使用 IoT 中樞訊息路由來篩選遙測事件。

## <a name="prerequisites"></a>必要條件

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. [建立 Azure 地圖服務帳戶](quick-demo-map-app.md#create-an-azure-maps-account)。

3. [取得主要訂用帳戶金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)，也稱為主要金鑰或訂用帳戶金鑰。 如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](how-to-manage-authentication.md)。

4. [建立資源群組](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups)。 在本教學課程中，我們會將資源群組命名為 ContosoRental，但您可以選擇任何您喜歡的名稱。

5. 下載 [rentalCarSimulation C# 專案](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation)。

本教學課程使用 [Postman](https://www.postman.com/) 應用程式，但您可以選擇不同的 API 開發環境。

## <a name="use-case-rental-car-tracking"></a>使用案例：出租車追蹤

本教學課程示範下列案例：汽車出租公司想要記錄其車輛的位置資訊、行駛距離和發動狀態。 此外，若車輛離開正確的授權地理區域時，公司會希望儲存此資訊。

在我們的使用案例中，出租車輛配備了 IoT 裝置，會定期將遙測資料傳送至 Azure IoT 中樞。 這些遙測資料包括目前的位置，且會指出車輛的引擎是否運轉中。 裝置位置結構描述會符合[地理空間資料的 IoT 隨插即用結構描述](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md)。 出租車輛的裝置遙測結構描述顯示為如下的 JSON 程式碼：

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

在本教學課程中，我們只會追蹤一台車輛。 設定 Azure 服務之後，您必須下載 [rentalCarSimulation C# 專案](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation)，才能執行車輛模擬器。 下列步驟摘要說明從事件到函式執行的整個程序：

1. 車輛內裝置會將遙測資料傳送至 IoT 中樞。

2. 如果車輛引擎狀態為發動中，IoT 中樞會將遙測資料發佈到事件方格。

3. 由於 Azure 函式的事件訂用帳戶屬於裝置遙測事件，因此會觸發 Azure 函式。

4. 函式會記錄車輛裝置位置座標、事件時間和裝置識別碼。 然後會使用 [Spatial Geofence Get API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) 來判斷車輛是否已行駛到地理柵欄之外。 如果已超出地理柵欄界限，函式會將從事件接收的位置資料儲存到我們的 Blob 容器中。 此外，我們的函式會查詢[反向位址搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)，將座標位置轉譯為街道位址，並連同其他裝置位置資料加以儲存。

下圖提供系統的高階概觀。

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="系統概觀":::

下圖以藍色標示的是地理柵欄區域。 出租車輛的路線會以綠色線表示。

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="地理柵欄路線":::

## <a name="create-an-azure-storage-account"></a>建立 Azure 儲存體帳戶

為了儲存車輛違規追蹤資料，我們將在您的資源群組中建立一個[一般用途 v2 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-account-overview#general-purpose-v2-accounts)。 如果您尚未建立資源群組，請依照[建立資源群組](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups)中的指示進行。 在本教學課程中，我們會將資源群組命名為 ContosoRental。

若要建立儲存體帳戶，請依照[建立儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)中的指示進行。 在本教學課程中，我們會將儲存體帳戶命名為 contosorentalstorage，但您可以隨意使用任何您喜歡的名稱。

成功建立儲存體帳戶之後，我們必須建立容器來儲存記錄資料。

1. 瀏覽到您新建立的儲存體帳戶。 按一下基本資訊區段中的 [容器] 連結。

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Blob 儲存體容器":::

2. 按一下左上角的 [+ 容器] 按鈕。 面板就會顯示在瀏覽器右側。 將您的容器命名 contoso-rental-logs 然後按一下 [建立]。

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="建立 Blob 容器":::

3. 在您的儲存體帳戶中瀏覽至**存取金鑰**刀鋒視窗，並複製**儲存體帳戶名稱**和 **key1** 區段中的**金鑰**值。 我們在[建立 Azure 函式並新增事件方格訂用帳戶](#create-an-azure-function-and-add-an-event-grid-subscription)中會需要這兩個值。

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="複製儲存體帳戶名稱和金鑰":::

## <a name="upload-a-geofence"></a>上傳地理柵欄

我們現在會使用 [Postman 應用程式](https://www.getpostman.com)，[將地理柵欄上傳至](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) Azure 地圖服務服務。 地理柵欄會定義出租車輛的授權地理區域。  我們會在 Azure 函式中使用地理柵欄，以判斷車輛是否已行駛到地理柵欄區域外。

開啟 Postman 應用程式，並依照下列步驟使用 Azure 地圖服務的 Data Upload API 上傳地理柵欄。  

1. 開啟 Postman 應用程式。 在 Postman 應用程式頂端處，選取 [新增]。 在 [新建] 視窗中，選取 [集合]。  為集合命名，然後選取 [建立] 按鈕。

2. 若要建立要求，請再次選取 [新增]。 在 [新建] 視窗中，選取 [要求]。 輸入要求的 [要求名稱]。 選取您在先前的步驟中建立的集合，然後選取 [儲存]。

3. 在產生器索引標籤中選取 **POST** HTTP 方法，然後輸入下列 URL，以將地理柵欄資料上傳至 Data Upload API。 請務必使用您的主要訂用帳戶金鑰取代 `{subscription-key}`。

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    URL 路徑中，`dataFormat` 參數的 "geojson" 值代表上傳資料的格式。

4. 按一下 [主體]然後選取 [原始] 輸入格式，並從下拉式清單中選擇 [JSON] 作為輸入格式。 [在這裡](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)開啟 JSON 資料檔案，並將 JSON 複製到主體區段。 按一下 [ **傳送**]。

5. 按一下藍色的 [傳送] 按鈕，然後等候系統處理要求。 在要求完成後，移至回應的 [標頭] 索引標籤。 複製 [位置] 索引鍵的值，也就是 `status URL`。

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. 若要檢查 API 呼叫的狀態，請在 `status URL` 建立 **GET** HTTP 要求。 您必須將主要訂用帳戶金鑰附加至 URL，以進行驗證。 **GET** 要求應會類似於下列 URL：

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}

7. When the **GET** HTTP request completes successfully, it will return a `resourceLocation`. The `resourceLocation` contains the unique `udid` for the uploaded content. You'll need to copy this `udid` for later use in this tutorial.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-azure-iot-hub"></a>建立 Azure IoT 中樞

Azure IoT 中樞可保障 IoT 應用程式和其所管理裝置之間雙向通訊的安全性及可靠性。  在案例中，我們想要從車輛內裝置取得資訊，以判斷出租車輛的位置。 在本節中，我們會在 ContosoRental 資源群組中建立 IoT 中樞。 IoT 中樞將負責發佈我們的裝置遙測事件。

> [!NOTE]
> IoT 中樞在事件方格上發佈裝置遙測事件的功能目前處於公開預覽狀態。 公開預覽功能適用於**美國東部、美國西部、西歐、Azure Government、Azure China 21Vianet** 和 **Azure 德國**以外的所有區域。

若要在 ContosoRental 資源群組中建立 IoT 中樞，請依照[建立 IoT 中樞](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub)中的步驟進行。

## <a name="register-a-device-in-iot-hub"></a>在 IoT 中樞登錄裝置

請先在 IoT 中樞身分識別登錄中註冊裝置，否則無法連線到 IoT 中樞。 在案例中，我們將建立名稱為 InVehicleDevice 的單一裝置。 若要在 IoT 中樞內建立和註冊裝置，請依照[在 IoT 中樞中註冊新裝置](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#register-a-new-device-in-the-iot-hub)中的步驟進行。 請務必複製您裝置的**主要連接字串**，我們在稍後的步驟中會用到此資訊。

## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>建立 Azure 函式並新增事件方格訂用帳戶

Azure Functions 是無伺服器計算服務，可讓您執行一小段程式碼 (稱為「函式」)，而無須明確佈建或管理計算基礎結構。 若要深入了解 Azure Functions，請參閱 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 文件。

函式會由特定事件「觸發」。 在案例中，我們將建立由事件方格觸發程序觸發的函式。 我們會建立 IoT 中樞裝置遙測事件的事件訂用帳戶，以建立觸發程序和功能之間的關聯性。 發生裝置遙測事件時，系統會將我們的函式呼叫為端點，此端點會接收[先前在 IoT 中樞中註冊之裝置](#register-a-device-in-iot-hub)的相關資料。

您可以在[這裡](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx)看到函式中會包含的 C# 指令碼程式碼。

現在，我們將設定 Azure 函式。

1. 在 Azure 入口網站儀表板中，按一下 [建立資源]。 在 [搜尋] 文字方塊中輸入**函式應用程式**。 按一下 [函式應用程式]。 按一下 [建立]  。

2. 在 [函式應用程式建立]**** 頁面上，為您的函式應用程式命名。 在 [資源群組] 下的下拉式清單中選取 ContosoRental。  選取 [.NET Core] 作為**執行階段堆疊**。 按一下 **下一步裝載 >** ，位於頁面底部。

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="建立函數應用程式":::

3. 在**儲存體帳戶**中，選取您在[建立 Azure 儲存體帳戶](#create-an-azure-storage-account)中建立的儲存體帳戶。 按一下 [檢閱 + 建立]。

4. 檢閱函式應用程式詳細資料，然後按一下 [建立]。

5. 建立應用程式後，我們必須為其新增函式。 返回函數應用程式。 按一下 [函式] 刀鋒視窗。 按一下頁面頂端的 [+新增]。 函式範本面板隨即顯示。 在面板上向下滾動。 按一下 [Azure 事件方格觸發程序]。

     >[!WARNING]
    > **Azure 事件中樞觸發程序**和 **Azure 事件方格觸發程序**範本的名稱十分類似。 請務必選取 **Azure 事件方格觸發程序**範本。

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="建立函式":::

6. 為函式命名。 在本教學課程中，我們將使用名稱 GetGeoFunction，但您可以使用任何您喜歡的名稱。 按一下 [建立函式]。

7. 按一下左側功能表中的 [程式碼 + 測試] 刀鋒視窗。 複製 [C# 指令碼](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) 並貼入程式碼視窗。

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="複製程式碼並貼到函式視窗":::

8. 在 C# 程式碼中，取代下列參數。 按一下 **[儲存]** 。 請先不要按 [測試/執行]
    * 以您的 Azure 地圖服務帳戶主要訂用帳戶金鑰取代 **SUBSCRIPTION_KEY**。
    * 以您在[上傳地理柵欄](#upload-a-geofence)中上傳的地理柵欄 `udid` 取代 **UDID**。
    * 指令碼中的 **CreateBlobAsync** 函式會在資料儲存體帳戶中為每個事件建立一個 Blob。 將 **ACCESS_KEY**、**ACCOUNT_NAME** 和 **STORAGE_CONTAINER_NAME** 取代為您儲存體帳戶的存取金鑰、帳戶名稱和資料儲存體容器。 當您在[建立 Azure 儲存體帳戶](#create-an-azure-storage-account)中建立儲存體帳戶時，會產生這些值 。

9. 按一下左側功能表中的 [整合] 刀鋒視窗。 按一下圖表中的 [事件方格觸發程序]。 輸入觸發程序的名稱，例如 eventCarTelemetry，然後按一下 [建立事件方格訂用帳戶]。

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="新增事件訂閱":::

10. 填妥訂用帳戶詳細資料。 為事件訂用帳戶命名。 針對「事件結構描述」，選取 [事件方格結構描述]。 針對**主題類型**，請選取 [Azure IoT 中樞帳戶]。 針對**資源群組**，選取您一開始為此教學課程建立的資源群組。 針對**資源**，請選取您在 [建立 Azure IoT 中樞](#create-an-azure-iot-hub)中所建立的 IoT 中樞。 針對**事件種類篩選**，選取 [裝置遙測]。 選擇這些選項後，您會看到**主題類型**自動變更為「IoT 中樞」。 針對**系統主題名稱**，您可以使用與資源相同的名稱。  最後，按一下**端點詳細資料**區段中的 [選取端點]。 接受所有設定，然後按一下 [確認選取]。

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="建立事件訂閱":::

11. 檢閱您的設定。 請確定端點指定您在本節開頭所建立的函式。 按一下 [建立]  。

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="建立事件訂用帳戶確認":::

12. 現在您已經回到**編輯觸發程序**面板。 按一下 **[儲存]** 。

## <a name="filter-events-using-iot-hub-message-routing"></a>使用 IoT 中樞訊息路由來篩選事件

當您將事件方格訂用帳戶新增至 Azure 函式時，系統會在指定的 IoT 中樞內自動建立訊息路由。 訊息路由可讓您將不同的資料類型路由至不同的端點。 例如，您可以路由裝置遙測訊息、裝置生命週期事件和裝置對應項變更事件。 若要深入了解 IoT 中樞訊息路由，請參閱[使用 IoT 中樞訊息路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)。

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="IoT 中樞內的訊息路由":::

在範例案例中，我們只想要在出租車輛移動時收到訊息。 我們會建立路由查詢，篩選在 `Engine` 屬性等於 **「ON」** 的事件。 若要建立路由查詢，請按一下 **RouteToEventGrid** 路由，並將**路由查詢**取代為 **"Engine='ON'"**，然後按一下 [儲存]****。 IoT 中樞現在只會發佈引擎已開啟的裝置遙測。

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="篩選路由訊息":::

>[!TIP]
>有多種方式可以查詢 IoT 裝置到雲端的訊息，若要深入了解訊息路由語法，請參閱 [IoT 中樞訊息路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax)。

## <a name="send-telemetry-data-to-iot-hub"></a>將遙測資料傳送至 IoT 中樞

當 Azure 函式啟動並執行後，我們即可將遙測資料傳送至 IoT 中樞，這會將其路由至事件方格。 我們將使用 C# 應用程式為出租車輛的載具內裝置模擬位置資料。 若要執行此應用程式，您的開發機器上必須要有 .NET Core SDK 2.1.0 或更高版本。 請依照下列步驟，將模擬的遙測資料傳送至 IoT 中樞。

1. 如果您尚未這麼做，請下載 [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C# 專案。

2. 在您選擇的文字編輯器中開啟 simulatedCar.cs 檔案，並將 `connectionString` 的值取代為您在註冊裝置時所儲存的值，並將變更儲存至檔案。

3. 確定您已在電腦上安裝 .NET Core。 在您的本機終端機視窗中，瀏覽至 C# 專案的根目錄，並執行下列命令以安裝模擬裝置應用程式所需的套件：

    ```cmd/sh
    dotnet restore
    ```

4. 在相同的終端機中執行下列命令，以建置並執行出租車輛的模擬應用程式：

    ```cmd/sh
    dotnet run
    ```

  您的本機終端機應會顯示如下。

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="終端機輸出":::

如果您現在開啟 Blob 儲存體容器，應該可以看到四個位置 Blob，分別顯示車輛已超出地理柵欄範圍。

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="檢視容器中的 Blob":::

下圖顯示位於地理柵欄之外的四部車輛位置點。 每個位置都是以固定時間間隔記錄。

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="違規地圖":::

## <a name="explore-azure-maps-and-iot"></a>探索 Azure 地圖服務和 IoT

若要探索本教學課程中使用的 Azure 地圖服務 API，請參閱：

* [取得位址反轉搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [取得地理柵欄](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

如需 Azure 地圖服務 REST API 的完整清單，請參閱：

* [Azure 地圖服務 REST API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

深入了解 IoT 隨插即用，請參閱：

* [IoT 隨插即用](https://docs.microsoft.com/azure/iot-pnp)

若要取得 Azure 認證的 IoT 裝置清單，請造訪：

* [Azure 認證裝置](https://catalog.azureiotsolutions.com/)

## <a name="next-steps"></a>後續步驟

若要深入了解如何將裝置傳送至雲端遙測或執行反向作業，請參閱：

> [!div class="nextstepaction"]
> [從裝置傳送遙測資料](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
