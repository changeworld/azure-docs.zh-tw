---
title: 匯出您的 Azure IoT Central 資料 |Microsoft Docs
description: 如何將資料從 Azure IoT Central 應用程式匯出至 Azure 事件中樞、Azure 服務匯流排和 Azure Blob 儲存體
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 04/07/2020
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: c83c97aab43b6978922202cc96ff92e1e046a7e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80811636"
---
# <a name="export-iot-data-to-destinations-in-azure"></a>將 IoT 資料匯出至 Azure 中的目的地

*此主題適用於系統管理員。*

本文說明如何使用 Azure IoT Central 中的資料匯出功能。 這項功能可讓您將資料持續匯出至**Azure 事件中樞**、 **Azure 服務匯流排**或**Azure Blob 儲存體**實例。 資料匯出會使用 JSON 格式，而且可以包含遙測、裝置資訊和裝置範本資訊。 將匯出的資料用於：

- 暖路徑深入解析與分析。 此選項包括觸發 Azure 串流分析中的自訂規則、在 Azure Logic Apps 中觸發自訂工作流程，或透過要轉換的 Azure Functions 進行傳遞。
- 冷路徑分析，例如在 Microsoft Power BI 中 Azure Machine Learning 或長期趨勢分析的定型模型。

> [!Note]
> 當您開啟資料匯出時，只會從該時間開始取得資料。 目前，當資料匯出已關閉時，無法取回資料。 若要保留更多歷程記錄資料，請提早開啟資料匯出。

## <a name="prerequisites"></a>先決條件

您必須是 IoT Central 應用程式中的系統管理員，或具有資料匯出許可權。

## <a name="set-up-export-destination"></a>設定匯出目的地

在設定資料匯出之前，您的匯出目的地必須存在。

### <a name="create-event-hubs-namespace"></a>建立事件中樞命名空間

如果您沒有要匯出的現有事件中樞命名空間，請遵循下列步驟：

