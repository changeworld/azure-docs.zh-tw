---
title: 將事件發送到環境 - Azure 時間序列見解 |微軟文檔
description: 瞭解如何配置事件中心、運行應用程式範例以及向 Azure 時間序列見解環境發送事件。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/11/2020
ms.custom: seodec18
ms.openlocfilehash: c3c7f59ecb3a06d80012917e2da4425a899859d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254244"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>使用事件中樞將事件傳送至時間序列深入解析環境

本文介紹如何在 Azure 事件中心中創建和配置事件中心。 它還介紹了如何運行應用程式範例，以將事件推送到事件中心中的 Azure 時間序列見解。 如果您有一個具有 JSON 格式事件的現有事件中心，請跳過本教程並在[Azure 時間序列見解](./time-series-insights-update-create-environment.md)中查看環境。

## <a name="configure-an-event-hub"></a>設定事件中樞

1. 要瞭解如何創建事件中心，請閱讀[事件中心文檔](https://docs.microsoft.com/azure/event-hubs/)。
1. 在搜尋方塊中搜尋**事件中樞**。 在傳回的清單中選取 [事件中樞]****。
1. 選取事件中樞。
1. 創建事件中心時，將創建事件中心命名空間。 如果尚未在命名空間中創建事件中心，則在"實體"下，在"**實體"** 下創建事件中心。  

    [![事件中樞清單](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. 建立事件中樞之後，在事件中樞清單中選取它。
1. 在功能表上，在 **"實體"** 下，選擇**事件中心**。
1. 選取事件中樞的名稱來對它進行設定。
1. 在 **"概述"** 下，選擇 **"消費者組**"，然後選擇 **"消費者組**"。

    [![建立取用者群組](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. 請確保創建由時序見解事件源獨佔使用的消費者組。

    > [!IMPORTANT]
    > 確保此消費者組未被任何其他服務（如 Azure 流分析作業或其他時間序列見解環境）使用。 如果有其他服務使用此取用者群組，讀取作業對此環境和其他服務都會造成負面影響。 如果使用 **$Default** 做為取用者群組，有可能會導致其他讀取者重複使用您的取用者群組。

1. 在功能表上，在 **"設置"** 下，選擇 **"共用訪問策略**"，然後選擇"**添加**"。

    [![選取 [共用存取原則]，然後選取 [新增] 按鈕](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. 在 [新增共用存取原則]**** 窗格中，建立名為 **MySendPolicy** 的共用存取。 使用此共用訪問策略在本文後面的 C# 示例中發送事件。

    [![在 [原則名稱] 方塊中，輸入 MySendPolicy](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. 在 **"索賠**"下，選擇"**發送**"核取方塊。

## <a name="add-a-time-series-insights-instance"></a>新增時間序列深入解析執行個體

時間序列深入解析更新會使用執行個體，將內容資料新增至傳入的遙測資料。 資料會在查詢期間透過使用 [時間序列識別碼]****.加入。 本文後面使用的風車示例專案的**時序 ID**是`id`。 要瞭解有關時間序列洞察實例和**時間序列 ID**的更多內容，請閱讀[時間序列模型](./time-series-insights-update-tsm.md)。

### <a name="create-a-time-series-insights-event-source"></a>建立時間序列深入解析事件來源

1. 如果您尚未建立事件來源，請完成這些步驟以[建立事件來源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)。

1. 設定 `timeSeriesId` 的值。 要瞭解有關**時間序列 ID**的更多內容，請閱讀[時間序列模型](./time-series-insights-update-tsm.md)。

### <a name="push-events-to-windmills-sample"></a>將事件推送到風車示例

1. 在搜尋列中搜尋**事件中樞**。 在傳回的清單中選取 [事件中樞]****。

1. 選擇事件中心實例。

1. 轉到**共用訪問策略** > **"我的發送策略**"。 複製**連接字串主鍵**的值。

    [![複製主索引鍵連接字串的值](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. 移至 https://tsiclientsample.azurewebsites.net/windFarmGen.html。 URL 創建並運行類比風車設備。
1. 在網頁上的事件**中心連接字串**框中，粘貼在[風車輸入欄位中](#push-events-to-windmills-sample)複製的連接字串。
  
    [![將主索引鍵連接字串貼到 [事件中樞連接字串] 方塊中](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. 選取 [按一下即可啟動]****。 

    > [!TIP]
    > 風車模擬器還創建JSON，你可以用作有效負載與[時間序列見解GA查詢API。](https://docs.microsoft.com/rest/api/time-series-insights/ga-query)

    > [!NOTE]
    > 模擬器將繼續發送資料，直到關閉瀏覽器選項卡。

1. 回到 Azure 入口網站中的事件中樞。 在 **"概述"** 頁上，將顯示事件中心接收的新事件。

    [![顯示事件中樞計量的事件中樞概觀頁面](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>支援的 JSON 樣貌

### <a name="example-one"></a>示例一

* **輸入**：一個簡單的 JSON 物件。

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **輸出**：一個事件。

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>示例 2

* **輸入**：具有兩個 JSON 物件的 JSON 陣列。 每個 JSON 物件都會轉換成事件。

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **輸出**：兩個事件。

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>示例三

* **輸入**：具有嵌套 JSON 陣列的 JSON 物件，該陣列包含兩個 JSON 物件。

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **輸出**：兩個事件。 系統會將屬性**位置**複製到每個事件。

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>示例四

* **輸入**：具有嵌套 JSON 陣列的 JSON 物件，該陣列包含兩個 JSON 物件。 此輸入示範可由複雜 JSON 物件表示的全域屬性。

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **輸出**：兩個事件。

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>後續步驟

- 在時序見解資源管理器中[查看您的環境](https://insights.timeseries.azure.com)。

- 閱讀有關[IoT 中心設備消息的更多資訊](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)
