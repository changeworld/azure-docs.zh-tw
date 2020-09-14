---
title: Azure IoT 中樞和事件格線 | Microsoft Docs
description: 使用「Azure 事件格線」以根據「IoT 中樞」中發生的動作來觸發程序。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: c1dc3b5fe4eecea42baf7073b9c806eea1648cff
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056161"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>使用事件方格來觸發動作以回應 IoT 中樞事件

Azure IoT 中樞與 Azure 事件格線整合，讓您可以將事件通知傳送到其他服務，並觸發下游處理程序。 將您的商務應用程式設定為接聽 IoT 中樞事件，讓您能夠以可靠、可擴充且安全的方式回應重大事件。例如，建置一個應用程式，此應用程式可更新資料庫、建立工作票證，並在每次有新的 IoT 裝置向 IoT 中樞註冊時，傳遞電子郵件通知。

[Azure 事件方格](../event-grid/overview.md)是一個完全受控的事件路由服務，其使用發佈-訂閱模型。 「事件格線」針對 Azure 服務 (例如 [Azure Functions](../azure-functions/functions-overview.md) 和 [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)) 內建支援，並可使用 Webhook 將事件警示傳遞給非 Azure 服務。 如需事件方格所支援的事件處理常式完整清單，請參閱 [Azure 事件方格簡介](../event-grid/overview.md)。

