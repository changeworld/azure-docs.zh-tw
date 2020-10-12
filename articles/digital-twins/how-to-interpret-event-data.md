---
title: 解譯事件資料
titleSuffix: Azure Digital Twins
description: 瞭解如何解讀不同的事件種類及其不同的通知訊息。
author: baanders
ms.author: baanders
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 10b74f7b795df2cf8c19d044fce44da3f798af7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88587628"
---
# <a name="understand-event-data"></a>瞭解事件資料

Azure 數位 Twins 中的不同事件會產生 **通知**，這可讓解決方案後端能夠在發生不同動作時察覺。 然後，這些會 [路由](concepts-route-events.md) 至 Azure 數位 Twins 內部和外部的不同位置，以使用這項資訊來採取行動。

有數種類型的通知可以產生，而通知訊息可能會根據產生的事件種類而有所不同。 本文提供不同類型訊息的詳細資料，以及它們的外觀。

此圖表顯示不同的通知類型：

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

一般情況下，通知是由兩個部分所組成：標頭和主體。 

### <a name="event-notification-headers"></a>事件通知標頭

通知訊息標頭會以索引鍵/值組表示。 根據 (MQTT、AMQP 或 HTTP) 所使用的通訊協定而定，訊息標頭將會以不同的方式進行序列化。 本節將討論通知訊息的一般標頭資訊，而不論所選擇的特定通訊協定和序列化為何。

