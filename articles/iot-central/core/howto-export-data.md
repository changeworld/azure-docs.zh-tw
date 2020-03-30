---
title: 匯出 Azure IoT 中心資料 |微軟文檔
description: 如何將資料從 Azure IoT 中央應用程式匯出到 Azure 事件中心、Azure 服務匯流排和 Azure Blob 存儲
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 725c5acf961fffb1fd4cf9bc17e37a5940f871cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157903"
---
# <a name="export-iot-data-to-destinations-in-azure"></a>將 IoT 資料匯出到 Azure 中的目標

*此主題適用於系統管理員。*

本文介紹如何使用 Azure IoT 集中中的連續資料匯出功能將資料匯出到 Azure**事件中心****、Azure 服務匯流排**或**Azure Blob 存儲**實例。 資料以 JSON 格式匯出，可以包括遙測、設備資訊和設備範本資訊。 使用匯出的資料進行以下工作：

- 溫路徑洞察和分析。 此選項包括在 Azure 流分析中觸發自訂規則、在 Azure 邏輯應用中觸發自訂工作流，或通過要轉換的 Azure 函數傳遞這些規則。
- 冷路徑分析，如 Azure 機器學習中的培訓模型或 Microsoft Power BI 中的長期趨勢分析。

> [!Note]
> 當您開啟「連續資料匯出」時，只會取得從該時刻的資料。 目前，無法擷取「連續資料匯出」時的資料。 若要保留更多歷史資料，請儘早開啟「連續資料匯出」。

## <a name="prerequisites"></a>Prerequisites

您必須是 IoT 中央應用程式中的管理員，或者具有資料匯出許可權。

## <a name="set-up-export-destination"></a>設定匯出目的地

在配置連續資料匯出之前，匯出目標必須存在。

### <a name="create-event-hubs-namespace"></a>建立事件中樞命名空間

如果沒有要匯出的現有事件中心命名空間，請按照以下步驟操作：

