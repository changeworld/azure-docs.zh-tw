---
title: 輸出和端點-Azure 數位 Twins |Microsoft Docs
description: 瞭解如何在 Azure 數位 Twins 中建立及輸出事件端點。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3803802a3d81655091d8be543ae9cb17221a98d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76511564"
---
# <a name="egress-and-endpoints-in-azure-digital-twins"></a>Azure 數位 Twins 中的輸出和端點

Azure Digital Twins 的「端點」** 代表使用者 Azure 訂用帳戶內的訊息或事件代理程式。 事件和訊息可以傳送至 Azure 事件中樞、Azure 事件方格和 Azure 服務匯流排主題。

事件會根據預先定義的路由喜好設定來路由至端點。 使用者會指定每個端點可收到的「事件類型」**。

若要深入了解事件、路由與事件類型，請參閱 [Azure Digital Twins 中的路由事件和訊息](./concepts-events-routing.md)。

## <a name="events"></a>事件

IoT 物件 (例如裝置與感應器) 會傳送事件，以便 Azure 訊息和事件代理程式進行處理。 事件會根據下列 [Azure 事件方格事件結構描述參考](../event-grid/event-schema.md)來定義。

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| 屬性 | 類型 | 描述 |
| --- | --- | --- |
| id | 字串 | 事件的唯一識別碼。 |
| subject | 字串 | 發行者定義事件主體的路徑。 |
| data | 物件 | 資源提供者特有的事件資料。 |
| eventType | 字串 | 此事件來源已註冊的事件類型之一。 |
| eventTime | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| dataVersion | 字串 | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | 字串 | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |
| 主題 | 字串 | 事件來源的完整資源路徑。 此欄位不可寫入。 Event Grid 提供此值。 |

如需有關事件方格事件結構描述的詳細資訊：

