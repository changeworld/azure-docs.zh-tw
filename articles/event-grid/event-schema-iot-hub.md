---
title: 做為事件方格來源 Azure IoT 中樞
description: 本文提供 Azure IoT 中樞事件的屬性和結構描述。 它會列出可用的事件種類、範例事件和事件屬性。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 02ecf8d4df55aa6b4319e40892778f85f94e29a7
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86113644"
---
# <a name="azure-iot-hub-as-an-event-grid-source"></a>做為事件方格來源 Azure IoT 中樞
本文提供 Azure IoT 中樞事件的屬性和結構描述。 如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。 

## <a name="event-grid-event-schema"></a>Event Grid 事件結構描述

### <a name="available-event-types"></a>可用的事件類型

Azure IoT 中樞會發出下列事件類型：

| 事件類型 | 描述 |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | 向 IoT 中樞註冊裝置時發佈。 |
| Microsoft.Devices.DeviceDeleted | 從 IoT 中樞刪除裝置時發佈。 | 
| Microsoft.Devices.DeviceConnected | 在裝置連線至 IoT 中樞時發佈。 |
| Microsoft.Devices.DeviceDisconnected | 在裝置從 IoT 中樞中斷連線時發佈。 | 
| DeviceTelemetry | 在遙測訊息傳送至 IoT 中樞時發行。 |

