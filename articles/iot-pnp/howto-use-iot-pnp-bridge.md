---
title: 如何將 Linux 或 Windows 上執行的 IoT 隨插即用 bridge 範例連線到 IoT 中樞 |Microsoft Docs
description: 在連接到 IoT 中樞的 Linux 或 Windows 上建立並執行 IoT 隨插即用橋接器。 使用 Azure IoT 檔案總管工具，檢視裝置傳送至中樞的資訊。
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: bf730dbc28d15c3d036e9ebeedbe035db087c5d8
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673016"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>如何將在 Linux 或 Windows 上執行的 IoT 隨插即用 bridge 範例連線到 IoT 中樞

本文說明如何建立 IoT 隨插即用橋接器的範例環境介面卡、將其連線至您的 IoT 中樞，以及使用 Azure IoT explorer 工具來查看它所傳送的遙測。 IoT 隨插即用橋接器是以 C 撰寫，並且包含適用于 C 的 Azure IoT 裝置 SDK。在本教學課程結束時，您應該能夠執行 IoT 隨插即用橋接器，並在 Azure IoT explorer 中查看其報告遙測：

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="顯示 Azure IoT explorer 的螢幕擷取畫面，其中包含回報的遙測資料表 (濕度、來自 Iot 隨插即用橋接器的溫度) 。":::

## <a name="prerequisites"></a>Prerequisites

