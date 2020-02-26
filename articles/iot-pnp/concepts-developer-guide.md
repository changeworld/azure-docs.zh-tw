---
title: 開發人員指南-IoT 隨插即用預覽 |Microsoft Docs
description: IoT 隨插即用開發人員的裝置模型描述
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5fda51e6d2f62b9cbef0fcac22d5bb2ea0df905b
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605215"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>IoT 隨插即用預覽模型開發人員指南

IoT 隨插即用預覽版可讓您建立裝置，以向 Azure IoT 應用程式公告其功能。 當客戶將 IoT 隨插即用裝置連接到已啟用 IoT 隨插即用的應用程式時，不需要手動設定。 IoT Central 是啟用 IoT 隨插即用應用程式的範例。

若要建立 IoT 隨插即用裝置，您必須建立裝置描述。 此描述是以簡單的定義語言（稱為數位 Twins 定義語言（DTDL））來完成。

## <a name="device-capability-model"></a>裝置功能模型

透過 DTDL，您可以建立_裝置功能模型_來描述裝置的各部分。 典型的 IoT 裝置是由下列各項所組成：

- 自訂群組件，這是讓裝置成為唯一的專案。
- 標準元件，這是所有裝置通用的專案。

這些元件在裝置功能模型中稱為_介面_。 介面會定義裝置所執行之每個元件的詳細資料。

下列範例顯示控溫器裝置的裝置功能模型：

