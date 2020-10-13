---
title: 將事件傳送至環境-Azure 時間序列深入解析 |Microsoft Docs
description: 瞭解如何設定事件中樞、執行範例應用程式，並將事件傳送至您的 Azure 時間序列深入解析環境。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 2b83433a135fec486701b4538793f0c3e0a6fa6e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91611821"
---
# <a name="send-events-to-an-azure-time-series-insights-gen1-environment-by-using-an-event-hub"></a>使用事件中樞將事件傳送至 Azure 時間序列深入解析 Gen1 環境

> [!CAUTION]
> 這是 Gen1 文章。

本文說明如何在 Azure 事件中樞中建立和設定事件中樞。 它也會說明如何執行範例應用程式，以將事件推送至事件中樞 Azure 時間序列深入解析。 如果您現有的事件中樞具有 JSON 格式的事件，請略過本教學課程，並在 [Azure 時間序列深入解析](./time-series-insights-update-create-environment.md)中查看您的環境。

## <a name="configure-an-event-hub"></a>設定事件中樞

1. 若要瞭解如何建立事件中樞，請閱讀 [事件中樞檔](https://docs.microsoft.com/azure/event-hubs/)。
1. 在搜尋方塊中搜尋**事件中樞**。 在傳回的清單中選取 [事件中樞]****。
1. 選取事件中樞。
1. 當您建立事件中樞時，您正在建立事件中樞命名空間。 如果您尚未在命名空間內建立事件中樞，請在功能表上的 [ **實體**] 下建立事件中樞。  

    [![事件中樞清單](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. 建立事件中樞之後，在事件中樞清單中選取它。
1. 在功能表的 [ **實體**] 下，選取 [ **事件中樞**]。
1. 選取事件中樞的名稱來對它進行設定。
1. 在 **[總覽**] 底下，選取 [取用 **者群組**]，然後選取 [取用 **者群組**]。

    [![建立取用者群組](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. 請確定您建立的取用者群組，是由您的 Azure 時間序列深入解析事件來源獨佔使用。

    > [!IMPORTANT]
    > 確定任何其他服務（例如 Azure 串流分析作業或其他 Azure 時間序列深入解析環境）都未使用此取用者群組。 如果有其他服務使用此取用者群組，讀取作業對此環境和其他服務都會造成負面影響。 如果使用 **$Default** 做為取用者群組，有可能會導致其他讀取者重複使用您的取用者群組。

1. 在功能表的 [ **設定**] 底下，選取 [ **共用存取原則**]，然後選取 [ **新增**]。

    [![選取 [共用存取原則]，然後選取 [新增] 按鈕](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. 在 [新增共用存取原則]**** 窗格中，建立名為 **MySendPolicy** 的共用存取。 您可以使用此共用存取原則，在本文稍後的 c # 範例中傳送事件。

    [![在 [原則名稱] 方塊中，輸入 MySendPolicy](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. 在 [宣告 **] 下，** 選取 [ **傳送** ] 核取方塊。

## <a name="add-an-azure-time-series-insights-instance"></a>新增 Azure 時間序列深入解析實例

在 Azure 時間序列深入解析 Gen2 中，您可以使用時間序列模型 (TSM) ，將內容資料新增至傳入的遙測。 在 TSM 中，您的標籤或信號稱為 *實例，* 而且您可以將內容資料儲存在 *實例欄位中。* 資料會在查詢期間透過使用 [時間序列識別碼]****.加入。 我們稍後在本文中使用的範例風車專案的 **時間序列識別碼** 是 `id` 。 若要深入瞭解如何在實例欄位中儲存資料，請閱讀 [時間序列模型](./concepts-model-overview.md) 總覽。

### <a name="create-an-azure-time-series-insights-event-source"></a>建立 Azure 時間序列深入解析事件來源

1. 如果您尚未建立事件來源，請完成這些步驟以[建立事件來源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)。

1. 設定 `timeSeriesId` 的值。 若要深入瞭解 **時間序列識別碼**，請閱讀 [時間序列模型](./concepts-model-overview.md)。

### <a name="push-events-to-windmills-sample"></a>將事件推送至風車範例

1. 在搜尋列中搜尋**事件中樞**。 在傳回的清單中選取 [事件中樞]****。

1. 選取您的事件中樞實例。

1. 移至 [**共用存取原則**]  >  **MySendPolicy**。 複製 [ **連接字串-主要金鑰**] 的值。

    [![複製主索引鍵連接字串的值](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. 前往 <https://tsiclientsample.azurewebsites.net/windFarmGen.html>。 此 URL 會建立並執行模擬風車裝置。
1. 在網頁的 [ **事件中樞連接字串** ] 方塊中，貼上您在 [ [風車輸入] 欄位](#push-events-to-windmills-sample)中複製的連接字串。
  
    [![將主索引鍵連接字串貼到 [事件中樞連接字串] 方塊中](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. 選取 [按一下即可啟動]****。

    > [!TIP]
    > 風車模擬器也會建立 JSON，您可以使用 [AZURE 時間序列深入解析 GA 查詢 api](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query)作為承載。

    > [!NOTE]
    > 模擬器會繼續傳送資料，直到瀏覽器索引標籤關閉為止。

1. 回到 Azure 入口網站中的事件中樞。 在 [ **總覽** ] 頁面中，會顯示事件中樞所收到的新事件。

    [![顯示事件中樞計量的事件中樞概觀頁面](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>支援的 JSON 樣貌

### <a name="example-one"></a>範例1

* **輸入**：簡單的 JSON 物件。

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

### <a name="example-two"></a>範例二

* **輸入**：具有兩個 json 物件的 json 陣列。 每個 JSON 物件都會轉換成事件。

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

### <a name="example-three"></a>範例三

* **輸入**：具有包含兩個 json 物件之嵌套 json 陣列的 json 物件。

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

### <a name="example-four"></a>範例四

* **輸入**：具有包含兩個 json 物件之嵌套 json 陣列的 json 物件。 此輸入示範可由複雜 JSON 物件表示的全域屬性。

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

* 在 Azure 時間序列深入解析 Explorer 中[查看您的環境](https://insights.timeseries.azure.com)。

* 深入瞭解 [IoT 中樞裝置訊息](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)