- 請檢閱 [Azure 事件方格事件結構描述參考](../event-grid/event-schema.md)。
- 請閱讀 [Azure EventGrid Node.js SDK EventGridEvent 參考](https://docs.microsoft.com/javascript/api/@azure/eventgrid/eventgridevent?view=azure-node-latest)。

## <a name="event-types"></a>事件類型

事件類型會以事件本質進行分類，並在 **eventType** 欄位中設定。 下列清單提供可用的事件類型：

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

以下幾小節將進一步說明每個事件類型的事件格式。

### <a name="topologyoperation"></a>TopologyOperation

**TopologyOperation** 適用於圖表變更。 subject**** 屬性會指定受影響的物件類型。 下列類型的物件可能會觸發此事件：

- 裝置
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- ExtendedType
- KeyStore
- 報表
- RoleDefinition
- 感應器
- SensorBlobMetadata
- SensorExtendedProperty
- Space
- SpaceBlobMetadata
- SpaceExtendedProperty
- SpaceResource
- SpaceRoleAssignment
- System (系統)
- User
- UserBlobMetadata
- UserExtendedProperty

#### <a name="example"></a>範例

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| 值 | 更換為 |
| --- | --- |
| YOUR_TOPIC_NAME | 自訂主題的名稱 |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** 是由使用者定義函式 (UDF) 傳送的事件。
  
> [!IMPORTANT]  
> 此事件必須以明確地從 UDF 本身傳送。

#### <a name="example"></a>範例

```JSON
{
  "id": "568fd394-380b-46fa-925a-ebb96f658cce",
  "subject": "UdfCustom",
  "data": {
    "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "ResourceType": "Space",
    "Payload": "\"Room is not available or air quality is poor\"",
    "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
  },
  "eventType": "UdfCustom",
  "eventTime": "2018-10-02T06:50:15.198Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| 值 | 更換為 |
| --- | --- |
| YOUR_TOPIC_NAME | 自訂主題的名稱 |

### <a name="sensorchange"></a>SensorChange

**SensorChange** 是以遙測變更為基礎的感應器狀態更新。

#### <a name="example"></a>範例

```JSON
{
  "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
  "subject": "SensorChange",
  "data": {
    "Type": "Classic",
    "DataType": "Motion",
    "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "Value": "False",
    "PreviousValue": "True",
    "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
    "MessageType": "sensor",
    "Properties": {
      "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
      "ms-activity-id": "ct22YwXEGJ5u.605.0"
    }
  },
  "eventType": "SensorChange",
  "eventTime": "2018-04-17T17:46:18.5452993Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| 值 | 更換為 |
| --- | --- |
| YOUR_TOPIC_NAME | 自訂主題的名稱 |

### <a name="spacechange"></a>SpaceChange

**SpaceChange** 是以遙測變更為基礎的空間狀態更新。

#### <a name="example"></a>範例

```JSON
{
  "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
  "subject": "SpaceChange",
  "data": {
    "Type": null,
    "DataType": "AvailableAndFresh",
    "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "Value": "Room is not available or air quality is poor",
    "PreviousValue": null,
    "RawData": null,
    "transactionId": null,
    "EventTimestamp": null,
    "MessageType": null,
    "Properties": null,
    "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
  },
  "eventType": "SpaceChange",
  "eventTime": "2018-10-02T06:50:20.128Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| 值 | 更換為 |
| --- | --- |
| YOUR_TOPIC_NAME | 自訂主題的名稱 |

### <a name="devicemessage"></a>DeviceMessage

使用 **DeviceMessage** 可以指定 **EventHub** 連線，原始遙測事件也可從 Azure Digital Twins 路由至此。

> [!NOTE]
> - **DeviceMessage** 只能與 **EventHub** 搭配使用。 您無法將 **DeviceMessage** 與任何其他事件類型結合。
> - 您只能指定一個結合 **EventHub** 或 **DeviceMessage** 類型的端點。

## <a name="configure-endpoints"></a>設定端點

端點管理可透過端點 API 來執行。

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

下列範例示範如何設定支援端點。

>[!IMPORTANT]
> 請特別注意 **eventTypes** 屬性。 此屬性會定義端點處理的事件類型，並以此決定其路由方式。

已驗證的 HTTP POST 要求：

```URL
YOUR_MANAGEMENT_API_URL/endpoints
```

- 路由傳送至服務匯流排事件類型 **SensorChange**、**SpaceChange** 和 **TopologyOperation**：

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME"
  }
  ```

    | 值 | 更換為 |
    | --- | --- |
    | YOUR_NAMESPACE | 端點的命名空間 |
    | YOUR_PRIMARY_KEY | 用於驗證的主要連接字串 |
    | YOUR_SECONDARY_KEY | 用於驗證的次要連接字串 |
    | YOUR_TOPIC_NAME | 自訂主題的名稱 |

- 路由傳送至事件方格事件類型 **SensorChange**、**SpaceChange** 和 **TopologyOperation**：

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME.westus-1.eventgrid.azure.net"
  }
  ```

    | 值 | 更換為 |
    | --- | --- |
    | YOUR_PRIMARY_KEY | 用於驗證的主要連接字串|
    | YOUR_SECONDARY_KEY | 用於驗證的次要連接字串 |
    | YOUR_TOPIC_NAME | 自訂主題的名稱 |

- 路由傳送至事件中樞事件類型 **SensorChange**、**SpaceChange** 和 **TopologyOperation**：

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | 值 | 更換為 |
    | --- | --- |
    | YOUR_NAMESPACE | 端點的命名空間 |
    | YOUR_PRIMARY_KEY | 用於驗證的主要連接字串 |
    | YOUR_SECONDARY_KEY | 用於驗證的次要連接字串 |
    | YOUR_EVENT_HUB_NAME | 事件中樞的名稱 |

- 路由傳送至事件中樞事件類型 **DeviceMessage**。 `EntityPath`在**connectionString**中包含會是強制的：

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | 值 | 更換為 |
    | --- | --- |
    | YOUR_NAMESPACE | 端點的命名空間 |
    | YOUR_PRIMARY_KEY | 用於驗證的主要連接字串 |
    | YOUR_SECONDARY_KEY | 用於驗證的次要連接字串 |
    | YOUR_EVENT_HUB_NAME | 事件中樞的名稱 |

> [!NOTE]  
> 建立新端點時，可能需要 5 到 10 分鐘的時間，才能開始在端點上接收事件。

## <a name="primary-and-secondary-connection-keys"></a>主要和次要連線金鑰

如果主要連線金鑰變成未授權，則系統會自動嘗試次要連線金鑰。 這可提供備份，並提高透過端點 API 依正常程序進行驗證及更新主要金鑰的機會。

如果主要和次要連線金鑰都變成未授權，則系統就會進入指數退避機制，並等候最多 30 分鐘的時間。 在每個觸發的退避等候時間上，事件會遭到捨棄。

只要系統處於退避等候狀態，透過端點 API 進行的連線金鑰更新可能要 30 分鐘後才會生效。

## <a name="unreachable-endpoints"></a>無法連線的端點

當端點變成無法連線時，系統會進入指數退避機制，並等候最多 30 分鐘的時間。 在每個觸發的退避等候時間上，事件會遭到捨棄。

## <a name="next-steps"></a>後續步驟

- 了解[如何使用 Azure Digital Twins Swagger](how-to-use-swagger.md)。

- 深入了解 Azure Digital Twins 中的[路由事件與訊息](concepts-events-routing.md)。
