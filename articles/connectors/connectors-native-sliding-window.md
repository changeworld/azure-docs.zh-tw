---
title: 排程處理連續資料的工作
description: 使用 Azure Logic Apps 中的滑動視窗來建立和執行可處理連續資料的週期性工作
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 103805fbf395dc120acc96fbcee273abcf14939d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010413"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>使用 Azure Logic Apps 中的滑動視窗觸發程式來排程和執行連續資料的工作

若要定期執行必須以連續區塊處理資料的工作、進程或作業，您可以使用 **滑動視窗** 觸發程式來啟動邏輯應用程式工作流程。 您可以設定日期和時間，以及用來啟動工作流程的時區，以及重複該工作流程的週期。 如果因為任何原因而遺漏了週期，例如因為中斷或停用的工作流程，此觸發程式會處理這些遺漏的週期。 例如，在資料庫與備份儲存體之間同步處理資料時，請使用滑動視窗觸發程式，讓資料同步處理，而不會產生間距。 如需內建排程觸發程式和動作的詳細資訊，請參閱 [使用 Azure Logic Apps 排程和執行循環性自動化、工作和工作流程](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

以下是此觸發程式支援的一些模式：

* 立即執行並重複每 *n* 秒、分鐘、小時、天、周或月數。

* 從特定日期和時間開始，然後每隔 *n* 秒、分鐘、小時、天、周或月執行並重複。 您可以使用此觸發程式來指定過去的開始時間，以執行所有過去的週期。

* 在執行之前，延遲每個週期的特定持續時間。

如需此觸發程式與迴圈觸發程式之間的差異，或如需排程週期性工作流程的詳細資訊，請參閱 [使用 Azure Logic Apps 排程和執行循環性的自動化工作、進程和工作流程](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

> [!TIP]
> 如果您想要觸發邏輯應用程式，並且在未來只執行一次，請參閱 [只執行一次作業](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [邏輯應用程式](../logic-apps/logic-apps-overview.md)的基本知識。 如果您不熟悉邏輯應用程式，請了解[如何建立您的第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-sliding-window-trigger"></a>新增滑動視窗觸發程式

1. 登入 [Azure 入口網站](https://portal.azure.com)。 建立空白邏輯應用程式。

1. 邏輯應用程式設計工具出現之後，在 [搜尋] 方塊中輸入 `sliding window` 做為篩選準則。 從觸發程式清單中，選取 **滑動視窗** 觸發程式作為邏輯應用程式工作流程中的第一個步驟。

   ![選取 [滑動視窗] 觸發程式](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. 設定重複的間隔和頻率。 在此範例中，將這些屬性設定為每週執行工作流程。

   ![設定間隔和頻率](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | 屬性 | JSON 名稱 | 必要 | 類型 | 說明 |
   |----------|----------|-----------|------|-------------|
   | **間隔** | `interval` | 是 | 整數 | 描述工作流程根據 frequency 多久執行一次的正整數。 以下是最小和最大間隔： <p>- 月：1-16 個月 <br>-周：1-71 周 <br>- 天：1-500 天 <br>- 小時：1-12,000 個小時 <br>- 分鐘：1-72,000 分鐘 <br>- 秒：1-9,999,999 秒 <p>例如，如果 interval 是 6，而 frequency 是「月」，則週期為每隔 6 個月。 |
   | **頻率** | `frequency` | 是 | String | 重複的時間單位：**秒**、**分鐘**、**小時**、**天**、**週** 或 **月** |
   ||||||

   ![Advanced 迴圈選項](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   如需更多週期選項，請開啟 [ **加入新的參數** ] 清單。 選取之後，您選取的任何選項都會出現在觸發程式上。

   | 屬性 | 必要 | JSON 名稱 | 類型 | 描述 |
   |----------|----------|-----------|------|-------------|
   | **延遲** | 否 | delay | String | 使用[ISO 8601 日期時間規格](https://en.wikipedia.org/wiki/ISO_8601#Durations)來延遲每個週期的持續時間 |
   | **時區** | 否 | timeZone | String | 只有當您有指定開始時間時才適用，因為此觸發程序並不接受 [UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 選取您要套用的時區。 |
   | **開始時間** | 否 | startTime | String | 以下列格式提供開始日期和時間： <p>YYYY-MM-DDThh:mm:ss (如果您選取時區) <p>-或- <p>YYYY-MM-DDThh:mm:ssZ (如果您未選取時區) <p>舉例來說，如果您想要在2017年9月18日下午2:00，請指定 "2017-09-18T14：00： 00"，然後選取時區，例如太平洋標準時間。 或是指定 "2017-09-18T14:00:00Z"，但不指定時區。 <p>**注意：** 這個開始時間必須依照 [UTC 日期時間格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)中的 [ISO 8601 日期時間規格](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，但不含 [UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 如果您不選取時區，就必須在結尾加上字母 "Z"，其中不含任何空格。 這個 "Z" 係指對等的[航海時間](https://en.wikipedia.org/wiki/Nautical_time)。 <p>針對簡單的排程，開始時間是第一次發生的時間，而在進行先進的週期時，觸發程式不會引發任何比開始時間更快的時間。 [*我可以使用開始日期和時間的方式有哪些？*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. 現在使用其他動作來建立剩餘的工作流程。 如需您可以新增的其他動作，請參閱 [Azure Logic Apps 的連接器](../connectors/apis-list.md)。

## <a name="workflow-definition---sliding-window"></a>工作流程定義-滑動視窗

在邏輯應用程式的基礎工作流程定義中，使用 JSON，您可以使用您選擇的選項來查看滑動視窗觸發程式定義。 若要查看這個定義，請在設計工具的工具列上選擇 [程式 **代碼視圖**]。 若要返回設計工具，請選擇設計工具工具列上的 [ **設計** 工具]。

此範例顯示滑動視窗觸發程序定義可能會查看基礎工作流程定義，其中每個週期的延遲為每小時週期的五秒：

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
