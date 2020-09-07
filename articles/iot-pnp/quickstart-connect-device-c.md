---
title: 將 IoT 隨插即用預覽版的範例 C 裝置程式碼連線至 IoT 中樞 | Microsoft Docs
description: 在 Linux 或 Windows 上建置並執行連線至 IoT 中樞的 IoT 隨插即用預覽版範例裝置程式碼。 使用 Azure IoT 檔案總管工具，檢視裝置傳送至中樞的資訊。
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 65b35c30d796f733e91c71e569634b61f9f7c52b
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88959284"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-or-windows-to-iot-hub-c"></a>快速入門：將在 Linux 或 Windows 上執行的範例 IoT 隨插即用預覽版裝置應用程式連線至 IoT 中樞 (C)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

本快速入門說明如何建置範例 IoT 隨插即用裝置應用程式、將其連線至您的 IoT 中樞，並使用 Azure IoT 總管工具檢視其所傳送的遙測資料。 範例應用程式以 C 撰寫，並且包含在適用於 C 的 Azure IoT 裝置 SDK 中。解決方案建立器可使用 Azure IoT 檔案總管工具直接了解 IoT 隨插即用裝置的功能，而不需檢視任何裝置程式碼。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>必要條件

您可以在 Linux 或 Windows 上執行本快速入門。 本快速入門中的殼層命令會遵循路徑分隔符號 '`/`' 的 Linux 慣例，如果您在 Windows 上遵循，請務必針對 '`\`' 交換這些分隔符號。

必要條件會因作業系統而有所不同：

### <a name="linux"></a>Linux

本快速入門假設您使用的是 Ubuntu Linux。 本快速入門中的步驟已使用 Ubuntu 18.04 進行測試。

若要在 Linux 上完成本快速入門，您必須在本機 Linux 環境上安裝下列軟體：

使用 `apt-get` 命令，安裝 **GCC**、**Git**、**cmake** 和所有必要的相依性：

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

確認 `cmake` 的版本高於 **2.8.12**，而且 **GCC** 的版本高於 **4.4.7**。

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

若要在 Windows 上完成本快速入門，請在您的本機 Windows 環境上安裝下列軟體：

* [Visual Studio (Community、Professional 或 Enterprise)](https://visualstudio.microsoft.com/downloads/) - 在[安裝](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) Visual Studio 時，請確實包含**使用 C++ 的桌面開發**工作負載。
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/)。

### <a name="azure-iot-explorer"></a>Azure IoT 總管

若要在本快速入門的第二個部分與範例裝置互動，您可以使用 **Azure IoT 總管**工具。 針對您的作業系統[下載並安裝最新版的 Azure IoT 總管](./howto-use-iot-explorer.md)。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

執行下列命令，以取得中樞的 _IoT 中樞連接字串_。 記下此連接字串，您稍後會在本快速入門中用到：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> 您也可以使用 Azure IoT 總管工具來尋找 IoT 中樞連接字串。

執行下列命令，針對您新增至中樞的裝置取得_裝置連接字串_。 記下此連接字串，您稍後會在本快速入門中用到：

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>下載程式碼

在本快速入門中，您會準備可用來複製及建置 Azure IoT 中樞裝置 C SDK 的開發環境。

在您選擇的目錄中開啟命令提示字元。 執行下列命令，將 [Azure IoT C SDK 和程式庫](https://github.com/Azure/azure-iot-sdk-c) GitHub 存放庫複製到下列位置：

```cmd\bash
git clone --depth 1 --recurse-submodules https://github.com/Azure/azure-iot-sdk-c.git
```

預期此作業需要幾分鐘的時間才能完成。

## <a name="build-the-code"></a>建置程式碼

您可以使用裝置 SDK 建置包含的範例程式碼：

1. 在裝置 SDK 根資料夾中建立 _cmake_ 子目錄，並瀏覽至該資料夾：

    ```cmd\bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. 執行下列命令，以建置 SDK 和範例：

    ```cmd\bash
    cmake ..
    cmake --build .
    ```

> [!TIP]
> 在 Windows 上，您可以在 Visual Studio 2019 中開啟 `cmake` 命令所產生的解決方案。 在 _cmake_ 目錄中開啟 azure_iot_sdks.sln 專案檔，並將 **pnp_simple_thermostat** 專案設定為解決方案中的啟始專案。 您現在可以在 Visual Studio 中建置範例，並於偵錯模式中執行。

## <a name="run-the-device-sample"></a>執行裝置範例

若要在 SDK 中執行範例應用程式，以模擬將遙測傳送到 IoT 中樞的 IoT 隨插即用裝置：

建立兩個環境變數，將範例設定為使用連接字串來連線到您的 IoT 中樞：

- 值為 `"connectionString"` 的 **IOTHUB_DEVICE_SECURITY_TYPE**
- **IOTHUB_DEVICE_CONNECTION_STRING**用以儲存您先前記下的裝置連接字串。

從 _cmake_ 資料夾瀏覽至包含可執行檔的資料夾並且執行：

```bash
# Bash
cd iothub_client/samples/pnp/pnp_simple_thermostat/
./pnp_simple_thermostat
```

```cmd
REM Windows
cd  iothub_client\samples\pnp\pnp_simple_thermostat\Debug\pnp_simple_thermostat.exe
```

> [!TIP]
> 若要在 Windows 上 Visual Studio 追蹤程式碼執行，請在 _pnp_simple_thermostat.c_ 檔案中新增 `main` 函式的中斷點。

裝置現在已可接收命令和屬性更新，並已開始將遙測資料傳送至中樞。 完成後續步驟後，請讓範例保持執行狀態。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 總管來驗證程式碼

當裝置用戶端範例啟動之後，請使用 Azure IoT 總管工具來驗證其是否正確運作。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>檢閱程式碼

此範例會實作一個簡單的 IoT 隨插即用控溫器裝置。 此範例實作的模型不會使用 IoT 隨插即用[元件](concepts-components.md)。 [適用於控溫器裝置的DTDL 模型檔案](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)會定義裝置所實作的遙測、屬性和命令。

裝置程式碼會使用標準的函式連線到您的 IoT 中樞：

```c
deviceHandle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, MQTT_Protocol)
```

裝置會在連線要求中，傳送其實作之 DTDL 模型的模型識別碼。 傳送模型識別碼的裝置是 IoT 隨插即用裝置：

```c
static const char g_ModelId[] = "dtmi:com:example:Thermostat;1";

...

IoTHubDeviceClient_SetOption(deviceHandle, OPTION_MODEL_ID, modelId)
```

更新屬性、處理命令及傳送遙測資料的程式碼，與不使用 IoT 隨插即用慣例的裝置程式碼相同。

此程式碼會使用 Parson 程式庫來剖析從您的 IoT 中樞所傳送承載中的 JSON 物件：

```c
// JSON parser
#include "parson.h"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 中樞。 若要深入了解如何建置與 IoT 隨插即用裝置互動的解決方案，請參閱：

> [!div class="nextstepaction"]
> [操作說明：連線到裝置並與其互動](howto-develop-solution.md)
