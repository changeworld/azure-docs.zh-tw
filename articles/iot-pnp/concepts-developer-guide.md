---
title: 開發人員指南 - IoT 隨插即用預覽 |微軟文檔
description: 物聯網隨插即用開發人員的設備建模說明
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5fda51e6d2f62b9cbef0fcac22d5bb2ea0df905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605215"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>IoT 隨插即用預覽建模開發人員指南

IoT 隨插即用預覽允許您構建向 Azure IoT 應用程式通告其功能的設備。 當客戶將其連接到啟用 IoT 隨插即用的應用程式時，IoT 隨插即用裝置不需要手動設定。 IoT 中心是支援 IoT 隨插即用的應用程式的示例。

要構建 IoT 隨插即用裝置，您需要創建設備說明。 描述使用一種稱為數位孿生定義語言 （DTDL） 的簡單定義語言完成。

## <a name="device-capability-model"></a>裝置功能模型

使用 DTDL，您可以創建_裝置功能模型_來描述設備的部件。 典型的 IoT 設備由：

- 自訂部件，這些部件使您的設備獨一無二。
- 標準部件，這是所有設備共有的部件。

這些部件稱為裝置功能模型中的_介面_。 介面定義設備實現的每個部件的詳細資訊。

下面的示例顯示了恒溫器設備的裝置功能模型：

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

功能模型具有一些必需欄位：

- `@id`：以簡單統一資源名稱的形式提供的唯一 ID。
- `@type`聲明此物件是功能模型。
- `@context`：指定用於功能模型的 DTDL 版本。
- `implements`：列出設備實現的介面。

實現部分中介面清單中的每個條目都有：

- `name`：介面的程式設計名稱。
- `schema`：功能模型實現的介面。

可以使用其他可選欄位向功能模型添加更多詳細資訊，如顯示名稱和說明。 在功能模型中聲明的介面可以被視為設備的元件。 對於公共預覽，每個架構的介面清單可能只有一個條目。

## <a name="interface"></a>介面

使用 DTDL，您可以使用介面描述設備的功能。 介面描述設備實現_的屬性、__遙測_和_命令_：

- `Properties`. 屬性是表示設備狀態的資料欄位。 使用屬性工作表示設備的持久狀態，例如冷卻液泵的開斷狀態。 屬性還可以表示基本裝置屬性，如設備的固件版本。 您可以將屬性聲明為唯讀或可寫。
- `Telemetry`. 遙測欄位表示來自感應器的測量值。 每當您的設備進行感應器測量時，它都應該發送包含感應器資料的遙測事件。
- `Commands`. 命令表示設備使用者可以在設備上執行的方法。 例如，重置命令或命令以打開或關閉風扇。

下面的示例顯示了恒溫器設備的介面：

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

介面具有一些必要欄位：

- `@id`：以簡單統一資源名稱的形式提供的唯一 ID。
- `@type`聲明此物件是介面。
- `@context`：指定用於介面的 DTDL 版本。
- `contents`：列出構成設備的屬性、遙測和命令。

在這個簡單示例中，只有一個遙測欄位。 最小欄位描述具有：

- `@type`： 指定功能的類型： `Telemetry`、`Property`或`Command`。
- `name`： 提供遙測值的名稱。
- `schema`：指定遙測的資料類型。 此值可以是基元類型，如雙精度、整數、布林或字串。 還支援複雜的物件類型、陣列和映射。

其他可選欄位（如顯示名稱和說明）允許您向介面和功能添加更多詳細資訊。

### <a name="properties"></a>屬性

預設情況下，屬性是唯讀的。 唯讀屬性表示設備向 IoT 中心報告屬性值更新。 IoT 中心無法設置唯讀屬性的值。

您還可以在介面上將屬性標記為可寫。 設備可以從 IoT 中心接收到可寫入屬性的更新，以及向中心報告屬性值更新。

不必連接設備來設置屬性值。 當設備下次連接到集線器時，將傳輸更新的值。 此行為同時適用于唯讀屬性和可寫入屬性。

不要使用屬性從設備發送遙測資料。 例如，唯讀屬性（如`temperatureSetting=80`應表示設備溫度已設置為 80，設備正在嘗試達到或保持此溫度）。

