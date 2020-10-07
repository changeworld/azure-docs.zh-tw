---
title: 與連線至 Azure IoT 解決方案的 IoT 隨插即用裝置互動 (Python) | Microsoft Docs
description: 使用 Python 連線至已連線 Azure IoT 解決方案的 IoT 隨插即用裝置，並與其互動。
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: be5ff3e863752dfc187bd91257425af5e8de85c4
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574956"
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

1. 此終端機視窗會作為您的**裝置**終端機。 移至複製存放庫的資料夾，並瀏覽至 /azure-iot-sdk-python/azure-iot-device/samples/pnp 資料夾。

1. 使用下列命令來執行範例控溫器裝置：

    ```cmd/sh
    python simple_thermostat.py
    ```

1. 您會看到訊息，指出裝置已傳送一些資訊並回報其本身的線上狀態。 這些訊息表示裝置已開始將遙測資料傳送至中樞，且已可接收命令和屬性更新。 請勿關閉此終端機，稍後您將需要用來確認服務範例也已正常運作。

## <a name="run-the-sample-solution"></a>執行範例解決方案

在本快速入門中，您會使用 Python 中的範例 IoT 解決方案，與您剛剛設定的範例裝置進行互動。

1. 開啟另一個終端機視窗，作為您的**服務**終端機。 

1. 瀏覽至複製的 Python SDK 存放庫所在的 */azure-iot-sdk-python/azure-iot-hub/samples* 資料夾。

1. 在 samples 資料夾中，有四個範例檔案會示範 Digital Twin Manager 類別的作業：*get_digital_twin_sample.py、update_digitial_twin_sample.py、invoke_command_sample.py 和 invoke_component_command_sample-.py*。  這些範例示範如何使用每個 API 來與 IoT 隨插即用裝置互動：

### <a name="get-digital-twin"></a>取得數位分身

在 [設定 IoT 隨插即用的環境快速入門和教學課程](set-up-environment.md)，您建立了兩個環境變數，以設定連線至 IoT 中樞和裝置的範例：

* **IOTHUB_CONNECTION_STRING**：您先前記下的 IoT 中樞連接字串。
* **IOTHUB_DEVICE_ID**：`"my-pnp-device"`。

在**服務**終端機中使用下列命令來執行此範例：

```cmd/sh
python get_digital_twin_sample.py
```

輸出會顯示裝置的數位分身，並列印其模型識別碼：

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

下列程式碼片段顯示來自 get_digital_twin_sample.py 的範例程式碼：

```python
    # Get digital twin and retrieve the modelId from it
    digital_twin = iothub_digital_twin_manager.get_digital_twin(device_id)
    if digital_twin:
        print(digital_twin)
        print("Model Id: " + digital_twin["$metadata"]["$model"])
    else:
        print("No digital_twin found")
```

### <a name="update-a-digital-twin"></a>更新數位分身

這個範例會示範如何使用「修補程式」，透過裝置的數位分身來更新屬性。 下列來自 update_digital_twin_sample.py 的程式碼片段會顯示如何建構修補程式：

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

在**服務**終端機中使用下列命令來執行此範例：

```cmd/sh
python update_digital_twin_sample.py
```

您可以確認更新已套用在顯示下列輸出的**裝置**終端機中：

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
previous values
42
```

**服務**終端機會確認修補程式已成功：

```cmd/sh
Patch has been successfully applied
```

### <a name="invoke-a-command"></a>叫用命令

若要叫用命令，請執行 invoke_command_sample.py 範例。 這個範例示範如何在簡單的控溫器裝置中叫用命令。 執行此範例之前，請先在**服務**終端機中設定 `IOTHUB_COMMAND_NAME` 和 `IOTHUB_COMMAND_PAYLOAD` 環境變數：

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

在**服務**終端機中使用下列命令來執行範例：
  
```cmd/sh
python invoke_command_sample.py
```

**服務**終端機會顯示來自裝置的確認訊息：

```cmd/sh
{"tempReport": {"avgTemp": 34.5, "endTime": "13/07/2020 16:03:38", "maxTemp": 49, "minTemp": 11, "startTime": "13/07/2020 16:02:18"}}
```

在**裝置**終端機中，您會看到裝置收到命令：

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
Sent message
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 解決方案。 若要深入了解 IoT 隨插即用裝置模組，請參閱：

> [!div class="nextstepaction"]
> [IoT 隨插即用模型開發人員指南](concepts-developer-guide-device-csharp.md)