1. [在 Azure 入口網站中建立新事件中樞命名空間](https://ms.portal.azure.com/#create/Microsoft.EventHub)。 您可以透過 [Azure 事件中樞文件](../../event-hubs/event-hubs-create.md)深入了解。

2. 選擇訂用帳戶。 您可以將資料匯出到與 IoT 中央應用程式不在同一訂閱中的其他訂閱。 在這種情況下，使用連接字串進行連接。

3. 在「事件中樞」命名空間中建立一個事件中樞。 移至您的命名空間，然後選取頂端的 [+ 事件中樞]**** 以建立事件中樞執行個體。

### <a name="create-service-bus-namespace"></a>建立服務匯流排命名空間

如果沒有要匯出的現有服務匯流排命名空間，請按照以下步驟操作：

1. 在[Azure 門戶中創建新的服務匯流排命名空間](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)。 您可以透過 [Azure 服務匯流排文件](../../service-bus-messaging/service-bus-create-namespace-portal.md)深入了解。
2. 選擇訂用帳戶。 您可以將資料匯出到與 IoT 中央應用程式不在同一訂閱中的其他訂閱。 在這種情況下，使用連接字串進行連接。

3. 移至您的「服務匯流排」命名空間，然後選取頂端的 [+ 佇列]**** 或 [+ 主題]**** 以建立要作為匯出目的地的佇列或主題。

當您選擇服務匯流排作為匯出目標時，佇列和主題不得啟用會話或重複檢測。 如果啟用這其中一個選項，有些訊息就不會送達到您的佇列或主題中。

### <a name="create-storage-account"></a>建立儲存體帳戶

如果沒有要匯出的現有 Azure 存儲帳戶，請按照以下步驟操作：

1. [在 Azure 入口網站中建立新儲存體帳戶](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 可以瞭解有關創建新[Azure Blob 存儲帳戶](https://aka.ms/blobdocscreatestorageaccount)或[Azure 資料存儲湖存儲 v2 存儲帳戶](../../storage/blobs/data-lake-storage-quickstart-create-account.md)。 資料匯出只能將資料寫入支援塊 blob 的存儲帳戶。 以下是已知相容類型的存儲帳戶的清單： 

    |效能層級|帳戶類型|
    |-|-|
    |標準|通用 V2|
    |標準|通用 V1|
    |標準|Blob 儲存體|
    |Premium|阻止 Blob 存儲|

2. 在您的儲存體帳戶中建立容器。 移至您的儲存體帳戶。 在 [Blob 服務]**** 下，選取 [瀏覽 Blob]****。 選取頂端的 [+ 容器]**** 以建立新的容器。

## <a name="set-up-continuous-data-export"></a>設定連續資料匯出

現在，您有一個要將資料匯出到的目標，請按照以下步驟設置連續資料匯出。

1. 登入您的 IoT Central 應用程式。

2. 在左側窗格中，選擇 **"資料匯出**"。

    > [!Note]
    > 如果在左側窗格中看不到"資料匯出"，則無權在應用中配置資料匯出。 請連絡系統管理員來設定資料匯出。

3. 選擇右上角的 **"新增"** 按鈕。 選擇**Azure 事件中心****、Azure 服務匯流排**或**Azure Blob 存儲**之一作為匯出的目標。 每個應用程式的最大匯出數為 5。

    ![建立新的連續資料匯出](media/howto-export-data/new-export-definition.png)

4. 在下拉式清單方塊中，選擇**事件中心命名空間**、**服務匯流排命名空間**、**存儲帳戶命名空間**或**輸入連接字串**。

    - 您只能看到與 IoT 中央應用程式相同的訂閱中的存儲帳戶、事件中心命名空間和服務匯流排命名空間。 如果您想要匯出到此訂用帳戶外的目的地，請選擇 [輸入連接字串]****，然後參閱步驟 5。
    - 對於使用免費定價計畫創建的應用，配置連續資料匯出的唯一方法是通過連接字串。 免費定價計畫中的應用沒有關聯的 Azure 訂閱。

    ![創建新的事件中心](media/howto-export-data/export-event-hub.png)

5. (選擇性) 如果您選擇 [輸入連接字串]****，就會顯示一個可供您貼上連接字串的新方塊。 取得下列項目的連接字串：
    - 事件中心或服務匯流排，轉到 Azure 門戶中的命名空間。
        - 在 **"設置"** 下，選擇**共用訪問策略**
        - 選擇預設的 [RootManageSharedAccessKey]****，或建立新的原則
        - 複製主要連接字串或次要連接字串
    - 存儲帳戶，轉到 Azure 門戶中的存儲帳戶：
        - 在 **"設置"** 下，選擇 **"便捷鍵"**
        - 複製鍵1連接字串或 key2 連接字串

6. 從下拉式清單方塊中選擇事件中心、佇列、主題或容器。

7. 在**要匯出的資料**下，通過將類型設置為**On，** 選擇要匯出的資料類型。

8. 要打開連續資料匯出，請確保**啟用**的切換處於**打開狀態**。 選取 [儲存]****。

9. 幾分鐘後，您的資料將顯示在您選擇的目標中。

## <a name="export-contents-and-format"></a>匯出內容和格式

匯出的遙測資料包含設備發送到 IoT Central 的全部消息，而不僅僅是遙測值本身。 匯出的設備資料包含對所有設備的屬性和中繼資料的更改，匯出的設備範本包含對所有設備範本的更改。

對於事件中心和服務匯流排，資料以近乎即時的方式匯出。 資料位於正文屬性中，採用 JSON 格式（有關示例，請參閱下文）。

對於 Blob 存儲，資料每分鐘匯出一次，每個檔包含自上次匯出檔以來的更改批次處理。 匯出的資料以 JSON 格式放置在三個資料夾中。 您儲存體帳戶中的預設路徑為：

- 遙測 _：[容器]/[應用 id]/遙測/[YYYY]/{MM}/{dd}/{h}/{\m}/{檔案名}_
- 設備 _：[容器]/[應用id]/設備/[YYYY]/[MM]/{d}/{h}/{mm}/{檔案名}_
- 設備範本 _：[容器]/[應用-id]/設備範本/[YYYY]/{MM}/{dd}/{h}/{{\m}/{檔案名}_

您可以通過導航到檔並選擇 **"編輯 Blob"** 選項卡來流覽 Azure 門戶中匯出的檔。


## <a name="telemetry"></a>遙測

對於事件中心和服務匯流排，在 IoT Central 從設備接收消息後，將快速匯出一條新消息，並且每個匯出的消息都包含設備以 JSON 格式在正文屬性中發送的完整消息。

對於 Blob 存儲，消息每分鐘批次處理和匯出一次。 匯出的檔使用與[IoT 中心消息路由](../../iot-hub/tutorial-routing.md)匯出的消息檔相同的格式到 Blob 存儲。 

> [!NOTE]
> 對於`contentType: application/JSON`Blob 存儲，請確保設備正在發送具有 和`contentEncoding:utf-8`（或`utf-16` `utf-32`） 的消息。 有關示例，請參閱[IoT 中心文檔](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body)。

發送遙測的設備由裝置識別碼 表示（請參閱以下部分）。 要獲取設備的名稱，請使用與設備消息**的裝置識別碼**匹配**的 connectDeviceId**匯出裝置資料並關聯每條消息。

這是在事件中心或服務匯流排佇列或主題中收到的示例消息。

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

這是匯出到 blob 存儲的示例記錄：

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

快照中的每個消息或記錄都表示自上次匯出消息以來對設備及其設備和雲屬性的一個或多個更改。 這包括：

- IoT Central 中裝置的 `id`
- 裝置的 `displayName`
- 設備範本 ID`instanceOf`
- `simulated`標誌，如果設備是類比設備，則為 true
- `provisioned`標誌，如果設備已預配，為 true
- `approved`標誌，如果設備已獲准發送資料，則為 true
- 屬性值
- `properties`包括設備和雲屬性值

未匯出已刪除的設備。 目前，在匯出的訊息中並沒有任何適用於已刪除之裝置的指標。

對於事件中心和服務匯流排，包含設備資料的消息會以近乎即時的方式發送到事件中心或服務匯流排佇列或主題，如 IoT 中央顯示的那樣。 

對於 Blob 存儲，包含自上次寫入以來的所有更改的新快照每分鐘匯出一次。

這是有關事件中心或服務匯流排佇列或主題中的設備和屬性資料的示例消息：

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

這是一個示例快照，其中包含 Blob 存儲中的設備和屬性資料。 匯出的檔包含每條記錄的一行。

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

自上次匯出的消息以來，每條消息或快照記錄都表示對已發佈的設備範本的一個或多個更改。 在每封郵件或記錄中發送的資訊包括：

- `id`與上述設備流匹配`instanceOf`的設備範本
- 裝置範本的 `displayName`
- 設備`capabilityModel`包括 其`interfaces`和 遙測、屬性和命令定義
- `cloudProperties`定義
- 覆蓋和初始值，內聯`capabilityModel`

未匯出已刪除的設備範本。 目前，在匯出的訊息中並沒有任何適用於已刪除之裝置範本的指標。

對於事件中心和服務匯流排，包含設備範本資料的消息會以近乎即時的方式發送到事件中心或服務匯流排佇列或主題，如 IoT 中央顯示的那樣。 

對於 Blob 存儲，包含自上次寫入以來的所有更改的新快照每分鐘匯出一次。

這是有關事件中心或服務匯流排佇列或主題中設備範本資料的示例消息：

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

這是一個示例快照，其中包含 Blob 存儲中的設備和屬性資料。 匯出的檔包含每條記錄的一行。

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
## <a name="data-format-change-notice"></a>資料格式更改通知

> [!Note]
> 遙測流資料格式不受此更改的影響。 只有設備和設備範本資料流程受到影響。

如果在打開 *"設備**和設備"範本*流的預覽應用程式中具有現有資料匯出，則需要在**2020 年 6 月 30 日前**更新匯出。 這適用于匯出到 Azure Blob 存儲、Azure 事件中心以及 Azure 服務匯流排。

從 2020 年 2 月 3 日起，啟用了設備和設備範本的應用程式中的所有新匯出都將具有上述資料格式。 在此之前創建的所有匯出將保持舊資料格式，直到 2020 年 6 月 30 日，之後這些匯出將自動遷移到新的資料格式。 新的資料格式與 IoT 中央公共 API 中的[設備](https://docs.microsoft.com/rest/api/iotcentral/devices/get)、[裝置屬性](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties)、[設備雲屬性](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties)[和設備範本](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get)物件匹配。 
 
對於**設備**，舊資料格式和新資料格式之間的顯著差異包括：
- `@id`對於設備被刪除，`deviceId`重命名為`id` 
- `provisioned`添加標誌以描述設備的預配狀態
- `approved`添加標誌以描述設備的審批狀態
- `properties`包括設備和雲屬性，在公共 API 中匹配實體

對於**設備範本**，舊資料格式和新資料格式之間的顯著差異包括：

- `@id`設備範本重命名為`id`
- `@type`設備範本將重命名為`types`，現在為 陣列

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>設備（截至2020年2月3日已棄用格式）
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

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>設備範本（截至 2020 年 2 月 3 日已棄用的格式）
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

現在，您已經瞭解如何將資料匯出到 Azure 事件中心、Azure 服務匯流排和 Azure Blob 存儲，請繼續執行下一步：

> [!div class="nextstepaction"]
> [如何創建網鉤](./howto-create-webhooks.md)
