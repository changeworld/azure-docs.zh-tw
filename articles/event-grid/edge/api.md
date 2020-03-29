---
title: REST API - Azure 事件網格 IoT 邊緣 |微軟文檔
description: 在 IoT 邊緣的事件網格上 REST API。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 19f86b1d8233e05844201e1095c1f79324955cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841824"
---
# <a name="rest-api"></a>REST API
本文介紹了 IoT 邊緣上 Azure 事件網格的 REST API

## <a name="common-api-behavior"></a>常見 API 行為

### <a name="base-url"></a>基底 URL
IoT Edge 上的事件網格具有通過 HTTP（埠 5888）和 HTTPS（埠 4438）公開的以下 API。

* 用於 HTTP 的基本 URL：http://eventgridmodule:5888
* 用於 HTTPS 的基本 URL：https://eventgridmodule:4438

### <a name="request-query-string"></a>請求查詢字串
所有 API 請求都需要以下查詢字串參數：

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>請求內容類型
所有 API 請求都必須具有**內容類型**。

在**事件網格架構**或**自訂架構**中，內容類型的值可以是以下值之一：

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

在結構化模式下**CloudEventSchemaV1_0**的情況下，內容類型的值可以是以下值之一：

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

如果**CloudEventSchemaV1_0**在二進位模式下，請參閱[文檔](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md)以瞭解詳細資訊。

### <a name="error-response"></a>錯誤回應
所有 API 都返回具有以下有效負載的錯誤：

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

### <a name="put-topic-create--update"></a>放置主題（創建/更新）

**請求**：``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**有效負載**：

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

**有效負載**：

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

### <a name="get-topic"></a>獲取主題

**請求**：``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**回應**： HTTP 200

**有效負載**：
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

### <a name="get-all-topics"></a>獲取所有主題

**請求**：``` GET /topics?api-version=2019-01-01-preview ```

**回應**： HTTP 200

**有效負載**：
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

**請求**：``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**回應**： HTTP 200，空負載

## <a name="manage-event-subscriptions"></a>管理事件訂閱
本節中的示例使用`EndpointType=Webhook;`。 下一節將為其`EndpointType=EdgeHub / EndpointType=EventGrid`的 json 示例。 

### <a name="put-event-subscription-create--update"></a>放置事件訂閱（創建/更新）

**請求**：``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**有效負載**：
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

**有效負載**：

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


### <a name="get-event-subscription"></a>獲取事件訂閱

**請求**：``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**回應**： HTTP 200

**有效負載**：
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

### <a name="get-event-subscriptions"></a>獲取事件訂閱

**請求**：``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**回應**： HTTP 200

**有效負載**：
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

### <a name="delete-event-subscription"></a>刪除事件訂閱

**請求**：``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**回應**： HTTP 200，無有效負載


## <a name="publish-events-api"></a>發佈事件 API

### <a name="send-batch-of-events-in-event-grid-schema"></a>發送事件批次處理（在事件網格架構中）

**請求**：``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

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

**回應**： HTTP 200，空負載


**有效負載欄位描述**
- ```Id```是強制性的。 它可以是調用方填充的任何字串值。 事件網格不執行任何重複檢測或強制執行此欄位上的任何語義。
- ```Topic```是可選的，但如果指定，則必須與請求 URL 中topic_name匹配
- ```Subject```是必填項，可以是任一字元串值
- ```EventType```是必填項，可以是任一字元串值
- ```EventTime```是強制性的，它未經過驗證，但應該是一個適當的日期時間。
- ```DataVersion``` 是必要的
- ```MetadataVersion```是可選的，如果指定，它必須是具有值的字串```"1"```
- ```Data```是可選的，可以是任何 JSON 權杖（數位、字串、布林、陣列、物件）

### <a name="send-batch-of-events-in-custom-schema"></a>發送一批事件（在自訂架構中）

**請求**：``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**回應**： HTTP 200，空負載


**有效負載限制**
- 必須是事件陣列。
- 每個陣列條目都必須是 JSON 物件。
- 沒有其他約束（有效負載大小）。

## <a name="examples"></a>範例

### <a name="set-up-topic-with-eventgrid-schema"></a>使用事件網格架構設置主題
設置主題以要求在**事件網格架構**中發佈事件。

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>使用自訂架構設置主題
設置主題以要求在 中`customschema`發佈事件。

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>使用雲事件架構設置主題
設置主題以要求在 中`cloudeventschema`發佈事件。

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>將 WebHook 設置為目標，在事件網格架構中傳遞的事件
使用此目標型別將事件發送到任何其他模組（承載 HTTP 終結點）或網路/Internet 上任何可定址終結點。

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

