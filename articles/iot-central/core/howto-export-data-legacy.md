---
title: 從 Azure IoT Central (舊版) 匯出資料 |Microsoft Docs
description: 如何將 Azure IoT Central 應用程式中的資料匯出至 Azure 事件中樞、Azure 服務匯流排和 Azure Blob 儲存體
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/25/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 1202e46f2ea12db62062ac50b8e83b51fe9e5ca0
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89428118"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-legacy"></a>使用資料匯出 (舊版) 將 IoT 資料匯出至雲端目的地

> [!Note]
> 本文說明 IoT Central 中的舊版資料匯出功能。
>
> - 如需新的預覽資料匯出功能的相關資訊，請參閱 [使用資料匯出 (預覽) 將 IoT 資料匯出至雲端目的地 ](./howto-export-data.md)。
> - 若要瞭解預覽資料匯出與舊版資料匯出功能之間的差異，請參閱 [比較表](./howto-export-data.md#comparison-of-legacy-data-export-and-preview-data-export)。

本文說明如何使用 Azure IoT Central 中的資料匯出功能。 這項功能可讓您將資料持續匯出至 **Azure 事件中樞**、 **Azure 服務匯流排**或 **Azure Blob 儲存體** 實例。 資料匯出會使用 JSON 格式，而且可以包含遙測、裝置資訊和裝置範本資訊。 使用匯出的資料：

- 暖路徑見解和分析。 此選項包括在 Azure 串流分析中觸發自訂規則、在 Azure Logic Apps 中觸發自訂工作流程，或透過 Azure Functions 將其傳遞至轉換。
- 冷路徑分析，例如在 Microsoft Power BI 的 Azure Machine Learning 或長期趨勢分析中定型模型。

> [!Note]
> 當您開啟資料匯出時，您只會取得該時間的資料。 目前，資料匯出關閉時無法取出資料。 若要保留更多歷程記錄資料，請提早開啟資料匯出。

## <a name="prerequisites"></a>必要條件

您必須是 IoT Central 應用程式的系統管理員，或具有資料匯出許可權。

## <a name="set-up-export-destination"></a>設定匯出目的地

設定資料匯出之前，您必須先有匯出目的地。

### <a name="create-event-hubs-namespace"></a>建立事件中樞命名空間

如果您沒有要匯出的現有事件中樞命名空間，請遵循下列步驟：

1. [在 Azure 入口網站中建立新事件中樞命名空間](https://ms.portal.azure.com/#create/Microsoft.EventHub)。 您可以透過 [Azure 事件中樞文件](../../event-hubs/event-hubs-create.md)深入了解。

2. 選擇訂用帳戶。 您可以將資料匯出至與您的 IoT Central 應用程式不同的訂用帳戶中的其他訂用帳戶。 在此情況下，您會使用連接字串進行連接。

3. 在「事件中樞」命名空間中建立一個事件中樞。 移至您的命名空間，然後選取頂端的 [+ 事件中樞]**** 以建立事件中樞執行個體。

### <a name="create-service-bus-namespace"></a>建立服務匯流排命名空間

如果您沒有要匯出的現有服務匯流排命名空間，請遵循下列步驟：

1. [在 Azure 入口網站中建立新的服務匯流排命名空間](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)。 您可以透過 [Azure 服務匯流排文件](../../service-bus-messaging/service-bus-create-namespace-portal.md)深入了解。
2. 選擇訂用帳戶。 您可以將資料匯出至與您的 IoT Central 應用程式不同的訂用帳戶中的其他訂用帳戶。 在此情況下，您會使用連接字串進行連接。

3. 若要建立要匯出的佇列或主題，請移至您的服務匯流排命名空間，然後選取 [ **+ 佇列** ] 或 [ **+ 主題**]。

當您選擇服務匯流排作為匯出目的地時，佇列和主題不得啟用會話或重複偵測。 如果啟用這其中一個選項，有些訊息就不會送達到您的佇列或主題中。

### <a name="create-storage-account"></a>建立儲存體帳戶

如果您沒有要匯出的現有 Azure 儲存體帳戶，請遵循下列步驟：

1. [在 Azure 入口網站中建立新儲存體帳戶](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 您可以深入瞭解如何建立新的 [Azure Blob 儲存體帳戶](https://aka.ms/blobdocscreatestorageaccount) 或 [Azure Data Lake Storage v2 儲存體帳戶](../../storage/blobs/data-lake-storage-quickstart-create-account.md)。 資料匯出只能將資料寫入支援區塊 blob 的儲存體帳戶。 下列清單顯示已知的相容儲存體帳戶類型：

    |效能層級|帳戶類型|
    |-|-|
    |標準|一般用途 V2|
    |標準|一般用途 V1|
    |標準|Blob 儲存體|
    |Premium|區塊 Blob 儲存體|

2. 在您的儲存體帳戶中建立容器。 移至您的儲存體帳戶。 在 [Blob 服務]**** 下，選取 [瀏覽 Blob]****。 選取頂端的 [+ 容器]**** 以建立新的容器。

## <a name="set-up-data-export"></a>設定資料匯出

現在您已有可匯出資料的目的地，請依照下列步驟來設定資料匯出。

1. 登入您的 IoT Central 應用程式。

2. 在左窗格中，選取 [ **資料匯出**]。

    > [!Tip]
    > 如果您在左窗格中看不到 [ **資料匯出** ]，則表示您沒有在應用程式中設定資料匯出的許可權。 請連絡系統管理員來設定資料匯出。

3. 選取 [+ 新增]**** 按鈕。 選擇其中一個 **Azure Blob 儲存體**、 **Azure 事件中樞**、 **Azure 服務匯流排佇列**或 **Azure 服務匯流排主題** 做為您匯出的目的地。 每個應用程式的匯出數目上限是五個。

4. 輸入匯出的名稱。 在下拉式清單方塊中，選取您的 **命名空間**，或 **輸入連接字串**。

    - 您只會在與 IoT Central 應用程式相同的訂用帳戶中看到儲存體帳戶、事件中樞命名空間和服務匯流排命名空間。 如果您想要匯出至此訂用帳戶以外的目的地，請選擇 [ **輸入連接字串** ]，然後參閱步驟6。
    - 針對使用免費定價方案建立的應用程式，設定資料匯出的唯一方式是透過連接字串。 免費定價方案上的應用程式沒有相關聯的 Azure 訂用帳戶。

    ![建立新的事件中樞](media/howto-export-data-legacy/export-event-hub.png)

5. 從下拉式清單方塊中選擇事件中樞、佇列、主題或容器。

6. (選擇性) 如果您選擇 [輸入連接字串]****，就會顯示一個可供您貼上連接字串的新方塊。 取得下列項目的連接字串：

    - 事件中樞或服務匯流排，請移至 Azure 入口網站中的命名空間：
        - 若要使用整個命名空間的連接字串：
            1. 在 [**設定**] 底下，選取 [**共用存取原則**]
            2. 建立新的金鑰，或選擇具有 **傳送** 許可權的現有金鑰。
            3. 複製主要連接字串或次要連接字串
        - 若要使用特定事件中樞實例或服務匯流排佇列或主題的連接字串，請移至 **> 事件中樞** 或實體 **> 佇列** 或 **實體 > 主題**的實體。 選擇特定的實例，並遵循上述相同步驟來取得連接字串。
    - 在 [儲存體帳戶] 中，移至 Azure 入口網站中的儲存體帳戶：
        - 僅支援整個儲存體帳戶的連接字串。 不支援範圍為單一容器的連接字串。
          1. 在 [**設定**] 底下，選取 [**存取金鑰**]
          2. 複製 key1 連接字串或 key2 連接字串

    貼上連接字串。 輸入實例或區分大小寫的 **容器名稱**。

7. 在 [ **要匯出的資料**] 底下，將 [類型] 設定為 [ **開啟**]，選擇要匯出的資料類型。

8. 若要開啟資料匯出，請確定已**開啟****啟用**的切換。 選取 [儲存]。

9. 幾分鐘後，您的資料就會出現在您選擇的目的地。

## <a name="export-contents-and-format"></a>匯出內容和格式

匯出的遙測資料包含裝置傳送給 IoT Central 的整個訊息，而不只是遙測值本身。 匯出的裝置資料包含所有裝置的屬性和中繼資料變更，而匯出的裝置範本則包含對所有裝置範本所做的變更。

針對事件中樞和服務匯流排，資料會以近乎即時的方式匯出。 資料位於 `body` 屬性中，且採用 JSON 格式。 如需範例，請參閱下文。

針對 Blob 儲存體，資料會每分鐘匯出一次，每個檔案都包含自上次匯出檔案之後的變更批次。 匯出的資料會以 JSON 格式放置於三個資料夾中。 您儲存體帳戶中的預設路徑為：

- 遙測： _{container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 裝置： _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 裝置範本： _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

若要流覽 Azure 入口網站中匯出的檔案，請流覽至該檔案，然後選取 [ **編輯 blob** ] 索引標籤。

## <a name="telemetry"></a>遙測

針對事件中樞和服務匯流排，IoT Central 會在收到來自裝置的訊息之後，快速匯出新訊息。 每個匯出的訊息都包含裝置在主體屬性中以 JSON 格式傳送的完整訊息。

針對 Blob 儲存體，每分鐘會批次處理和匯出訊息一次。 匯出的檔案所使用的格式，與 [IoT 中樞訊息路由](../../iot-hub/tutorial-routing.md) 到 blob 儲存體所匯出的訊息檔案相同。

> [!NOTE]
> 針對 Blob 儲存體，請確定您的裝置正在傳送具有 `contentType: application/JSON` 和 `contentEncoding:utf-8` (或 `utf-16`) `utf-32` 的訊息。 如需範例，請參閱 [IoT 中樞檔](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) 。

傳送遙測的裝置會以裝置識別碼來表示 (請參閱下列各節) 。 若要取得裝置的名稱，請匯出裝置資料，並使用符合裝置訊息**deviceId**的 **>connectiondeviceid**來相互關聯每個訊息。

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

若要從 Azure 串流分析查詢中的訊息資料取得裝置識別碼，請使用 [>getmetadatapropertyvalue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue) 函數。 如需範例，請參閱 [使用串流分析、Azure Functions 和 SendGrid，利用自訂規則擴充 Azure IoT Central](./howto-create-custom-rules.md)中的查詢。

若要取出 Azure Databricks 或 Apache Spark 工作區中的裝置識別碼，請使用 [systemProperties](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)。 如需範例，請參閱 [使用 Azure Databricks 使用自訂分析擴充 Azure IoT Central](./howto-create-custom-analytics.md)中的 Databricks 工作區。

下列範例顯示已匯出至 blob 儲存體的記錄：

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

快照中的每個訊息或記錄代表自上次匯出訊息之後，對裝置及其裝置和雲端屬性進行的一或多項變更。 此訊息包含：

- IoT Central 中裝置的 `id`
- 裝置的 `displayName`
- 中的裝置範本識別碼 `instanceOf`
- `simulated` 旗標，如果裝置是模擬裝置，則為 true
- `provisioned` 旗標，如果裝置已布建，則為 true
- `approved` 旗標，如果裝置已獲准傳送資料，則為 true
- 屬性值
- `properties` 包含裝置和雲端屬性值

未匯出已刪除的裝置。 目前，在匯出的訊息中並沒有任何適用於已刪除之裝置的指標。

針對事件中樞和服務匯流排，IoT Central 會以近乎即時的方式將包含裝置資料的訊息傳送至事件中樞或服務匯流排佇列或主題。

針對 Blob 儲存體，包含最後一次寫入後所有變更的新快照集會每分鐘匯出一次。

下列範例訊息會顯示事件中樞或服務匯流排佇列或主題中裝置和屬性資料的相關資訊：

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

此快照集是顯示 Blob 儲存體中的裝置和屬性資料的範例訊息。 匯出的檔案會在每一筆記錄中包含一行。

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

每個訊息或快照集記錄代表自上次匯出訊息之後，已發佈裝置範本的一或多項變更。 每個訊息或記錄中傳送的資訊包括：

- `id`符合上述裝置資料流程的裝置範本 `instanceOf`
- 裝置範本的 `displayName`
- 裝置 `capabilityModel` ，包括其 `interfaces` 、遙測、屬性和命令定義
- `cloudProperties` 定義
- 覆寫和初始值（以內嵌） `capabilityModel`

未匯出已刪除的裝置範本。 目前，在匯出的訊息中並沒有任何適用於已刪除之裝置範本的指標。

針對事件中樞和服務匯流排，IoT Central 會以近乎即時的方式將包含裝置範本資料的訊息傳送至事件中樞或服務匯流排佇列或主題。

針對 Blob 儲存體，包含最後一次寫入後所有變更的新快照集會每分鐘匯出一次。

此範例顯示事件中樞或服務匯流排佇列或主題中裝置範本資料的相關訊息：

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

此範例快照會顯示訊息，其中包含 Blob 儲存體中的裝置和屬性資料。 匯出的檔案會在每一筆記錄中包含一行。

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
> 遙測資料流程資料格式不受這項變更的影響。 只有裝置和裝置範本的資料流程會受到影響。

如果您在預覽應用程式中有現有的資料匯出，且已開啟 *裝置* 和 *裝置範本* 資料流程，請將您的匯出更新為 **2020 年6月30日**。 這項需求適用于匯出至 Azure Blob 儲存體、Azure 事件中樞和 Azure 服務匯流排。

自2020年2月3日起，在啟用裝置和裝置範本的應用程式中，所有新的匯出都會有上述的資料格式。 在此日期之前建立的所有匯出都會維持舊的資料格式，直到2020年6月30日為止，屆時這些匯出會自動遷移至新的資料格式。 新的資料格式符合 IoT Central 公用 API 中的 [裝置](https://docs.microsoft.com/rest/api/iotcentral/devices/get)、 [裝置屬性](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties)、 [裝置雲端屬性](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties)和 [裝置範本](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) 物件。

針對 **裝置**，舊的資料格式與新的資料格式之間有顯著的差異，包括：
- `@id` 移除裝置時， `deviceId` 會重新命名為 `id` 
- `provisioned` 新增旗標以描述裝置的布建狀態
- `approved` 新增旗標以描述裝置的核准狀態
- `properties` 包含裝置和雲端屬性，符合公用 API 中的實體

針對 **裝置範本**，舊的資料格式與新的資料格式之間有顯著的差異，包括：

- `@id` 裝置範本的重新命名為 `id`
- `@type` 裝置範本已重新命名為 `types` ，且現在為數組

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>裝置 (格式已于2020年2月3日淘汰) 

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

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>裝置範本 (格式已于2020年2月3日淘汰) 

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

## <a name="next-steps"></a>接下來的步驟

現在您已瞭解如何將資料匯出至 Azure 事件中樞、Azure 服務匯流排和 Azure Blob 儲存體，請繼續進行下一個步驟：

> [!div class="nextstepaction"]
> [如何使用 Databricks 執行自訂分析](./howto-create-custom-analytics.md)
