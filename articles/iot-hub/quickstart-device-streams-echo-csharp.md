---
title: 透過 Azure IoT 中樞裝置串流與使用 C# 的裝置應用程式進行通訊
description: 在本快速入門中，您會執行兩個範例 C# 應用程式，並透過以 IoT 中樞建立的裝置串流進行通訊。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 64af62cb6c2c56ca8c7e67e2f1467d4a7e8335a0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675529"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>快速入門：透過 IoT 中樞裝置串流與使用 C# 的裝置應用程式進行通訊 (預覽)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT 中樞目前支援裝置串流作為[預覽功能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[IoT 中樞裝置串流](./iot-hub-device-streams-overview.md)可讓服務和裝置應用程式以安全且便於設定防火牆的方式進行通訊。 本快速入門會用到 C# 應用程式，該應用程式會利用裝置串流來回傳送資料 (回應)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>Prerequisites

* 裝置串流的預覽版目前僅支援在下列區域建立的 IoT 中樞：
  * 美國中部
  * 美國中部 EUAP
  * 北歐
  * 東南亞

* 您在此快速入門中執行的兩個範例應用程式是以 C# 撰寫的。 您的開發電腦上需要有 .NET Core SDK 2.1.0 或更新版本。
  * 您可以[從 .NET 下載適用於多種平台的 .NET Core SDK](https://www.microsoft.com/net/download/all) \(英文\)。
  * 使用下列命令，確認開發電腦上目前的 C# 版本：

   ```
   dotnet --version
   ```

* 執行下列命令，將適用於 Azure CLI 的 Azure IoT 擴充功能新增至您的 Cloud Shell 執行個體。 IoT 擴充功能可將 IoT 中樞、IoT Edge 和 IoT 裝置佈建服務的特定命令新增至 Azure CLI。

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* [下載 Azure IoT C# 範例](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)，然後將 ZIP 封存檔解壓縮。 您在裝置端和服務端都會用到它。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>註冊裝置

裝置必須向的 IoT 中樞註冊，才能進行連線。 在這一節中，您會使用 Azure Cloud Shell 來註冊模擬的裝置。

1. 若要建立裝置身分識別，請在 Cloud Shell 中執行下列命令：

   > [!NOTE]
   > * 以您為 IoT 中樞選擇的名稱取代 YourIoTHubName  預留位置。
   > * 如需您所註冊的裝置名稱，建議使用如下所示的 *MyDevice*。 如果您為裝置選擇不同的名稱，請在本文中使用該名稱，並先在應用程式範例中更新該裝置名稱，再執行應用程式。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. 若要針對您剛註冊的裝置取得「裝置連接字串」  ，請在 Azure Cloud Shell 中執行下列命令：

   > [!NOTE]
   > 以您為 IoT 中樞選擇的名稱取代 YourIoTHubName  預留位置。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    請記下所傳回的裝置連接字串，以供稍後在本快速入門中使用。 看起來會像下列範例：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. 您也需要 IoT 中樞的*服務連接字串*，讓服務端應用程式能夠連線到您的 IoT 中樞並建立裝置串流。 下列命令會擷取您 IoT 中樞的這個值：

   > [!NOTE]
   > 以您為 IoT 中樞選擇的名稱取代 YourIoTHubName  預留位置。

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    請記下所傳回的服務連接字串，以供稍後在本快速入門中使用。 看起來會像下列範例：

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>透過裝置串流在裝置與服務之間進行通訊

在本節中，您會執行裝置端應用程式和服務端應用程式，並在兩者之間進行通訊。

### <a name="run-the-service-side-application"></a>執行服務端應用程式

在本機終端視窗中，瀏覽至未解壓縮專案資料夾中的 `iot-hub/Quickstarts/device-streams-echo/service` 目錄。 將下列資訊保存在隨手可及的位置：

| 參數名稱 | 參數值 |
|----------------|-----------------|
| `ServiceConnectionString` | IoT 中樞的服務連接字串。 |
| `MyDevice` | 您先前建立之裝置的識別碼。 |

使用下列命令來編譯和執行程式碼：

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{ServiceConnectionString}" "MyDevice"

# In Windows
dotnet run {ServiceConnectionString} MyDevice
```
應用程式會等待裝置應用程式變成可用。

> [!NOTE]
> 如果裝置端應用程式未及時回應，就會發生逾時。

### <a name="run-the-device-side-application"></a>執行裝置端應用程式

在另一個本機終端視窗中，瀏覽至未解壓縮專案資料夾中的 `iot-hub/Quickstarts/device-streams-echo/device` 目錄。 將下列資訊保存在隨手可及的位置：

| 參數名稱 | 參數值 |
|----------------|-----------------|
| `DeviceConnectionString` | IoT 中樞的裝置連接字串。 |

使用下列命令來編譯和執行程式碼：

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{DeviceConnectionString}"

# In Windows
dotnet run {DeviceConnectionString}
```

在最後一個步驟結束時，服務端應用程式會起始您裝置的資料流。 建立資料流之後，應用程式會透過資料流將字串緩衝區傳送到服務。 在此範例中，服務端應用程式會直接將相同的資料傳回給裝置，以示範兩個應用程式之間成功的雙向通訊。

裝置端上的主控台輸出：

![裝置端上的主控台輸出](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

服務端上的主控台輸出：

![服務端上的主控台輸出](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

透過串流傳送的流量會經由 IoT 中樞輸送，而不是直接傳送。 [裝置串流處理優點](./iot-hub-device-streams-overview.md#benefits)中詳述提供的權益。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您會設定 IoT 中樞、註冊裝置、在裝置和服務端建立 C# 應用程式之間的裝置串流，以及使用串流在應用程式之間來回傳送資料。

若要深入了解裝置串流，請參閱：

> [!div class="nextstepaction"]
> [裝置串流概觀](./iot-hub-device-streams-overview.md)
