---
title: 將 IoT 隨插即用預覽範例 Python 元件裝置程式碼連線至 IoT 中樞 | Microsoft Docs
description: 建置並執行使用多個元件的 IoT 隨插即用預覽範例 Python 裝置程式碼，並連線至 IoT 中樞。 使用 Azure IoT 檔案總管工具，檢視裝置傳送至中樞的資訊。
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 571f0e0ceff0adfbf1814abc627fcab6b23acbe1
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905849"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-python"></a>教學課程：將範例 IoT 隨插即用預覽多個元件裝置應用程式連線至 IoT 中樞 (Python)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

此教學課程說明如何建置具有元件和根介面的範例 IoT 隨插即用裝置應用程式，將其連線至您的 IoT 中樞，並使用 Azure IoT 總管工具來檢視其傳送至中樞的資訊。 範例應用程式是以 Python 撰寫的，並隨附於適用於 Python 的 Azure IoT 裝置 SDK 中。 解決方案建置人員可以使用 Azure IoT 總管工具來了解 IoT 隨插即用裝置的功能，而不需檢視任何裝置程式碼。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您的開發電腦上必須要有 Python 3.7。 您可以從 [python.org](https://www.python.org/) \(英文\) 下載適用於多個平台的最新建議版本。您可以使用下列命令來檢查 Python 版本：  

```cmd/sh
python --version
```

您可以從 [python.org](https://www.python.org/) \(英文\) 下載適用於多個平台的最新建議版本。

### <a name="azure-iot-explorer"></a>Azure IoT 總管

若要在此教學課程的第二部分中與範例裝置互動，您會使用 **Azure IoT 總管**工具。 針對您的作業系統[下載並安裝最新版的 Azure IoT 總管](./howto-use-iot-explorer.md)。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

執行下列命令，以取得中樞的「IoT 中樞連接字串」。 記下此連接字串，您稍後會在此教學課程中用到：

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

## <a name="set-up-your-environment"></a>設定您的環境

此套件會發佈為 PIP，以供公開預覽重新整理之用。 套件版本應該是最新版或 `2.1.4`

在您的本機 Python 環境中安裝檔案，如下所示：

```cmd/sh
pip install azure-iot-device
```

複製 Python SDK IoT 存放庫，並簽出 **pnp-preview-refresh**：

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>檢閱程式碼

這個範例會實作 IoT 隨插即用的溫度控制器裝置。 這個範例所實作的模型會使用[多個元件](concepts-components.md)。 [適用於溫度裝置的 Digital Twins 定義語言 (DTDL) 模型檔案](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) \(英文\) 會定義裝置所實作的遙測、屬性和命令。

[azure-iot-sdk-python\azure-iot-device\samples\pnp] 資料夾包含適用於 IoT 隨插即用裝置的範例程式碼。 適用於溫度控制器範例的檔案包括：

- pnp_temp_controller_with_thermostats.py
- pnp_helper_preview_refresh.py

溫度控制器具有多個元件和一個根介面，以溫度控制器 DTDL 模型為基礎。

在您選擇的編輯器中，開啟 *pnp_temp_controller_with_thermostats.py* 檔案。 此檔案中的程式碼：

1. 匯入 `pnp_helper_preview_refresh.py` 以取得協助程式方法的存取權。

2. 定義兩個 Digital Twins 模型識別碼 (DTMI)，其能唯一代表 DTDL 模型中所定義的兩個不同介面。 實際溫度控制器中的元件應該要實作這兩個介面。 這兩個介面已在中央存放庫中發佈。 使用者必須知道這些 DTMI，且這些介面也會取決於裝置實作的案例。 針對目前的範例，這兩個介面代表：

  - 控溫器
  - Azure 所開發的裝置資訊。

3. 定義正在實作之裝置的 DTMI `model_id`。 DTMI 是使用者定義的，而且必須符合 DTDL 模型檔案中的 DTMI。

4. 定義提供給 DTDL 檔案中元件的名稱。 DTDL 中有兩個控溫器和一個裝置資訊元件。 名為 `serial_number` 的常數也會定義於根介面中。 無法變更裝置的 `serial_number`。

5. 定義命令處理常式實作。 這些會定義裝置接收到命令要求時的行為。

6. 定義函式以建立命令回應。 這些會定義裝置如何回應命令要求。 如果命令需要將自訂回應傳送回 IoT 中樞，您會建立命令回應函式。 如果未提供命令的回應函式，則會傳送一般回應。 在此範例中，只有 **getMaxMinReport** 命令具有自訂回應。

7. 定義函式以從此裝置傳送遙測。 控溫器和根介面都會傳送遙測。 此函式會採用選擇性元件名稱參數，讓其能夠識別傳送遙測的元件。

8. 定義命令要求的接聽程式。

9. 定義所需屬性更新的接聽程式。

10. 具有 `main` 函式，其能夠：

    1. 使用裝置 SDK 來建立裝置用戶端，並連線到您的 IoT 中樞。 裝置會傳送 `model_id`，讓 IoT 中樞可以將裝置識別為 IoT 隨插即用裝置。

    1. 使用協助程式檔案中的 `create_reported_properties` 函式來建立屬性。 將元件名稱和屬性當作索引鍵值組傳遞至此函式。

    1. 呼叫 `patch_twin_reported_properties` 來更新其元件的可讀取屬性。

    1. 使用 `execute_command_listener` 函式開始接聽命令要求。 此函式會針對來自服務的命令要求設定接聽程式。 當您設定接聽程式時，會提供 `method_name`、`user_command_handler` 和選擇性的 `create_user_response_handler` 作為參數。
        - `method_name` 會定義命令要求。 在此範例中，模型會定義命令 **reboot** 和 **getMaxMinReport**。
        - `user_command_handler` 函式會定義裝置收到命令時應執行的動作。
        - `create_user_response_handler` 函式會建立要在命令成功執行時傳送至 IoT 中樞的回應。 您可以在入口網站中檢視此回應。 如果未提供此函式，則會將一般回應傳送至服務。

    1. 使用 `execute_property_listener` 來接聽屬性更新。

    1. 使用 `send_telemetry` 開始傳送遙測。 範例程式碼會使用迴圈來呼叫三個遙測傳送函式。 每隔八秒便會呼叫一個

    1. 在您按下 **Q** 或 **q** 時停用所有接聽程式和工作，並結束迴圈。

既然您已看過程式碼，請建立名為 **IOTHUB_DEVICE_CONNECTION_STRING** 的環境變數，來儲存您先前記下的裝置連接字串。使用下列命令來執行範例：

```cmd/sh
python pnp_temp_controller_with_thermostats.py
```

範例裝置每隔幾秒就會將遙測訊息傳送到您的 IoT 中樞。

您會看到下列輸出，這表示裝置正在將遙測資料傳送至中樞，且現在已可接收命令和屬性更新。

![裝置確認訊息](media/tutorial-multiple-components-python/multiple-component.png)

完成後續步驟後，請讓範例保持執行狀態。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 總管來驗證程式碼

當裝置用戶端範例啟動之後，請使用 Azure IoT 總管工具來驗證其是否正確運作。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已了解如何將具有元件的 IoT 隨插即用裝置連線至 IoT 中樞。 若要深入了解 IoT 隨插即用裝置模型，請參閱：

> [!div class="nextstepaction"]
> [IoT 隨插即用預覽模型開發人員指南](concepts-developer-guide.md)
