---
title: 針對工作流程失敗進行疑難排解和診斷
description: 瞭解如何在 Azure Logic Apps 中，針對工作流程中的問題、錯誤和失敗進行疑難排解和診斷
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76905064"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>針對 Azure Logic Apps 中的失敗進行疑難排解和診斷

您的邏輯應用程式會產生可協助您診斷和偵錯應用程式邏輯中問題的資訊。 您可以藉由在 Azure 入口網站檢閱工作流程中的每個步驟來診斷邏輯應用程式。 或者，您可以在工作流程中加入一些步驟來進行執行時間的偵錯工具。

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>檢查觸發程式歷程記錄

每個邏輯應用程式執行會從觸發程式嘗試開始，因此如果不引發觸發程式，請遵循下列步驟：

1. 檢查觸發程式的歷程[記錄](../logic-apps/monitor-logic-apps.md#review-trigger-history)，以檢查觸發程式的狀態。 若要查看觸發程式嘗試的詳細資訊，請選取該觸發程式事件，例如：

   ![查看觸發程式狀態和歷程記錄](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. 請檢查觸發程式的輸入，以確認它們是否如您所預期般出現。 在 [**輸入連結**] 底下，選取顯示 [**輸入**] 窗格的連結。

   觸發程式輸入包括觸發程式所預期的資料，以及啟動工作流程所需的資訊。 檢查這些輸入可協助您判斷觸發程式輸入是否正確，以及是否符合條件，讓工作流程可以繼續。

   例如，這裡的 `feedUrl` 屬性具有不正確的 RSS 摘要值：

   ![審查觸發程式輸入中的錯誤](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. 檢查觸發程式輸出（如果有的話），以確認它們是否如您所預期般出現。 在 [**輸出連結**] 底下，選取顯示 [**輸出**] 窗格的連結。

   觸發程式輸出包含觸發程式傳遞至工作流程下一個步驟的資料。 查看這些輸出可協助您判斷是否將正確或預期的值傳遞至工作流程中的下一個步驟，例如：

   ![審查觸發程式輸出中的錯誤](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > 如果您發現任何無法辨識的內容，請在 Azure Logic Apps 中深入瞭解[不同的內容類型](../logic-apps/logic-apps-content-type.md)。

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>檢查執行歷程記錄

每次引發專案或事件的觸發程式時，Logic Apps 引擎都會針對每個專案或事件建立並執行個別的工作流程實例。 如果執行失敗，請遵循下列步驟來檢查該執行期間發生的情況，包括工作流程中每個步驟的狀態，以及每個步驟的輸入和輸出。

1. [檢查執行歷程記錄](../logic-apps/monitor-logic-apps.md#review-runs-history)，以檢查工作流程的執行狀態。 若要查看失敗執行的詳細資訊，包括該執行狀態中的所有步驟，請選取失敗的執行。

   ![查看執行歷程記錄並選取失敗的執行](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. 出現執行中的所有步驟之後，請展開第一個失敗的步驟。

   ![展開第一個失敗的步驟](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. 檢查失敗的步驟的輸入，以確認是否如預期般出現。

1. 檢閱特定執行中每個步驟的詳細資料。 在 [執行歷程記錄]**** 下，選取您想要檢查的執行。

   ![檢閱執行歷程記錄](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![檢視邏輯應用程式執行的詳細資料](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. 若要檢查特定步驟的輸入、輸出和任何錯誤訊息，請選擇該步驟，以便圖形展開並顯示詳細資料。 例如：

   ![檢視步驟詳細資料](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>執行執行階段偵錯

若要協助進行偵錯工具，您可以將診斷步驟新增至邏輯應用程式工作流程，以及檢查觸發程式和執行歷程記錄。 例如，您可以新增使用 [Webhook Tester](https://webhook.site/) 服務的步驟，使得您可以檢查 HTTP 要求，並判斷其確切的大小、圖形與格式。

1. 移至[Webhook 測試人員](https://webhook.site/)網站，並複製產生的唯一 URL。

1. 在您的邏輯應用程式中，新增 HTTP POST 動作加上您想要測試的本文內容，例如，運算式或另一個步驟輸出。

1. 將您的 URL 從 Webhook 測試人員貼入 HTTP POST 動作。

1. 若要在從 Logic Apps 引擎產生時，檢查要求的形成方式，請執行邏輯應用程式，然後重新流覽 Webhook 測試者網站以取得詳細資料。

## <a name="next-steps"></a>後續步驟

* [監視邏輯應用程式](../logic-apps/monitor-logic-apps.md)
