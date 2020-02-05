---
title: 將資料匯出至 Azure 事件中樞和 Azure 服務匯流排 | Microsoft Docs
description: 如何將資料從 Azure IoT Central 應用程式匯出至 Azure 事件中樞和 Azure 服務匯流排
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 84539c4cd5e4e1712c28ab70e9afc259e8d6d04b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990057"
---
# <a name="export-your-data-in-azure-iot-central"></a>匯出 Azure IoT Central 中的資料

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

*此主題適用於系統管理員。*

本文說明如何使用 Azure IoT Central 中的連續資料匯出功能，將您的資料匯出至您自己的**Azure 事件中樞**和**Azure 服務匯流排**實例。 您可以將**度量**、**裝置**及**裝置範本**匯出到您自己的目的地，以取得暖路徑見解和分析。 這包括觸發「Azure 串流分析」中的自訂規則、觸發 Azure Logic Apps 中的自訂工作流程，或透過 Azure Functions 來轉換及傳遞資料。 

> [!Note]
> 同樣地，當您開啟連續資料匯出時，只會取得從該時刻起的資料。 目前，無法擷取「連續資料匯出」時的資料。 若要保留更多歷史資料，請儘早開啟「連續資料匯出」。


## <a name="prerequisites"></a>必要條件

- 您必須是 IoT Central 應用程式中的系統管理員

## <a name="set-up-export-destination"></a>設定匯出目的地

如果您沒有要匯出的現有事件中樞/服務匯流排，請遵循下列步驟來建立一個：

### <a name="create-event-hubs-namespace"></a>建立事件中樞命名空間

