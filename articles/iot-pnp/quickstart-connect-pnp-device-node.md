---
title: 將 IoT 隨插即用預覽版的範例裝置程式碼連線至 IoT 中樞 | Microsoft Docs
description: 使用 Node.js 建置並執行連線至 IoT 中樞的 IoT 隨插即用預覽版範例裝置程式碼。 使用 Azure IoT 檔案總管工具，檢視裝置傳送至中樞的資訊。
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: c5d69ae21cd240b0c68b9694a55de2cf879a1966
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964782"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-nodejs"></a>快速入門：將範例 IoT 隨插即用預覽版裝置應用程式連線至 IoT 中樞 (Node.js)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

本快速入門說明如何建置範例 IoT 隨插即用裝置應用程式、將其連線至您的 IoT 中樞，並使用 Azure IoT 檔案總管工具來檢視它傳送至中樞的資訊。 範例應用程式是專為 Node.js 所撰寫的，並且包含在適用於 Node.js 的 Azure IoT 中樞裝置 SDK 中。 解決方案開發人員可使用 Azure IoT 檔案總管工具直接了解 IoT 隨插即用裝置的功能，而不需檢視任何裝置程式碼。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門，您的開發電腦上必須要有 Node.js。 您可以從 [nodejs.org](https://nodejs.org) 下載多個平台的最新建議版本。

您可以使用下列命令，以確認開發電腦上目前的 Node.js 版本：

```cmd/sh
node --version
```

### <a name="install-the-azure-iot-explorer"></a>安裝 Azure IoT 檔案總管

從工具的 [存放庫](https://github.com/Azure/azure-iot-explorer/releases) 頁面下載並安裝最新版的 **Azure IoT 檔案總管**，方法是，選取 [資產] 下的 .msi 檔案，以取得最新的更新。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

執行下列命令，以取得中樞的 _IoT 中樞連接字串_ (請記下以供後續使用)：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>準備開發環境

您會在本快速入門中準備開發環境，以用來複製及建置適用於 Node.js 的 Azure IoT 中樞裝置 SDK。

在您選擇的目錄中開啟命令提示字元。 執行下列命令，將[適用於 Node.js 的 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub 存放庫複製到下列位置：

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node --recursive -b digitaltwins-preview
```

此作業可能需要幾分鐘的時間才能完成。

## <a name="install-required-libraries"></a>安裝必要的程式庫

您可以使用裝置 SDK 建置包含的範例程式碼。 您所建置的應用程式會模擬連線至 IoT 中樞的裝置。 應用程式會傳送遙測資料和屬性，並接收命令。

1. 在本機終端機視窗中，移至您複製存放庫的資料夾，然後瀏覽至 **/azure-iot-sdk-node/digitaltwins/samples/device/javascript** 資料夾。 然後執行下列命令來安裝必要的程式庫：

    ```cmd/sh
    npm install
    ```
1. 設定_裝置連接字串_：

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>執行範例裝置

在 SDK 中執行範例應用程式，以模擬將遙測傳送到 IoT 中樞的 IoT 隨插即用裝置。 若要執行範例應用程式，請使用下列命令：

```cmd\sh
    node sample_device.js
```

您會看到下列輸出，這表示裝置已開始將遙測資料傳送至中樞，且已可接收命令和屬性更新。

   ![裝置確認訊息](media/quickstart-connect-pnp-device/device-confirmation-node.png)

 完成後續步驟後，請讓範例保持執行狀態。

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 檔案總管來驗證程式碼

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. 若要確保工具可從您的裝置讀取介面模型定義，請選取 [設定]  。 在 [設定] 功能表中，[在已連線的裝置上]  可能已出現在 [隨插即用設定] 中；如果沒有，請選取 [+ 新增模組定義來源]  ，然後選取 [在已連線的裝置上]  加以新增。

1. 回到**裝置**概觀頁面，尋找您先前建立的裝置身分識別。 當裝置應用程式仍在命令提示字元中執行時，請在 Azure IoT 檔案總管中檢查裝置的**線上狀態**是否會回報為「已連線」  (如果不是，請按 **重新整理**，直到變成已連線為止)。 選取裝置以檢視更多詳細資料。

1. 展開識別碼為 **urn:contoso:com:EnvironmentalSensor:1** 的介面，以顯示介面和 IoT 隨插即用基本項目 — 屬性、命令和遙測資料。

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 中樞。 若要深入了解如何建置與 IoT 隨插即用裝置互動的解決方案，請參閱：

> [!div class="nextstepaction"]
> [操作說明：連線至 IoT 隨插即用預覽版裝置並與其互動](howto-develop-solution.md)