您可以在 Windows 或 Linux 上的文章中執行此範例。 本操作指南中的 shell 命令遵循路徑分隔符號 ' ' 的 Windows 慣例 `\` ，如果您在 Linux 上遵循，請務必將這些分隔符號換成 ' `/` '。

### <a name="azure-iot-explorer"></a>Azure IoT 總管

若要在本文的第二個部分與範例裝置互動，您可以使用 **Azure IoT explorer** 工具。 針對您的作業系統[下載並安裝最新版的 Azure IoT 總管](./howto-use-iot-explorer.md)。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

執行下列命令以取得中樞的 _IoT 中樞連接字串_ 。 請記下此連接字串，您稍後會在本文中使用它：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

執行下列命令，針對您新增至中樞的裝置取得「裝置連接字串」。 請記下此連接字串，您稍後會在本文中使用它：

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="download-and-run-the-bridge"></a>下載並執行橋接器

在本文中，您有兩個執行橋接器的選項。 您可以：

- 下載預先建立的可執行檔並執行，如本節所述。
- 下載原始程式碼，然後 [建立並執行橋接器](#build-and-run-the-bridge) ，如下一節所述。

若要下載並執行橋接器：

1. 移至 IoT 隨插即用 [版本] 頁面](https://github.com/Azure/iot-plug-and-play-bridge/releases)。
1. 下載適用于您作業系統的預建可執行檔：適用于 Windows 的 **pnpbridge_bin.exe** ，或適用于 Linux 的 **pnpbridge_bin** 。
1. 下載環境感應器範例的設定檔範例 [config.js](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/src/adapters/samples/environmental_sensor/config.json) 。 請確定設定檔與可執行檔位於相同的資料夾中。
1. 編輯檔案 *上的config.js* ：

    - 新增 `connection-string` 您先前所記下的 _裝置連接字串_ 值。
    - `symmetric_key`從 _裝置連接字串_ 新增共用存取金鑰值的值。
    - 將 `root_interface_model_id` 值取代為 `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` 。

    檔案 *config.js* 的第一個區段現在看起來會像下列程式碼片段：

    ```json
    {
      "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
      "pnp_bridge_connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
        "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
        "auth_parameters": {
            "auth_type": "symmetric_key",
            "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
        },
    ```

1. 在您的命令列環境中執行可執行檔。 橋接器會產生如下所示的輸出：

    ```output
    c:\temp\temp-bridge>dir
     Volume in drive C is OSDisk
     Volume Serial Number is 38F7-DA4A
    
     Directory of c:\temp\temp-bridge
    
    10/12/2020  12:24    <DIR>          .
    10/12/2020  12:24    <DIR>          ..
    08/12/2020  15:26             1,216 config.json
    10/12/2020  12:21         3,617,280 pnpbridge_bin.exe
                   2 File(s)      3,618,496 bytes
                   2 Dir(s)  12,999,147,520 bytes free
    
    c:\temp\temp-bridge>pnpbridge_bin.exe
    Info:
     -- Press Ctrl+C to stop PnpBridge
    
    Info: Using default configuration location
    Info: Starting Azure PnpBridge
    Info: Pnp Bridge is running as am IoT egde device.
    Info: Pnp Bridge creation succeeded.
    Info: Connection_type is [connection_string]
    Info: Tracing is disabled
    Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
    Info: IoT Edge Device configuration initialized successfully
    Info: Building Pnp Bridge Adapter Manager, Adapters & Components
    Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
    Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
    Info: Pnp Adapter Manager created successfully.
    Info: Pnp components created successfully.
    Info: Pnp components built in model successfully.
    Info: Connected to Azure IoT Hub
    Info: Environmental Sensor: Starting Pnp Component
    Info: IoTHub client call to _SendReportedState succeeded
    Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
    Info: Pnp components started successfully.
    ```

## <a name="build-and-run-the-bridge"></a>建立並執行橋接器

如果您想要自行建立可執行檔，您可以下載原始程式碼和組建腳本。

在您選擇的資料夾中開啟命令提示字元。 執行下列命令，將 [IoT 隨插即用 bridge](https://github.com/Azure/iot-plug-and-play-bridge) GitHub 存放庫複製到下列位置：

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

複製存放庫之後，請更新子模組。 子模組包含適用于 C 的 Azure IoT SDK：

```cmd
cd iot-plug-and-play-bridge
git submodule update --init --recursive
```

預期此作業需要幾分鐘的時間才能完成。

> [!TIP]
> 如果您遇到 git 複製子模組更新失敗的問題，這是 Windows 檔案路徑的已知問題。 您可以嘗試下列命令來解決此問題： `git config --system core.longpaths true`

建立橋接器的必要條件會因作業系統而異：

### <a name="windows"></a>Windows

若要在 Windows 上建立 IoT 隨插即用橋接器，請安裝下列軟體：

* [Visual Studio (Community、Professional 或 Enterprise)](https://visualstudio.microsoft.com/downloads/) - 在 [安裝](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) Visual Studio 時，請確實包含 **使用 C++ 的桌面開發** 工作負載。
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/)。

### <a name="linux"></a>Linux

本文假設您使用的是 Ubuntu Linux。 本文中的步驟已使用 Ubuntu 18.04 進行測試。

若要在 Linux 上建立 IoT 隨插即用橋接器，請使用下列命令來安裝 **GCC**、 **Git**、 **cmake** 和所有必要的相依性 `apt-get` ：

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

確認 `cmake` 的版本高於 **2.8.12**，而且 **GCC** 的版本高於 **4.4.7**。

```sh
cmake --version
gcc --version
```

### <a name="build-the-iot-plug-and-play-bridge"></a>建立 IoT 隨插即用橋接器

流覽至存放庫目錄中的 *pnpbridge* 資料夾。

若為 Windows，請在 [Visual Studio 的開發人員命令提示字元](/dotnet/framework/tools/developer-command-prompt-for-vs)中執行下列程式：

```cmd
cd scripts\windows
build.cmd
```

同樣地，若為 Linux，請執行下列動作：

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>在 Windows 上，您可以在 Visual Studio 2019 中開啟 cmake 命令所產生的解決方案。 開啟 cmake 目錄中的 *azure_iot_pnp_bridge .sln* 專案檔，並將 *pnpbridge_bin* 專案設定為方案中的啟始專案。 您現在可以在 Visual Studio 中建置範例，並於偵錯模式中執行。

### <a name="edit-the-configuration-file"></a>編輯組態檔

您可以在 [IoT 隨插即用 bridge 概念](concepts-iot-pnp-bridge.md)檔中深入瞭解設定檔。

在文字編輯器中開啟檔案 *iot-plug-and-play-bridge\pnpbridge\src\adapters\samples\environmental_sensor\config.js* 。

- 新增 `connection-string` 您先前所記下的 _裝置連接字串_ 值。
- `symmetric_key`從 _裝置連接字串_ 新增共用存取金鑰值的值。
- 將 `root_interface_model_id` 值取代為 `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` 。

檔案 *config.js* 的第一個區段現在看起來會像下列程式碼片段：

```json
{
  "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
  "pnp_bridge_connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
    "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
    "auth_parameters": {
        "auth_type": "symmetric_key",
        "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
    },
```

### <a name="run-the-iot-plug-and-play-bridge"></a>執行 IoT 隨插即用橋接器

啟動 IoT 隨插即用 bridge 環境感應器範例。 參數是 `config.json` 您在上一節中編輯的檔案路徑：

```cmd
REM Windows
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console
Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
```

橋接器會產生如下所示的輸出：

```output
c:\temp>cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

c:\temp\iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console>Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info:
 -- Press Ctrl+C to stop PnpBridge

Info: Using configuration from specified file path: ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info: Starting Azure PnpBridge
Info: Pnp Bridge is running as am IoT egde device.
Info: Pnp Bridge creation succeeded.
Info: Connection_type is [connection_string]
Info: Tracing is disabled
Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
Info: IoT Edge Device configuration initialized successfully
Info: Building Pnp Bridge Adapter Manager, Adapters & Components
Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
Info: Pnp Adapter Manager created successfully.
Info: Pnp components created successfully.
Info: Pnp components built in model successfully.
Info: Connected to Azure IoT Hub
Info: Environmental Sensor: Starting Pnp Component
Info: IoTHub client call to _SendReportedState succeeded
Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
Info: Pnp components started successfully.
Info: IoTHub client call to _SendEventAsync succeeded
Info: PnpBridge_PnpBridgeStateTelemetryCallback called, result=0, telemetry=PnpBridge configuration complete
Info: Processing property update for the device or module twin
Info: Environmental Sensor Adapter:: Successfully delivered telemetry message for <environmentalSensor>
```

使用下列命令，在 Linux 上執行 bridge：

```bash
cd iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_x86/src/pnpbridge/samples/console
./pnpbridge_bin ../../../../../../src/adapters/samples/environmental_sensor/config.json
```

## <a name="download-the-model-files"></a>下載模型檔案

您稍後會使用 Azure IoT Explorer 來查看裝置連線到您的 IoT 中樞。 Azure IoT Explorer 需要模型檔案的本機複本，且該檔案必須符合裝置所傳送的 **模型識別碼** 。 模型檔案可讓 IoT Explorer 顯示您裝置所執行的遙測、屬性和命令。

若要下載 Azure IoT explorer 的模型：

1. 在本機電腦上建立名為 *models* 的資料夾。
1. 將 [EnvironmentalSensor.js](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/docs/schemas/EnvironmentalSensor.json) 儲存到您在上一個步驟中建立的 [ *模型* ] 資料夾。
1. 如果您在文字編輯器中開啟這個模型檔案，您可以看到模型將元件定義 `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` 為其識別碼。 這與您在 *config.json* file 中使用的模型識別碼相同。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 總管來驗證程式碼

橋接器開始之後，請使用 Azure IoT explorer 工具來確認它是否正常運作。 您可以看到模型中所定義的遙測、屬性和命令 `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` 。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何將 IoT 隨插即用裝置連線到 IoT 中樞。 若要深入了解如何建置與 IoT 隨插即用裝置互動的解決方案，請參閱：

* [什麼是 IoT 隨插即用橋接器](./concepts-iot-pnp-bridge.md)
* [建立、部署及擴充 IoT 隨插即用橋接器](howto-build-deploy-extend-pnp-bridge.md)
* [在 GitHub 上 IoT 隨插即用 bridge](https://github.com/Azure/iot-plug-and-play-bridge)