某些通知符合 [CloudEvents](https://cloudevents.io/) 標準。 CloudEvents 一致性如下所示。
* 從裝置發出的通知會繼續遵循現有的通知規格
* IoT 中樞所處理和發出的通知會繼續遵循現有的通知規格，但 IoT 中樞會選擇支援 CloudEvents，例如透過事件方格
* 從 [數位 twins](concepts-twins-graph.md) 發出的通知與 [模型](concepts-models.md) 符合 CloudEvents
* Azure 數位 Twins 所處理和發出的通知符合 CloudEvents

服務必須在所有通知上新增序號以表示其順序，或以其他方式維護自己的順序。 

依事件方格主題中定義的架構類型而定，Azure 數位 Twins 對事件方格發出的通知會自動格式化為 CloudEvents 架構或 EventGridEvent 架構。 

標頭上的擴充屬性將會新增為承載內事件方格架構的屬性。 

### <a name="event-notification-bodies"></a>事件通知主體

通知訊息的本文在 JSON 中有說明。 根據訊息主體所需的序列化 (例如，使用 JSON、CBOR、Protobuf ) 等），訊息本文可能會以不同方式序列化。

本文所包含的一組欄位會因不同的通知類型而有所不同。 以下是兩個範例訊息內文，可讓您瞭解它們一般看起來的樣子，也可能包含。

遙測訊息：

```json
{
  "specversion": "1.0",
  "id": "df5a5992-817b-4e8a-b12c-e0b18d4bf8fb",
  "type": "microsoft.iot.telemetry",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net/digitaltwins/room1",
  "data": {
    "Temperature": 10
  },
  "dataschema": "dtmi:example:com:floor4;2",
  "datacontenttype": "application/json",
  "traceparent": "00-7e3081c6d3edfb4eaf7d3244b2036baa-23d762f4d9f81741-01"
}
```

生命週期通知訊息：

```json
{
  "specversion": "1.0",
  "id": "d047e992-dddc-4a5a-b0af-fa79832235f8",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net",
  "data": {
    "$dtId": "floor1",
    "$etag": "W/\"e398dbf4-8214-4483-9d52-880b61e491ec\"",
    "$metadata": {
      "$model": "dtmi:example:Floor;1"
    }
  },
  "subject": "floor1",
  "time": "2020-06-23T19:03:48.9700792Z",
  "datacontenttype": "application/json",
  "traceparent": "00-18f4e34b3e4a784aadf5913917537e7d-691a71e0a220d642-01"
}
```

## <a name="message-format-detail-for-different-event-types"></a>不同事件種類的訊息格式詳細資料

本節將詳細說明 IoT 中樞和 Azure 數位 Twins (或其他 Azure IoT 服務) 所發出的不同通知類型。 您將會瞭解觸發每種通知類型的專案，以及每一種通知主體所包含的一組欄位。

### <a name="digital-twin-life-cycle-notifications"></a>數位對應項生命週期通知

所有 [數位 twins](concepts-twins-graph.md) 都會發出通知，不論它們是否代表 [Azure 數位 Twins 中的 IoT 中樞裝置](how-to-ingest-iot-hub-data.md) 。 這是因為 **生命週期通知**，與數位對應項本身有關。

生命週期通知會在下列情況觸發：
* 建立數位對應項
* 數位對應項已刪除

#### <a name="properties"></a>屬性

以下是生命週期通知主體中的欄位。

| 名稱 | 值 |
| --- | --- |
| `id` | 通知的識別碼，例如 UUID 或服務所維護的計數器。 `source` + `id` 對每個相異事件而言是唯一的。 |
| `source` | IoT 中樞或 Azure 數位 Twins 實例的名稱，例如 *myhub.azure-devices.net* 或 *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | *1.0*<br>此訊息符合此版本的 [CloudEvents 規格](https://github.com/cloudevents/spec)。 |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | 數位對應項的識別碼 |
| `time` | 在對應項上發生作業的時間戳記 |
| `traceparent` | 事件的 W3C 追蹤內容 |

#### <a name="body-details"></a>主體詳細資料

主體是受影響的數位對應項，以 JSON 格式表示。 此架構的架構為 *數位 Twins 資源 7.1*。

針對建立事件，承載會在建立資源之後反映對應項的狀態，因此它應該包含所有系統產生的元素，就像呼叫一樣 `GET` 。

以下是 [IoT 隨插即用 (PnP) ](../iot-pnp/overview-iot-plug-and-play.md) 裝置的主體範例，其中包含元件和沒有最上層的屬性。 針對裝置 (的屬性（例如報告的屬性) ）應該省略。

```json
{
  "$dtId": "device-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

以下是數位對應項的另一個範例。 這項功能是以 [模型](concepts-models.md)為基礎，不支援元件：

```json
{
  "$dtId": "logical-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="digital-twin-relationship-change-notifications"></a>數位對應項關聯性變更通知

建立、更新或刪除數位對應項的任何關聯性時，會觸發**關聯性變更通知**。 

#### <a name="properties"></a>屬性

以下是 edge 變更通知主體中的欄位。

| 名稱    | 值 |
| --- | --- |
| `id` | 通知的識別碼，例如 UUID 或服務所維護的計數器。 `source` + `id` 對每個相異事件而言都是唯一的 |
| `source` | Azure 數位 Twins 實例的名稱，例如 *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | *1.0*<br>此訊息符合此版本的 [CloudEvents 規格](https://github.com/cloudevents/spec)。 |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete`
|`datacontenttype`| `application/json` |
| `subject` | 關聯性的識別碼，例如 `<twinID>/relationships/<relationshipID>` |
| `time` | 關聯性發生作業時的時間戳記 |
| `traceparent` | 事件的 W3C 追蹤內容 |

#### <a name="body-details"></a>主體詳細資料

主體是關聯性的裝載，也是 JSON 格式。 它會使用與透過 `GET` [DigitalTwins API](how-to-use-apis-sdks.md)的關聯性要求相同的格式。 

「更新關聯性」表示關聯性的屬性已變更。 

以下是更新關聯性通知以更新屬性的範例：

```json
{
    "modelId": "dtmi:example:Floor;1",
    "patch": [
      {
        "value": "user3",
        "path": "/ownershipUser",
        "op": "replace"
      }
    ]
  }
```

若為 `Relationship.Delete` ，主體與 `GET` 要求相同，而且會在刪除前取得最新狀態。

以下是建立或刪除關聯性通知的範例：

```json
{
    "$relationshipId": "device_to_device",
    "$etag": "W/\"72479873-0083-41a8-83e2-caedb932d881\"",
    "$relationshipName": "Connected",
    "$targetId": "device2",
    "connectionType": "WIFI"
}
```

### <a name="digital-twin-change-notifications"></a>數位對應項變更通知

更新數位對應項時，會觸發數位對應項**變更通知**，例如：
* 當屬性值或中繼資料變更時。
* 數位對應項或元件中繼資料變更時。 此案例的其中一個範例是變更數位對應項的模型。

#### <a name="properties"></a>屬性

以下是數位對應項變更通知主體中的欄位。

| 名稱    | 值 |
| --- | --- |
| `id` | 通知的識別碼，例如 UUID 或服務所維護的計數器。 `source` + `id` 對每個相異事件而言都是唯一的 |
| `source` | IoT 中樞或 Azure 數位 Twins 實例的名稱，例如 *myhub.azure-devices.net* 或 *mydigitaltwins.westus2.azuredigitaltwins.net*
| `specversion` | *1.0*<br>此訊息符合此版本的 [CloudEvents 規格](https://github.com/cloudevents/spec)。 |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | `application/json` |
| `subject` | 數位對應項的識別碼 |
| `time` | 數位對應項作業發生時的時間戳記 |
| `traceparent` | 事件的 W3C 追蹤內容 |

#### <a name="body-details"></a>主體詳細資料

通知的主體 `Twin.Update` 是包含數位對應項更新的 JSON 修補檔。

例如，假設已使用下列修補程式來更新數位對應項。

```json
[
    {
        "op": "replace",
        "value": 40,
        "path": "/Temperature"
    },
    {
        "op": "add",
        "value": 30,
        "path": "/comp1/prop1"
    }
]
```

對應的通知 (如果服務以同步方式執行，例如 Azure 數位 Twins 更新數位對應項) 會有如下的主體：

```json
{
    "modelId": "dtmi:example:com:floor4;2",
    "patch": [
      {
        "value": 40,
        "path": "/Temperature",
        "op": "replace"
      },
      {
        "value": 30,
        "path": "/comp1/prop1",
        "op": "add"
      }
    ]
  }
```

## <a name="next-steps"></a>後續步驟

瞭解如何建立端點和路由以傳遞事件：
* [*如何：管理端點和路由*](how-to-manage-routes-apis-cli.md)

或者，深入瞭解 Azure 數位 Twins Api 和 SDK 選項：
* [*How to：使用 Azure 數位 Twins Api 和 Sdk*](how-to-use-apis-sdks.md)