對於可寫屬性，設備應用程式返回所需的狀態碼、版本和說明，以指示它是否接收和應用屬性值。

### <a name="telemetry"></a>遙測

預設情況下，IoT 中心將所有遙測消息從設備路由到與[事件中心](https://azure.microsoft.com/documentation/services/event-hubs/)相容[的內置面向服務的終結點（**消息/事件**）。](../iot-hub/iot-hub-devguide-messages-read-builtin.md)

您可以使用[IoT 中心的自訂終結點和路由規則](../iot-hub/iot-hub-devguide-messages-d2c.md)將遙測發送到其他目標，如 Blob 存儲或其他事件中心。 路由規則使用消息屬性來選擇消息。

### <a name="commands"></a>命令

命令是同步的或非同步。 預設情況下，同步命令必須在 30 秒內執行，並且必須在命令到達時連接設備。 如果設備及時回應，或者設備未連接，則命令將失敗。

對長時間運行的操作使用非同步命令。 設備使用遙測消息發送進度資訊。 這些進度消息具有以下標頭屬性：

- `iothub-command-name`：命令名稱，例如`UpdateFirmware`。
- `iothub-command-request-id`：在伺服器端生成並在初始調用中發送到設備的請求 ID。
- `iothub-interface-id`：此命令定義的介面 ID，例如`urn:example:AssetTracker:1`。
 `iothub-interface-name`：此介面的實例名稱，例如`myAssetTracker`。
- `iothub-command-statuscode`：從設備返回的狀態碼，例如`202`。

## <a name="register-a-device"></a>註冊裝置

IoT 隨插即用可讓您輕鬆宣傳設備的功能。 使用 IoT 隨插即用，在設備連接到 IoT 中心後，必須註冊裝置功能模型。 註冊使客戶能夠使用設備的 IoT 隨插即用功能。

本指南演示如何使用 Azure IoT 設備 SDK 為 C 註冊設備。

對於設備實現的每個介面，必須創建一個介面並將其連接到其實現。

對於前面顯示的恒溫器介面，使用 C SDK 創建恒溫器介面並將其連接到其實現：

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

對設備實現的每個介面重複此代碼。

創建介面後，請註冊裝置功能模型和與 IoT 中心介面：

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

## <a name="use-a-device"></a>使用設備

IoT 隨插即用允許您使用已在 IoT 中心註冊其功能的設備。 例如，您可以直接存取設備的屬性和命令。

要使用連接到 IoT 中心功能中心的 IoT 隨插即用裝置，請使用 IoT 中心 REST API 或 IoT 語言 SDK 之一。 以下示例使用 IoT 中心 REST API。 API 的當前版本為`2019-07-01-preview`。 追加`?api-version=2019-07-01-preview`到 REST PI 呼叫。

要獲取恒溫器中`fwVersion``DeviceInformation`介面中的裝置屬性（如固件版本 （ ） 的值，請使用數位孿生 REST API。

如果調用`t-123`恒溫器設備，則通過 REST API GET 調用獲取設備實現的所有介面上的所有屬性：

```REST
GET /digitalTwins/t-123/interfaces
```

更一般地說，所有介面上的所有屬性都使用此 REST API`{device-id}`範本進行訪問，其中是設備的識別碼：

```REST
GET /digitalTwins/{device-id}/interfaces
```

如果知道介面的名稱（如`deviceInformation`），並且想要獲取該特定介面的屬性，請按名稱將請求範圍限定為特定介面：

```REST
GET /digitalTwins/t-123/interfaces/deviceInformation
```

更一般地說，可以通過此 REST API 範本訪問特定介面的屬性，`device-id`該範本是設備的識別碼，是`{interface-name}`介面的名稱：

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

您可以直接調用 IoT 隨插即用裝置命令。 如果`Thermostat``t-123`設備中的介面具有`restart`命令，則可以使用 REST API POST 調用調用它：

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

更一般地說，可以通過以下 REST API 範本調用命令：

- `device-id`：設備的識別碼。
- `interface-name`：裝置功能模型中實現部分的介面的名稱。
- `command-name`：命令的名稱。

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解了設備建模，下面是一些其他資源：

- [數位雙定義語言 （DTDL）](https://aka.ms/DTDL)
- [C 裝置 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [物聯網 REST API](https://docs.microsoft.com/rest/api/iothub/device)
