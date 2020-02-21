---
title: 匯出您的 Azure IoT Central 資料 |Microsoft Docs
description: 如何將資料從您的 Azure IoT Central 應用程式匯出至 Azure 事件中樞、Azure 服務匯流排和 Azure Blob 儲存體
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 0386897b6cecc27781626cfecd6f1f5f8a3752e4
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524378"
---
# <a name="export-iot-data-to-destinations-in-azure"></a>將 IoT 資料匯出至 Azure 中的目的地

*此主題適用於系統管理員。*

本文說明如何使用 Azure IoT Central 中的連續資料匯出功能，將您的資料匯出至**Azure 事件中樞**、 **Azure 服務匯流排**或**Azure Blob 儲存體**實例。 資料會以 JSON 格式匯出，而且可以包含遙測、裝置資訊和裝置範本資訊。 將匯出的資料用於：

- 暖路徑深入解析與分析。 此選項包括觸發 Azure 串流分析中的自訂規則、在 Azure Logic Apps 中觸發自訂工作流程，或透過要轉換的 Azure Functions 進行傳遞。
- 冷路徑分析，例如在 Microsoft Power BI 中 Azure Machine Learning 或長期趨勢分析的定型模型。

> [!Note]
> 當您開啟「連續資料匯出」時，只會取得從該時刻的資料。 目前，無法擷取「連續資料匯出」時的資料。 若要保留更多歷史資料，請儘早開啟「連續資料匯出」。

## <a name="prerequisites"></a>必要條件

您必須是 IoT Central 應用程式中的系統管理員，或具有資料匯出許可權。

## <a name="set-up-export-destination"></a>設定匯出目的地

在設定連續資料匯出之前，您的匯出目的地必須存在。

### <a name="create-event-hubs-namespace"></a>建立事件中樞命名空間

如果您沒有要匯出的現有事件中樞命名空間，請遵循下列步驟：

