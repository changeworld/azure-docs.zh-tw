---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 190e2144e92ffd77c098b4bf9205de03e55af7df
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511243"
---
本教學課程說明如何建置具有元件的範例 IoT 隨插即用裝置應用程式、將其連線至您的 IoT 中樞，並使用 Azure IoT 總管工具來檢視其傳送至中樞的資訊。 範例應用程式以 C 撰寫，並且包含在適用於 C 的 Azure IoT 裝置 SDK 中。解決方案建立器可使用 Azure IoT 檔案總管工具直接了解 IoT 隨插即用裝置的功能，而不需檢視任何裝置程式碼。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

您可以在 Linux 或 Windows 上完成本教學課程。 本教學課程中的殼層命令會遵循路徑分隔符號 '`/`' 的 Linux 慣例，如果您在 Windows 上遵循，請務必針對 '`\`' 交換這些分隔符號。

必要條件會因作業系統而有所不同：

### <a name="linux"></a>Linux

本教學課程假設您使用 Ubuntu Linux。 本教學課程中的步驟已使用 Ubuntu 18.04 進行測試。

若要在 Linux 上完成本教學課程，您必須在本機 Linux 環境上安裝下列軟體：

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

若要在 Windows 上完成此教學課程，請在您的本機 Windows 環境上安裝下列軟體：

* [Visual Studio (Community、Professional 或 Enterprise)](https://visualstudio.microsoft.com/downloads/) - 在 [安裝](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) Visual Studio 時，請確實包含 **使用 C++ 的桌面開發** 工作負載。
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/)。

## <a name="download-the-code"></a>下載程式碼

如果您已完成[快速入門：將在 Linux 或 Windows 上執行的範例 IoT 隨插即用裝置應用程式連線至 IoT 中樞 (C)](../articles/iot-pnp/quickstart-connect-device.md)，您已經下載了程式碼。

在此教學課程中，您會準備可用來複製及建置 Azure IoT 中樞裝置 C SDK 的開發環境。

