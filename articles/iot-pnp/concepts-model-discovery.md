---
title: 執行 IoT 隨插即用預覽模型探索 |Microsoft Docs
description: 作為解決方案產生器，深入瞭解如何在您的解決方案中執行 IoT 隨插即用模型探索。
author: prashmo
ms.author: prashmo
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 364b85a8ead09858b97d5d7e6ca8c130b9960b2c
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337376"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>在 IoT 解決方案中執行 IoT 隨插即用預覽模型探索

本文說明如何作為解決方案產生器，您可以在 IoT 解決方案中執行 IoT 隨插即用預覽模型探索。 模型探索說明如何：

- IoT 隨插即用裝置會註冊其模型識別碼。
- IoT 解決方案會抓取裝置所執行的介面。

IoT 解決方案有兩種廣泛的類別：

- *專門建立的 iot 解決方案*適用于一組已知的 iot 隨插即用裝置型號。

- *模型驅動的 iot 解決方案*可與任何 IoT 隨插即用裝置搭配使用。 建立模型驅動的解決方案比較複雜，但好處是您的解決方案會與未來新增的任何裝置搭配運作。

    若要建立模型驅動的 IoT 解決方案，您必須針對 IoT 隨插即用介面基本類型建立邏輯：遙測、屬性和命令。 您的解決方案邏輯會藉由結合多個遙測、屬性和命令功能來代表裝置。

本文說明如何在這兩種類型的方案中執行模型探索。

## <a name="model-discovery"></a>模型探索

若要探索裝置所執行的模型，解決方案可以使用以事件為基礎的探索或對應項型探索來取得模型識別碼：

### <a name="event-based-discovery"></a>以事件為基礎的探索

當 IoT 隨插即用裝置連接到 IoT 中樞時，它會註冊其所執行的模型。 此註冊會產生數位對應項[變更事件](concepts-digital-twin.md#digital-twin-change-events)通知。 若要瞭解如何啟用數位對應項事件的路由，請參閱[使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至不同的端點](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。

解決方案可以使用下列程式碼片段中顯示的事件，以瞭解連線並取得其模型識別碼的 IoT 隨插即用裝置：

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

新增或更新裝置型號識別碼時，就會觸發此事件。

### <a name="twin-based-discovery"></a>以對應項為基礎的探索

如果解決方案想要知道特定裝置的功能，它可以使用「[取得數位](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin/getdigitaltwin)對應項 API」來抓取資訊。

在下列數位對應項程式碼片段中， `$metadata.$model` 包含 IoT 隨插即用裝置的型號識別碼：

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

解決方案也可以使用**Get**對應項，從裝置對應項中取出模型識別碼，如下列程式碼片段所示：

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

## <a name="model-resolution"></a>模型解析

解決方案會使用模型解析來存取從模型識別碼組成模型的介面。 

- 解決方案可以選擇將這些介面當做檔案儲存在本機資料夾中。 
- 解決方案可以使用[模型存放庫](concepts-model-repository.md)。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何使用 IoT 解決方案來探索模型，請深入瞭解[Azure iot 平臺](overview-iot-plug-and-play.md)以將其他功能用於您的解決方案。

- [從您的解決方案與裝置互動](quickstart-service-node.md)
- [IoT 數位對應項 REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Azure IoT explorer](howto-use-iot-explorer.md)