1. [在 Azure 入口網站中建立新事件中樞命名空間](https://ms.portal.azure.com/#create/Microsoft.EventHub)。 您可以透過 [Azure 事件中樞文件](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)深入了解。
2. 選擇訂用帳戶。 

    > [!Note] 
    > 您現在可以將資料匯出到其他與標準 IoT Central 應用程式不同的訂用帳戶。 在此情況下，您將使用連接字串來進行連線。
3. 在「事件中樞」命名空間中建立一個事件中樞。 移至您的命名空間，然後選取頂端的 [+ 事件中樞] 以建立事件中樞執行個體。

### <a name="create-service-bus-namespace"></a>建立服務匯流排命名空間

1. [在 Azure 入口網站中建立新的服務匯流排命名空間](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)。 您可以透過 [Azure 服務匯流排文件](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal)深入了解。
2. 選擇訂用帳戶。 

    > [!Note] 
    > 您現在可以將資料匯出到其他與標準 IoT Central 應用程式不同的訂用帳戶。 在此情況下，您將使用連接字串來進行連線。

3. 移至您的「服務匯流排」命名空間，然後選取頂端的 [+ 佇列] 或 [+ 主題] 以建立要作為匯出目的地的佇列或主題。


## <a name="set-up-continuous-data-export"></a>設定連續資料匯出

現在您已有事件中樞/服務匯流排目的地可將資料匯出至，請遵循下列步驟來設定連續資料匯出。 

1. 登入您的 IoT Central 應用程式。

2. 在左窗格中，選取 [**連續資料匯出**]。

    > [!Note]
    > 如果您在左窗格中沒有看到 [連續資料匯出]，則您不是應用程式中的系統管理員。 請連絡系統管理員來設定資料匯出。

3. 選取右上方的 [ **+ 新增**] 按鈕。 選擇其中一個**Azure 事件中樞**或**Azure 服務匯流排**做為匯出的目的地。 

    > [!NOTE] 
    > 每一應用程式的匯出數目上限是 5。 

    ![建立新的連續資料匯出](media/howto-export-data/export-new2.png)

4. 在下拉式清單方塊中，選取您的**事件中樞命名空間/服務匯流排命名空間**。 您也可以挑選清單中的最後一個選項，也就是 [輸入連接字串]。 

    > [!NOTE] 
    > 您只會看到**與 IoT Central 應用程式位於相同訂用帳戶中**的「儲存體帳戶」/「事件中樞命名空間」/「服務匯流排命名空間」。 如果您想要匯出到此訂用帳戶外的目的地，請選擇 [輸入連接字串]，然後參閱步驟 5。

    > [!NOTE] 
    > 如果是 7 天試用版應用程式，則設定連續資料匯出的唯一方式是透過連接字串。 這是因為 7 天試用版應用程式並沒有相關聯的 Azure 訂用帳戶。

    ![建立新的 cde 事件中樞](media/howto-export-data/export-eh.png)

5. (選擇性) 如果您選擇 [輸入連接字串]，就會顯示一個可供您貼上連接字串的新方塊。 取得下列項目的連接字串：
    - 事件中樞或服務匯流排，請移至 Azure 入口網站中的命名空間。
        - 在 [**設定**] 底下，選取 [**共用存取原則**]
        - 選擇預設的 [RootManageSharedAccessKey]，或建立新的原則
        - 複製主要連接字串或次要連接字串
 
6. 從下拉式清單方塊中選擇事件中樞/佇列或主題。

7. 在 [要匯出的資料] 下，透過將類型設定為 [開啟] 以指定每種要匯出的資料類型。

8. 若要開啟連續資料匯出，請確定 [**資料匯出**切換] 為 [**開啟**]。 選取 [儲存]。

9. 幾分鐘後，您的資料就會出現在所選擇的目的地中。


## <a name="data-format"></a>資料格式

度量、裝置及裝置範本資料會以近乎即時的方式，匯出至事件中樞或「服務匯流排」佇列或主題。 匯出的度量資料包含您裝置傳送給 IoT Central 的訊息整體，而不僅限於度量本身的值。 匯出的裝置資料包含對所有裝置之屬性與設定進行的變更，而匯出的裝置範本則包含對所有裝置範本進行的變更。 匯出的資料會在 "body" 屬性中，且採用 JSON 格式。

> [!NOTE]
> 選擇「服務匯流排」作為匯出目的地時，佇列和主題**不得啟用 [工作階段] 或 [重複資料偵測]** 。 如果啟用這其中一個選項，有些訊息就不會送達到您的佇列或主題中。

### <a name="measurements"></a>度量

在 IoT Central 收到來自裝置的訊息之後，就會快速匯出新訊息。 「事件中樞」和「服務匯流排」中的每個匯出訊息都包含裝置以 JSON 格式在 "body" 屬性中傳送的完整訊息。 

> [!NOTE]
> 傳送量測的裝置會以裝置識別碼來表示 (請參閱以下各節)。 若要取得裝置的名稱，請匯出裝置資料，並使用與裝置訊息之 **deviceId** 相符的 **connectionDeviceId** 將每個訊息相互關聯。

下列範例示範事件中樞或「服務匯流排」佇列或主題中所收到度量資料的相關訊息。

```json
{
  "body": {
    "humidity": 29.06963648666288,
    "temp": 8.4503795661685,
    "pressure": 1075.8334910110093,
    "magnetometerX": 408.6966458887116,
    "magnetometerY": -532.8809796603962,
    "magnetometerZ": 174.70653875528205,
    "accelerometerX": 1481.546749013788,
    "accelerometerY": -1139.4316656437406,
    "accelerometerZ": 811.6928695575307,
    "gyroscopeX": 442.19879163299856,
    "gyroscopeY": 123.23710975717177,
    "gyroscopeZ": 708.5397575786151,
    "deviceState": "DANGER"
  },
  "annotations": {
    "iothub-connection-device-id": "<connectionDeviceId>",
    "iothub-connection-auth-method": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "<generationId>",
    "iothub-enqueuedtime": 1539381029965,
    "iothub-message-source": "Telemetry",
    "x-opt-sequence-number": 25325,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539381030200
  },
  "sequenceNumber": 25325,
  "enqueuedTimeUtc": "2018-10-02T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>裝置

包含裝置資料的訊息會以每幾分鐘一次的頻率，傳送到您的事件中樞或「服務匯流排」佇列或主題。 這意謂著每隔幾分鐘就會有一批訊息送達，其中包含下列相關資料
- 已新增的新裝置
- 屬性和設定值已變更的裝置

每個訊息皆代表自上次匯出訊息之後，對裝置進行的一或多項變更。 將在每個訊息中傳送的資訊包括：
- IoT Central 中裝置的 `id`
- 裝置的 `name`
- 來自[裝置佈建服務](/azure/iot-central/core/howto-connect-nodejs) 的 `deviceId`
- 裝置範本資訊
- 屬性值
- 設定值

> [!NOTE]
> 系統不會匯出自上個批次後刪除的裝置。 目前，在匯出的訊息中並沒有任何適用於已刪除之裝置的指標。
>
> 每個裝置所屬的裝置範本會以裝置範本識別碼來表示。 若要取得裝置範本的名稱，請務必一併匯出裝置範本資料。

下列範例示範事件中樞或「服務匯流排」佇列或主題中裝置資料的相關訊息：


```json
{
  "body": {
    "id": "<id>",
    "name": "<deviceName>",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
      "id": "<templateId>",
      "version": "1.0.0"
    },
    "properties": {
      "cloud": {
        "location": "Seattle"
      },
      "device": {
        "dieNumber": 5445.5862873026645
      }
    },
    "settings": {
      "device": {
        "fanSpeed": 0
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "devices",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 39740,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274959654
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 39740,
  "enqueuedTimeUtc": "2018-10-02T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>裝置範本

包含裝置範本資料的訊息會以每幾分鐘一次的頻率，傳送到您的事件中樞或「服務匯流排」佇列或主題。 這意謂著每隔幾分鐘就會有一批訊息送達，其中包含下列相關資料
- 已新增的新裝置範本
- 度量、屬性及設定定義已變更的裝置範本

每個訊息皆代表自上次匯出訊息之後，對裝置範本進行的一或多項變更。 將在每個訊息中傳送的資訊包括：
- 裝置範本的 `id`
- 裝置範本的 `name`
- 裝置範本的 `version`
- 量測資料類型和最小值/最大值
- 屬性資料類型和預設值
- 設定資料類型和預設值

> [!NOTE]
> 系統不會匯出自上個批次後刪除的裝置範本。 目前，在匯出的訊息中並沒有任何適用於已刪除之裝置範本的指標。

下列範例示範事件中樞或「服務匯流排」佇列或主題中裝置範本資料的相關訊息：

```json
{ 
  "body":{ 
    "id":"<id>",
    "version":"1.0.0",
    "name":"<templateName>",
    "measurements":{ 
      "telemetry":{ 
        "humidity":{ 
          "dataType":"double",
          "name":"humidity"
        },
        "pressure":{ 
          "dataType":"double",
          "name":"pressure"
        },
        "temp":{ 
          "dataType":"double",
          "name":"temperature"
        }
      }
    },
    "properties":{ 
      "cloud":{ 
        "location":{ 
          "dataType":"string",
          "name":"Location"
        }
      },
      "device":{ 
        "dieNumber":{ 
          "dataType":"double",
          "name":"Die Number"
        }
      }
    },
    "settings":{ 
      "device":{ 
        "fanSpeed":{ 
          "dataType":"double",
          "name":"Fan Speed",
          "initialValue":0
        }
      }
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
  "enqueuedTimeUtc":"2018-10-02T16:23:05.085Z",
  "offset":"<offset>"
}
```

## <a name="next-steps"></a>後續步驟

既然您已知道如何將資料匯出至「Azure 事件中樞」和「Azure 服務匯流排」，請繼續進行下一個步驟：

> [!div class="nextstepaction"]
> [如何觸發 Azure Functions](howto-trigger-azure-functions.md)
