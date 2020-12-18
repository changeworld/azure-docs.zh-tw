---
title: IoT 隨插即用橋接器 |Microsoft Docs
description: 瞭解 IoT 隨插即用橋接器，以及如何使用它來將連接至 Windows 或 Linux 閘道的現有裝置連線為 IoT 隨插即用裝置。
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 34af380d057ad47811e394da1e7a29198e102920
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672775"
---
# <a name="iot-plug-and-play-bridge"></a>IoT 隨插即用橋接器

IoT 隨插即用橋接器是開放原始碼應用程式，可將連接至 Windows 或 Linux 閘道的現有裝置連線為 IoT 隨插即用裝置。 在 Windows 或 Linux 電腦上安裝並設定應用程式之後，您可以使用它來將連接的裝置連線到 IoT 中樞。 您可以使用橋接器將 IoT 隨插即用介面對應至附加裝置正在傳送的遙測資料、使用裝置屬性，以及叫用命令。

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="左側有幾個現有的感應器附加 (有線和無線) 到包含 IoT 隨插即用橋接器的 Windows 或 Linux 電腦。IoT 隨插即用橋接器接著會連接到右側的 IoT 中樞":::

IoT 隨插即用橋接器可以在任何 IoT 裝置、產業電腦、伺服器或執行 Windows 10 或 Linux 的閘道上部署為獨立可執行檔。 您也可以將它編譯成您的應用程式程式碼。 簡單的設定 JSON 檔案會告訴 IoT 隨插即用橋接器，哪些連結的裝置/週邊設備應公開至 Azure。

## <a name="supported-protocols-and-sensors"></a>支援的通訊協定和感應器

IoT 隨插即用橋接器預設支援下列類型的週邊設備，以及介面卡檔的連結：

|周邊設備|Windows|Linux|
|---------|---------|---------|
|[藍牙感應器介面卡](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/bluetooth_sensor_adapter.md) 連線偵測到 Bluetooth 低能量 (BLE) 已啟用的感應器。       |是|否|
|[攝影機介面卡](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/camera_adapter.md) 會連接 Windows 10 裝置上的相機。               |是|否|
|[Modbus 介面卡](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/modbus_adapters.md) 會連接 Modbus 裝置上的感應器。              |是|是|
|[MQTT 介面卡](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/mqtt_adapter.md) 會連接使用 MQTT 訊息代理程式的裝置。                  |是|是|
|[SerialPnP 介面卡](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/serialpnp/Readme.md) 會連接透過序列連接進行通訊的裝置。               |是|是|
|[WINDOWS USB 週邊設備](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/coredevicehealth_adapter.md) 使用介面卡支援的裝置介面類別別清單來連接具有特定硬體識別碼的裝置。  |是|不適用|

若要瞭解如何擴充 IoT 隨插即用橋接器以支援其他裝置通訊協定，請參閱 [建立、部署及擴充 IoT 隨插即用橋接器](howto-build-deploy-extend-pnp-bridge.md)。

## <a name="iot-plug-and-play-bridge-architecture"></a>IoT 隨插即用 bridge 架構

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="左側有幾個方塊，表示連接到包含 IoT 隨插即用橋接器之 Windows 或 Linux 電腦的各種週邊設備。從頂端，標示設定點的方塊指向橋接器。橋接器接著會連接到圖表右邊的 IoT 中樞。":::

### <a name="iot-plug-and-play-bridge-adapters"></a>IoT 隨插即用橋接器介面卡

IoT 隨插即用橋接器支援各種裝置類型的一組 IoT 隨插即用橋接器介面卡。 *介面卡資訊清單* 會以靜態方式定義橋接器的介面卡。

橋接器介面卡管理員會使用資訊清單來識別和呼叫介面卡功能。 介面卡管理員只會呼叫設定檔中所列介面元件所需的橋接器介面卡上的 create 函式。 建立每個 IoT 隨插即用元件的介面卡實例。

