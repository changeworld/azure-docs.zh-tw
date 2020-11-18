---
title: 快速入門 - 與連線至 Azure IoT 解決方案的 IoT 隨插即用裝置互動 (Python) | Microsoft Docs
description: 快速入門 - 使用 Python 連線至已連線 Azure IoT 解決方案的 IoT 隨插即用裝置，並與其互動。
author: elhorton
ms.author: elhorton
ms.date: 10/05/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 681568c3aee88483c7f9c813529f0eb500a59e93
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421493"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-python"></a>快速入門：與已連線至解決方案的 IoT 隨插即用裝置互動 (Python)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT 隨插即用讓您無須具備基礎裝置實作的知識，即可與裝置的模型互動，而使 IoT 得以簡化。 本快速入門說明如何使用 Python 連線至您的解決方案，並控制已連線至解決方案的 IoT 隨插即用裝置。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

若要完成本快速入門，您的開發機器上必須要有 Python 3.7。 您可以從 [python.org](https://www.python.org/) 下載多個平台的最新建議版本。您可以使用下列命令來檢查 Python 版本：  

```cmd/sh
python --version
```

**azure-iot-device** 套件會發佈為 PIP。

在您的本機 Python 環境中安裝套件，如下所示：

```cmd/sh
pip install azure-iot-device
```

執行下列命令安裝 **azure-iot-hub** 套件：

```cmd/sh
pip install azure-iot-hub
```

## <a name="run-the-sample-device"></a>執行範例裝置

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

若要深入了解範例設定，請參閱[範例讀我檔案](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md)。

在本快速入門中，您可以使用以 Python 撰寫的範例控溫器裝置，作為 IoT 隨插即用裝置。 若要執行範例裝置：

1. 在您選擇的資料夾中開啟終端機視窗。 執行下列命令，將 [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) GitHub 存放庫複製到下列位置：

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. 此終端機視窗會作為您的 **裝置** 終端機。 移至複製存放庫的資料夾，並瀏覽至 /azure-iot-sdk-python/azure-iot-device/samples/pnp 資料夾。

1. 使用下列命令來執行範例控溫器裝置：

    ```cmd/sh
    python simple_thermostat.py
    ```

1. 您會看到訊息，指出裝置已傳送一些資訊並回報其本身的線上狀態。 這些訊息表示裝置已開始將遙測資料傳送至中樞，且已可接收命令和屬性更新。 請勿關閉此終端機，稍後您將需要用來確認服務範例也已正常運作。

## <a name="run-the-sample-solution"></a>執行範例解決方案

在本快速入門中，您會使用 Python 中的範例 IoT 解決方案，與您剛剛設定的範例裝置進行互動。

1. 開啟另一個終端機視窗，作為您的 **服務** 終端機。

1. 瀏覽至複製的 Python SDK 存放庫所在的 */azure-iot-sdk-python/azure-iot-hub/samples* 資料夾。

1. 開啟 registry_manager_pnp_sample.py 檔案並檢閱程式碼。 這個範例示範如何使用 **IoTHubRegistryManager** 類別來與 IoT 隨插即用裝置互動。

> [!NOTE]
> 這些服務範例會使用 **IoT 中樞服務用戶端** 中的 **IoTHubRegistryManager** 類別。 若要深入了解 API (包括數位對應項 API)，請參閱[服務開發人員指南](concepts-developer-guide-service.md)。

### <a name="get-the-device-twin"></a>取得裝置對應項

在 [設定 IoT 隨插即用的環境快速入門和教學課程](set-up-environment.md)，您建立了兩個環境變數，以設定連線至 IoT 中樞和裝置的範例：

* **IOTHUB_CONNECTION_STRING**：您先前記下的 IoT 中樞連接字串。
* **IOTHUB_DEVICE_ID**：`"my-pnp-device"`。

在 **服務** 終端機中使用下列命令來執行此範例：

```cmd/sh
set IOTHUB_METHOD_NAME="getMaxMinReport"
set IOTHUB_METHOD_PAYLOAD="hello world"
python registry_manager_pnp_sample.py
```

> [!NOTE]
> 如果您是在 Linux 上執行此範例，請使用 `export` 來取代 `set`。

輸出會顯示裝置對應項，並列印其模型識別碼：

```cmd/sh
The Model ID for this device is:
dtmi:com:example:Thermostat;1
```

下列程式碼片段顯示來自 registry_manager_pnp_sample.py 的範例程式碼：

```python
    # Create IoTHubRegistryManager
    iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

    # Get device twin
    twin = iothub_registry_manager.get_twin(device_id)
    print("The device twin is: ")
    print("")
    print(twin)
    print("")

    # Print the device's model ID
    additional_props = twin.additional_properties
    if "modelId" in additional_props:
        print("The Model ID for this device is:")
        print(additional_props["modelId"])
        print("")
```

### <a name="update-a-device-twin"></a>更新裝置對應項

此範例說明如何更新裝置中的 `targetTemperature` 可寫入屬性：

```python
    # Update twin
    twin_patch = Twin()
    twin_patch.properties = TwinProperties(
        desired={"targetTemperature": 42}
    )  # this is relevant for the thermostat device sample
    updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
    print("The twin patch has been successfully applied")
    print("")
```

您可以確認更新已套用在顯示下列輸出的 **裝置** 終端機中：

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
```

**服務** 終端機會確認修補程式已成功：

```cmd/sh
The twin patch has been successfully applied
```

### <a name="invoke-a-command"></a>叫用命令

範例會接著叫用命令：

**服務** 終端機會顯示來自裝置的確認訊息：

```cmd/sh
The device method has been successfully invoked
```

在 **裝置** 終端機中，您會看到裝置收到命令：

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 解決方案。 若要深入了解 IoT 隨插即用裝置模組，請參閱：

> [!div class="nextstepaction"]
> [IoT 隨插即用模型開發人員指南](concepts-developer-guide-device-csharp.md)
