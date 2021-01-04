---
title: 將遙測傳送至 Azure IoT 中樞快速入門 (C#) | Microsoft Docs
description: 本快速入門中，您會執行兩個範例 C# 應用程式，以將模擬的遙測資料傳送至 IoT 中樞以及從 IoT 中樞讀取遙測資料，以便在雲端中處理。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 06/01/2020
ms.openlocfilehash: e4c87d8579b06cdfb37c1635a25db5ce67aa3545
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094789"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-service-application-net"></a>快速入門：將遙測從裝置傳送至 IoT 中樞，並使用服務應用程式讀取遙測 (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT 中樞是一項 Azure 服務，可讓您從 IoT 裝置將大量的遙測擷取到雲端進行儲存或處理。 在此快速入門中，您透過 IoT 中樞，將遙測從模擬裝置應用程式傳送到服務應用程式以進行處理。

本快速入門會使用兩個預先撰寫的 C# 應用程式，一個用來傳送遙測，而另一個用來從中樞讀取遙測。 在執行下列兩個應用程式之前，您需要建立一個 IoT 中樞，並向中樞註冊一個裝置。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

* 您在此快速入門中執行的兩個範例應用程式是使用 C# 所撰寫的。 您的開發機器上必須有 .NET Core SDK 3.1 或以上版本。

    您可以從 [.NET](https://www.microsoft.com/net/download/all) 下載適用於多種平台的 .NET Core SDK。

    您可以使用下列命令，以確認開發電腦上目前的 C# 版本：

    ```cmd/sh
    dotnet --version
    ```

    > [!NOTE]
    > 建議使用 .NET Core SDK 3.1 或以上版本來編譯在本快速入門中用來讀取遙測的事件中樞服務程式碼。


* 從 [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) 下載 Azure IoT C# 範例，然後將 ZIP 封存檔解壓縮。

* 請確定您的防火牆已開啟連接埠 8883。 本快速入門中的裝置範例會使用 MQTT 通訊協定，其會透過連接埠 8883 進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>註冊裝置

裝置必須向的 IoT 中樞註冊，才能進行連線。 在本快速入門中，您會使用 Azure Cloud Shell 來註冊模擬的裝置。

1. 在 Azure Cloud Shell 中執行下列命令，以建立裝置身分識別。

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

   **MyDotnetDevice**：這是您要註冊之裝置的名稱。 建議您使用 **MyDotnetDevice**，如下所示。 如果您為裝置選擇不同的名稱，則也必須在本文中使用該名稱，並先在範例應用程式中更新該裝置名稱，再執行應用程式。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. 在 Azure Cloud Shell 中執行下列命令，以針對您剛註冊的裝置取得「裝置連接字串」  ：

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    記下裝置連接字串，它看起來如下：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    您稍後將會在快速入門中使用此值。

3. 您還需要 IoT 中樞的「事件中樞相容端點」、「事件中樞相容路徑」和「服務主要金鑰」，以便讓服務應用程式連線到 IoT 中樞並擷取訊息。 下列命令會針對您的 IoT 中樞擷取這些值：

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    請記下這三個值，您稍後將會在快速入門中使用。

## <a name="send-simulated-telemetry"></a>傳送模擬的遙測

模擬裝置應用程式會連線到 IoT 中樞上的裝置特定端點，並且傳送模擬的溫度和溼度遙測。

1. 在本機終端機視窗中，瀏覽至範例 C# 專案的根資料夾。 然後瀏覽至 **iot-hub\Quickstarts\SimulatedDevice** 資料夾。

2. 在本機終端機視窗中，執行下列命令以安裝模擬裝置應用程式所需的套件：

    ```cmd/sh
    dotnet restore
    ```

3. 在本機終端機視窗中，使用稍早記下的裝置連接字串執行下列命令，以建置並執行模擬裝置應用程式：

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    下列螢幕擷取畫面顯示模擬裝置應用程式將遙測傳送到 IoT 中樞時的輸出：

    ![執行模擬的裝置](media/quickstart-send-telemetry-dotnet/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>從您的中樞讀取遙測

服務應用程式會連線到 IoT 中樞上的服務端 **事件** 端點。 應用程式會接收模擬裝置所傳送的「裝置到雲端」訊息。 IoT 中樞服務應用程式通常在雲端中執行，以接收和處理「裝置到雲端」訊息。

1. 在另一個終端機視窗中，瀏覽至範例 C# 專案的根資料夾。 然後瀏覽至 **iot-hub\Quickstarts\ReadD2cMessages** 資料夾。

2. 在本機終端機視窗中，執行下列命令以安裝應用程式所需的程式庫：

    ```cmd/sh
    dotnet restore
    ```

3. 在本機終端機視窗中，執行下列命令以查看參數選項。

    ```cmd/sh
    dotnet run
    ```

4. 在本機終端機視窗中，執行下列其中一個命令以建置並執行應用程式：

    ```cmd/sh
    dotnet run -- -c {EventHubConnectionString}
    ```

    或

    ```cmd/sh
    dotnet run -- -e {EventHubCompatibleEndpoint} -n {EventHubName} -s {SharedAccessKey}
    ```

    下列螢幕擷取畫面顯示由模擬裝置傳送遙測至中樞時，服務應用程式接收遙測的輸出：

    ![執行服務應用程式](media/quickstart-send-telemetry-dotnet/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

在此快速入門中，您會設定 IoT 中樞、註冊裝置、使用 C# 應用程式將模擬的遙測傳送到中樞，並使用簡單的服務應用程式從中樞讀取遙測。

若要了解如何從服務應用程式控制模擬裝置，請繼續下一個快速入門。

> [!div class="nextstepaction"]
> [快速入門：控制連線到 IoT 中樞的裝置](quickstart-control-device-dotnet.md)
