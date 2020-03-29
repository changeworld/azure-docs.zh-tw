---
title: 計畫處理連續資料的任務
description: 創建和運行通過 Azure 邏輯應用中的滑動視窗處理連續資料的定期任務
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: ab4bf802772c95d8c48a8cdba48def05e8a2761b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74786906"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>在 Azure 邏輯應用中使用滑動視窗觸發器計畫並運行連續資料的任務

要定期運行必須連續處理資料塊的任務、進程或作業，可以使用**滑動視窗**觸發器啟動邏輯應用工作流。 您可以設置日期和時間以及啟動工作流的時區以及重複該工作流的重複。 如果由於任何原因錯過了重複，此觸發器將處理那些錯過的重複。 例如，在資料庫和備份存儲之間同步資料時，請使用滑動視窗觸發器，以便資料同步而不會造成間隙。 有關內置計畫觸發器和操作的詳細資訊，請參閱[使用 Azure 邏輯應用計畫並運行定期自動化、任務和工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

以下是此觸發器支援的一些模式：

* 立即運行 *，每*n 秒、分鐘或小時重複一次。

* 從特定的日期和時間開始，然後每*n*秒、分鐘或小時運行並重複一次。 使用此觸發器，您可以指定過去的開始時間，該時間運行所有過去的重複。

* 在運行之前，延遲特定持續時間中的每個重複。

有關此觸發器和定期觸發器之間的差異，或者有關計畫定期工作流的詳細資訊，請參閱使用 Azure[邏輯應用計畫並運行重複的自動化任務、進程和工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

> [!TIP]
> 如果要觸發邏輯應用，並且將來只運行一次，請參閱[僅運行一次作業](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果沒有訂閱，可以[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 關於[邏輯應用](../logic-apps/logic-apps-overview.md)的基本知識。 如果您是邏輯應用的新功能，請[瞭解如何創建第一個邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-sliding-window-trigger"></a>添加滑動視窗觸發器

1. 登錄到 Azure[門戶](https://portal.azure.com)。 建立空白邏輯應用程式。

1. 邏輯應用設計器顯示後，在搜索框中，輸入"滑動視窗"作為篩選器。 從觸發器清單中，選擇此觸發器作為邏輯應用工作流中的第一步：**滑動視窗**

   ![選擇"滑動視窗"觸發器](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. 設定重複的間隔和頻率。 在此範例中，將這些屬性設定為每週執行工作流程。

   ![設定間隔和頻率](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | 屬性 | 必要 | JSON 名稱 | 類型 | 描述 |
   |----------|----------|-----------|------|-------------|
   | **區間** | 是 | interval | 整數  | 描述工作流程根據 frequency 多久執行一次的正整數。 以下是最小和最大間隔： <p>- 小時：1-12,000 個小時 </br>- 分鐘：1-72,000 分鐘 </br>- 秒：1-9,999,999 秒<p>例如，如果間隔為 6，並且頻率為"小時"，則定期為每 6 小時一次。 |
   | **頻率** | 是 | frequency | String | 重複的時間單位：**第二**、**分鐘**或**小時** |
   ||||||

   ![高級定期選項](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   有關更多重複選項，打開 **"添加新參數**清單"。 
   您選擇的任何選項在選擇後將顯示在觸發器上。

   | 屬性 | 必要 | JSON 名稱 | 類型 | 描述 |
   |----------|----------|-----------|------|-------------|
   | **延遲** | 否 | delay | String | 使用[ISO 8601 日期時間規範](https://en.wikipedia.org/wiki/ISO_8601#Durations)延遲每次重複的持續時間 |
   | **時區** | 否 | timeZone | String | 只有當您有指定開始時間時才適用，因為此觸發程序並不接受 [UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 選取您要套用的時區。 |
   | **開始時間** | 否 | startTime | String | 以此格式提供開始日期和時間： <p>YYYY-MM-DDThh:mm:ss (如果您選取時區) <p>-或- <p>YYYY-MM-DDThh:mm:ssZ (如果您未選取時區) <p>例如，如果您希望 2017 年 9 月 18 日下午 2：00，請指定"2017-09-18T14：00"並選擇時區，如太平洋標準時間。 或是指定 "2017-09-18T14:00:00Z"，但不指定時區。 <p>**注意：** 這個開始時間必須依照 [UTC 日期時間格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)中的 [ISO 8601 日期時間規格](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，但不含 [UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 如果您不選取時區，就必須在結尾加上字母 "Z"，其中不含任何空格。 這個 "Z" 係指對等的[航海時間](https://en.wikipedia.org/wiki/Nautical_time)。 <p>對於簡單的計畫，開始時間是第一次出現，而對於高級重複，觸發器不會比開始時間早觸發。 [*我可以使用開始日期和時間的方式有哪些？*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. 現在，使用其他操作構建剩餘的工作流。 有關可以添加的更多操作，請參閱[Azure 邏輯應用的連接器](../connectors/apis-list.md)。

## <a name="workflow-definition---sliding-window"></a>工作流定義 - 滑動視窗

在邏輯應用使用 JSON 的基礎工作流定義中，您可以使用您選擇的選項查看滑動視窗觸發器定義。 要查看此定義，在設計器工具列上，請選擇 **"代碼"視圖**。 要返回到設計器，請選擇設計器工具列"**設計器**"。

此示例顯示滑動視窗觸發器定義在基礎工作流定義中的外觀，其中每個重複的延遲為每小時重複的延遲為五秒：

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>後續步驟

* [延遲工作流中的下一個操作](../connectors/connectors-native-delay.md)
* [適用於 Logic Apps 的連接器](../connectors/apis-list.md)