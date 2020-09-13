---
title: 在解決方案中使用 IoT 隨插即用模型 |Microsoft Docs
description: 作為解決方案產生器，您可以瞭解如何在 IoT 解決方案中使用 IoT 隨插即用模型。
author: arunmannengal
ms.author: arunmann
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9752589c8863cc911369225d268035d9f61c0273
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032022"
---
# <a name="use-iot-plug-and-play-models-in-an-iot-solution"></a>在 IoT 解決方案中使用 IoT 隨插即用模型

本文說明如何在 IoT 解決方案中，識別 IoT 隨插即用裝置的模型識別碼，然後取得其模型定義。

IoT 解決方案有兩個廣泛的類別：

- *專門建立的解決方案*適用于將連接至解決方案的 IoT 隨插即用裝置的一組已知模型。 當您開發方案時，會使用這些模型。

- *模型驅動*解決方案可以與任何 IoT 隨插即用裝置的模型搭配使用。 建立模型驅動解決方案比較複雜，但優點是您的解決方案可與未來可能新增的任何裝置搭配使用。 模型驅動的 IoT 解決方案會抓取模型，並用它來判斷裝置所執行的遙測、屬性和命令。

若要使用 IoT 隨插即用模型，IoT 解決方案：

1. 識別連接至方案的 IoT 隨插即用裝置所執行之模型的模型識別碼。

1. 使用模型識別碼，從模型儲存機制或自訂存放區取出連接裝置的模型定義。

## <a name="identify-model-id"></a>識別模型識別碼

當 IoT 隨插即用裝置連線到 IoT 中樞時，它會向 IoT 中樞註冊其所建立之模型的模型識別碼。

IoT 中樞會以裝置型號識別碼通知解決方案，作為裝置連接流程的一部分。

解決方案可以使用下列三種方法的其中一種，取得 IoT 隨插即用裝置的模型識別碼：

### <a name="get-device-twin-api"></a>取得裝置對應項 API

解決方案可以使用 [取得裝置](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin?view=azure-java-stable) 對應項 API 來取出 IoT 隨插即用裝置的模型識別碼。

在下列裝置對應項回應片段中， `modelId` 包含 IoT 隨插即用裝置的模型識別碼：

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

### <a name="get-digital-twin-api"></a>取得數位對應項 API

解決方案可以使用「 [取得數位](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin/getdigitaltwin) 對應項 API」來取出 IoT 隨插即用裝置所執行之模型的模型識別碼。

在下列數位對應項回應程式碼片段中， `$metadata.$model` 包含 IoT 隨插即用裝置的模型識別碼：

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

### <a name="digital-twin-change-event-notification"></a>數位對應項變更事件通知

裝置連接會產生數位對應項 [變更事件](concepts-digital-twin.md#digital-twin-change-events) 通知。 解決方案必須訂閱此事件通知。 若要瞭解如何啟用數位對應項事件的路由，請參閱 [使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至不同的端點](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。

解決方案可以使用下列程式碼片段中所示的事件，來瞭解正在連線的 IoT 隨插即用裝置，並取得其模型識別碼：

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

## <a name="retrieve-a-model-definition"></a>取出模型定義

解決方案會使用上面所識別的模型識別碼來取出對應的模型定義。

解決方案可以使用下列其中一個選項來取得模型定義：

### <a name="model-repository"></a>模型存放庫

解決方案可以使用 [模型存放庫](concepts-model-repository.md) 來取出模型。 裝置產生器或解決方案產生器必須事先將其模型上傳至存放庫，讓解決方案可以取出它們。

在您識別新裝置連線的模型識別碼之後，請遵循下列步驟：

1. 使用模型存放庫中的模型識別碼抓取模型定義。 如需詳細資訊，請參閱 [取得模型](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync)。

1. 您可以使用連線裝置的模型定義來列舉裝置的功能。

1. 您可以使用裝置的列舉功能，讓使用者 [與裝置互動](quickstart-service-node.md)。

### <a name="custom-store"></a>自訂存放區

解決方案可以將這些模型定義儲存在本機檔案系統、公用檔案存放區中，或使用自訂的執行。

在您識別新裝置連線的模型識別碼之後，請遵循下列步驟：

1. 使用自訂存放區中的模型識別碼抓取模型定義。

1. 您可以使用連線裝置的模型定義來列舉裝置的功能。 

1. 您可以使用裝置的列舉功能，讓使用者 [與裝置互動](quickstart-service-node.md)。  

## <a name="next-steps"></a>接下來的步驟

現在您已瞭解如何在 IoT 解決方案中整合 IoT 隨插即用模型，接下來的一些建議步驟如下：

- [從您的解決方案與裝置互動](quickstart-service-node.md)
- [IoT 數位對應項 REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Azure IoT 總管](howto-use-iot-explorer.md)
