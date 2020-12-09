---
title: 搭配裝置模擬使用 Protocol Buffers - Azure |Microsoft Docs
description: 在本操作指南中，您會了解如何使用 Protocol Buffers 將裝置模擬解決方案加速器傳送的遙測資料序列化。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc, amqp, devx-track-csharp
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 77a795b8f704084b612a2dcbd364b8d5be0a3333
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852144"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>使用 Protocol Buffers 將遙測資料序列化

Protocol Buffers (Protobuf) 是結構化資料的二進位序列化格式。 Protobuf 的設計強調簡潔和效能，目標是要比 XML 更小、更快。

裝置模擬支援 Protocol Buffers 語言的 **proto3** 版本。

因為 Protobuf 需要編譯的程式碼，才能將資料序列化，所以您必須建置自訂的裝置模擬版本。

本操作指南中的步驟說明如何：

1. 準備開發環境
1. 指定在裝置型號中使用 Protobuf 格式
1. 定義 Protobuf 格式
1. 產生 Protobuf 類別
1. 本機測試

## <a name="prerequisites"></a>必要條件

若要遵循本操作指南中的步驟，您需要：

* Visual Studio Code。 您可以[下載適用於 Mac、Linux 和 Windows 的 Visual Studio Code](https://code.visualstudio.com/download)。
* .NET Core。 您可以下載[適用於 Mac、Linux 和 Windows 的 .NET Code](https://www.microsoft.com/net/download)。
* Postman。 您可以下載[適用於 Mac、Windows 或 Linux 的 Postman](https://www.getpostman.com/apps)。
* [部署至 Azure 訂用帳戶的 IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。 您需要 IoT 中樞的連接字串才能完成本指南中的步驟。 您可以從 Azure 入口網站取得連接字串。
* [部署到 Azure 訂用帳戶的 Cosmos DB 資料庫](../cosmos-db/create-sql-api-dotnet.md#create-account)，其必須使用 SQL API，並已針對[強式一致性](../cosmos-db/how-to-manage-database-account.md)進行設定。 您需要 Cosmos DB 資料庫的連接字串才能完成本指南中的步驟。 您可以從 Azure 入口網站取得連接字串。
* [部署到 Azure 訂用帳戶的 Azure 儲存體帳戶](../storage/common/storage-account-create.md)。 您需要儲存體帳戶的連接字串才能完成本指南中的步驟。 您可以從 Azure 入口網站取得連接字串。

## <a name="prepare-your-development-environment"></a>準備您的開發環境

請完成下列工作以準備開發環境：

* 下載裝置模擬微服務的來源。
* 下載儲存體配接器微服務的來源。
* 本機執行儲存體配接器微服務。

本文中的指示假設您使用的是 Windows。 如果您使用其他作業系統，則可能需要調整某些檔案路徑和命令以符合環境。

### <a name="download-the-microservices"></a>下載微服務

從 GitHub 將[遠端監視微服務](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip)下載並解壓縮到本機電腦上的適當位置。 此存放庫包含您進行此操作指南所需的儲存體配接器微服務。

從 GitHub 將[裝置模擬微服務](https://github.com/Azure/device-simulation-dotnet/archive/master.zip)下載並解壓縮到本機電腦上的適當位置。

### <a name="run-the-storage-adapter-microservice"></a>執行儲存體配接器微服務

在 Visual Studio Code 中，開啟 **remote-monitoring-services-dotnet-master\storage-adapter** 資料夾。 按一下任何 [還原] 按鈕，以修正無法解決的相依性。

開啟檔案的 **vscode/launch.js** ，並將您的 Cosmos DB 連接字串指派給 **\_ >storageadapter \_ DOCUMENTDB \_ CONNSTRING** 環境變數的電腦。

> [!NOTE]
> 當您在本機電腦上執行微服務時，仍需要 Azure 中的 Cosmos DB 執行個體才能正常運作。

若要在本機執行儲存體配接器微服務，請按一下 [偵錯] \> [開始偵錯]。

Visual Studio Code 中的 [終端機] 視窗會顯示執行中微服務的輸出，包括 Web 服務健康情況檢查的 URL：<http://127.0.0.1:9022/v1/status>。 當您瀏覽至此位址時，狀態應該是「正確: 運作良好」。

完成下列步驟後，請讓儲存體配接器微服務繼續在 Visual Studio Code 的這個執行個體中執行。

## <a name="define-your-device-model"></a>定義裝置型號

在 Visual Studio Code 的新執行個體中，開啟您從 GitHub 下載的 **device-simulation-dotnet-master** 資料夾。 按一下任何 [還原] 按鈕，以修正任何無法解決的相依性。

在此操作指南中，您可以為資產追蹤器建立新的裝置型號：

1. 在 **Services\data\devicemodels** 資料夾中，建立稱為 **assettracker 01.json** 的新裝置型號檔案。

1. 定義裝置型號 **assettracker 01.json** 檔案中的裝置功能。 Protobuf 裝置型號的遙測區段必須：

   * 包含您為裝置所產生 Protobuf 類別的名稱。 下一節會示範如何產生此類別。
   * 指定 Protobuf 作為訊息格式。

     ```json
     {
     "SchemaVersion": "1.0.0",
     "Id": "assettracker-01",
     "Version": "0.0.1",
     "Name": "Asset Tracker",
     "Description": "An asset tracker with location, temperature, and humidity",
     "Protocol": "AMQP",
     "Simulation": {
       "InitialState": {
         "online": true,
         "latitude": 47.445301,
         "longitude": -122.296307,
         "temperature": 38.0,
         "humidity": 62.0
       },
       "Interval": "00:01:00",
       "Scripts": [
         {
           "Type": "javascript",
           "Path": "assettracker-01-state.js"
         }
       ]
     },
     "Properties": {
       "Type": "AssetTracker",
       "Location": "Field",
       "Latitude": 47.445301,
       "Longitude": -122.296307
     },
     "Telemetry": [
       {
         "Interval": "00:00:10",
         "MessageTemplate": "{\"latitude\":${latitude},\"longitude\":${longitude},\"temperature\":${temperature},\"humidity\":${humidity}}",
         "MessageSchema": {
           "Name": "assettracker-sensors;v1",
           "ClassName": "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf.AssetTracker",
           "Format": "Protobuf",
           "Fields": {
             "latitude": "double",
             "longitude": "double",
             "temperature": "double",
             "humidity": "double"
           }
         }
       }
     ]
     }
     ```

### <a name="create-device-behaviors-script"></a>建立裝置行為指令碼

撰寫行為指令碼，定義裝置的行為方式。 如需詳細資訊，請參閱[建立進階模擬裝置](iot-accelerators-device-simulation-advanced-device.md)。

## <a name="define-your-protobuf-format"></a>定義 Protobuf 格式

當您有裝置型號，並決定訊息格式後，就可以建立 **proto** 檔案。 在 **proto** 檔案中，您要新增：

* `csharp_namespace`，其要與裝置型號中的 **ClassName** 屬性相符。
* 每個資料結構要序列化的訊息。
* 訊息中每個欄位的名稱與類型。

1. 在 **Services\Models\Protobuf\proto** 資料夾中，建立稱為 **assettracker.proto** 的新檔案。

1. 在 **proto** 檔案中，定義語法、命名空間以及訊息結構描述，如下所示：

    ```proto
    syntax = "proto3";

    option csharp_namespace = "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf";

    message AssetTracker {
      double latitude=1;
      double longitude=2;
      double temperature=5;
      double humidity=6;
    }
    ```

每個元素上的 `=1`、`=2` 標記會指定欄位在二進位編碼中使用的唯一標記。 數字 1 到 15 會比編碼更高的數字少一個位元組。

## <a name="generate-the-protobuf-class"></a>產生 Protobuf 類別

當您有了 **proto** 檔案，下一個步驟就是產生讀取和寫入訊息所需的類別。 若要完成此步驟，您需要 **Protoc** Protobuf 編譯器。

1. [從 GitHub 下載 Protobuf 編譯器](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. 執行編譯器，指定來源目錄、目的地目錄，以及 **proto** 檔案的名稱。 例如：

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    此命令會在 **Services\Models\Protobuf** 資料夾中產生 **Assettracker.cs** 檔案。

## <a name="test-protobuf-locally"></a>在本機測試 Protobuf

在本節中，您會測試在先前章節中於本機建立的資產追蹤器裝置。

### <a name="run-the-device-simulation-microservice"></a>執行裝置模擬微服務

開啟 **.vscode/launch.json** 檔案，並指派：

* **PCS\_IOTHUB\_CONNSTRING** 環境變數的 IoT 中樞連接字串。
* **PCS\_AZURE\_STORAGE\_ACCOUNT** 環境變數的儲存體帳戶連接字串。
* **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** 環境變數的 Cosmos DB 連接字串。

開啟 **WebService/Properties/launchSettings.json** 檔案，並指派：

* **PCS\_IOTHUB\_CONNSTRING** 環境變數的 IoT 中樞連接字串。
* **PCS\_AZURE\_STORAGE\_ACCOUNT** 環境變數的儲存體帳戶連接字串。
* **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** 環境變數的 Cosmos DB 連接字串。

開啟 **WebService\appsettings.ini** 檔案並修改設定，如下所示：

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>設定解決方案以包含新的裝置型號檔案

根據預設，您的新裝置模型 JSON 和 JS 檔案不會複製到建立的方案中。 您需要明確地包含在內。

針對您要包含的每個檔案在 **services\services.csproj** 檔案中新增一個項目。 例如：

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

若要在本機執行微服務，請按一下 [偵錯] \> [開始偵錯]。

Visual Studio Code 中的 [終端機] 視窗會顯示執行中微服務的輸出。

在完成後續步驟時，請讓裝置模擬微服務繼續在 Visual Studio Code 的這個執行個體中執行。

### <a name="set-up-a-monitor-for-device-events"></a>設定裝置事件監視器

在本節中，您會使用 Azure CLI 來設定事件監視器，以檢視從連線至 IoT 中樞的裝置所傳來的遙測資料。

下列指令碼假設 IoT 中樞的名稱是 **device-simulation-test**。

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

在測試模擬裝置時，請讓事件監視器繼續執行。

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>使用資產追蹤器裝置類型來建立模擬

在本節中，您會使用 Postman 工具來要求裝置模擬微服務，以使用資產追蹤器裝置類型來執行模擬。 Postman 這個工具可讓您傳送 REST 要求給 Web 服務。

若要設定 Postman：

1. 在本機電腦上開啟 Postman。

1. 按一下 [檔案] \> [匯入]。 然後按一下 [選擇檔案]。

1. 選取 [Azure IoT Device Simulation solution accelerator.postman\_collection] 和 [Azure IoT Device Simulation solution accelerator.postman\_environment]，然後按一下 [開啟]。

1. 將 **Azure IoT Device Simulation solution accelerator** 展開以檢視您可以傳送的要求。

1. 按一下 [無環境]，然後選取 [Azure IoT Device Simulation solution accelerator]。

現在，您已在 Postman 工作區中載入集合和環境，以供用來與裝置模擬微服務互動。

若要設定和執行模擬：

1. 在 Postman 集合中，選取 [建立資產追蹤器模擬]，然後按一下 [傳送]。 此要求會針對模擬的資產追蹤器裝置類型建立四個執行個體。

1. Azure CLI 視窗中的事件監視器輸出會顯示模擬裝置的遙測資料。

若要停止模擬，請在 Postman 中選取 [停止模擬] 要求，然後按一下 [傳送]。

### <a name="clean-up-resources"></a>清除資源

您可以停止兩個在其 Visual Studio Code 執行個體中本機執行的微服務 ([偵錯] \> [停止偵錯])。

如果您不再需要 IoT 中樞和 Cosmos DB 執行個體，請從 Azure 訂用帳戶中加以刪除，以避免產生任何不必要的費用。

## <a name="iot-hub-support"></a>IoT 中樞支援

許多 IoT 中樞功能原本就不支援 Protobuf 或其他二進位格式。 例如，IoT 中樞無法處理訊息裝載，因此您無法根據訊息內容來路由傳送。 不過，您可以根據訊息標頭來路由傳送。

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何自訂裝置模擬以使用 Protobuf 來傳送遙測，下一步是造訪 GitHub 存放庫，以深入瞭解 [裝置模擬](https://github.com/Azure/device-simulation-dotnet)。