1. [在 Azure 入口網站中建立新事件中樞命名空間](https://ms.portal.azure.com/#create/Microsoft.EventHub)。 您可以透過 [Azure 事件中樞文件](../../event-hubs/event-hubs-create.md)深入了解。

2. 選擇訂用帳戶。 您可以將資料匯出至與 IoT Central 應用程式位於相同訂用帳戶中的其他訂閱。 在此情況下，您會使用連接字串來連接。

3. 在「事件中樞」命名空間中建立一個事件中樞。 移至您的命名空間，然後選取頂端的 [+ 事件中樞]**** 以建立事件中樞執行個體。

### <a name="create-service-bus-namespace"></a>建立服務匯流排命名空間

如果您沒有要匯出的現有服務匯流排命名空間，請遵循下列步驟：

1. [在 Azure 入口網站中建立新的服務匯流排命名空間](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)。 您可以透過 [Azure 服務匯流排文件](../../service-bus-messaging/service-bus-create-namespace-portal.md)深入了解。
2. 選擇訂用帳戶。 您可以將資料匯出至與 IoT Central 應用程式位於相同訂用帳戶中的其他訂閱。 在此情況下，您會使用連接字串來連接。

3. 若要建立要匯出至的佇列或主題，請移至您的服務匯流排命名空間，然後選取 [ **+ 佇列**] 或 [ **+ 主題**]。

當您選擇服務匯流排做為匯出目的地時，佇列和主題不能啟用會話或重複偵測。 如果啟用這其中一個選項，有些訊息就不會送達到您的佇列或主題中。

### <a name="create-storage-account"></a>建立儲存體帳戶

如果您沒有可匯出的現有 Azure 儲存體帳戶，請遵循下列步驟：

1. [在 Azure 入口網站中建立新儲存體帳戶](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 您可以深入瞭解如何建立新的[Azure Blob 儲存體帳戶](https://aka.ms/blobdocscreatestorageaccount)或[Azure Data Lake Storage v2 儲存體帳戶](../../storage/blobs/data-lake-storage-quickstart-create-account.md)。 資料匯出只能將資料寫入支援區塊 blob 的儲存體帳戶。 下列清單顯示已知的相容儲存體帳戶類型：

    |效能層級|帳戶類型|
    |-|-|
    |Standard|一般用途 V2|
    |Standard|一般用途 V1|
    |Standard|Blob 儲存體|
    |Premium|區塊 Blob 儲存體|

2. 在您的儲存體帳戶中建立容器。 移至您的儲存體帳戶。 在 [Blob 服務]**** 下，選取 [瀏覽 Blob]****。 選取頂端的 [+ 容器]**** 以建立新的容器。

## <a name="set-up-data-export"></a>設定資料匯出

現在您已有目的地可匯出資料，請遵循下列步驟來設定資料匯出。

1. 登入您的 IoT Central 應用程式。

2. 在左窗格中，選取 [**資料匯出**]。

    > [!Tip]
    > 如果您在左窗格中沒有看到 [**資料匯出**]，表示您沒有在應用程式中設定資料匯出的許可權。 請連絡系統管理員來設定資料匯出。

3. 選取右上方的 [ **+ 新增**] 按鈕。 選擇**Azure 事件中樞**、 **Azure 服務匯流排**或**Azure Blob 儲存體**的其中一個作為匯出目的地。 每個應用程式的匯出數目上限為五個。

    ![建立新的資料匯出](media/howto-export-data/new-export-definition.png)

4. 在下拉式清單方塊中，選取您的**事件中樞命名空間**、**服務匯流排命名空間**、**儲存體帳戶命名空間**，或**輸入連接字串**。

    - 您只會在與 IoT Central 應用程式相同的訂用帳戶中看到儲存體帳戶、事件中樞命名空間和服務匯流排命名空間。 如果您想要匯出至此訂用帳戶以外的目的地，請選擇 [**輸入連接字串**]，然後查看下一個步驟。
    - 針對使用免費定價方案所建立的應用程式，設定資料匯出的唯一方式是透過連接字串。 免費定價方案上的應用程式沒有相關聯的 Azure 訂用帳戶。

    ![建立新的事件中樞](media/howto-export-data/export-event-hub.png)

5. (選擇性) 如果您選擇 [輸入連接字串]****，就會顯示一個可供您貼上連接字串的新方塊。 取得下列項目的連接字串：
    - 事件中樞或服務匯流排，請移至 Azure 入口網站中的命名空間：
        - 在 [**設定**] 底下，選取 [**共用存取原則**]
        - 選擇預設的 [RootManageSharedAccessKey]****，或建立新的原則
        - 複製主要連接字串或次要連接字串
    - 儲存體帳戶，請移至 Azure 入口網站中的儲存體帳戶：
        - 在 [**設定**] 底下，選取 [**存取金鑰**]
        - 複製 key1 連接字串或 key2 連接字串

6. 從下拉式清單方塊中選擇 [事件中樞]、[佇列]、[主題] 或 [容器]。

7. 在 [**要匯出的資料**] 底下，將 [類型] 設定為 [**開啟**]，以選擇要匯出的資料類型。

8. 若要開啟資料匯出，請確定已**開啟**[**啟用**] 切換。 選取 [儲存]  。

9. 幾分鐘後，您的資料就會出現在您選擇的目的地。

## <a name="export-contents-and-format"></a>匯出內容和格式

匯出的遙測資料包含裝置傳送至 IoT Central 的全部訊息，而不只是遙測值本身。 匯出的裝置資料包含所有裝置的屬性和中繼資料變更，而匯出的裝置範本則包含所有裝置範本的變更。

對於事件中樞和服務匯流排，資料會以近乎即時的方式匯出。 資料位於`body`屬性中，而且是 JSON 格式。 如需範例，請參閱下文。

對於 Blob 儲存體，資料會每分鐘匯出一次，每個檔案都包含自上次匯出檔案之後的變更批次。 匯出的資料會以 JSON 格式放在三個資料夾中。 您儲存體帳戶中的預設路徑為：

- 遙測： _{container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 裝置： _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 裝置範本： _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

若要流覽 Azure 入口網站中匯出的檔案，請流覽至該檔案，然後選取 [**編輯 blob** ] 索引標籤。

## <a name="telemetry"></a>遙測

針對事件中樞和服務匯流排，IoT Central 會在收到來自裝置的訊息之後，快速匯出新訊息。 每個匯出的訊息都會包含裝置以 JSON 格式在 body 屬性中傳送的完整訊息。

針對 Blob 儲存體，每分鐘會批次處理並匯出訊息一次。 匯出的檔案會使用與[IoT 中樞訊息路由傳送](../../iot-hub/tutorial-routing.md)至 blob 儲存體所匯出的訊息檔案相同的格式。

> [!NOTE]
> 對於 Blob 儲存體，請確定您的裝置正在傳送`contentType: application/JSON`具有和`contentEncoding:utf-8` （或`utf-16` `utf-32`）的訊息。 如需範例，請參閱[IoT 中樞檔](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body)。

傳送遙測的裝置會以裝置識別碼來表示（請參閱下列各節）。 若要取得裝置的名稱，請匯出裝置資料，並使用符合裝置訊息**deviceId**的**connectionDeviceId** ，將每個訊息相互關聯。

下列範例顯示從事件中樞或服務匯流排佇列或主題接收的訊息：

```json
{
  "temp":81.129693132351775,
  "humid":59.488071477541247,
  "EventProcessedUtcTime":"2020-04-07T09:41:15.2877981Z",
  "PartitionId":0,
  "EventEnqueuedUtcTime":"2020-04-07T09:38:32.7380000Z"
}
```

此訊息不包含傳送裝置的裝置識別碼。

若要從 Azure 串流分析查詢的訊息資料中取出裝置識別碼，請使用[GetMetadataPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue)函數。 如需範例，請參閱[使用串流分析、Azure Functions 和 SendGrid 以自訂規則擴充 Azure IoT Central](./howto-create-custom-rules.md)中的查詢。

若要取出 Azure Databricks 或 Apache Spark 工作區中的裝置識別碼，請使用[systemProperties](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)。 如需範例，請參閱[使用 Azure Databricks 以自訂分析延伸 Azure IoT Central](./howto-create-custom-analytics.md)中的 Databricks 工作區。

下列範例顯示匯出至 blob 儲存體的記錄：

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

## <a name="devices"></a>裝置

快照中的每個訊息或記錄都代表裝置的一或多項變更，以及自上次匯出訊息之後的裝置和雲端屬性。 訊息包含：

- IoT Central 中裝置的 `id`
- 裝置的 `displayName`
- 中的裝置範本識別碼`instanceOf`
- `simulated`旗標，如果裝置是模擬裝置，則為 true
- `provisioned`旗標，如果裝置已布建，則為 true
- `approved`旗標，如果裝置已核准傳送資料則為 true
- 屬性值
- `properties`包括裝置和雲端屬性值

未匯出已刪除的裝置。 目前，在匯出的訊息中並沒有任何適用於已刪除之裝置的指標。

針對事件中樞和服務匯流排，IoT Central 會以近乎即時的方式，將包含裝置資料的訊息傳送至事件中樞或服務匯流排佇列或主題。

對於 Blob 儲存體，新的快照集包含自上一次寫入後的所有變更，每分鐘會匯出一次。

下列範例訊息顯示事件中樞或服務匯流排佇列或主題中的裝置和屬性資料的相關資訊：

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
},
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

此快照集是在 Blob 儲存體中顯示裝置和屬性資料的範例訊息。 匯出的檔案包含每筆記錄一行。

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>裝置範本

每個訊息或快照記錄都代表自上次匯出訊息之後，已發行之裝置範本的一或多項變更。 在每個訊息或記錄中傳送的資訊包括：

- `id`符合上述裝置資料流程`instanceOf`之的裝置範本的
- 裝置範本的 `displayName`
- 裝置`capabilityModel` ，包括其`interfaces`、遙測、屬性和命令定義
- `cloudProperties`定義
- 覆寫和初始值，以內嵌`capabilityModel`

刪除的裝置範本不會匯出。 目前，在匯出的訊息中並沒有任何適用於已刪除之裝置範本的指標。

對於事件中樞和服務匯流排，IoT Central 會以近乎即時的方式，將包含裝置範本資料的訊息傳送至事件中樞或服務匯流排佇列或主題。

對於 Blob 儲存體，新的快照集包含自上一次寫入後的所有變更，每分鐘會匯出一次。

這個範例會顯示事件中樞或服務匯流排佇列或主題中裝置範本資料的相關訊息：

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

此範例快照會顯示訊息，其中包含 Blob 儲存體中的裝置和屬性資料。 匯出的檔案包含每筆記錄一行。

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```

## <a name="data-format-change-notice"></a>資料格式變更通知

> [!Note]
> 遙測資料流程資料格式不會受到這項變更的影響。 只有裝置和裝置範本的資料流程會受到影響。

如果您在預覽應用程式中有現有的資料匯出，並已開啟 [*裝置*和*裝置範本*] 串流，請將您的匯出更新為**2020 年6月 30**日。 這項需求適用于匯出至 Azure Blob 儲存體、Azure 事件中樞和 Azure 服務匯流排。

從2020年2月3日開始，已啟用裝置和裝置範本之應用程式中的所有新匯出都會有上述的資料格式。 在此日期之前建立的所有匯出都會保留舊的資料格式，直到2020年6月30日為止，這些匯出會自動遷移至新的資料格式。 新的資料格式符合 IoT Central 公用 API 中的[裝置](https://docs.microsoft.com/rest/api/iotcentral/devices/get)、[裝置屬性](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties)、[裝置雲端屬性](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties)和[裝置範本](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get)物件。

針對**裝置**，舊資料格式與新資料格式之間的顯著差異包括：
- `@id`已移除`deviceId`裝置的已重新命名為`id` 
- `provisioned`已新增旗標來描述裝置的布建狀態
- `approved`已新增旗標來描述裝置的核准狀態
- `properties`包括裝置和雲端屬性、符合公用 API 中的實體

針對**裝置範本**，舊資料格式與新資料格式之間的顯著差異包括：

- `@id`裝置範本已重新命名為`id`
- `@type`裝置範本的已重新命名為`types`，且現在是陣列

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>裝置（從2020年2月3日起已淘汰的格式）

```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>裝置範本（從2020年2月3日起已淘汰的格式）

```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何將資料匯出至 Azure 事件中樞、Azure 服務匯流排和 Azure Blob 儲存體，請繼續進行下一個步驟：

> [!div class="nextstepaction"]
> [如何建立 webhook](./howto-create-webhooks.md)
