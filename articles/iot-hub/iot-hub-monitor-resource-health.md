---
title: 監視 Azure IoT 中樞的健康情況 | Microsoft Docs
description: 使用「Azure 監視器」和「Azure 資源健康狀態」來監視您的「IoT 中樞」並快速診斷問題
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: robinsh
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: Technical Support'
- devx-track-csharp
ms.openlocfilehash: 100f87b8a13fb424706c3b5ec13268cd3ba42bbe
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438393"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>監視 Azure IoT 中樞的健康情況並快速診斷問題

實作「Azure IoT 中樞」的企業會期望其資源有可靠的效能。 為了協助您對作業維持密切的監看，「IoT 中樞」已與 [Azure 監視器](../azure-monitor/index.yml)和 [Azure 資源健康狀態](../service-health/resource-health-overview.md)完全整合。 這兩項服務可為您提供所需的資料，讓您的 IoT 解決方案保持正常運作狀態。

「Azure 監視器」是您所有 Azure 服務之監視和記錄功能的單一來源。 您可以將 Azure 監視器產生的診斷記錄傳送至 Azure 監視器記錄、事件中樞或 Azure 儲存體以進行自訂處理。 Azure 監視器的計量和診斷設定可讓您檢視資源的效能。 請繼續閱讀本文來了解如何搭配您的 IoT 中樞來[使用 Azure 監視器](#use-azure-monitor)。 

> [!IMPORTANT]
> 不保證使用 Azure 監視器診斷記錄透過 IoT 中樞服務發出的事件是可靠的或按順序的。 某些事件可能會遺失，或未按順序傳遞。 診斷記錄也不是即時的，而且它可能需要幾分鐘的時間才能將事件記錄到您所選擇的目的地。

Azure 資源健康情況可協助您進行診斷，並在 Azure 問題影響您的資源時取得支援。 儀表板會針對您的每個 IoT 中樞提供目前和過去的健康情況狀態。 請繼續閱讀本文底部的一節，以瞭解如何搭配 [使用 Azure 資源健康狀態](#use-azure-resource-health) 與 IoT 中樞。 

「IoT 中樞」也提供自己的計量，可供您用來了解 IoT 資源的狀態。 若要深入了解，請參閱[了解 IoT 中樞計量](iot-hub-metrics.md)。

## <a name="use-azure-monitor"></a>使用 Azure 監視器

「Azure 監視器」提供 Azure 資源的診斷資訊，這意謂著您可以監視在 IoT 中樞內進行的作業。

若要深入了解「Azure 監視器」所監視的特定計量和事件，請參閱 [Azure 監視器支援的計量](../azure-monitor/platform/metrics-supported.md)和[Azure 診斷記錄支援的服務、結構描述和類別](../azure-monitor/platform/diagnostic-logs-schema.md)。

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>了解記錄

「Azure 監視器」會追蹤「IoT 中樞」中進行的各種不同作業。 每個類別都有一個結構描述，當中定義該類別中事件的回報方式。

#### <a name="connections"></a>連接

連線類別會追蹤來自 IoT 中樞的裝置連線和中斷連線事件以及錯誤。 此類別對於識別未經授權的連線嘗試，及/或在遺失裝置連線時發出警示非常有用。

> [!NOTE]
> 針對可靠的裝置連線狀態，勾選 [裝置活動訊號](iot-hub-devguide-identity-registry.md#device-heartbeat)。

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-commands"></a>雲端到裝置的命令

雲端到裝置的命令類別會追蹤在 IoT 中樞發生，且與雲端到裝置訊息管線相關的錯誤。 此類別包括發生自下列動作的錯誤：

* 傳送雲端到裝置訊息 (例如未經授權的寄件者錯誤)、
* 接收雲端到裝置訊息 (例如已超過傳遞計數錯誤)，以及
* 接收雲端到裝置訊息意見反應 (例如意見反應已過期的錯誤)。

當雲端到裝置訊息成功傳遞但裝置未正確處理時，此類別不會攔截錯誤。

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-identity-operations"></a>裝置身分識別作業

裝置身分識別作業類別會追蹤您嘗試在其 IoT 中樞的身分識別登錄中建立、更新或刪除項目時所發生的錯誤。 佈建案例就很適合追蹤此類別。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="routes"></a>路由

[訊息路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)類別會追蹤訊息路由評估期間發生的錯誤，以及 IoT 中樞所察覺的端點健康情況。 此類別包括下列事件，例如：

* 規則評估為「未定義」、
* IoT 中樞將端點標示為無效，或
* 從端點收到的任何錯誤。

此類別不包含有關訊息本身的特定錯誤 (例如裝置節流錯誤)，這些是在「裝置遙測」類別下報告。

```json
{
    "records":
    [
        {
            "time":"2019-12-12T03:25:14Z",
            "resourceId":"/SUBSCRIPTIONS/91R34780-3DEC-123A-BE2A-213B5500DFF0/RESOURCEGROUPS/ANON-TEST/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/ANONHUB1",
            "operationName":"endpointUnhealthy",
            "category":"Routes",
            "level":"Error",
            "resultType":"403004",
            "resultDescription":"DeviceMaximumQueueDepthExceeded",
            "properties":"{\"deviceId\":null,\"endpointName\":\"anon-sb-1\",\"messageId\":null,\"details\":\"DeviceMaximumQueueDepthExceeded\",\"routeName\":null,\"statusCode\":\"403\"}",
            "location":"westus"
        }
    ]
}
```

以下是路由診斷記錄的更多詳細資料：

* [路由診斷記錄錯誤碼的清單](troubleshoot-message-routing.md#diagnostics-error-codes)
* [路由診斷記錄 operationNames 的清單](troubleshoot-message-routing.md#diagnostics-operation-names)

#### <a name="device-telemetry"></a>裝置遙測

裝置遙測類別會追蹤在 IoT 中樞發生，且與遙測管線相關的錯誤。 此類別包括在傳送遙測事件 (例如節流) 和接收遙測事件 (例如未經授權的讀取器) 時所發生的錯誤。 這個類別無法捕捉裝置本身所執行之程式碼所造成的錯誤。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

#### <a name="file-upload-operations"></a>檔案上傳作業

檔案上傳類別會追蹤在 IoT 中樞發生且與檔案上傳功能相關的錯誤。 此類別包括︰

* SAS URI 所發生的錯誤，例如當它在裝置通知中樞已完成上傳之前就到期時。

* 裝置所報告的失敗上傳。

* IoT 中樞通知訊息建立期間在儲存體中找不到檔案時所發生的錯誤。

此類別無法捕捉直接發生在裝置將檔案上傳到儲存體時的錯誤。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-twin-operations"></a>雲端到裝置對應項作業

雲端到裝置對應項作業類別會追蹤裝置對應項上服務起始的事件。 這些作業可能包括取得對應項、更新或取代標記，以及更新或取代所需的屬性。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-to-cloud-twin-operations"></a>裝置到雲端對應項作業

裝置到雲端對應項作業類別會追蹤裝置對應項上裝置起始的事件。 這些作業可能包括取得對應項、更新回報的屬性，以及訂閱所需的屬性。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="twin-queries"></a>對應項查詢

對應項查詢類別會針對在雲端中起始之裝置對應項的查詢要求，進行回報。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="jobs-operations"></a>作業的操作

作業操作類別會針對用來更新裝置對應項或對多部裝置叫用直接方法的作業要求，進行回報。 這些要求是在雲端中起始的。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="direct-methods"></a>直接方法

直接方法類別會追蹤傳送給個別裝置的「要求-回應」互動。 這些要求是在雲端中起始的。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="distributed-tracing-preview"></a>分散式追蹤 (預覽)

分散式追蹤類別會追蹤相互關聯識別碼，以尋找帶有追蹤內容標頭的訊息。 若要完整啟用這些記錄，您必須更新用戶端程式代碼，方法是遵循 [Iot 中樞分散式追蹤的端對端分析和診斷 iot 應用程式 (預覽) ](iot-hub-distributed-tracing.md)。

請注意， `correlationId` 符合 [W3C 追蹤內容](https://github.com/w3c/trace-context) 提案，其中包含和 `trace-id` `span-id` 。

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT 中樞 D2C (裝置到雲端) 記錄

當包含有效追蹤屬性的訊息抵達 IoT 中樞，IoT 中樞便會記下這個記錄。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

在這裡，`durationMs` 不會進行計算，因為 IoT 中樞的時鐘可能未與裝置的時鐘同步，因此計算持續時間可能會產生誤導。 建議您使用 `properties` 區段中的時間戳記來撰寫邏輯，以擷取暴增的裝置到雲端延遲。

| 屬性 | 類型 | 描述 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | 整數 | 裝置到雲端訊息的大小 (以位元組為單位) |
| **deviceId** | 由 ASCII 7 位元英數字元組成的字串 | 裝置的身分識別 |
| **callerLocalTimeUtc** | UTC 時間戳記 | 裝置本機時鐘所報告的訊息建立時間 |
| **calleeLocalTimeUtc** | UTC 時間戳記 | IoT 中樞服務端時鐘所報告、訊息抵達 IoT 中樞閘道的時間 |

##### <a name="iot-hub-ingress-logs"></a>IoT 中樞輸入記錄

當包含有效追蹤屬性的訊息寫入到內部或內建的事件中樞時，IoT 中樞便會記下這個記錄。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

在 `properties` 區段中，此記錄檔包含有關訊息輸入的其他資訊。

| 屬性 | 類型 | 描述 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | String | 可為 true 或 false，會指出 IoT 中樞是否已啟用訊息路由 |
| **parentSpanId** | String | 父代訊息的[範圍識別碼](https://w3c.github.io/trace-context/#parent-id)，在此案例中會是 D2C 訊息追蹤 |

##### <a name="iot-hub-egress-logs"></a>IoT 中樞輸出記錄

當[路由](iot-hub-devguide-messages-d2c.md)已啟用且有訊息寫入到[端點](iot-hub-devguide-endpoints.md)時，IoT 中樞便會記下這個記錄。 如果路由未啟用，IoT 中樞則不會記下這個記錄。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

在 `properties` 區段中，此記錄檔包含有關訊息輸入的其他資訊。

| 屬性 | 類型 | 描述 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **點** | String | 路由端點的名稱 |
| **endpointType** | String | 路由端點的類型 |
| **parentSpanId** | String | 父代訊息的[範圍識別碼](https://w3c.github.io/trace-context/#parent-id)，在此案例中會是 IoT 中樞輸入訊息追蹤 |

#### <a name="configurations"></a>組態

IoT 中樞設定記錄會追蹤自動裝置管理功能集的事件和錯誤。

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>裝置串流 (預覽) 

裝置串流類別會追蹤傳送給個別裝置的要求-回應互動。

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

### <a name="sdk-version"></a>SDK 版本

某些作業會傳回 `sdkVersion` 其物件中的屬性 `properties` 。 針對這些作業，當裝置或後端應用程式使用其中一個 Azure IoT Sdk 時，此屬性會包含所使用之 SDK 的相關資訊、SDK 版本，以及 SDK 執行所在的平臺。 下列範例顯示 `sdkVersion` `deviceConnect` 使用 Node.js 裝置 SDK 時，為作業發出的屬性： `"azure-iot-device/1.17.1 (node v10.16.0; Windows_NT 10.0.18363; x64)"` 。 以下是針對 .NET (c # ) SDK 所發出的值範例： `".NET/1.21.2 (.NET Framework 4.8.4200.0; Microsoft Windows 10.0.17763 WindowsProduct:0x00000004; X86)"` 。

下表顯示用於不同 Azure IoT Sdk 的 SDK 名稱：

| SdkVersion 屬性中的 SDK 名稱 | 語言 |
|----------|----------|
| .NET | .NET (C#) |
| microsoft azure. 裝置 | .NET (c # ) service SDK |
| microsoft azure. 用戶端 | .NET (c # ) 裝置 SDK |
| >iothubclient | C 或 Python v1 (已淘汰) 裝置 SDK |
| iothubserviceclient | C 或 Python v1 (已淘汰) service SDK |
| azure-iot-裝置-iothub-.py | Python 裝置 SDK |
| azure-iot-device | Node.js 裝置 SDK |
| azure-iothub | Node.js 服務 SDK |
| iothub-java-用戶端 | JAVA 裝置 SDK |
| iothub.. a. sdk | JAVA 服務 SDK |
| .com. iot-裝置-用戶端 | JAVA 裝置 SDK |
| .com. iot-服務-用戶端 | JAVA 服務 SDK |
| C | 內嵌 C |
| C + (OSSimplified = Azure RTO)  | Azure RTOS |

當您針對診斷記錄執行查詢時，可以解壓縮 SDK 版本屬性。 下列查詢會從連接事件所傳回的屬性中，解壓縮 SDK 版本屬性 (和裝置識別碼) 。 這兩個屬性會寫入結果中，以及事件的時間和裝置所連線之 IoT 中樞的資源識別碼。

```kusto
// SDK version of devices
// List of devices and their SDK versions that connect to IoT Hub
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s) 
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
| distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
```

### <a name="read-logs-from-azure-event-hubs"></a>從 Azure 事件中樞讀取記錄

在透過診斷設定來設定事件記錄功能之後，您可以建立會將記錄讀出的應用程式，以便根據記錄中的資訊來採取行動。 以下範例程式碼會從事件中樞擷取記錄：

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="use-azure-resource-health"></a>使用 Azure 資源健康狀態

使用「Azure 資源健康狀態」來監視您的 IoT 中樞是否已啟動並執行。 您也可以了解區域性服務中斷是否對您的 IoT 中樞造成影響。 若要了解有關您「Azure IoT 中樞」健全狀態的特定詳細資料，建議您[使用 Azure 監視器](#use-azure-monitor)。

「Azure IoT 中樞」會指出區域層級的健康情況。 如果有區域性服務中斷影響到您的 IoT 中樞，健全狀態就會顯示為 [不明]****。 若要深入了解，請參閱 [Azure 資源健康狀態中的資源類型和健康狀態檢查](../service-health/resource-health-checks-resource-types.md)。

若要檢查您 IoT 中樞的健康情況，請依照下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 流覽至**服務健康**情況  >  **資源健康狀態**。

3. 從下拉式方塊中選取您的訂用帳戶，然後選取 [ **IoT 中樞** ] 作為資源類型。

若要深入瞭解如何解讀健康情況資料，請參閱 [Azure 資源健康狀態總覽](../service-health/resource-health-overview.md)。

## <a name="next-steps"></a>接下來的步驟

* [了解 IoT 中樞計量](iot-hub-metrics.md)
* [搭配連接 IoT 中樞和信箱的 Azure Logic Apps 進行 IoT 遠端監視和通知](iot-hub-monitoring-notifications-with-azure-logic-apps.md)
