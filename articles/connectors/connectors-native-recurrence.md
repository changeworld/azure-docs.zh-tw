---
title: 計畫定期任務和工作流
description: 使用 Azure 邏輯應用中的定期觸發器計畫並運行重複的自動化任務和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: a9c167c5767a4156147e13a1e4ae21162e506474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445866"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>使用 Azure 邏輯應用中的定期觸發器創建、計畫和運行重複的任務和工作流

要按特定計劃定期運行任務、進程或作業，可以使用內置**的"定期 - 計畫**"觸發器啟動邏輯應用工作流。 您可以設置日期和時間以及啟動工作流的時區以及重複該工作流的重複。 如果由於任何原因錯過了重複，則此觸發器在下一個計畫間隔繼續重複。 有關內置計畫觸發器和操作的詳細資訊，請參閱[使用 Azure 邏輯應用計畫並運行定期自動化、任務和工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

以下是此觸發器支援的一些模式以及更高級的定期和複雜的計畫：

* 立即運行，每隔*n*秒、分鐘、小時、天、周或月重複一次。

* 從特定的日期和時間開始，然後每隔*n*秒、分鐘、小時、數、周或月份運行和重複一次。

* 每天執行並重複一次或多次，例如上午 8:00 和下午 5:00。

* 每週執行並重複，但僅在特定的星期幾，例如星期六和星期日。

* 每週執行並重複，但僅在特定的星期幾和時間，例如星期一到星期五的上午 8:00 和下午 5:00。

有關此觸發器和滑動視窗觸發器之間的差異，或者有關計畫定期工作流的詳細資訊，請參閱使用 Azure[邏輯應用計畫並運行重複的自動化任務、進程和工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

> [!TIP]
> 如果要觸發邏輯應用，並且將來只運行一次，請參閱[僅運行一次作業](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 關於[邏輯應用](../logic-apps/logic-apps-overview.md)的基本知識。 如果您是邏輯應用的新功能，請[瞭解如何創建第一個邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-recurrence-trigger"></a>添加重複觸發器

1. 登錄到 Azure[門戶](https://portal.azure.com)。 建立空白邏輯應用程式。

1. 邏輯應用設計器顯示後，在搜索框中，輸入`recurrence`為篩選器。 從觸發器清單中，選擇此觸發器作為邏輯應用工作流中的第一步：**重複**

   ![選擇"重複"觸發器](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. 設定重複的間隔和頻率。 在此範例中，將這些屬性設定為每週執行工作流程。

   ![設定間隔和頻率](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | 屬性 | JSON 名稱 | 必要 | 類型 | 描述 |
   |----------|-----------|----------|------|-------------|
   | **區間** | `interval` | 是 | 整數  | 描述工作流程根據 frequency 多久執行一次的正整數。 以下是最小和最大間隔： <p>- 月：1-16 個月 </br>- 天：1-500 天 </br>- 小時：1-12,000 個小時 </br>- 分鐘：1-72,000 分鐘 </br>- 秒：1-9,999,999 秒<p>例如，如果 interval 是 6，而 frequency 是「月」，則週期為每隔 6 個月。 |
   | **頻率** | `frequency` | 是 | String | 重複的時間單位：**秒**、**分鐘**、**小時**、**天**、**週**或**月** |
   ||||||

   > [!IMPORTANT]
   > 當定期未指定高級計畫選項時，將來的定期將基於上次運行時。
   > 由於存儲調用期間的延遲等因素，這些重複的開始時間可能會漂移。 為了確保邏輯應用不會錯過重複，尤其是在頻率以天或更長時程表示時，請使用以下選項之一：
   > 
   > * 提供重複的開始時間。
   > 
   > * 使用 **"At 這些小時**"和"**這些分鐘**"屬性指定運行重複的小時和分鐘。
   > 
   > * 使用[滑動視窗觸發器](../connectors/connectors-native-sliding-window.md)，而不是"重複"觸發器。

1. 要設置高級計畫選項，打開 **"添加新參數**清單"。 您選擇的任何選項在選擇後將顯示在觸發器上。

   ![進階排程選項](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | 屬性 | JSON 名稱 | 必要 | 類型 | 描述 |
   |----------|-----------|----------|------|-------------|
   | **時區** | `timeZone` | 否 | String | 只有當您有指定開始時間時才適用，因為此觸發程序並不接受 [UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 選取您要套用的時區。 |
   | **開始時間** | `startTime` | 否 | String | 以此格式提供開始日期和時間： <p>YYYY-MM-DDThh:mm:ss (如果您選取時區) <p>-或- <p>YYYY-MM-DDThh:mm:ssZ (如果您未選取時區) <p>例如，如果您希望 2017 年 9 月 18 日下午 2：00，請指定"2017-09-18T14：00"並選擇時區，如太平洋標準時間。 或是指定 "2017-09-18T14:00:00Z"，但不指定時區。 <p>**注：** 此開始時間在未來最多為 49 年，必須遵循[UTC 日期格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)的[ISO 8601 日期時間規範](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，但沒有[UTC 偏移](https://en.wikipedia.org/wiki/UTC_offset)量。 如果您不選取時區，就必須在結尾加上字母 "Z"，其中不含任何空格。 這個 "Z" 係指對等的[航海時間](https://en.wikipedia.org/wiki/Nautical_time)。 <p>就簡單排程來說，開始時間係指第一次發生的時間，而就複雜排程來說，觸發程序會在開始時間一到就立即引發。 [*我可以使用開始日期和時間的方式有哪些？*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **在這幾天內** | `weekDays` | 否 | 字串或字串陣列 | 如果您選取 [週]，可以選取想要在哪一天或哪幾天執行工作流程：**星期一**、**星期二**、**星期三**、**星期四**、**星期五**、**星期六**和**星期日** |
   | **在這幾小時內** | `hours` | 否 | 整數或整數陣列 | 如果選擇"Day"或"周"，則可以選擇 0 到 23 中的一個或多個整數作為要運行工作流的一天中的小時數。 <p><p>例如，如果指定"10"、"12"和"14"，則一天中的小時為上午 10 點、12 點和下午 2 點，但一天的分鐘數將根據重複開始的時間計算。 要設置一天的分鐘數，請指定 **"At 這些分鐘"** 屬性的值。 |
   | **在這幾分鐘內** | `minutes` | 否 | 整數或整數陣列 | 如果您選取 [天] 或 [週]，可以選取從 0 到 59 的一或多個整數，來表示想要在小時的哪幾個分鐘執行工作流程。 <p>例如，您可以指定 "30" 作為分鐘標記，然後使用上個範例代表一天中的整點，這樣就會得出上午 10:30、下午 12:30 及下午 2:30。 |
   |||||

   例如，假設今天是 2017 年 9 月 4 日星期一。 以下"重複"觸發器不會*早于*開始日期和時間，即 2017 年 9 月 18 日星期一上午 8：00（PST）。 不過，重複排程已設定為只在星期一的上午 10:30、下午 12:30 和下午 2:30。 因此觸發程序第一次引發並建立邏輯應用程式工作流程執行個體的時間是在上午 10:30。 若要深入了解開始時間如何運作，請參閱這些[開始時間範例](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)。

   未來則會在同一天的下午 12:30 和下午 2:30 執行。 每次重複都會建立自己的工作流程執行個體。 之後，整個排程會在下個星期一全部重複一次。 [*還有其他哪些範例重複？*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![進階排程範例](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > 只有當您選取 [天] 或 [週] 作為頻率時，觸發程序才會顯示指定週期的預覽。

1. 現在，使用其他操作構建剩餘的工作流。 有關可以添加的更多操作，請參閱[Azure 邏輯應用的連接器](../connectors/apis-list.md)。

## <a name="workflow-definition---recurrence"></a>工作流定義 - 重複

在邏輯應用使用 JSON 的基礎工作流定義中，您可以使用您選擇的選項查看["定期觸發器"定義](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)。 要查看此定義，在設計器工具列上，請選擇 **"代碼"視圖**。 要返回到設計器，請選擇設計器工具列"**設計器**"。

此示例顯示定期觸發器定義在基礎工作流定義中的外觀：

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2017-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>後續步驟

* [使用延遲操作暫停工作流](../connectors/connectors-native-delay.md)
* [適用於 Logic Apps 的連接器](../connectors/apis-list.md)