在您選擇的資料夾中開啟命令提示字元。 執行下列命令，將 [Azure IoT C SDK 和程式庫](https://github.com/Azure/azure-iot-sdk-c) GitHub 存放庫複製到下列位置：

```cmd/bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

預期此作業需要幾分鐘的時間才能完成。

## <a name="build-and-run-the-code"></a>建置並執行程式碼

您可以使用 Visual Studio 或在命令列上使用 `cmake`，來建立和執行程式碼。

### <a name="use-visual-studio"></a>使用 Visual Studio

1. 開啟所複製存放庫的根資料夾。 幾秒後，Visual Studio 中的 **CMake** 支援就會建立執行和偵錯專案所需的所有項目。
1. 當 Visual Studio 準備就緒時，在 **方案總管** 中瀏覽至 iothub_client/samples/pnp/pnp_temperature_controller/ 範例。
1. 以滑鼠右鍵按一下 pnp_temperature_controller.c 檔案，然後選取 [新增偵錯設定]。 選取 [預設]。
1. Visual Studio 會開啟 launch.vs.json 檔案。 編輯此檔案，以下列程式碼片段所示的方式，設定必要的環境變數。 當您完成下列程序後，您會記下範圍識別碼和註冊主要金鑰：[設定 IoT 隨插即用的環境快速入門和教學課程](../articles/iot-pnp/set-up-environment.md)：

    ```json
    {
      "version": "0.2.1",
      "defaults": {},
      "configurations": [
        {
          "type": "default",
          "project": "iothub_client\\samples\\pnp\\pnp_temperature_controller\\pnp_temperature_controller.c",
          "projectTarget": "",
          "name": "pnp_temperature_controller.c",
          "env": {
            "IOTHUB_DEVICE_SECURITY_TYPE": "DPS",
            "IOTHUB_DEVICE_DPS_ID_SCOPE": "<Your ID scope>",
            "IOTHUB_DEVICE_DPS_DEVICE_ID": "my-pnp-device",
            "IOTHUB_DEVICE_DPS_DEVICE_KEY": "<Your enrollment primary key>"
          }
        }
      ]
    }
    ```

1. 以滑鼠右鍵按一下 pnp_temperature_controller.c 檔案，然後選取 [設定為起始項目]。
1. 若要追蹤 Visual Studio 中的程式碼執行，請在 pnp_temperature_controller.c 檔案中，將中斷點新增至 `main` 函式。
1. 您現在可以從 [偵錯] 功能表中執行和偵錯範例。

裝置現在已可接收命令和屬性更新，並已開始將遙測資料傳送至中樞。 完成後續步驟後，請讓範例保持執行狀態。

### <a name="use-cmake-at-the-command-line"></a>在命令列上使用 `cmake`

建置範例：

1. 在複製的裝置 SDK 根資料夾中建立 cmake 子資料夾，並瀏覽至該資料夾：

    ```cmd/bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. 執行下列命令，以產生並建立 SDK 的專案檔和範例：

    ```cmd/bash
    cmake ..
    cmake --build .
    ```

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

若要深入了解範例設定，請參閱[範例讀我檔案](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md)。

若要執行範例：

1. 從 _cmake_ 資料夾瀏覽至包含可執行檔的資料夾並且執行：

    ```bash
    # Bash
    cd iothub_client/samples/pnp/pnp_temperature_controller/
    ./pnp_temperature_controller
    ```

    ```cmd
    REM Windows
    cd iothub_client\samples\pnp\pnp_temperature_controller\Debug
    pnp_temperature_controller.exe
    ```

裝置現在已可接收命令和屬性更新，並已開始將遙測資料傳送至中樞。 完成後續步驟後，請讓範例保持執行狀態。

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 檔案總管來驗證程式碼

當裝置用戶端範例啟動之後，請使用 Azure IoT 總管工具來驗證其是否正確運作。

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>檢閱程式碼

這個範例會實作 IoT 隨插即用的溫度控制器裝置。 此範例會使用[多個元件](../articles/iot-pnp/concepts-components.md)來執行模型。 [適用於溫度裝置的 Digital Twins 定義語言 (DTDL) 模型檔案](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) \(英文\) 會定義裝置所實作的遙測、屬性和命令。

### <a name="iot-plug-and-play-helper-functions"></a>IoT 隨插即用 helper 函式

在此範例中，程式碼會使用 /common 資料夾中的一些 helper 函式：

pnp_device_client_ll 包含 IoT 隨插即用的 connect 方法，其中包含作為參數的 `model-id`：`PnP_CreateDeviceClientLLHandle`。

pnp_protocol：包含 IoT 隨插即用 helper 函式：

* `PnP_CreateReportedProperty`
* `PnP_CreateReportedPropertyWithStatus`
* `PnP_ParseCommandName`
* `PnP_CreateTelemetryMessageHandle`
* `PnP_ProcessTwinData`
* `PnP_CopyPayloadToString`
* `PnP_CreateDeviceClientLLHandle_ViaDps`

這些通用的 helper 函式，足以讓您在自己的專案中使用。 此範例會在對應至模型中每個元件的三個檔案中使用這些函式：

* *pnp_deviceinfo_component*
* *pnp_temperature_controller*
* *pnp_thermostat_component*

例如，在 pnp_deviceinfo_component 檔案中，`SendReportedPropertyForDeviceInformation` 函式會使用兩個 helper 函式：

```c
if ((jsonToSend = PnP_CreateReportedProperty(componentName, propertyName, propertyValue)) == NULL)
{
    LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", propertyName, propertyValue);
}
else
{
    const char* jsonToSendStr = STRING_c_str(jsonToSend);
    size_t jsonToSendStrLen = strlen(jsonToSendStr);

    if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(deviceClientLL, (const unsigned char*)jsonToSendStr, jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send reported state for property=%s, error=%d", propertyName, iothubClientResult);
    }
    else
    {
        LogInfo("Sending device information property to IoTHub.  propertyName=%s, propertyValue=%s", propertyName, propertyValue);
    }
}
```

範例中的每個元件都會遵循此模式。

### <a name="code-flow"></a>程式碼流程

`main` 函式會初始化連線，並傳送模型識別碼：

```c
deviceClient = CreateDeviceClientAndAllocateComponents();
```

程式碼會使用 `PnP_CreateDeviceClientLLHandle` 來連線到 IoT 中樞、將 `modelId` 設定為選項，以及設定適用於直接方法和裝置對應項更新的裝置方法和裝置對應項回呼處理常式：

```c
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
g_pnpDeviceConfiguration.deviceTwinCallback = PnP_TempControlComponent_DeviceTwinCallback;
g_pnpDeviceConfiguration.modelId = g_temperatureControllerModelId;
...

deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

`&g_pnpDeviceConfiguration` 也包含連線資訊。 環境變數 **IOTHUB_DEVICE_SECURITY_TYPE** 會判斷範例是否使用連接字串或裝置佈建服務來連線到 IoT 中樞。

裝置傳送模型識別碼時，就會變成 IoT 隨插即用裝置。

回呼處理常式就緒之後，裝置就會回應對應項更新和直接方法呼叫：

* 針對裝置對應項回呼，`PnP_TempControlComponent_DeviceTwinCallback` 會呼叫 `PnP_ProcessTwinData` 函式來處理資料。 `PnP_ProcessTwinData` 會使用「訪客模式」來剖析 JSON，然後瀏覽每個屬性，並在每個元素上呼叫 `PnP_TempControlComponent_ApplicationPropertyCallback`。

* 針對命令回呼，`PnP_TempControlComponent_DeviceMethodCallback` 函式會使用 helper 函式來剖析命令和元件名稱：

    ```c
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);
    ```

    然後，`PnP_TempControlComponent_DeviceMethodCallback` 函式會在元件上呼叫命令：

    ```c
    LogInfo("Received PnP command for component=%.*s, command=%s", (int)componentNameSize, componentName, pnpCommandName);
    if (strncmp((const char*)componentName, g_thermostatComponent1Name, g_thermostatComponent1Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle1, pnpCommandName, rootValue, response, responseSize);
    }
    else if (strncmp((const char*)componentName, g_thermostatComponent2Name, g_thermostatComponent2Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle2, pnpCommandName, rootValue, response, responseSize);
    }
    else
    {
        LogError("PnP component=%.*s is not supported by TemperatureController", (int)componentNameSize, componentName);
        result = PNP_STATUS_NOT_FOUND;
    }
    ```

`main` 函式會初始化傳送至 IoT 中樞的唯讀屬性：

```c
PnP_TempControlComponent_ReportSerialNumber_Property(deviceClient);
PnP_DeviceInfoComponent_Report_All_Properties(g_deviceInfoComponentName, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle2, deviceClient);
```

`main` 函式會進入迴圈，以更新每個元件的事件和遙測資料：

```c
while (true)
{
    PnP_TempControlComponent_SendWorkingSet(deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle2, deviceClient);
}
```

`PnP_ThermostatComponent_SendTelemetry` 函式會顯示如何使用 `PNP_THERMOSTAT_COMPONENT` 結構。 此範例會使用此結構，將兩個控溫器的相關資訊儲存在溫度控制器中。 程式碼會使用 `PnP_CreateTelemetryMessageHandle` 函式來準備訊息並傳送訊息：

```c
messageHandle = PnP_CreateTelemetryMessageHandle(pnpThermostatComponent->componentName, temperatureStringBuffer);
...
iothubResult = IoTHubDeviceClient_LL_SendEventAsync(deviceClientLL, messageHandle, NULL, NULL);
```

`main` 函式最後會終結不同的元件，並關閉與中樞的連線。

[!INCLUDE [iot-pnp-clean-resources.md](iot-pnp-clean-resources.md)]