屬性的約束`endpointUrl`：
- 它必須是非 null 的。
- 它必須是絕對的 URL。
- 如果在事件網格模組設置中outbound__webhook__HTTPsOnly設置為 true，則它必須僅是 HTTPS。
- 如果outbound__webhook__HTTPsOnly設置為 false，則可以是 HTTP 或 HTTPS。

屬性的約束`eventDeliverySchema`：
- 它必須與訂閱主題的輸入架構匹配。
- 它可以是 Null。 它預設為主題的輸入架構。

### <a name="set-up-iot-edge-as-destination"></a>將 IoT 邊緣設置為目標

使用此目標將事件發送到 IoT 邊緣中心，並受邊緣中心路由/篩選/轉發子系統的影響。

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

### <a name="set-up-event-grid-cloud-as-destination"></a>將事件網格雲設置為目標

使用此目標可以將事件發送到雲中的事件網格 （Azure）。 您需要先在雲中設置一個使用者主題，然後在邊緣創建事件訂閱之前，應將事件發送到該主題。

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

端點Url：
- 它必須是非 null 的。
- 它必須是絕對的 URL。
- 路徑`/api/events`必須在請求 URL 路徑中定義。
- 它必須在`api-version=2018-01-01`查詢字串中。
- 如果在事件網格模組設置中將outbound__eventgrid__HTTPsOnly設置為 true（預設情況下為 true），則它必須僅為 HTTPS。
- 如果outbound__eventgrid__HTTPsOnly設置為 false，則可以是 HTTP 或 HTTPS。
- 如果outbound__eventgrid__allowInvalidHostnames設置為 false（預設情況下為 false），則必須針對以下終結點之一：
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

薩斯基：
- 必須為非空。

主題名稱：
- 如果訂閱.事件交付架構設置為事件GridSchema，則此欄位中的值將放入每個事件的主題欄位中，然後再轉發到雲中的事件網格。
- 如果訂閱.event 傳遞架構設置為自訂事件架構，則將忽略此屬性，並且自訂事件裝載將完全按照收到的格式轉發。

## <a name="set-up-event-hubs-as-a-destination"></a>將事件中心設置為目標

要發佈到事件中心，請設置為`endpointType`並提供： `eventHub`

* 連接字串：通過共用訪問策略生成的特定事件中心的連接字串。

    >[!NOTE]
    > 連接字串必須是特定于實體的。 使用命名空間連接字串將不起作用。 可以通過導航到要在 Azure 門戶中發佈到的特定事件中心，然後按一下**共用訪問策略**來生成新的實體特定連接字串，從而生成實體特定的連接字串。

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

## <a name="set-up-service-bus-queues-as-a-destination"></a>將服務匯流排佇列設置為目標

要發佈到服務匯流排佇列，請設置為`endpointType`和`serviceBusQueue`提供：

* 連接字串：通過共用訪問策略生成的特定服務匯流排佇列的連接字串。

    >[!NOTE]
    > 連接字串必須是特定于實體的。 使用命名空間連接字串將不起作用。 通過導航到要在 Azure 門戶中發佈到的特定服務匯流排佇列，然後按一下**共用訪問策略**以生成新的實體特定連接字串，生成實體特定的連接字串。

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

## <a name="set-up-service-bus-topics-as-a-destination"></a>將服務匯流排主題設置為目標

要發佈到服務匯流排主題，請設置為`endpointType`和`serviceBusTopic`提供：

* 連接字串：通過共用訪問策略生成的特定服務匯流排主題的連接字串。

    >[!NOTE]
    > 連接字串必須是特定于實體的。 使用命名空間連接字串將不起作用。 通過導航到要在 Azure 門戶中發佈到的特定服務匯流排主題，然後按一下**共用訪問策略**以生成新的實體特定連接字串，生成實體特定的連接字串。

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

## <a name="set-up-storage-queues-as-a-destination"></a>將存儲佇列設置為目標

要發佈到存儲佇列，請設置為`endpointType`和`storageQueue`提供：

* 佇列名稱：要發佈到的存儲佇列的名稱。
* 連接字串：存儲佇列中的存儲帳戶的連接字串。

    >[!NOTE]
    > 取消行線事件中心、服務匯流排佇列和服務匯流排主題，用於存儲佇列的連接字串不是特定于實體的。 相反，它必須，但存儲帳戶的連接字串。

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