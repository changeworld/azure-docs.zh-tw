---
title: 教學課程：執行 IoT 空間分析 | Microsoft Azure 地圖服務
description: 整合 IoT 中樞與 Microsoft Azure 地圖服務 API。
author: philmea
ms.author: philmea
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: cf1d732391f86bec9c0ec2de1e6bace2e808bb19
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2020
ms.locfileid: "85318921"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>教學課程：使用 Azure 地圖服務執行 IoT 空間分析

IoT 案例通常會捕捉及追蹤在空間和時間中發生的相關事件。 範例案例包括車隊管理、資產追蹤、行動和智慧城市應用程式。 本教學課程會引導您完成使用 Azure 地圖服務 API 的解決方案模式。 IoT 中樞會使用事件方格所提供的事件訂用帳戶模型來擷取相關事件。

在本教學課程中，您將：

> [!div class="checklist"]
> * 建立 IoT 中樞。
> * 使用 Data Upload API 在 Azure 地圖服務的資料服務中上傳地理柵欄區域。
> * 在 Azure Functions 中建立函式，並根據 Azure 地圖服務空間分析來實作商務邏輯。
> * 透過事件方格從 Azure 函式訂閱 IoT 裝置遙測事件。
> * 使用 IoT 中樞訊息路由來篩選遙測事件。
> * 建立用來儲存相關事件資料的儲存體帳戶。
> * 模擬車載 IoT 裝置。
    

## <a name="use-case"></a>使用案例

此解決方案將示範下列案例：某家汽車出租公司計劃要監視和記錄其出租車輛的事件。 車輛出租公司通常會將車輛出租到特定的地理區域。 他們必須在出租時追蹤汽車的行蹤。 汽車離開所選地理區域的實例，必須加以記錄。 記錄資料可確保原則、費用和其他商務層面會受到適當的處理。

在我們的使用案例中，出租車輛配備了 IoT 裝置，會定期將遙測資料傳送至 Azure IoT 中樞。 這些遙測資料包括目前的位置，且會指出車輛的引擎是否運轉中。 裝置位置結構描述會符合[地理空間資料的 IoT 隨插即用結構描述](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md)。 出租車輛的裝置遙測結構描述顯示如下：

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

我們將車載裝置遙測來達成目標。 我們想要執行地理柵欄規則。 而且，我們想要在收到表示車輛已移動的事件時做出回應。 為此，我們將透過事件方格從 IoT 中樞訂閱裝置遙測事件。 

有數種方式可訂閱事件方格，在本教學課程中，我們將使用 Azure Functions。 Azure Functions 可對事件方格中發佈的事件做出回應。 它也可根據 Azure 地圖服務空間分析來實作汽車出租商務邏輯。 

Azure 函式內的程式碼會檢查車輛是否已離開地理柵欄。 如果車輛離開了地理柵欄，Azure 函式即會收集其他資訊，例如與目前的位置相關聯的位址。 此函式也會實作邏輯，將有意義的事件資料儲存在資料 Blob 儲存體中，以利提供事件狀況的說明。 

事件狀況對汽車出租公司和租車客戶可能很有幫助。 下圖提供系統的高階概觀。

 
  <center>

  ![系統概觀](./media/tutorial-iot-hub-maps/system-diagram.png)
  
  </center>