```json
{
  "@id": "urn:example:Thermostat_T_1000:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "name": "thermostat",
      "schema": "urn:example:Thermostat:1"
    },
    {
      "name": "urn_azureiot_deviceManagement_DeviceInformation",
      "schema": "urn:azureiot:deviceManagement:DeviceInformation:1"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

功能模型有一些必要的欄位：

- `@id`：唯一的識別碼，格式為簡單的統一資源名稱。
- `@type`：宣告此物件是功能模型。
- `@context`：指定功能模型所使用的 DTDL 版本。
- `implements`：列出您的裝置所執行的介面。

在 [執行] 區段的介面清單中，每個專案都有：

- `name`：介面的程式設計名稱。
- `schema`：功能模型所執行的介面。

還有其他選擇性欄位，您可以用來新增更多詳細資料至功能模型，例如 [顯示名稱] 和 [描述]。 可以將功能模型中宣告的介面視為裝置的元件。 對於公開預覽，介面清單在每個架構中可能只會有一個專案。

## <a name="interface"></a>介面

透過 DTDL，您可以使用介面來描述裝置的功能。 介面會描述您的裝置中所執行的_屬性_、_遙測_和_命令_：

- `Properties`第 1 課：建立 Windows Azure 儲存體物件{2}。 屬性是代表裝置狀態的資料欄位。 使用屬性來表示裝置的持久狀態，例如 coolant 泵的關閉狀態。 屬性也可以代表基本裝置屬性，例如裝置的「固件」版本。 您可以將屬性宣告為唯讀或可寫入。
- `Telemetry`第 1 課：建立 Windows Azure 儲存體物件{2}。 遙測欄位代表來自感應器的測量。 每當您的裝置接受感應器測量時，它應該會傳送包含感應器資料的遙測事件。
- `Commands`第 1 課：建立 Windows Azure 儲存體物件{2}。 命令代表裝置的使用者可以在裝置上執行的方法。 例如，可切換或關閉風扇的 reset 命令或命令。

下列範例顯示控溫器裝置的介面：

```json
{
  "@id": "urn:example:Thermostat:1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Telemetry",
      "name": "temperature",
      "schema": "double"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

介面有一些必要的欄位：

- `@id`：唯一的識別碼，格式為簡單的統一資源名稱。
- `@type`：宣告此物件為介面。
- `@context`：指定用於介面的 DTDL 版本。
- `contents`：列出組成您裝置的屬性、遙測和命令。

在這個簡單的範例中，只有一個遙測欄位。 最小欄位描述具有：

- `@type`：指定功能類型： `Telemetry`、`Property`或 `Command`。
- `name`：提供遙測值的名稱。
- `schema`：指定遙測的資料類型。 這個值可以是基本類型，例如雙精度浮點數、整數、布林值或字串。 也支援複雜物件類型、陣列和對應。

其他選擇性欄位（例如 [顯示名稱] 和 [描述]）可讓您將更多詳細資料新增至介面和功能。

### <a name="properties"></a>屬性

根據預設，屬性為唯讀。 唯讀屬性表示裝置會向您的 IoT 中樞報告屬性值更新。 您的 IoT 中樞無法設定唯讀屬性的值。

您也可以在介面上將屬性標示為可寫入。 裝置可以從您的 IoT 中樞接收可寫入屬性的更新，以及將屬性值更新報告至您的中樞。

裝置不需要連線來設定屬性值。 當裝置下一次連線至中樞時，會傳送更新的值。 這種行為適用于唯讀和可寫入的屬性。

請勿使用屬性從您的裝置傳送遙測。 例如，`temperatureSetting=80` 的 readonly 屬性應該表示裝置溫度已設定為80，且裝置正嘗試進入或維持在此溫度。

針對可寫入的屬性，裝置應用程式會傳回所需狀態的狀態碼、版本和描述，以指出它是否已接收並套用屬性值。

### <a name="telemetry"></a>遙測

根據預設，IoT 中樞會將來自裝置的所有遙測訊息，路由至其與[事件中樞](https://azure.microsoft.com/documentation/services/event-hubs/)相容的[內建服務面向端點（**訊息/事件**）](../iot-hub/iot-hub-devguide-messages-read-builtin.md) 。

您可以使用[IoT 中樞的自訂端點和路由規則](../iot-hub/iot-hub-devguide-messages-d2c.md)，將遙測資料傳送至其他目的地，例如 blob 儲存體或其他事件中樞。 路由規則會使用訊息屬性來選取訊息。

### <a name="commands"></a>命令

命令可能是同步或非同步。 同步命令預設必須在30秒內執行，而且當命令抵達時，裝置必須連接。 如果裝置在時間內回應，或裝置未連線，則命令會失敗。

使用非同步命令進行長時間執行的作業。 裝置會使用遙測訊息傳送進度資訊。 這些進度訊息具有下列標頭屬性：

- `iothub-command-name`：命令名稱，例如 `UpdateFirmware`。
- `iothub-command-request-id`：在伺服器端產生的要求識別碼，並會在初始呼叫中傳送至裝置。
- `iothub-interface-id`：此命令定義所在介面的識別碼，例如 `urn:example:AssetTracker:1`。
 `iothub-interface-name`：此介面的實例名稱，例如 `myAssetTracker`。
- `iothub-command-statuscode`：從裝置傳回的狀態碼，例如 `202`。

## <a name="register-a-device"></a>註冊裝置

IoT 隨插即用可讓您輕鬆地公告裝置的功能。 使用 IoT 隨插即用，在您的裝置連線到 IoT 中樞之後，您必須註冊裝置功能模型。 註冊可讓客戶使用您裝置的 IoT 隨插即用功能。

本指南說明如何使用適用于 C 的 Azure IoT 裝置 SDK 來註冊裝置。

針對您的裝置所執行的每個介面，您必須建立介面，並將它連接到其實作為。

針對先前所示的控溫器介面，您可以使用 C SDK 來建立控溫器介面並將其連接到其實作為其執行：

```c
DIGITALTWIN_INTERFACE_HANDLE thermostatInterfaceHandle;

DIGITALTWIN_CLIENT_RESULT result = DigitalTwin_InterfaceClient_Create(
    "thermostat",
    "urn:example:Thermostat:1",
    null, null,
    &thermostatInterfaceHandle);

result = DigitalTwin_Interface_SetCommandsCallbacks(
    thermostatInterfaceHandle,
    commandsCallbackTable);

result = DigitalTwin_Interface_SetPropertiesUpdatedCallbacks(
    thermostatInterfaceHandle,
    propertiesCallbackTable);

```

針對您的裝置所執行的每個介面重複此程式碼。

建立介面之後，請向 IoT 中樞註冊您的裝置功能模型和介面：

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaces, 2,
    null, null);
```

## <a name="use-a-device"></a>使用裝置

IoT 隨插即用可讓您使用已向 IoT 中樞註冊其功能的裝置。 例如，您可以直接存取裝置的屬性和命令。

若要使用已連線到您 IoT 中樞的 IoT 隨插即用裝置，請使用 IoT 中樞 REST API 或其中一個 IoT 語言 Sdk。 下列範例會使用 IoT 中樞 REST API。 目前的 API 版本為 `2019-07-01-preview`。 將 `?api-version=2019-07-01-preview` 附加至 REST PI 呼叫。

若要取得裝置屬性的值（例如控溫器中 `DeviceInformation` 介面的固件版本（`fwVersion`），您可以使用數位 twins REST API。

如果 `t-123`呼叫您的控溫器裝置，您可以使用 REST API GET 呼叫，取得裝置所執行之所有介面上的所有屬性：

```REST
GET /digitalTwins/t-123/interfaces
```

更常見的情況是，所有介面上的所有屬性都是使用此 REST API 範本來存取，其中 `{device-id}` 是裝置的識別碼：

```REST
GET /digitalTwins/{device-id}/interfaces
```

如果您知道介面的名稱，例如 `deviceInformation`，而且想要取得該特定介面的屬性，請依名稱將要求的範圍限定于特定的介面：

```REST
GET /digitalTwins/t-123/interfaces/deviceInformation
```

更常見的情況是，可以透過此 REST API 範本來存取特定介面的屬性，其中 `device-id` 是裝置的識別碼，而 `{interface-name}` 是介面的名稱：

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

您可以直接呼叫 IoT 隨插即用裝置命令。 如果 `t-123` 裝置中的 `Thermostat` 介面具有 `restart` 命令，您可以使用 REST API POST 呼叫來呼叫它：

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

一般來說，您可以透過此 REST API 範本呼叫命令：

- `device-id`：裝置的識別碼。
- `interface-name`：裝置功能模型中，來自 [執行] 區段的介面名稱。
- `command-name`：命令的名稱。

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>後續步驟

既然您已瞭解裝置模型，以下是一些額外的資源：

- [數位對應項定義語言（DTDL）](https://aka.ms/DTDL)
- [C 裝置 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
