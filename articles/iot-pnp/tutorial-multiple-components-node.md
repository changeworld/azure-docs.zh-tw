---
title: 將 IoT 隨插即用預覽範例 Node.js 元件裝置程式碼連線至 IoT 中樞 | Microsoft Docs
description: 建置並執行 IoT 隨插即用預覽範例 Node.js 裝置程式碼，以使用多個元件並連線至 IoT 中樞。 使用 Azure IoT 檔案總管工具，檢視裝置傳送至中樞的資訊。
author: olivakar
ms.author: olkar
ms.date: 07/10/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.custom: devx-track-javascript
ms.openlocfilehash: d26179ab82f29ce8f937f5b444463c1308d92047
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904049"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-nodejs"></a>教學課程：將範例 IoT 隨插即用預覽多個元件裝置應用程式連線至 IoT 中樞 (Node.js)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

此教學課程說明如何使用元件和根介面來建置範例 IoT 隨插即用裝置應用程式、將其連線至您的 IoT 中樞，並使用 Azure IoT 總管工具來檢視其傳送至中樞的資訊。 範例應用程式是專為 Node.js 所撰寫的，並且包含在適用於 Node.js 的 Azure IoT 中樞裝置 SDK 中。 解決方案建置人員可以使用 Azure IoT 總管工具來了解 IoT 隨插即用裝置的功能，而不需檢視任何裝置程式碼。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您的開發電腦上必須要有 Node.js。 您可以從 [nodejs.org](https://nodejs.org) 下載多個平台的最新建議版本。

您可以使用下列命令，以確認開發電腦上目前的 Node.js 版本：

```cmd/sh
node --version
```

### <a name="azure-iot-explorer"></a>Azure IoT 總管

若要在此教學課程的第二個部分與範例裝置互動，您可以使用 **Azure IoT 總管**工具。 針對您的作業系統[下載並安裝最新版的 Azure IoT 總管](./howto-use-iot-explorer.md)。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

執行下列命令，以取得中樞的 _IoT 中樞連接字串_。 記下此連接字串，您稍後會在此教學課程中用到：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> 您也可以使用 Azure IoT 總管工具來尋找 IoT 中樞連接字串。

執行下列命令，針對您新增至中樞的裝置取得「裝置連接字串」。 記下此連接字串，您稍後會在此教學課程中用到：

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>下載程式碼

您會在本教學課程中準備開發環境，以用來複製及建置適用於 Node.js 的 Azure IoT 中樞裝置 SDK。

在您選擇的目錄中開啟命令提示字元。 執行下列命令，將[適用於 Node.js 的 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub 存放庫複製到下列位置：

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

此作業可能需要幾分鐘的時間才能完成。

## <a name="install-required-libraries"></a>安裝必要的程式庫

您可以使用裝置 SDK 建置包含的範例程式碼。 您所建置的應用程式會模擬具有多個元件的隨插即用裝置，以及連線到 IoT 中樞的根介面。 應用程式會傳送遙測資料和屬性，並接收命令。

1. 在本機終端機視窗中，移至複製存放庫的資料夾，並瀏覽至 /azure-iot-sdk-node/device/samples/pnp 資料夾。 然後執行下列命令來安裝必要的程式庫：

```cmd/sh
npm install
```

這會安裝要在資料夾中執行範例所需的相關 npm 檔案。

1. 使用先前所記錄的裝置連接字串來設定環境變數：

```cmd/sh
set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
```

## <a name="review-the-code"></a>檢閱程式碼

瀏覽至 azure-iot-sdk-node\device\samples\pnp 資料夾。

azure-iot-sdk-node\device\samples\pnp 資料夾包含 IoT 隨插即用溫度控制器裝置的範例程式碼。

pnpTemperatureController.js 檔案中的程式碼會實作 IoT 隨插即用溫度控制器裝置。 這個範例所實作的模型會使用[多個元件](concepts-components.md)。 [適用於溫度裝置的 Digital Twins 定義語言 (DTDL) 模型檔案](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) \(英文\) 會定義裝置所實作的遙測、屬性和命令。

在您選擇的程式碼編輯器中開啟 pnpTemperatureController.js 檔案。 範例程式碼會顯示如何：

1. 定義 `modelId`，這是所要實作的裝置所擁有的 DTMI。 此 DTMI 是使用者定義的，而且必須符合[溫度控制器 DTDL 模型](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)的 DTMI。

2. 實作在溫度控制器 DTDL 模型中定義的元件。 實際溫度控制器中的元件應該會執行這兩個介面。 這兩個介面已在中央存放庫中發佈。 在此範例中，這兩個介面分別是：
  - 控溫器
  - Azure 所開發的裝置資訊

3. 定義元件名稱。 這個範例有兩個恒溫器和一個裝置資訊元件。

4. 定義命令名稱。 這些是裝置所回應的命令。

5. 定義 `serialNumber` 常數。 `serialNumber` 是固定的任何指定裝置。

6. 定義命令處理常式。

7. 定義用來傳送命令回應的函式。

8. 定義協助程式函式來記錄命令要求。

9. 定義協助程式函式來建立屬性。

10. 定義用於屬性更新的接聽程式。

11. 定義從這個裝置傳送遙測的函式。 恒溫器和根元件都會傳送遙測。 此函式會接收元件名稱來作為參數。

12. 定義 `main` 函式，此函式會：

    1. 使用裝置 SDK 來建立裝置用戶端，並連線到您的 IoT 中樞。 裝置會提供 `modelId`，讓 IoT 中樞可以將裝置識別為 IoT 隨插即用裝置。

    1. 使用 `onDeviceMethod` 函式開始接聽命令要求。 此函式會針對來自服務的命令要求設定接聽程式：
        - 裝置 DTDL 會定義 `reboot` 和 `getMaxMinReport` 命令。
        - `commandHandler` 函式會定義裝置如何回應命令。

    1. 使用 `setInterval` 和 `sendTelemetry` 來開始傳送遙測。

    1. 使用 `helperCreateReportedPropertiesPatch` 函式來建立屬性，使用 `updateComponentReportedProperties` 來更新屬性。

    1. 使用 `desiredPropertyPatchListener` 來接聽屬性更新。

    1. 停用所有接聽程式和工作，並在您按下 **Q** 或 **q** 時結束迴圈。

現在您已看過程式碼，請使用下列命令來執行範例：

```cmd\sh
node pnpTemperatureController.js
```

您會看到下列輸出，這表示裝置已開始將遙測資料傳送至中樞，且已可接收命令和屬性更新。

![裝置確認訊息](media/tutorial-multiple-components-node/multiple-component.png)

完成後續步驟後，請讓範例保持執行狀態。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 總管來驗證程式碼

當裝置用戶端範例啟動之後，使用 Azure IoT 總管工具來驗證其是否正確運作。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已了解如何使用元件來將 IoT 隨插即用裝置連線至 IoT 中樞。 若要深入了解 IoT 隨插即用裝置模組，請參閱：

> [!div class="nextstepaction"]
> [IoT 隨插即用預覽模型開發人員指南](concepts-developer-guide.md)
