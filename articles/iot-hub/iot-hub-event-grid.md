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
ms.openlocfilehash: a67d90a0888c39938f07c294f8e161ce98fd945a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732507"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>使用事件方格來觸發動作以回應 IoT 中樞事件

「Azure IoT 中樞」與「Azure 事件格線」整合，讓您能夠將事件通知傳送給其他服務並觸發下游程序。 請設定您的商務應用程式來接聽「IoT 中樞」事件，以便以可靠、彈性且安全的方式回應重大事件。例如,生成更新資料庫、創建工作票證並在每次將新的 IoT 設備註冊到 IoT 中心時發送電子郵件通知的應用程式。

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
| 微軟.設備.設備遙測 | 將裝置遙測訊息傳送到 IoT 中心時發布 |

請使用 Azure 入口網站或 Azure CLI 來設定要從每個 IoT 中樞發佈哪些事件。 例如，請試試[使用 Logic Apps 來傳送 Azure IoT 中樞事件的相關電子郵件通知](../event-grid/publish-iot-hub-events-to-logic-apps.md)教學課程。

## <a name="event-schema"></a>結構描述

「IoT 中樞」事件包含了回應裝置生命週期變更時所需的所有資訊。 您可以透過檢查 eventType 屬性的開頭是否為 **Microsoft.Devices** 來識別「IoT 中樞」事件。 如需有關如何使用「事件格線」事件屬性的詳細資訊，請參閱[事件格線事件結構描述](../event-grid/event-schema.md)。

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

設備遙測消息必須採用有效的 JSON 格式,內容類型設置為**應用程式/json,** 內容編碼設置為消息[系統屬性](iot-hub-devguide-routing-query-syntax.md#system-properties)中的**UTF-8。** 這兩個屬性都是不區分大小寫的。 如果未設置內容編碼,則 IoT 中心將以基 64 編碼格式寫入消息。

您可以通過選擇終結點作為事件網格來豐富設備遙測事件之前,這些事件發佈到事件網格。 有關詳細資訊,請參閱[消息豐富概述](iot-hub-message-enrichments-overview.md)。

下面的範例顯示了裝置遙測事件的架構:

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

有關每個屬性的詳細說明,請參閱[IoT 中心的 Azure 事件網格事件架構](../event-grid/event-schema-iot-hub.md)。

## <a name="filter-events"></a>篩選事件

IoT 中心事件訂閱可以根據事件類型、資料內容和主題(即設備名稱)篩選事件。

事件格線支援對事件類型、主題與資料內容[進行篩選](../event-grid/event-filtering.md)。 建立事件網格訂閱時,您可以選擇訂閱選定的 IoT 事件。 事件方格中的主旨篩選會根據**開始於** (前置詞) 和**結尾是** (尾碼) 的相符項而運作。 篩選會使用 `AND` 運算子，讓主旨同時符合前置詞和尾碼的事件會傳遞給訂閱者。

「IoT 事件」的主旨使用以下格式：

```json
devices/{deviceId}
```

事件網格還允許篩選每個事件的屬性,包括數據內容。 這允許您選擇基於遙測消息的內容傳遞的事件。 請參閱[高級篩選](../event-grid/event-filtering.md#advanced-filtering)以查看範例。 要篩選遙測消息正文,必須在消息[系統屬性](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)中將內容類型設置為**應用程式/json**和內容編碼到**UTF-8。** 這兩個屬性都是不區分大小寫的。

對於非遙測事件(如設備連接、設備斷開連接、設備創建和設備刪除),可以在創建訂閱時使用事件網格篩選。 對於遙測事件,除了事件網格中的篩選外,使用者還可以通過消息路由查詢篩選設備孿生、消息屬性和正文。 

當您通過事件網格訂閱遙測事件時,IoT 中心將創建預設消息路由,以將數據源類型設備訊息發送到事件網格。 有關訊息路由的詳細資訊,請參閱[IoT 中心訊息路由](iot-hub-devguide-messages-d2c.md)。 此路由將在 IoT 中心>消息路由下的門戶中可見。 無論為遙測事件創建的 EG 訂閱數如何,都只創建一條事件網格路由。 因此,如果需要多個具有不同篩選器的訂閱,則可以在同一路由上的這些查詢中使用 OR 運算符。 路由的創建和刪除是通過通過事件網格訂閱遙測事件來控制的。 不能使用 IoT 中心消息路由創建或刪除事件網格路由。

在寄送遙測資料之前篩選訊息,可以更新[路由查詢](iot-hub-devguide-routing-query-syntax.md)。 請注意,僅當正文為 JSON 時,才能將路由查詢應用於消息正文。 您還必須在消息[系統屬性](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)中將內容類型設置為**應用程式/json**和內容編碼到**UTF-8。**

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>裝置連線和裝置中斷連線事件的限制

要接收設備連接狀態事件,設備必須使用Iot中心執行"D2C發送遙測"或"C2D接收消息"操作。 但是,請注意,如果裝置使用 AMQP 協定與 Iot Hub 連接,建議他們執行「C2D 接收消息」操作,否則其連接狀態通知可能會延遲幾分鐘。 如果您的裝置使用 MQTT 通訊協定，則 IoT 中樞會讓 C2D 連結保持開啟。 對於 AMQP,您可以通過呼叫[同步 API (I2D API)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)或[AMQP 的裝置客戶端](iot-hub-amqp-support.md#device-client)來打開 C2D 連結。

如果您要傳送遙測資料，應開啟 D2C 連結。 

如果設備連接閃爍,這意味著設備頻繁連接和斷開連接,我們不會發送每個連接狀態,而是將發佈在定期快照上拍攝的當前連接狀態,直到閃爍繼續。 接收具有不同序列號或不同連接狀態事件的相同連接狀態事件都意味著設備連接狀態發生變化。

## <a name="tips-for-consuming-events"></a>取用事件的秘訣

處理「IoT 中樞」事件的應用程式應該依循下列建議做法：

* 可以將多個訂閱配置為將事件路由到同一事件處理程式,因此不要假定事件來自特定源。 請一律檢查訊息主題，以確保訊息來自您預期的 IoT 中樞。

* 請勿假設您收到的所有事件都是您預期的類型。 處理訊息之前，請一律先檢查 eventType。

* 訊息的抵達可能會不按順序或有延遲。 使用 etag 欄位瞭解有關物件的資訊是否為設備創建或設備已刪除事件的最新資訊。

## <a name="next-steps"></a>後續步驟

* [試試 IoT 中樞事件教學課程](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [了解如何排序裝置連線和中斷連線事件](iot-hub-how-to-order-connection-state-events.md)

* [瞭解有關事件網格的更多資訊](../event-grid/overview.md)

* [比較路由傳送 IoT 中樞事件與訊息之間的差異](iot-hub-event-grid-routing-comparison.md)

* [瞭解如何使用 IoT 遙測事件使用 Azure 地圖實現 IoT 空間分析](../azure-maps/tutorial-iot-hub-maps.md#create-an-azure-function-and-add-an-event-grid-subscription)
