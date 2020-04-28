---
title: 排程處理連續資料的工作
description: 建立並執行循環性工作，以使用中的滑動視窗來處理連續的資料 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: ab4bf802772c95d8c48a8cdba48def05e8a2761b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74786906"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>使用 Azure Logic Apps 中的滑動視窗觸發程式來排程和執行連續資料的工作

若要定期執行必須處理連續區塊中資料的工作、進程或作業，您可以使用**滑動視窗**觸發程式來啟動邏輯應用程式工作流程。 您可以設定日期和時間，以及用來啟動工作流程的時區，以及重複該工作流程的週期。 如果因任何原因而遺漏了週期，此觸發程式會處理這些遺漏的週期。 例如，在資料庫與備份儲存體之間同步處理資料時，請使用滑動視窗觸發程式，讓資料得以同步處理，而不會產生間距。 如需內建排程觸發程式和動作的詳細資訊，請參閱[使用 Azure Logic Apps 排程和執行循環性自動化、工作和工作流程](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

以下是此觸發程式支援的一些模式：

* 立即執行，並每隔*n*秒、分鐘或小時重複。

* 從特定的日期和時間開始，然後執行並重複每*n*秒、分鐘或小時。 透過此觸發程式，您可以指定過去的開始時間，這會執行所有過去的週期。

* 在執行之前，請先延遲特定持續時間的每一個週期。

如需此觸發程式與迴圈觸發程式之間的差異，或如需排程週期性工作流程的詳細資訊，請參閱[使用 Azure Logic Apps 排程和執行循環性的自動化工作、進程和工作流程](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

> [!TIP]
> 如果您想要觸發邏輯應用程式，並在未來只執行一次，請參閱[僅執行一次作業](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [邏輯應用程式](../logic-apps/logic-apps-overview.md)的基本知識。 如果您不熟悉邏輯應用程式，請瞭解[如何建立您的第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-sliding-window-trigger"></a>新增滑動視窗觸發程式

1. 登入 [Azure 入口網站](https://portal.azure.com)。 建立空白邏輯應用程式。

1. 邏輯應用程式設計工具出現之後，在搜尋方塊中，輸入「滑動視窗」作為篩選準則。 從觸發程式清單中，選取此觸發程式作為邏輯應用程式工作流程中的第一個步驟：**滑動視窗**

   ![選取 [滑動視窗] 觸發程式](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. 設定重複的間隔和頻率。 在此範例中，將這些屬性設定為每週執行工作流程。

   ![設定間隔和頻率](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | 屬性 | 必要 | JSON 名稱 | 類型 | 描述 |
   |----------|----------|-----------|------|-------------|
   | **期間** | 是 | interval | 整數 | 描述工作流程根據 frequency 多久執行一次的正整數。 以下是最小和最大間隔： <p>- 小時：1-12,000 個小時 </br>- 分鐘：1-72,000 分鐘 </br>- 秒：1-9,999,999 秒<p>例如，如果間隔為6，而 frequency 為 "Hour"，則週期為每隔6小時。 |
   | **頻率** | 是 | frequency | String | 週期的時間單位：**秒**、**分鐘**或**小時** |
   ||||||

   ![Advanced 迴圈選項](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   如需更多週期選項，請開啟 [**加入新的參數**] 清單。 
   您選取的任何選項都會出現在選取範圍後的觸發程式上。

   | 屬性 | 必要 | JSON 名稱 | 類型 | 描述 |
   |----------|----------|-----------|------|-------------|
   | **延遲** | 否 | delay | String | 使用[ISO 8601 日期時間規格](https://en.wikipedia.org/wiki/ISO_8601#Durations)來延遲每個週期的持續時間 |
   | **時區** | 否 | timeZone | String | 只有當您有指定開始時間時才適用，因為此觸發程序並不接受 [UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 選取您要套用的時區。 |
   | **開始時間** | 否 | startTime | String | 提供此格式的開始日期和時間： <p>YYYY-MM-DDThh:mm:ss (如果您選取時區) <p>-或- <p>YYYY-MM-DDThh:mm:ssZ (如果您未選取時區) <p>例如，如果您想要2017年9月18日下午2:00，請指定 "2017-09-18T14：00： 00"，然後選取時區，例如太平洋標準時間。 或是指定 "2017-09-18T14:00:00Z"，但不指定時區。 <p>**注意：** 這個開始時間必須依照 [UTC 日期時間格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)中的 [ISO 8601 日期時間規格](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，但不含 [UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 如果您不選取時區，就必須在結尾加上字母 "Z"，其中不含任何空格。 這個 "Z" 係指對等的[航海時間](https://en.wikipedia.org/wiki/Nautical_time)。 <p>針對簡單的排程，開始時間是第一次發生，而在進行先進的週期時，觸發程式不會比開始時間更快地引發。 [*我可以使用開始日期和時間的方式有哪些？*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. 現在使用其他動作建立剩餘的工作流程。 如需您可以新增的其他動作，請參閱[Azure Logic Apps 的連接器](../connectors/apis-list.md)。

## <a name="workflow-definition---sliding-window"></a>工作流程定義-滑動視窗

在邏輯應用程式的基礎工作流程定義中，使用 JSON，您可以使用您選擇的選項來查看滑動視窗觸發程式定義。 若要查看此定義，請在設計工具工具列上選擇 [程式**代碼視圖**]。 若要回到設計工具，請在設計工具工具列上選擇 [**設計師**]。

這個範例顯示滑動視窗觸發程式定義可能會在基礎工作流程定義中尋找，其中每個週期的延遲為每小時週期的五秒：

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

* [延遲工作流程中的下一個動作](../connectors/connectors-native-delay.md)
* [適用於 Logic Apps 的連接器](../connectors/apis-list.md)