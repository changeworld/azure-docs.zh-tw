---
title: 將 IoT 隨插即用預覽版範例裝置程式碼連線至 IoT 中樞 (Linux) | Microsoft Docs
description: 在 Linux 上建置並執行連線至 IoT 中樞的 IoT 隨插即用預覽版範例裝置程式碼。 使用 Azure CLI 檢視裝置傳送至中樞的資訊。
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 8134c0a97f6350cfa2cf616695c5990618455393
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "75531244"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>快速入門：將在 Linux 上執行的範例 IoT 隨插即用預覽版裝置應用程式連線至 IoT 中樞 (C Linux)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

本快速入門說明如何在 Linux 上建置範例 IoT 隨插即用裝置應用程式、將其連線至您的 IoT 中樞，並使用 Azure CLI 檢視它傳送至中樞的資訊。 範例應用程式以 C 撰寫，並且包含在適用於 C 的 Azure IoT 裝置 SDK 中。解決方案開發人員可以使用 Azure CLI 了解 IoT 隨插即用裝置的功能，而不需檢視任何裝置程式碼。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisites

本快速入門假設您使用的是 Ubuntu Linux。 本教學課程中的步驟已使用 Ubuntu 18.04 進行測試。

若要完成本快速入門，您必須在本機 Linux 電腦上安裝下列軟體：

使用 `apt-get` 命令，安裝 **GCC**、**Git**、**cmake** 和所有相依性：

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

確認 `cmake` 的版本高於 **2.8.12**，而且 **GCC** 的版本高於 **4.4.7**。

```sh
cmake --version
gcc --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>準備開發環境

在本快速入門中，您會準備可用來複製及建置 Azure IoT 中樞裝置 C SDK 的開發環境。

在您選擇的目錄中開啟命令提示字元。 執行下列命令，將 [Azure IoT C SDK 和程式庫](https://github.com/Azure/azure-iot-sdk-c) GitHub 存放庫複製到下列位置：

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

預期此作業需要幾分鐘的時間才能完成。

## <a name="build-the-code"></a>建置程式碼

您可以使用裝置 SDK 建置包含的範例程式碼。 您所建置的應用程式會模擬連線至 IoT 中樞的裝置。 應用程式會傳送遙測資料和屬性，並接收命令。

1. 在裝置 SDK 根資料夾中建立 `cmake` 子目錄，並瀏覽至該資料夾：

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. 執行下列命令，以建置裝置 SDK 和產生的程式碼 Stub：

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>更新模型存放庫

執行範例之前，請先將裝置功能模型和介面定義新增至您的公司模型存放庫：

1. 使用您的 Microsoft 公司或學校帳戶，或您的 Microsoft 合作夥伴識別碼 (如果有的話)，登入 [Azure IoT 認證入口網站](https://preview.catalog.azureiotsolutions.com)。

1. 依序選取 [公司存放庫]  和 [功能模型]  。

1. 選取 [新增]  ，然後選取 [上傳]  。

1. 在裝置 SDK 根資料夾的 `digitaltwin_client/samples` 資料夾中選取檔案 `SampleDevice.capabilitymodel.json`。 選取 [開啟]  ，然後選取 [儲存]  ，將模型檔案上傳到您的存放庫。

1. 依序選取 [公司存放庫]  和 [介面]  。

1. 選取 [新增]  ，然後選取 [上傳]  。

1. 在裝置 SDK 根資料夾的 `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` 資料夾中選取檔案 `EnvironmentalSensor.interface.json`。 選取 [開啟]  ，然後選取 [儲存]  ，將介面檔案上傳到您的存放庫。

1. 依序選取 [公司存放庫]  和 [連接字串]  。 請記下第一個_公司模型存放庫連接字串_，您稍後會在本快速入門中加以使用。

## <a name="run-the-device-sample"></a>執行裝置範例

在 SDK 中執行範例應用程式，以模擬將遙測傳送到 IoT 中樞的 IoT 隨插即用裝置。 若要執行範例應用程式：

1. 從 `cmake` 資料夾瀏覽至包含可執行檔的資料夾：

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. 執行可執行檔：

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

裝置現在已可接收命令和屬性更新，並已開始將遙測資料傳送至中樞。 完成後續步驟後，請讓範例保持執行狀態。

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>使用 Azure IoT CLI 驗證程式碼

當裝置用戶端範例啟動之後，請確認它是否與 Azure CLI 一起運作。

使用下列命令檢視範例裝置正在傳送的遙測。 您可能需要等候一或兩分鐘，才會在輸出中看到任何遙測：

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

使用下列命令檢視裝置所傳送的屬性：

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 中樞。 若要深入了解如何建置與 IoT 隨插即用裝置互動的解決方案，請參閱：

> [!div class="nextstepaction"]
> [操作說明：連線到裝置並與其互動](howto-develop-solution.md)