橋接器介面卡會建立並取得數位對應項介面控制碼。 介面卡會使用此控制碼將裝置功能系結至數位對應項。

使用設定檔中的資訊，橋接器介面卡會使用下列技術，透過橋接器啟用完整裝置對數位對應項的通訊：

- 直接建立通道。
- 建立裝置監看員以等候通道變成可用。

### <a name="configuration-file"></a>組態檔

IoT 隨插即用橋接器使用以 JSON 為基礎的設定檔，其指定：

- 如何連線到 IoT 中樞或 IoT Central 的應用程式：選項包含連接字串、驗證參數或裝置布建服務 (DPS) 。
- 橋接器所使用 IoT 隨插即用功能模型的位置。 此模型會定義 IoT 隨插即用裝置的功能，且為靜態和不可變。
- IoT 隨插即用介面元件的清單，以及每個元件的下列資訊：
- 介面識別碼和元件名稱。
- 與元件互動所需的橋接器介面卡。
- 橋接器介面卡建立與裝置通訊所需的裝置資訊。 例如硬體識別碼，或介面卡、介面或通訊協定的特定資訊。
- 如果介面卡支援具有類似裝置的多個通訊類型，則選用的橋接器介面卡子類型或介面設定。 此範例顯示如何設定藍牙感應器元件：

    ```json
    {
      "_comment": "Component BLE sensor",
      "pnp_bridge_component_name": "blesensor1",
      "pnp_bridge_adapter_id": "bluetooth-sensor-pnp-adapter",
      "pnp_bridge_adapter_config": {
        "bluetooth_address": "267541100483311",
        "blesensor_identity" : "Blesensor1"
      }
    }
    ```

- 全域橋接器介面卡參數的選擇性清單。 例如，藍牙感應器橋接器介面卡有支援設定的字典。 需要藍牙感應器介面卡的介面元件可以選擇其中一項設定，如下所示 `blesensor_identity` ：

    ```json
    {
      "pnp_bridge_adapter_global_configs": {
        "bluetooth-sensor-pnp-adapter": {
          "Blesensor1" : {
            "company_id": "0x499",
            "endianness": "big",
            "telemetry_descriptor": [
              {
                "telemetry_name": "humidity",
                "data_parse_type": "uint8",
                "data_offset": 1,
                "conversion_bias": 0,
                "conversion_coefficient": 0.5
              },
              {
                "telemetry_name": "temperature",
                "data_parse_type": "int8",
                "data_offset": 2,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "pressure",
                "data_parse_type": "int16",
                "data_offset": 4,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "acceleration_x",
                "data_parse_type": "int16",
                "data_offset": 6,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_y",
                "data_parse_type": "int16",
                "data_offset": 8,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_z",
                "data_parse_type": "int16",
                "data_offset": 10,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              }
            ]
          }
        }
      }
    }
    ```

## <a name="download-iot-plug-and-play-bridge"></a>下載 IoT 隨插即用橋接器

您可以在 [IoT 隨插即用橋接器版](https://github.com/Azure/iot-plug-and-play-bridge/releases) 中，使用支援的介面卡下載橋接器的預先建立版本，並展開最新版本的資產清單。 為您的作業系統下載應用程式的最新版本。

您也可以 [在 GitHub 上](https://github.com/Azure/iot-plug-and-play-bridge)下載並查看 IoT 隨插即用橋接器的原始程式碼。

## <a name="next-steps"></a>後續步驟

現在您已大致瞭解 IoT 隨插即用橋接器的架構，接下來的步驟是深入瞭解：

- [如何使用 IoT 隨插即用橋接器](./howto-use-iot-pnp-bridge.md)
- [建立、部署及擴充 IoT 隨插即用橋接器](howto-build-deploy-extend-pnp-bridge.md)
- [在 GitHub 上 IoT 隨插即用 bridge](https://github.com/Azure/iot-plug-and-play-bridge)