1. [在 Azure 入口網站中建立新事件中樞命名空間](https://ms.portal.azure.com/#create/Microsoft.EventHub)。 您可以透過 [Azure 事件中樞文件](../../event-hubs/event-hubs-create.md)深入了解。

2. 選擇訂用帳戶。 您可以將資料匯出至與 IoT Central 應用程式不在相同訂用帳戶中的其他訂閱。 在此情況下，您會使用連接字串來連接。

3. 在「事件中樞」命名空間中建立一個事件中樞。 移至您的命名空間，然後選取頂端的 [+ 事件中樞] 以建立事件中樞執行個體。

### <a name="create-service-bus-namespace"></a>建立服務匯流排命名空間

如果您沒有要匯出的現有服務匯流排命名空間，請遵循下列步驟：

1. [在 Azure 入口網站中建立新的服務匯流排命名空間](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)。 您可以透過 [Azure 服務匯流排文件](../../service-bus-messaging/service-bus-create-namespace-portal.md)深入了解。
2. 選擇訂用帳戶。 您可以將資料匯出至與 IoT Central 應用程式不在相同訂用帳戶中的其他訂閱。 在此情況下，您會使用連接字串來連接。

3. 移至您的「服務匯流排」命名空間，然後選取頂端的 [+ 佇列] 或 [+ 主題] 以建立要作為匯出目的地的佇列或主題。

當您選擇服務匯流排做為匯出目的地時，佇列和主題不能啟用會話或重複偵測。 如果啟用這其中一個選項，有些訊息就不會送達到您的佇列或主題中。

### <a name="create-storage-account"></a>建立儲存體帳戶

如果您沒有可匯出的現有 Azure 儲存體帳戶，請遵循下列步驟：

1. [在 Azure 入口網站中建立新儲存體帳戶](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 您可以深入瞭解如何建立新的[Azure Blob 儲存體帳戶](https://aka.ms/blobdocscreatestorageaccount)或[Azure Data Lake Storage v2 儲存體帳戶](../../storage/blobs/data-lake-storage-quickstart-create-account.md)。 資料匯出只能將資料寫入支援區塊 blob 的儲存體帳戶。 下列是已知相容類型的儲存體帳戶清單： 

    |效能層級|帳戶類型|
    |-|-|
    |標準|一般用途 V2|
    |標準|一般用途 V1|
    |標準|Blob 儲存體|
    |高階|封鎖 Blob 儲存體|

2. 在您的儲存體帳戶中建立容器。 移至您的儲存體帳戶。 在 [Blob 服務] 下，選取 [瀏覽 Blob]。 選取頂端的 [+ 容器] 以建立新的容器。

## <a name="set-up-continuous-data-export"></a>設定連續資料匯出

現在您已有目的地可匯出資料，請遵循下列步驟來設定連續資料匯出。

1. 登入您的 IoT Central 應用程式。

2. 在左窗格中，選取 [**資料匯出**]。

    > [!Note]
    > 如果您在左窗格中沒有看到 [資料匯出]，表示您沒有在應用程式中設定資料匯出的許可權。 請連絡系統管理員來設定資料匯出。

3. 選取右上方的 [ **+ 新增**] 按鈕。 選擇**Azure 事件中樞**、 **Azure 服務匯流排**或**Azure Blob 儲存體**的其中一個作為匯出目的地。 每個應用程式的匯出數目上限為五個。

    ![建立新的連續資料匯出](media/howto-export-data/new-export-definition.png)

4. 在下拉式清單方塊中，選取您的**事件中樞命名空間**、**服務匯流排命名空間**、**儲存體帳戶命名空間**，或**輸入連接字串**。

    - 您只會在與 IoT Central 應用程式相同的訂用帳戶中看到儲存體帳戶、事件中樞命名空間和服務匯流排命名空間。 如果您想要匯出到此訂用帳戶外的目的地，請選擇 [輸入連接字串]，然後參閱步驟 5。
    - 針對使用免費定價方案所建立的應用程式，設定連續資料匯出的唯一方式是透過連接字串。 免費定價方案上的應用程式沒有相關聯的 Azure 訂用帳戶。

    ![建立新的事件中樞](media/howto-export-data/export-event-hub.png)

5. (選擇性) 如果您選擇 [輸入連接字串]，就會顯示一個可供您貼上連接字串的新方塊。 取得下列項目的連接字串：
    - 事件中樞或服務匯流排，請移至 Azure 入口網站中的命名空間。
        - 在 [**設定**] 底下，選取 [**共用存取原則**]
        - 選擇預設的 [RootManageSharedAccessKey]，或建立新的原則
        - 複製主要連接字串或次要連接字串
    - 儲存體帳戶，請移至 Azure 入口網站中的儲存體帳戶：
        - 在 [**設定**] 底下，選取 [**存取金鑰**]
        - 複製 key1 連接字串或 key2 連接字串

6. 從下拉式清單方塊中選擇 [事件中樞]、[佇列]、[主題] 或 [容器]。

7. 在 [**要匯出的資料**] 底下，將 [類型] 設定為 [**開啟**]，以選擇要匯出的資料類型。

8. 若要開啟連續資料匯出，請確定已**開啟**[**啟用**] 切換。 選取 [儲存]。

9. 幾分鐘後，您的資料就會出現在您選擇的目的地。

## <a name="export-contents-and-format"></a>匯出內容和格式

匯出的遙測資料包含裝置傳送至 IoT Central 的全部訊息，而不只是遙測值本身。 匯出的裝置資料包含所有裝置的屬性和中繼資料變更，而匯出的裝置範本則包含所有裝置範本的變更。

對於事件中樞和服務匯流排，資料會以近乎即時的方式匯出。 資料位於 body 屬性中，並採用 JSON 格式（如需範例，請參閱下文）。

針對 Blob 儲存體，每分鐘會匯出一次資料，每個檔案都包含自上次匯出檔案之後的變更批次。 匯出的資料會以 JSON 格式放在三個資料夾中。 您儲存體帳戶中的預設路徑為：

- 遙測： _{container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 裝置： _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 裝置範本： _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

您可以流覽至檔案，然後選擇 [**編輯 blob** ] 索引標籤，以流覽 Azure 入口網站中匯出的檔案。


## <a name="telemetry"></a>遙測

針對事件中樞和服務匯流排，IoT Central 從裝置接收訊息之後，會快速匯出新的訊息，而每個匯出的訊息會包含裝置以 JSON 格式在 body 屬性中傳送的完整訊息。

針對 Blob 儲存體，每分鐘會批次處理並匯出訊息一次。 匯出的檔案會使用與[IoT 中樞訊息路由傳送](../../iot-hub/tutorial-routing.md)至 blob 儲存體所匯出的訊息檔案相同的格式。 

> [!NOTE]
> 針對 Blob 儲存體，請確定您的裝置正在傳送具有 `contentType: application/JSON` 和 `contentEncoding:utf-8` （或 `utf-16``utf-32`）的訊息。 如需範例，請參閱[IoT 中樞檔](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body)。

傳送遙測的裝置會以裝置識別碼來表示（請參閱下列各節）。 若要取得裝置的名稱，請匯出裝置資料，並使用符合裝置訊息**deviceId**的**connectionDeviceId** ，將每個訊息相互關聯。

這是在事件中樞或服務匯流排的佇列或主題中收到的範例訊息。

```json
{
  "body":{
    "temp":67.96099945281145,
    "humid":58.51139305465015,
    "pm25":36.91162432340187
  },
  "annotations":{
    "iothub-connection-device-id":"<deviceId>",
    "iothub-connection-auth-method":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id":"<generationId>",
    "iothub-enqueuedtime":1539381029965,
    "iothub-message-source":"Telemetry",
    "x-opt-sequence-number":25325,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539381030200
  },
  "sequenceNumber":25325,
  "enqueuedTimeUtc":"2018-10-12T21:50:30.200Z",
  "offset":"<offset>",
  "properties":{
    "content_type":"application/json",
    "content_encoding":"utf-8"
  }
}
```

這是匯出至 blob 儲存體的範例記錄：

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

快照中的每個訊息或記錄都代表裝置的一或多項變更，以及自上次匯出訊息之後的裝置和雲端屬性。 這包括：

- IoT Central 中裝置的 `id`
- 裝置的 `displayName`
- `instanceOf` 中的裝置範本識別碼
- `simulated` 旗標，如果裝置是模擬裝置，則為 true
- `provisioned` 旗標，如果已布建裝置，則為 true
- `approved` 旗標，如果裝置已核准傳送資料，則為 true
- 屬性值
- `properties` 包括裝置和雲端屬性值

未匯出已刪除的裝置。 目前，在匯出的訊息中並沒有任何適用於已刪除之裝置的指標。

對於事件中樞和服務匯流排，包含裝置資料的訊息會以近乎即時的方式傳送到您的事件中樞或服務匯流排佇列或主題，因為它出現在 IoT Central 中。 

針對 Blob 儲存體，包含自最後一個寫入後所有變更的新快照集會每分鐘匯出一次。

這是有關事件中樞或服務匯流排的佇列或主題中的裝置和屬性資料的範例訊息：

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

這是包含 Blob 儲存體中的裝置和屬性資料的範例快照集。 匯出的檔案包含每筆記錄一行。

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

- 符合上述裝置串流 `instanceOf` 的裝置範本 `id`
- 裝置範本的 `displayName`
- 裝置 `capabilityModel` 包括其 `interfaces`，以及遙測、屬性和命令定義
- `cloudProperties` 定義
- 覆寫和初始值，以內嵌 `capabilityModel`

刪除的裝置範本不會匯出。 目前，在匯出的訊息中並沒有任何適用於已刪除之裝置範本的指標。

針對事件中樞和服務匯流排，包含裝置範本資料的訊息會以近乎即時的方式傳送到您的事件中樞或服務匯流排佇列或主題，因為它出現在 IoT Central 中。 

針對 Blob 儲存體，包含自最後一個寫入後所有變更的新快照集會每分鐘匯出一次。

這是有關事件中樞或服務匯流排的佇列或主題中的裝置範本資料的範例訊息：

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

這是包含 Blob 儲存體中的裝置和屬性資料的範例快照集。 匯出的檔案包含每筆記錄一行。

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

如果您在預覽應用程式中已開啟 [*裝置*] 和 [*裝置範本*] 資料流程的現有資料匯出，則必須將您的匯出更新為**2020 年6月 30**日。 這適用于匯出至 Azure Blob 儲存體、Azure 事件中樞和 Azure 服務匯流排。

從2020年2月3日開始，已啟用裝置和裝置範本之應用程式中的所有新匯出都會有上述的資料格式。 在此之前建立的所有匯出都會維持舊的資料格式，直到2020年6月30日為止，之後這些匯出會自動遷移至新的資料格式。 新的資料格式符合 IoT Central 公用 API 中的[裝置](https://docs.microsoft.com/rest/api/iotcentral/devices/get)、[裝置屬性](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties)、[裝置雲端屬性](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties)和[裝置範本](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get)物件。 
 
針對**裝置**，舊資料格式與新資料格式之間的顯著差異包括：
- 已移除裝置的 `@id`，`deviceId` 重新命名為 `id` 
- 已新增 `provisioned` 旗標來描述裝置的布建狀態
- 新增 `approved` 旗標，以描述裝置的核准狀態
- 包含裝置和雲端屬性的 `properties`，符合公用 API 中的實體

針對**裝置範本**，舊資料格式與新資料格式之間的顯著差異包括：

- 裝置範本的 `@id` 會重新命名為 `id`
- 裝置範本的 `@type` 會重新命名為 `types`，而現在是陣列

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

既然您已經知道如何將資料匯出至 Azure 事件中樞、Azure 服務匯流排和 Azure Blob 儲存體，請繼續進行下一個步驟：

> [!div class="nextstepaction"]
> [如何建立 webhook](./howto-create-webhooks.md)
