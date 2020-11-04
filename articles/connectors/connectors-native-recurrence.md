---
title: 排程週期性工作和工作流程
description: 使用 Azure Logic Apps 中的週期觸發程式來排程和執行循環性的自動化工作和工作流程
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 11/03/2020
ms.openlocfilehash: a9c6017ed46853b9dec991fc02097ee88c67a7cb
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342176"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>使用 Azure Logic Apps 中的迴圈觸發程式來建立、排程和執行循環性的工作和工作流程

若要定期執行特定排程的工作、進程或作業，您可以使用內建的 **週期排程** 觸發程式來啟動邏輯應用程式工作流程。 您可以設定日期和時間，以及用來啟動工作流程的時區，以及重複該工作流程的週期。 如果因為任何原因而遺漏了週期，例如因為中斷或停用的工作流程，此觸發程式不會處理遺失的週期，但會在下一個排程的間隔重新開機週期。 如需內建排程觸發程式和動作的詳細資訊，請參閱 [使用 Azure Logic Apps 排程和執行循環性自動化、工作和工作流程](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

以下是此觸發程式支援的一些模式，以及更先進的週期和複雜排程：

* 立即執行並重複每 *n* 秒、分鐘、小時、天、周或月數。

* 從特定日期和時間開始，然後每隔 *n* 秒、分鐘、小時、天、周或月執行並重複。

* 每天執行並重複一次或多次，例如上午 8:00 和下午 5:00。

* 每週執行並重複，但僅在特定的星期幾，例如星期六和星期日。

* 每週執行並重複，但僅在特定的星期幾和時間，例如星期一到星期五的上午 8:00 和下午 5:00。

如需此觸發程式與滑動視窗觸發程式之間的差異，或如需排程週期性工作流程的詳細資訊，請參閱 [使用 Azure Logic Apps 排程和執行循環性自動化工作、進程和工作流程](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

> [!TIP]
> 如果您想要觸發邏輯應用程式，並且在未來只執行一次，請參閱 [只執行一次作業](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [邏輯應用程式](../logic-apps/logic-apps-overview.md)的基本知識。 如果您不熟悉邏輯應用程式，請了解[如何建立您的第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-recurrence-trigger"></a>新增迴圈觸發程式

1. 登入 [Azure 入口網站](https://portal.azure.com)。 建立空白邏輯應用程式。

1. 邏輯應用程式設計工具出現之後，在 [搜尋] 方塊中輸入 `recurrence` 做為篩選準則。 從觸發程式清單中，選取此觸發程式作為邏輯應用程式工作流程中的第一個步驟： **週期**

   ![選取 [週期] 觸發程式](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. 設定重複的間隔和頻率。 在此範例中，將這些屬性設定為每週執行工作流程。

   ![設定間隔和頻率](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | 屬性 | JSON 名稱 | 必要 | 類型 | 說明 |
   |----------|-----------|----------|------|-------------|
   | **間隔** | `interval` | 是 | 整數 | 描述工作流程根據 frequency 多久執行一次的正整數。 以下是最小和最大間隔： <p>- 月：1-16 個月 <br>-周：1-71 周 <br>- 天：1-500 天 <br>- 小時：1-12,000 個小時 <br>- 分鐘：1-72,000 分鐘 <br>- 秒：1-9,999,999 秒<p>例如，如果 interval 是 6，而 frequency 是「月」，則週期為每隔 6 個月。 |
   | **頻率** | `frequency` | 是 | String | 重複的時間單位： **秒** 、 **分鐘** 、 **小時** 、 **天** 、 **週** 或 **月** |
   ||||||

   > [!IMPORTANT]
   > 當週期未指定 advanced 排程選項時，未來的週期會以上次執行時間為基礎。
   > 這些週期的開始時間可能會因為像是儲存體呼叫期間的延遲等因素而漂移。 若要確定您的邏輯應用程式不會錯過迴圈，特別是當頻率為天或更久時，請使用下列其中一個選項：
   > 
   > * 提供週期的開始時間。
   > 
   > * 使用 [ **在這些** 時數] 和 [ **在這幾分鐘** ] 屬性，指定執行迴圈的時數和分鐘數。
   > 
   > * 使用 [滑動視窗觸發](../connectors/connectors-native-sliding-window.md)程式，而不是迴圈觸發程式。

1. 若要設定 advanced 排程選項，請開啟 [ **加入新的參數** ] 清單。 選取之後，您選取的任何選項都會出現在觸發程式上。

   ![進階排程選項](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | 屬性 | JSON 名稱 | 必要 | 類型 | 描述 |
   |----------|-----------|----------|------|-------------|
   | **時區** | `timeZone` | 否 | String | 只有當您有指定開始時間時才適用，因為此觸發程序並不接受 [UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 選取您要套用的時區。 |
   | **開始時間** | `startTime` | 否 | String | 提供開始日期和時間，其未來最多會有49年，且必須遵循 [ISO 8601 日期時間規格](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) （ [utc 日期時間格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)），但不含 [utc 時差](https://en.wikipedia.org/wiki/UTC_offset)： <p><p>YYYY-MM-DDThh:mm:ss (如果您選取時區) <p>-或- <p>YYYY-MM-DDThh:mm:ssZ (如果您未選取時區) <p>舉例來說，如果您想要在2020年9月18日下午2:00，請指定 "2020-09-18T14：00： 00"，然後選取時區，例如太平洋標準時間。 或者，指定 "2020-09-18T14：00： 00Z"，而不使用時區。 <p><p>**重要事項：** 如果您未選取時區，就必須在結尾加上字母 "Z"，不含任何空格。 這個 "Z" 係指對等的[航海時間](https://en.wikipedia.org/wiki/Nautical_time)。 如果您選取 [時區] 值，就不需要在 [ **開始時間** ] 值的結尾加上「Z」。 如果您這樣做，Logic Apps 會忽略時區值，因為 "Z" 表示 UTC 時間格式。 <p><p>就簡單排程來說，開始時間係指第一次發生的時間，而就複雜排程來說，觸發程序會在開始時間一到就立即引發。 [*我可以使用開始日期和時間的方式有哪些？*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **在這幾天內** | `weekDays` | 否 | 字串或字串陣列 | 如果您選取 [週]，可以選取想要在哪一天或哪幾天執行工作流程： **星期一** 、 **星期二** 、 **星期三** 、 **星期四** 、 **星期五** 、 **星期六** 和 **星期日** |
   | **在這幾小時內** | `hours` | 否 | 整數或整數陣列 | 如果您選取 [天] 或 [周]，可以選取從0到23的一或多個整數，做為您想要執行工作流程的當日時間。 <p><p>例如，如果您指定「10」、「12」和「14」，則會在一天中的時數取得上午10點、下午12點和下午2點，但會根據週期開始時間來計算一天中的分鐘數。 若要設定一天中的特定分鐘數（例如，10:00 AM、12:00 PM 和 2:00 PM），請使用 [ **在以下幾分鐘** ] 屬性來指定這些值。 |
   | **在這幾分鐘內** | `minutes` | 否 | 整數或整數陣列 | 如果您選取 [天] 或 [週]，可以選取從 0 到 59 的一或多個整數，來表示想要在小時的哪幾個分鐘執行工作流程。 <p>例如，您可以指定 "30" 作為分鐘標記，然後使用上個範例代表一天中的整點，這樣就會得出上午 10:30、下午 12:30 及下午 2:30。 <p>**注意** ：有時候，觸發執行的時間戳記最多可能會從排程的時間變更為1分鐘。 如果您需要將時間戳記完全按照排程傳遞給後續的動作，您可以使用範本運算式，據以變更時間戳記。 如需詳細資訊，請參閱 [運算式的日期和時間函數](../logic-apps/workflow-definition-language-functions-reference.md#date-time-functions)。 |
   |||||

   例如，假設今天是2020年9月4日星期五。 下列週期觸發程式不會在開始日期和時間（在2020年9月18日星期五上午8:00 時 *）引發。* 不過，重複排程已設定為只在星期一的上午 10:30、下午 12:30 和下午 2:30。 因此，第一次引發觸發程式並建立邏輯應用程式工作流程實例時，會在星期一上午10:30。 若要深入了解開始時間如何運作，請參閱這些[開始時間範例](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)。

   未來則會在同一天的下午 12:30 和下午 2:30 執行。 每次重複都會建立自己的工作流程執行個體。 之後，整個排程會在下個星期一全部重複一次。 [*還有其他哪些範例重複？*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![進階排程範例](./media/connectors-native-recurrence/recurrence-trigger-advanced-schedule-options.png)

   > [!NOTE]
   > 只有當您選取 [天] 或 [週] 作為頻率時，觸發程序才會顯示指定週期的預覽。

1. 現在使用其他動作來建立剩餘的工作流程。 如需您可以新增的其他動作，請參閱 [Azure Logic Apps 的連接器](../connectors/apis-list.md)。

## <a name="workflow-definition---recurrence"></a>工作流程定義-週期

在邏輯應用程式的基礎工作流程定義中，使用 JSON，您可以使用您選擇的選項來查看 [週期觸發程序定義](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) 。 若要查看這個定義，請在設計工具的工具列上選擇 [程式 **代碼視圖** ]。 若要返回設計工具，請選擇設計工具工具列上的 [ **設計** 工具]。

這個範例會示範週期性觸發程序定義在基礎工作流程定義中的外觀：

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
         "startTime": "2020-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>後續步驟

* [使用延遲動作暫停工作流程](../connectors/connectors-native-delay.md)
* [適用於 Logic Apps 的連接器](../connectors/apis-list.md)