除了裝置遙測事件以外的所有裝置事件，都已在事件方格支援的所有區域中正式推出。 裝置遙測事件處於公開預覽狀態，適用于美國東部、美國西部、西歐、 [Azure Government](../azure-government/documentation-government-welcome.md)、 [azure 中國世紀](/azure/china/china-welcome)和[azure 德國](https://azure.microsoft.com/global-infrastructure/germany/)以外的所有區域。

### <a name="example-event"></a>事件範例

DeviceConnected 和 DeviceDisconnected 事件的結構描述具有相同的結構。 此事件範例顯示裝置連線至 IoT 中樞註冊時引發的事件結構描述：

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

當遙測事件傳送至 IoT 中樞時，就會引發 DeviceTelemetry 事件。 此事件的範例架構如下所示。

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

DeviceCreated 和 DeviceDeleted 事件的結構描述具有相同的結構。 此事件範例顯示向 IoT 中樞註冊裝置時引發之事件的結構描述：

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>事件屬性

所有事件都包含相同的最高層級資料： 

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| id | 字串 | 事件的唯一識別碼。 |
| 主題 | 字串 | 事件來源的完整資源路徑。 此欄位不可寫入。 Event Grid 提供此值。 |
| subject | 字串 | 發行者定義事件主體的路徑。 |
| eventType | 字串 | 此事件來源已註冊的事件類型之一。 |
| eventTime | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| data | 物件 (object) | IoT 中樞事件資料。  |
| dataVersion | 字串 | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | 字串 | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |

對於所有 IoT 中樞事件，資料物件都會包含下列屬性：

| 屬性 | 類型 | Description |
| -------- | ---- | ----------- |
| hubName | 字串 | 已建立或刪除裝置的 IoT 中樞名稱。 |
| deviceId | 字串 | 裝置的唯一識別碼。 此區分大小寫的字串最長為 128 個字元，並支援 ASCII 7 位元英數字元和下列特殊字元：`- : . + % _ # * ? ! ( ) , = @ ; $ '`。 |

每個事件發行者有不同的資料物件內容。 

對於**裝置連線**和**裝置中斷連線** IoT 中樞事件，資料物件會包含下列屬性：

| 屬性 | 類型 | Description |
| -------- | ---- | ----------- |
| moduleId | 字串 | 模組的唯一識別碼。 針對模組裝置才會輸出此欄位。 此區分大小寫的字串最長為 128 個字元，並支援 ASCII 7 位元英數字元和下列特殊字元：`- : . + % _ # * ? ! ( ) , = @ ; $ '`。 |
| deviceConnectionStateEventInfo | 物件 (object) | 裝置連線狀態事件資訊
| sequenceNumber | 字串 | 一個號碼，有助於指出裝置連線或裝置中斷連線事件的順序。 最新的事件會有高於前一個事件的序號。 此號碼的變動有可能超過 1，但只會增加不會減少。 請參閱[如何使用序號](../iot-hub/iot-hub-how-to-order-connection-state-events.md)。 |

針對**裝置遙測**IoT 中樞事件，資料物件會包含[IoT 中樞訊息格式](../iot-hub/iot-hub-devguide-messages-construct.md)的裝置到雲端訊息，而且具有下列屬性：

| 屬性 | 類型 | Description |
| -------- | ---- | ----------- |
| body | 字串 | 來自裝置的訊息內容。 |
| properties | 字串 | 應用程式屬性為可新增至訊息的使用者定義字串。 這些欄位為選擇性。 |
| 系統屬性 | 字串 | [系統屬性](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties)有助於識別訊息的內容和來源。 裝置遙測訊息必須是有效的 JSON 格式，並將 contentType 設定為 JSON，並在訊息系統屬性中將 contentEncoding 設定為 UTF-8。 如果未設定，則 IoT 中樞會以 base 64 編碼格式寫入訊息。  |

對於**裝置建立**和**裝置刪除** IoT 中樞事件，資料物件會包含下列屬性：

| 屬性 | 類型 | Description |
| -------- | ---- | ----------- |
| twin | 物件 (object) | 裝置對應項的相關資訊，這是應用程式裝置中繼資料的雲端標記法。 | 
| deviceID | 字串 | 裝置對應項的唯一識別碼。 | 
| etag | 字串 | 可確保裝置對應項的更新具有一致性的驗證程式。 對每個裝對應項而言，每個 etag 保證都是唯一的。 |  
| deviceEtag| 字串 | 可確保裝置登錄的更新具有一致性的驗證程式。 對每個裝置登錄而言，每個 deviceEtag 保證都是唯一的。 |
| status | 字串 | 已啟用或停用裝置對應項。 | 
| statusUpdateTime | 字串 | 上次更新裝置對應項狀態的 ISO8601 時間戳記。 |
| connectionState | 字串 | 裝置已連線或已中斷連線。 | 
| lastActivityTime | 字串 | 上次活動的 ISO8601 時間戳記。 | 
| cloudToDeviceMessageCount | integer | 傳送到此裝置的雲端到裝置訊息計數。 | 
| authenticationType | 字串 | 用於這個裝置的驗證類型：`SAS`、`SelfSigned` 或 `CertificateAuthority`。 |
| x509Thumbprint | 字串 | 指紋是 x509 憑證的唯一值，通常用來尋找憑證存放區中的特定憑證。 指紋是使用 SHA1 演算法所動態產生的，並未實際存在於憑證中。 | 
| primaryThumbprint | 字串 | x509 憑證的主要指紋。 |
| secondaryThumbprint | 字串 | x509 憑證的次要指紋。 | 
| version | integer | 每次更新裝置對應項時，整數就會遞增 1。 |
| desired | 物件 (object) | 只能由應用程式後端寫入，並可由裝置讀取的屬性部分。 | 
| reported | 物件 (object) | 只能由裝置寫入，並可由應用程式後端讀取的屬性部分。 |
| lastUpdated | 字串 | 上次更新裝置對應項屬性的 ISO8601 時間戳記。 | 

## <a name="tutorials-and-how-tos"></a>教學課程和操作說明
|Title  |描述  |
|---------|---------|
| [使用 Logic Apps 來傳送 Azure IoT 中樞事件的相關電子郵件通知](publish-iot-hub-events-to-logic-apps.md) | 每當有裝置新增至您的 IoT 中樞時，邏輯應用程式就會傳送電子郵件通知。 |
| [使用事件方格來觸發動作以回應 IoT 中樞事件](../iot-hub/iot-hub-event-grid.md) | 整合 IoT 中樞與事件方格的概觀。 |
| [排序裝置連線和裝置中斷連線事件](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | 說明如何排序裝置連線狀態事件。 |

## <a name="next-steps"></a>後續步驟

* 如需 Azure Event Grid 的簡介，請參閱[什麼是 Event Grid？](overview.md)
* 若要深入了解 IoT 中樞與 Event Grid 一起運作的方式，請參閱[使用 Event Grid 觸發動作來回應 IoT 中樞事件](../iot-hub/iot-hub-event-grid.md)。