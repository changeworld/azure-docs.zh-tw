---
title: 使用 MQTT 建立 IoT 隨插即用裝置用戶端 | Microsoft Docs
description: 直接使用 MQTT 通訊協定建立 IoT 隨插即用裝置用戶端，而無須使用 Azure IoT 裝置 SDK
author: ericmitt
ms.author: ericmitt
ms.date: 05/13/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d0ac0f000b6a096ae3de1f4f00a17b64f1948c1e
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046276"
---
# <a name="use-mqtt-to-develop-an-iot-plug-and-play-device-client"></a>使用 MQTT 開發 IoT 隨插即用裝置用戶端

如果可能，您應該使用其中一個 Azure IoT 裝置 SDK 來建置您的 IoT 隨插即用裝置用戶端。 不過，在使用記憶體限制裝置的案例中，您可能需要使用 MQTT 程式庫來與 IoT 中樞通訊。

本教學課程中的範例會使用 [Eclipse Mosquitto](http://mosquitto.org/) MQTT 程式庫和 Visual Studio。 本教學課程中的步驟假設您在開發電腦上使用 Windows。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

若要在 Windows 上完成此教學課程，請在您的本機 Windows 環境上安裝下列軟體：

* [Visual Studio (Community、Professional 或 Enterprise)](https://visualstudio.microsoft.com/downloads/) - 在[安裝](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) Visual Studio 時，請確實包含**使用 C++ 的桌面開發**工作負載
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)

使用 *Azure IoT 總管*工具，將新裝置新增至您的 IoT 中樞。 當您完成下列程序後，您即已設定 IoT 中樞和 Azure IoT 總管工具：[設定 IoT 隨插即用的環境快速入門和教學課程](set-up-environment.md)：

1. 啟動 **Azure IoT 總管**工具。
1. 在 [IoT 中樞] 頁面上，選取 [檢視此中樞內的裝置]。
1. 在 [裝置] 頁面上，選取 [+ 新增]。
1. 建立使用自動產生的對稱金鑰、名稱為 *my-mqtt-device* 的裝置。
1. 在 [裝置身分識別] 頁面上，展開 [具有 SAS 權杖的連接字串]。
1. 選擇 [主要金鑰] 作為 [對稱金鑰]、將到期時間設為 60 分鐘，然後選取 [產生]。
1. 複製產生的 **SAS 權杖連接字串**，稍後將會在本教學課程中使用此值。

## <a name="clone-sample-repo"></a>複製範例存放庫

使用下列命令，將範例存放庫複製到本機電腦中的替代位置：

```cmd
git clone https://github.com/Azure-Samples/IoTMQTTSample.git
```

## <a name="install-mqtt-library"></a>安裝 MQTT 程式庫

使用 `vcpkg` 工具下載並建置 Eclipse Mosquitto 程式庫。

使用下列命令，將 **Vcpkg** 存放庫複製到本機電腦中的替代位置：

```cmd
git clone https://github.com/Microsoft/vcpkg.git
```

使用下列命令，以準備 `vcpkg` 環境。 當您執行 `vcpkg integrate install`時，需要提高權限的命令提示字元：

```cmd
cd vcpkg
.\bootstrap-vcpkg.bat
.\vcpkg integrate install
```

使用下列命令下載並建置 Eclipse Mosquitto 程式庫：

```cmd
.\vcpkg install mosquitto:x64-windows
```

## <a name="migrate-the-sample-to-iot-plug-and-play"></a>將範例遷移至 IoT 隨插即用

### <a name="review-the-non-iot-plug-and-play-sample-code"></a>檢閱非 IoT 隨插即用範例程式碼

建置並執行程式碼之前，請先使用 IoT 中樞和裝置的詳細資料來更新程式碼。

若要在 Visual Studio 中檢視範例程式碼，請在 IoTMQTTSample\src\Windows 資料夾中開啟 MQTTWin32.sln 方案檔。

在 [方案總管] 中，以滑鼠右鍵按一下 **TelemetryMQTTWin32** 專案，然後選取 [設定為啟始專案]。

在 **TelemetryMQTTWin32** 專案中，開啟 **MQTT_Mosquitto.cpp** 來源檔案。 以您先前記下的裝置詳細資料，更新連線資訊定義。 取代下列項目的權杖字串預留位置：

* `IOTHUBNAME` 識別碼取代為您的 IoT 中樞名稱。
* `DEVICEID` 識別碼取代為 `my-mqtt-device`。
* `PWD` 識別碼取代為您為裝置產生的 SAS 權杖連接字串的正確部分。 請使用從 `SharedAccessSignature sr=` 到結尾處的連接字串部分。

啟動 Azure IoT 總管並開始接聽遙測，以確認程式碼運作正常。

執行應用程式 (Ctrl+F5) 幾秒鐘後，您會看到如下所示的輸出：

:::image type="content" source="media/tutorial-use-mqtt/mqtt-sample-output.png" alt-text="MQTT 範例應用程式的輸出":::

在 Azure IoT 總管中，您可以看到裝置並非 IoT 隨插即用裝置：

:::image type="content" source="media/tutorial-use-mqtt/non-pnp-iot-explorer.png" alt-text="MQTT 範例應用程式的輸出":::

### <a name="make-the-device-an-iot-plug-and-play-device"></a>讓裝置成為 IoT 隨插即用裝置

IoT 隨插即用裝置必須遵循一組簡單的慣例。 如果裝置在連線時傳送模型識別碼，就會變成 IoT 隨插即用裝置。

在此範例中，您會將模型識別碼新增至 MQTT 連線封包。 您會在 `USERNAME` 中將模型識別碼當作 querystring 參數傳遞，並將 `api-version` 變更為 `2020-09-30`：

```c
// computed Host Username and Topic
//#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2018-06-30"
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-09-30&model-id=dtmi:com:example:Thermostat;1"
#define PORT 8883
#define HOST IOTHUBNAME //".azure-devices.net"
#define TOPIC "devices/" DEVICEID "/messages/events/"
```

重建並執行範例。

裝置對應項現在包含模型識別碼：

:::image type="content" source="media/tutorial-use-mqtt/model-id-iot-explorer.png" alt-text="MQTT 範例應用程式的輸出":::

您現在可以瀏覽 IoT 隨插即用元件：

:::image type="content" source="media/tutorial-use-mqtt/components-iot-explorer.png" alt-text="MQTT 範例應用程式的輸出":::

您現在可以修改裝置程式碼，以實作模型中所定義的遙測、屬性和命令。 若要查看使用 Mosquitto 程式庫之控溫器裝置的範例實作，請參閱 GitHub 上的 [在 Windows 中使用 MQTT PnP 搭配 Azure IoTHub (不含 IoT SDK)](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32)。

> [!NOTE]
>用戶端會使用 `IoTHubRootCA_Baltimore.pem` 根憑證檔案來驗證連線的 IoT 中樞身分識別。

### <a name="mqtt-topics"></a>MQTT 主題

下列定義適用於裝置用來將資訊傳送至 IoT 中樞的 MQTT 主題。 若要深入了解，請參閱[使用 MQTT 通訊協定與 IoT 中樞通訊](../iot-hub/iot-hub-mqtt-support.md)：

* `DEVICE_CAPABILITIES_MESSAGE` 定義裝置用來報告其實作介面的主題。
* `DEVICETWIN_PATCH_MESSAGE` 定義裝置用來向 IoT 中樞報告屬性更新的主題。
* `DEVICE_TELEMETRY_MESSAGE` 定義裝置用來向 IoT 中樞傳送遙測的主題。

如需 MQTT 的詳細資訊，請造訪 GitHub 存放庫中的[適用於 Azure IoT 的 MQTT 範例](https://github.com/Azure-Samples/IoTMQTTSample/)。
  
## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何修改 MQTT 裝置用戶端，以遵循 IoT 隨插即用慣例。 深入了解 IoT 隨插即用，請參閱：

> [!div class="nextstepaction"]
> [架構](concepts-architecture.md)

若要深入了解 MQTT 通訊協定的 IoT 中樞支援，請參閱：

> [!div class="nextstepaction"]
> [使用 MQTT 通訊協定來與 IoT 中樞通訊](../iot-hub/iot-hub-mqtt-support.md)