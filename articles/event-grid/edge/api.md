---
title: REST API-Azure 事件方格 IoT Edge |Microsoft Docs
description: IoT Edge 上事件方格上的 REST API。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 414487d460d897eff787b11915db560706b29eb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171749"
---
# <a name="rest-api"></a>REST API
本文說明 IoT Edge 上的 Azure 事件方格的 REST Api

## <a name="common-api-behavior"></a>常見的 API 行為

### <a name="base-url"></a>基底 URL
IoT Edge 上的事件方格具有下列透過 HTTP (埠 5888) 和 HTTPS (埠 4438) 公開的 Api。

* HTTP 的基底 URL： http://eventgridmodule:5888
* HTTPS 的基底 URL： https://eventgridmodule:4438

### <a name="request-query-string"></a>要求查詢字串
所有 API 要求都需要下列查詢字串參數：

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>要求內容類型
所有 API 要求都必須有 **content-type**。

如果是 **>eventgridschema** 或 **CustomSchema**，content-type 的值可以是下列其中一個值：

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

在結構化模式中 **CloudEventSchemaV1_0** 的情況下，content-type 的值可以是下列其中一個值：

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

如果是以二進位模式 **CloudEventSchemaV1_0** ，請參閱 [檔](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) 以取得詳細資料。

### <a name="error-response"></a>錯誤回應
所有 Api 都會傳回具有下列承載的錯誤：

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>管理主題

### <a name="put-topic-create--update"></a>將主題放 (建立/更新) 

**要求**： ``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**Payload**承載：

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**回應**： HTTP 200

**Payload**承載：

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>取得主題

**要求**： ``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**回應**： HTTP 200

**Payload**承載：
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>取得所有主題

**要求**： ``` GET /topics?api-version=2019-01-01-preview ```

**回應**： HTTP 200

**Payload**承載：
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>刪除主題

**要求**： ``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**回應**： HTTP 200，空的承載

## <a name="manage-event-subscriptions"></a>管理事件訂閱
本章節中的範例會使用 `EndpointType=Webhook;` 。 的 json 範例 `EndpointType=EdgeHub / EndpointType=EventGrid` 在下一節中。 

### <a name="put-event-subscription-create--update"></a>Put 事件訂用帳戶 (建立/更新) 

**要求**： ``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Payload**承載：
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "persistencePolicy": "true",
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**回應**： HTTP 200

**Payload**承載：

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>取得事件訂用帳戶

**要求**： ``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**回應**： HTTP 200

**Payload**承載：
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>取得事件訂閱

**要求**： ``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**回應**： HTTP 200

**Payload**承載：
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>刪除事件訂用帳戶

**要求**： ``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**回應**： HTTP 200、無承載


## <a name="publish-events-api"></a>發佈事件 API

### <a name="send-batch-of-events-in-event-grid-schema"></a>事件方格架構中 (傳送事件批次) 

**要求**： ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**回應**： HTTP 200，空的承載


**裝載欄位描述**
- ```Id``` 是必要的。 它可以是由呼叫端填入的任何字串值。 事件方格不會執行任何重複的偵測，或在此欄位上強制執行任何語義。
- ```Topic``` 是選擇性的，但如果有指定，必須符合要求 URL 中的 topic_name
- ```Subject``` 是必要的，可以是任何字串值
- ```EventType``` 是必要的，可以是任何字串值
- ```EventTime``` 是必要的，但不會經過驗證，但應為正確的日期時間。
- ```DataVersion``` 是必要的
- ```MetadataVersion``` 是選擇性的，如果指定的話，它必須是具有值的字串 ```"1"```
- ```Data``` 是選擇性的，而且可以是任何 JSON 權杖 (數位、字串、布林值、陣列、物件) 

### <a name="send-batch-of-events-in-custom-schema"></a>在自訂架構中 (傳送事件批次) 

**要求**： ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**回應**： HTTP 200，空的承載


