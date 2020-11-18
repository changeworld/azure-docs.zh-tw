---
title: 快速入門 - 將 IoT 隨插即用範例 Node.js 裝置程式碼連線至 Azure IoT 中樞 | Microsoft Docs
description: 快速入門 - 使用 Node.js 建置並執行連線至 IoT 中樞的 IoT 隨插即用範例裝置程式碼。 使用 Azure IoT 檔案總管工具，檢視裝置傳送至中樞的資訊。
author: ericmitt
ms.author: ericmitt
ms.date: 07/10/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 24f6cc1e9656957d6894ecb6b7c3e0476b233cf6
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421578"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-to-iot-hub-nodejs"></a>快速入門：將範例 IoT 隨插即用裝置應用程式連線至 IoT 中樞 (Node.js)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

本快速入門說明如何建置範例 IoT 隨插即用裝置應用程式、將其連線至您的 IoT 中樞，並使用 Azure IoT 總管工具檢視其所傳送的遙測資料。 範例應用程式是專為 Node.js 所撰寫的，並且包含在適用於 Node.js 的 Azure IoT 裝置 SDK 中。 解決方案建置人員可以使用 Azure IoT 總管工具來了解 IoT 隨插即用裝置的功能，而不需檢視任何裝置程式碼。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

若要完成本快速入門，您的開發電腦上必須要有 Node.js。 您可以從 [nodejs.org](https://nodejs.org) 下載多個平台的最新建議版本。

您可以使用下列命令，以確認開發電腦上目前的 Node.js 版本：

```cmd/sh
node --version
```

## <a name="download-the-code"></a>下載程式碼

您會在本快速入門中準備開發環境，以用來複製及建置適用於 Node.js 的 Azure IoT 中樞裝置 SDK。

在您選擇的目錄中開啟命令提示字元。 執行下列命令，將[適用於 Node.js 的 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub 存放庫複製到下列位置：

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>安裝必要的程式庫

您可以使用裝置 SDK 建置包含的範例程式碼。 您所建置的應用程式會模擬連線至 IoT 中樞的裝置。 應用程式會傳送遙測資料和屬性，並接收命令。

1. 在本機終端機視窗中，移至複製存放庫的資料夾，並瀏覽至 /azure-iot-sdk-node/device/samples/pnp 資料夾。 然後執行下列命令來安裝必要的程式庫：

    ```cmd/sh
    npm install
    ```

1. 使用先前所記錄的裝置連接字串來設定環境變數：

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>執行範例裝置

此範例會實作一個簡單的 IoT 隨插即用控溫器裝置。 此範例實作的模型不會使用 IoT 隨插即用[元件](concepts-components.md)。 [適用於控溫器裝置的DTDL 模型檔案](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)會定義裝置所實作的遙測、屬性和命令。

開啟 _simple_thermostat.js_ 檔案。 在此檔案中，您可以了解如何：

1. 匯入必要的介面。
1. 撰寫屬性更新處理常式和命令處理常式。
1. 處理所需的屬性修補程式並傳送遙測。
1. 選擇性地使用 Azure 裝置佈建服務 (DPS) 佈建您的裝置。

在 main 函式中，您可以看到其如何彙整在一起：

1. 從您的連接字串建立裝置，或使用 DPS 進行佈建。)
1. 使用 **modelID** 選項來指定 IoT 隨插即用裝置模型。
1. 啟用命令處理常式。
1. 將遙測資料從裝置傳送到您的中樞。
1. 取得裝置對應項，並更新報告的屬性。
1. 啟用所需的屬性更新處理常式。

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

若要深入了解範例設定，請參閱[範例讀我檔案](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md)。

執行範例應用程式，以模擬將遙測傳送到 IoT 中樞的 IoT 隨插即用裝置。 若要執行範例應用程式，請使用下列命令：

```cmd\sh
node simple_thermostat.js
```

您會看到下列輸出，這表示裝置已開始將遙測資料傳送至中樞，且已可接收命令和屬性更新。

![裝置確認訊息](media/quickstart-connect-device-node/device-confirmation-node.png)

完成後續步驟後，請讓範例保持執行狀態。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 總管來驗證程式碼

當裝置用戶端範例啟動之後，請使用 Azure IoT 總管工具來驗證其是否正確運作。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 中樞。 若要深入了解如何建置與 IoT 隨插即用裝置互動的解決方案，請參閱：

> [!div class="nextstepaction"]
> [與已連線至解決方案的 IoT 隨插即用裝置互動](quickstart-service-node.md)
