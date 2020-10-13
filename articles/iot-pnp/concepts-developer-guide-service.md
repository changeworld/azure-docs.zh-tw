---
title: 服務開發人員指南-IoT 隨插即用 |Microsoft Docs
description: 服務開發人員的 IoT 隨插即用描述
author: dominicbetts
ms.author: dobett
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: f082e4d4c6c71e460842f80a5aa17130b6a41279
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91614218"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>IoT 隨插即用 service 開發人員指南

IoT 隨插即用可讓您建立智慧型裝置，以將其功能公告至 Azure IoT 應用程式。 當客戶將裝置連線到已啟用 IoT 隨插即用的應用程式時，IoT 隨插即用裝置不需要手動設定。

IoT 隨插即用可讓您使用已向 IoT 中樞宣告其模型識別碼的裝置。 例如，您可以直接存取裝置的屬性和命令。

若要使用連線到 IoT 中樞的 IoT 隨插即用裝置，請使用其中一個 IoT 服務 Sdk 或 IoT 中樞 REST API：

## <a name="service-sdks"></a>服務 SDK

使用您解決方案中的 Azure IoT 服務 Sdk 來與裝置和模組互動。 例如，您可以使用服務 Sdk 來讀取和更新對應項屬性，並叫用命令。 支援的語言包括 c #、JAVA、Node.js 和 Python。

服務 Sdk 可讓您從解決方案存取裝置資訊，例如桌面或 web 應用程式。 服務 Sdk 包含兩個命名空間和物件模型，您可以使用這些命名空間和物件模型來取得模型識別碼：

- Iot 中樞服務用戶端。 這項服務會將模型識別碼公開為裝置對應項屬性。

- 數位 Twins 服務用戶端。 新的數位 Twins API 會在高階結構上運作，例如定義了數位 Twins 定義語言模型的元件、屬性和命令。 數位對應項 Api 可讓解決方案產生器更輕鬆地建立 IoT 隨插即用的解決方案。

| 平台 | IoT 中樞服務用戶端 | 數位 Twins 服務用戶端 |
| -------- | ---------------------- | ---------------------------- |
| .NET     | [文件集](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.shared.twin.modelid?view=azure-dotnet#Microsoft_Azure_Devices_Shared_Twin_ModelId&preserve-view=true) <br/> [範例](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples)| [範例](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples) |
| Java     | [文件集](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?view=azure-java-stable&preserve-view=true) <br/> [範例](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample)| [範例](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples) |
| Node.js  | [文件集](https://docs.microsoft.com/javascript/api/azure-iothub/twin?view=azure-node-latest&preserve-view=true) <br/> [範例](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)| [文件集](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/?view=azure-node-latest&preserve-view=true) <br/> [範例](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js) |
| Python   | [文件集](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?view=azure-python&preserve-view=true) <br/> [範例](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)| [文件集](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubdigitaltwinmanager?view=azure-python&preserve-view=true) <br/> [範例](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py) |

## <a name="rest-api"></a>REST API

下列範例會使用 IoT 中樞 REST API 與連線的 IoT 隨插即用裝置互動。 目前的 API 版本為 `2020-09-30` 。 附加 `?api-version=2020-09-30` 至 REST PI 呼叫。

> [!NOTE]
> API 目前不支援模組 twins `digitalTwins` 。

如果呼叫您的控溫器裝置 `t-123` ，您可以使用 REST API get 呼叫，取得裝置所執行之所有介面上的所有屬性：

```REST
GET /digitalTwins/t-123
```

此呼叫會包含 `$metadata.$model` 具有裝置所宣告之模型識別碼的 Json 屬性。

所有介面上的所有屬性都是使用 `GET /DigitalTwin/{device-id}` REST API 範本來存取，其中 `{device-id}` 是裝置的識別碼：

```REST
GET /digitalTwins/{device-id}
```

您可以直接呼叫 IoT 隨插即用的裝置命令。 如果 `Thermostat` 裝置中的元件 `t-123` 有 `restart` 命令，您可以使用 REST API 的 POST 呼叫來呼叫它：

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

更常見的情況是，您可以透過此 REST API 範本來呼叫命令：

- `device-id`：裝置的識別碼。
- `component-name`：裝置功能模型中的 [implements] 區段的介面名稱。
- `command-name`：命令的名稱。

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>後續步驟

現在您已瞭解裝置模型化，以下是一些額外的資源：

- [數位分身定義語言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 裝置 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [模型元件](./concepts-components.md)
- [安裝和使用 DTDL authoring tools](howto-use-dtdl-authoring-tools.md)