![Azure 事件格線架構](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>區域可用性

位於支援「事件格線」之區域中的 IoT 中樞都可使用「事件格線」整合。 如需最新的區域清單，請參閱 [Azure 事件方格簡介](../event-grid/overview.md)。

## <a name="event-types"></a>事件類型

「IoT 中樞」會發佈下列事件類型：

| 事件類型 | 描述 |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | 向 IoT 中樞註冊裝置時發佈。 |
| Microsoft.Devices.DeviceDeleted | 從 IoT 中樞刪除裝置時發佈。 |
| Microsoft.Devices.DeviceConnected | 在裝置連線至 IoT 中樞時發佈。 |
| Microsoft.Devices.DeviceDisconnected | 在裝置從 IoT 中樞中斷連線時發佈。 |
| Microsoft.Devices.DeviceTelemetry | 裝置遙測訊息傳送至 IoT 中樞時發佈 |

請使用 Azure 入口網站或 Azure CLI 來設定要從每個 IoT 中樞發佈哪些事件。 例如，請試試[使用 Logic Apps 來傳送 Azure IoT 中樞事件的相關電子郵件通知](../event-grid/publish-iot-hub-events-to-logic-apps.md)教學課程。

## <a name="event-schema"></a>結構描述

「IoT 中樞」事件包含了回應裝置生命週期變更時所需的所有資訊。 您可藉由檢查開頭為 Microsoft.Devices 的 **eventType** 屬性來識別 IoT 中樞事件。 如需有關如何使用「事件格線」事件屬性的詳細資訊，請參閱[事件格線事件結構描述](../event-grid/event-schema.md)。

### <a name="device-connected-schema"></a>裝置連線結構描述

下列範例說明裝置連線事件的結構描述：

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
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="device-telemetry-schema"></a>裝置遙測架構

裝置遙測訊息必須是有效的 JSON 格式，且 contentType 設定為**application/JSON** ，而且 contentEncoding 在訊息[系統屬性](iot-hub-devguide-routing-query-syntax.md#system-properties)中設定為**utf-8** 。 這兩個屬性都不區分大小寫。 如果未設定內容編碼，則 IoT 中樞會以基底64編碼格式寫入訊息。

您可以藉由選取端點作為事件方格，先擴充裝置遙測事件，然後再將其發佈至事件方格。 如需詳細資訊，請參閱 [Message 擴充總覽](iot-hub-message-enrichments-overview.md)。

下列範例顯示裝置遙測事件的架構：

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

### <a name="device-created-schema"></a>已建立裝置結構描述

以下範例說明已建立裝置事件的結構描述：

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
      "deviceEtag":"null",
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

如需每個屬性的詳細說明，請參閱 [適用于 IoT 中樞的 Azure 事件方格事件架構](../event-grid/event-schema-iot-hub.md)。

## <a name="filter-events"></a>篩選事件

IoT 中樞事件訂閱可以根據事件種類、資料內容和主體（也就是裝置名稱）來篩選事件。

事件方格可讓您 [篩選](../event-grid/event-filtering.md) 事件種類、主題和資料內容。 建立事件方格訂用帳戶時，您可以選擇訂閱選取的 IoT 事件。 事件方格中的主旨篩選會根據**開始於** (前置詞) 和**結尾是** (尾碼) 的相符項而運作。 篩選會使用 `AND` 運算子，讓主旨同時符合前置詞和尾碼的事件會傳遞給訂閱者。

「IoT 事件」的主旨使用以下格式：

```json
devices/{deviceId}
```

事件方格也可以篩選每個事件的屬性，包括資料內容。 這可讓您根據遙測訊息的內容，選擇要傳遞的事件。 請參閱 [advanced 篩選](../event-grid/event-filtering.md#advanced-filtering) 以查看範例。 若要篩選遙測訊息本文，您必須在訊息[系統屬性](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)中將 contentType 設為**application/json**並 contentEncoding 為**utf-8** 。 這兩個屬性都不區分大小寫。

針對 DeviceConnected、DeviceDisconnected、DeviceCreated 和 DeviceDeleted 等非遙測事件，可在建立訂用帳戶時使用事件方格篩選。 針對遙測事件，除了事件方格中的篩選之外，使用者也可以透過訊息路由查詢來篩選裝置 twins、訊息屬性和主體。 

當您透過事件方格訂閱遙測事件時，IoT 中樞會建立預設的訊息路由，將資料來源類型裝置訊息傳送至事件方格。 如需有關訊息路由的詳細資訊，請參閱 [IoT 中樞訊息路由](iot-hub-devguide-messages-d2c.md)。 此路由會顯示在入口網站的 [IoT 中樞] > 訊息路由。 無論為遙測事件建立的例如訂用帳戶數目為何，都只會建立一個事件方格的路由。 因此，如果您需要多個具有不同篩選準則的訂用帳戶，您可以在相同路由上的這些查詢中使用 OR 運算子。 建立和刪除路由是透過事件方格的遙測事件訂用帳戶來控制。 您無法使用 IoT 中樞訊息路由來建立或刪除事件方格的路由。

若要在傳送遙測資料之前篩選訊息，您可以更新您的 [路由查詢](iot-hub-devguide-routing-query-syntax.md)。 請注意，只有在主體是 JSON 時，才可以將路由查詢套用至訊息主體。 您也必須在訊息[系統屬性](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)中將 contentType 設定為**application/json** ，並將 contentEncoding 設定為**utf-8** 。

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>裝置連線和裝置中斷連線事件的限制

若要接收裝置線上狀態事件，裝置必須使用 Iot 中樞來進行「a 2C 傳送遙測」或「C2D 接收訊息」作業。 不過，請注意，如果裝置使用 AMQP 通訊協定來與 Iot 中樞連線，建議他們進行「C2D 接收訊息」作業，否則其線上狀態通知可能會延遲幾分鐘。 如果您的裝置使用 MQTT 通訊協定，則 IoT 中樞會讓 C2D 連結保持開啟。 針對 AMQP，您可以藉由呼叫適用于 IoT 中樞 c # SDK 的 Receive Async API，或 [適用于 AMQP 的裝置用戶端](iot-hub-amqp-support.md#device-client)，來開啟 C2D 連結。

如果您要傳送遙測資料，應開啟 D2C 連結。 

如果裝置連線閃爍，這表示裝置會頻繁地連接和中斷連接，我們將不會傳送每個單一線上狀態，而是會在發生閃爍的情況下，發行定期快照的目前連接狀態。 接收具有不同序號或不同連接狀態事件的相同線上狀態事件，兩者皆表示裝置線上狀態有所變更。

## <a name="tips-for-consuming-events"></a>取用事件的提示

處理 IoT 中樞事件的應用程式應遵循下列建議做法：

* 您可以設定多個訂用帳戶，將事件路由傳送至相同的事件處理常式，因此請不要假設事件來自特定來源。 請務必檢查訊息主題，以確保其來自您預期的 IoT 中樞。

* 請不要假設您收到的所有事件都是所預期類型。 處理訊息之前，請一律先檢查 eventType。

* 訊息可能會不按順序抵達或在延遲之後抵達。 使用 [etag] 欄位來瞭解有關物件的資訊是否為裝置建立或裝置刪除事件的最新狀態。

## <a name="next-steps"></a>接下來的步驟

* [試試 IoT 中樞事件教學課程](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [了解如何排序裝置連線和中斷連線事件](iot-hub-how-to-order-connection-state-events.md)

* [深入瞭解事件方格](../event-grid/overview.md)

* [比較路由傳送 IoT 中樞事件與訊息之間的差異](iot-hub-event-grid-routing-comparison.md)

* [瞭解如何使用 IoT 遙測事件，利用 Azure 地圖服務來執行 IoT 空間分析](../azure-maps/tutorial-iot-hub-maps.md)