**承載限制**
- 必須是事件的陣列。
- 每個陣列專案都必須是 JSON 物件。
- 除了承載大小) 之外，沒有其他限制式 (。

## <a name="examples"></a>範例

### <a name="set-up-topic-with-eventgrid-schema"></a>使用 EventGrid 架構設定主題
設定主題以要求在 **>eventgridschema**中發佈事件。

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>使用自訂架構設定主題
設定主題以要求在中發佈事件 `customschema` 。

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>使用雲端事件架構設定主題
設定主題以要求在中發佈事件 `cloudeventschema` 。

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>將 WebHook 設定為目的地、要在 >eventgridschema 中傳遞的事件
使用此目的地類型可將事件傳送至裝載 HTTP 端點) 的任何其他模組 (，或傳送至網路/網際網路上任何 HTTP 可定址的端點。

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

屬性的條件約束 `endpointUrl` ：
- 它必須為非 null。
- 它必須是絕對 URL。
- 如果 EventGridModule 設定中的 outbound__webhook__HTTPsOnly 設定為 true，則必須僅限 HTTPS。
- 如果 outbound__webhook__HTTPsOnly 設為 false，則可以是 HTTP 或 HTTPS。

屬性的條件約束 `eventDeliverySchema` ：
- 它必須符合訂閱主題的輸入架構。
- 它可以是 Null。 它會預設為主題的輸入架構。

### <a name="set-up-iot-edge-as-destination"></a>將 IoT Edge 設定為目的地

使用此目的地將事件傳送至 IoT Edge 中樞，並受限於 Edge 中樞的路由/篩選/轉送子系統。

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>將事件方格雲端設定為目的地

使用此目的地可將事件傳送至雲端中的事件方格 (Azure) 。 在邊緣上建立事件訂用帳戶之前，您必須先在雲端中設定要將事件傳送至其中的使用者主題。

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

EndpointUrl
- 它必須為非 null。
- 它必須是絕對 URL。
- `/api/events`必須在要求 URL 路徑中定義路徑。
- 它必須 `api-version=2018-01-01` 在查詢字串中。
- 如果 outbound__eventgrid__HTTPsOnly 在 [EventGridModule 設定] 中設定為 [true] (預設) 的 [true]，則必須是 [僅 HTTPS]。
- 如果 outbound__eventgrid__HTTPsOnly 設定為 false，則可以是 HTTP 或 HTTPS。
- 如果 outbound__eventgrid__allowInvalidHostnames 設定為 false (預設為 false) ，它必須以下列其中一個端點為目標：
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- 必須為非 null。

TopicName:
- 如果將 EventDeliverySchema 設定為 >eventgridschema，這個欄位的值就會放入每個事件的主題欄位，然後再轉送到雲端中的事件方格。
- 如果將 EventDeliverySchema 設定為 CustomEventSchema，則會忽略此屬性，並將自訂事件裝載的轉送完全依照收到的方式轉寄。

## <a name="set-up-event-hubs-as-a-destination"></a>將事件中樞設定為目的地

若要發佈至事件中樞，請將設定 `endpointType` 為， `eventHub` 並提供：

* connectionString：透過共用存取原則所產生之特定事件中樞的連接字串。

    >[!NOTE]
    > 連接字串必須是實體特定的。 使用命名空間連接字串將無法運作。 您可以藉由流覽至您想要在 Azure 入口網站中發佈的特定事件中樞，然後按一下 [ **共用存取原則** ] 來產生新的實體特定 connecection 字串，藉以產生實體特定的連接字串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>將服務匯流排佇列設定為目的地

若要發行至服務匯流排佇列，請將設定 `endpointType` 為， `serviceBusQueue` 並提供：

* connectionString：您的目標特定服務匯流排佇列的連接字串，是透過共用存取原則所產生。

    >[!NOTE]
    > 連接字串必須是實體特定的。 使用命名空間連接字串將無法運作。 藉由流覽至您想要在 Azure 入口網站中發佈的特定服務匯流排佇列，然後按一下 [ **共用存取原則** ] 來產生新的實體特定 connecection 字串，以產生實體特定的連接字串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>將服務匯流排主題設定為目的地

若要發佈至服務匯流排主題，請將設定 `endpointType` 為， `serviceBusTopic` 並提供：

* connectionString：您的目標特定服務匯流排主題的連接字串，是透過共用存取原則所產生。

    >[!NOTE]
    > 連接字串必須是實體特定的。 使用命名空間連接字串將無法運作。 藉由流覽至您想要在 Azure 入口網站中發佈的特定服務匯流排主題，然後按一下 [ **共用存取原則** ] 以產生新的實體特定 connecection 字串，來產生實體特定的連接字串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>將儲存體佇列設定為目的地

若要發行至儲存體佇列，請將設定  `endpointType` 為， `storageQueue` 並提供：

* queueName：您要發佈的儲存體佇列的名稱。
* connectionString：儲存體佇列所在之儲存體帳戶的連接字串。

    >[!NOTE]
    > Unline 事件中樞、服務匯流排佇列和服務匯流排主題，用於儲存體佇列的連接字串不是實體特有的。 相反地，它必須是儲存體帳戶的連接字串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```