下圖以藍色標示的是地理柵欄區域。 出租車輛的路線會以綠色線表示。

  ![地理柵欄路線](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Prerequisites 

### <a name="create-a-resource-group"></a>建立資源群組

若要完成本教學課程中的步驟，您必須先在 Azure 入口網站中建立資源群組。 若要建立資源群組，請執行下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 [資源群組]  。
    
   ![資源群組](./media/tutorial-iot-hub-maps/resource-group.png)

3. 在 [資源群組]  底下，選取 [新增]  。
    
   ![新增資源群組](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. 輸入下列屬性值：
    * **訂用帳戶︰** 選取 Azure 訂用帳戶。
    * **資源群組：** 輸入 "ContosoRental" 作為資源群組名稱。
    * **區域：** 選取資源群組的區域。  

    ![資源群組詳細資料](./media/tutorial-iot-hub-maps/resource-details.png)

    選取 [檢閱 + 建立]  ，然後在下一頁選取 [建立]  。

### <a name="create-an-azure-maps-account"></a>建立 Azure 地圖服務帳戶 

若要根據 Azure 地圖服務空間分析來實作商務邏輯，我們必須在已建立的資源群組中建立 Azure 地圖服務帳戶。 依照[建立帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)中的指示，在 S1 定價層中建立 Azure 地圖服務帳戶訂用帳戶。 請依照[取得主要金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的步驟取得帳戶的主要金鑰。 如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](how-to-manage-authentication.md)。



### <a name="create-a-storage-account"></a>建立儲存體帳戶

若要記錄事件資料，我們將會建立一般用途 **v2storage** 提供所有 Azure 儲存體服務的存取權：Blob、檔案、佇列、資料表和磁碟。  我們必須將此儲存體帳戶放在「ContosoRental」資源群組中，以將資料儲存為 blob。 若要建立儲存體帳戶，請依照[建立儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)中的指示操作。 接著，我們必須建立用來儲存 Blob 的容器。 請依照下列步驟執行此作業：

1. 在您的「儲存體帳戶 - Blob、檔案、資料表、佇列」中，瀏覽至 [容器]。

    ![Blob](./media/tutorial-iot-hub-maps/blobs.png)

2. 按一下左上方的 [容器] 按鈕，並將您的容器命名為 "contoso-rental-logs"，然後按一下 [確定]。

    ![blob-container](./media/tutorial-iot-hub-maps/blob-container.png)

3. 在您的儲存體帳戶中瀏覽至 [存取金鑰]  刀鋒視窗，並複製「儲存體帳戶名稱」和「存取金鑰」。 在後續步驟中將會用到這些資料。

    ![access-keys](./media/tutorial-iot-hub-maps/access-keys.png)


現在，我們已有儲存體帳戶和用來記錄事件資料的容器。 接下來我們將建立 IoT 中樞。

### <a name="create-an-iot-hub"></a>建立 IoT 中樞

IoT 中樞是雲端中的受控服務。 IoT 中樞可作為 IoT 應用程式和其所管理裝置之間雙向通訊的中央訊息中樞。 若要將裝置遙測訊息路由至事件方格，請在 "ContosoRental" 資源群組中建立 IoT 中樞。 請設定將根據汽車的引擎狀態來篩選訊息的訊息路由整合。 當車輛移動時，我們也會將裝置遙測訊息傳送至事件方格。

> [!Note] 
> IoT 中樞在事件方格上發佈裝置遙測事件的功能目前處於公開預覽狀態。 公開預覽功能適用於**美國東部、美國西部、西歐、Azure Government、Azure China 21Vianet** 和 **Azure 德國**以外的所有區域。 

依照[建立 IoT 中樞](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub)一節中的步驟，建立 Iot 中樞。


### <a name="register-a-device"></a>註冊裝置 

若要連線至 IoT 中樞，必須註冊裝置。 若要向 IoT 中樞註冊裝置，請遵循下列步驟：

1. 在您的 IoT 中樞按一下 [IoT 裝置] 刀鋒視窗，然後按一下 [新增]。

    ![add-device](./media/tutorial-iot-hub-maps/add-device.png)

2. 在 [建立裝置] 頁面上，為您的 IoT 裝置命名，然後按一下 [儲存]。
    
    ![register-device](./media/tutorial-iot-hub-maps/register-device.png)

3. 儲存裝置的**主要連接字串**，以便在稍後的步驟中使用，而您需要使用此連接字串來變更預留位置。

    ![add-device](./media/tutorial-iot-hub-maps/connection-string.png)

## <a name="upload-geofence"></a>上傳地理柵欄

我們將使用 [Postman 應用程式](https://www.getpostman.com)，透過 Azure 地圖服務 Data Upload API 將[地理柵欄上傳](https://docs.microsoft.com/azure/azure-maps/geofence-geojson)至 Azure 地圖服務。 有車輛超出此地理柵欄的任何事件，都將記錄下來。

開啟 Postman 應用程式，並依照下列步驟使用 Azure 地圖服務的 Data Upload API 上傳地理柵欄。  

1. 在 Postman 應用程式中，按一下 [新增] | [新建]，然後選取 [要求]。 輸入上傳地理柵欄資料的要求名稱，選取集合或資料夾加以儲存，然後按一下 [Save] (儲存)。

    ![使用 Postman 上傳地理柵欄](./media/tutorial-iot-hub-maps/postman-new.png)

2. 選取建立器索引標籤上的 POST HTTP 方法，然後輸入下列 URL 以提出 POST 要求。

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    URL 路徑中，`dataFormat` 參數的 "geojson" 值代表上傳資料的格式。

3. 按一下 [Params]  (參數)，然後輸入下列金鑰/值組用於 POST 要求 URL。 將 subscription-key 值取代為您的 Azure 地圖服務金鑰。
   
    ![金鑰-值參數 Postman](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. 按一下 [本文]  ，然後選取**原始**輸入格式，並從下拉式清單中選擇 [JSON (應用程式/文字)]  作為輸入格式。 在[這裡](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)開啟 JSON 資料檔案，並將 JSON 複製到 [本文] 區段作為要上傳的資料，然後按一下 [傳送]  。
    
    ![發佈資料](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. 檢閱回應標頭。 要求成功時，[位置]  標頭將會包含狀態 URI，用以查看上傳要求目前的狀態。 狀態 URI 將是下列格式。 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. 複製您的狀態 URI，並向其附加 `subscription-key` 參數。 將 Azure 地圖服務帳戶訂用帳戶金鑰的值指派給 `subscription-key` 參數。 狀態 URI 格式應如下所示，且 `{Subscription-key}` 取代為您的訂用帳戶金鑰。

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. 若要取得 `udId`，請在 Postman 應用程式中開啟新的索引標籤，然後在 [建立器] 索引標籤上選取 GET HTTP 方法，並對狀態 URI 提出 GET 要求。 如果您的資料上傳成功，您將會在回應本文中收到 udId。 請複製 udId 以供稍後使用。

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


接著，我們將在 "ContosoRental" 資源群組中建立 Azure 函式，然後在 IoT 中樞設定訊息路由，以篩選裝置遙測訊息。


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>建立 Azure 函式並新增事件方格訂用帳戶

Azure Functions 是無伺服器計算服務，可讓我們依需求執行程式碼，而無須明確佈建或管理計算基礎結構。 若要深入了解 Azure Functions，請參閱 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 文件。 

我們在函式中實作的邏輯，會使用來自車載裝置遙測的位置資料來評估地理柵欄狀態。 如果指定的車輛超出地理柵欄，此函式會透過[取得搜尋位址反轉 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 收集更多資訊，例如位置的地址。 此 API 會將指定的位置座標轉譯為人類了解的街道地址。 

所有相關事件資訊都會儲存在 Blob 存放區中。 下方的步驟 5 會指向實作此類邏輯的可執行程式碼。 請依照下列步驟建立 Azure 函式，以將資料記錄傳送至 Blob 儲存體帳戶中的 Blob 容器，並在其中新增事件方格訂用帳戶。

1. 在 Azure 入口網站儀表板中，選取 [建立資源]。 從可用的資源類型清單中選取 [計算]  ，然後選取 [函式應用程式]  。

    ![create-resource](./media/tutorial-iot-hub-maps/create-resource.png)

2. 在 [函式應用程式建立]  頁面上，為您的函式應用程式命名。 在 [資源群組]  底下選取 [使用現有的]  ，然後從下拉式清單中選取 [ContosoRental]。 選取 [.NET Core] 作為執行時間堆疊。 在 [裝載]  下，針對 [儲存體帳戶]  ，從先前的步驟中選取儲存體帳戶名稱。 在先前的步驟中，我們將儲存體帳戶命名為 **v2storage**。  接著，請選取 [檢閱 + 建立]  。
    
    ![create-app](./media/tutorial-iot-hub-maps/rental-app.png)

2. 檢閱函式應用程式詳細資料，然後選取 [建立]。

3. 應用程式建立後，我們必須為其新增函式。 返回函數應用程式。 按一下 [新增函式]  以新增函式，然後選擇 [入口網站內]  作為開發環境。 然後，選取 [繼續]  。

    ![create-function](./media/tutorial-iot-hub-maps/function.png)

4. 選擇 [更多範本]  ，然後按一下 [完成並檢視範本]  。 

5. 選取具有 **Azure 事件方格觸發程序**的範本。 出現提示時請安裝延伸模組，並為函式命名，然後選取 [建立]  。

    ![function-template](./media/tutorial-iot-hub-maps/eventgrid-funct.png)
    
    **Azure 事件中樞觸發程序** 和 **Azure 事件方格觸發程序** 有類似的圖示。 請確定您選取了 **Azure 事件方格觸發程序**。

6. 將 [C# 程式碼](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx)複製到您的函式中。
 
7. 在 C# 指令碼中，取代下列參數。 按一下 [檔案]  。 請先不要按 [執行] 
    * 將 **SUBSCRIPTION_KEY** 取代為您的 Azure 地圖服務帳戶主要訂用帳戶金鑰。
    * 將 **UDID** 取代為您上傳之地理柵欄的 udId。 
    * 指令碼中的 **CreateBlobAsync** 函式會在資料儲存體帳戶中為每個事件建立一個 Blob。 將 **ACCESS_KEY**、**ACCOUNT_NAME** 和 **STORAGE_CONTAINER_NAME** 取代為您儲存體帳戶的存取金鑰、帳戶名稱和資料儲存體容器。

10. 按一下 [新增事件方格訂用帳戶]  。
    
    ![add-event-grid](./media/tutorial-iot-hub-maps/add-egs.png)

11. 在 [事件訂閱詳細資料]  下方填入訂閱詳細資料，為您的事件訂用帳戶命名。 針對 [事件架構]，選擇 [事件方格架構]。 在 [主題詳細資料]  底下，選取 [Azure IoT 中樞帳戶] 作為 [主題類型]。 選擇您用來建立資源群組的相同訂用帳戶、選取 [ContosoRental] 作為 [資源群組]。 選擇您建立的 IoT 中樞作為 [資源]。 選擇 [裝置遙測]  作為 [事件類型]。 選擇這些選項後，您會看到 [主題類型] 自動變更為 [IoT 中樞]。

    ![event-grid-subscription](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>使用 IoT 中樞訊息路由來篩選事件

將事件方格訂用帳戶新增至 Azure 函式之後，您會在 IoT 中樞的 [訊息路由]  刀鋒視窗中看到事件方格的預設訊息路由。 訊息路由可讓您將不同的資料類型路由至不同的端點。 例如，您可以路由裝置遙測訊息、裝置生命週期事件和裝置對應項變更事件。 若要深入了解 IoT 中樞訊息路由，請參閱[使用 IoT 中樞訊息路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)。

![hub-EG-route](./media/tutorial-iot-hub-maps/hub-route.png)

在我們的範例案例中，我們想要篩選出出租車輛有所移動的所有訊息。 為了將這類裝置遙測事件發佈至事件方格，我們將使用路由查詢來篩選 `Engine` 屬性為 **"ON"** 的事件。 有多種方式可以查詢 IoT 裝置到雲端的訊息，若要深入了解訊息路由語法，請參閱 [IoT 中樞訊息路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax)。 若要建立路由查詢，請按一下 **RouteToEventGrid** 路由，並將**路由查詢**取代為 **"Engine='ON'"** ，然後按一下 [儲存]  。 IoT 中樞現在只會發佈引擎已開啟的裝置遙測。

![hub-EG-filter](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>將遙測資料傳送至 IoT 中樞

當 Azure 函式啟動並執行後，我們即可將遙測資料傳送至 IoT 中樞，這會將其路由至事件方格。 我們將使用 C# 應用程式為出租車輛的載具內裝置模擬位置資料。 若要執行此應用程式，您的開發機器上必須要有 .NET Core SDK 2.1.0 或更高版本。 請依照下列步驟，將模擬的遙測資料傳送至 IoT 中樞。

1. 下載 [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C# 專案。 

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

  ![終端機輸出](./media/tutorial-iot-hub-maps/terminal.png)

如果您現在開啟 Blob 儲存體容器，您應該可以看到四個 Blob，分別顯示車輛已超出地理柵欄範圍的位置。

![輸入 Blob](./media/tutorial-iot-hub-maps/blob.png)

下列地圖顯示車輛已超出地理柵欄範圍的四個點，以固定時間間隔記錄。

![違規地圖](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>後續步驟

若要探索本教學課程中使用的 Azure 地圖服務 API，請參閱：

* [取得搜尋位址反轉](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [取得地理柵欄](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

如需 Azure 地圖服務 REST API 的完整清單，請參閱：

* [Azure 地圖服務 REST API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

深入了解 IoT 隨插即用，請參閱：

* [IoT 隨插即用](https://docs.microsoft.com/azure/iot-pnp)

若要取得 Azure 認證的 IoT 裝置清單，請造訪：

* [Azure 認證裝置](https://catalog.azureiotsolutions.com/)

若要深入了解如何將裝置傳送至雲端遙測或執行反向作業，請參閱：

* [從裝置傳送遙測資料](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
