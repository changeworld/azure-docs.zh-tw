---
title: 針對工作流程失敗進行疑難排解和診斷
description: 瞭解如何在 Azure 邏輯應用中對工作流中的問題、錯誤和故障進行故障排除和診斷
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905064"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>針對 Azure Logic Apps 中的失敗進行疑難排解和診斷

您的邏輯應用程式會產生可協助您診斷和偵錯應用程式邏輯中問題的資訊。 您可以藉由在 Azure 入口網站檢閱工作流程中的每個步驟來診斷邏輯應用程式。 或者，您可以將一些步驟添加到工作流以進行運行時調試。

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>檢查觸發器歷史記錄

每個邏輯應用運行都以觸發器嘗試開始，因此，如果觸發器未觸發，請按照以下步驟操作：

1. [通過檢查觸發器歷史記錄來檢查觸發器](../logic-apps/monitor-logic-apps.md#review-trigger-history)的狀態。 要查看有關觸發器嘗試的詳細資訊，請選擇該觸發器事件，例如：

   ![查看觸發器狀態和歷史記錄](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. 檢查觸發器的輸入，確認它們按預期顯示。 在 **"輸入"連結**下，選擇顯示 **"輸入"** 窗格的連結。

   觸發器輸入包括觸發器啟動工作流所需的資料。 查看這些輸入可以説明您確定觸發器輸入是否正確，以及是否滿足條件，以便工作流可以繼續。

   例如，`feedUrl`此處的屬性具有不正確的 RSS 源值：

   ![檢查觸發器輸入是否存在錯誤](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. 檢查觸發器輸出（如果有）以確認它們按預期顯示。 在 **"輸出"連結**下，選擇顯示 **"輸出"** 窗格的連結。

   觸發器輸出包括觸發器傳遞到工作流下一步的資料。 查看這些輸出可以説明您確定正確的值還是預期值傳遞給工作流中的下一步，例如：

   ![查看觸發器輸出是否存在錯誤](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > 如果發現任何無法識別的內容，請瞭解有關 Azure 邏輯應用中[不同內容類型](../logic-apps/logic-apps-content-type.md)的更多資訊。

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>檢查執行歷程記錄

每次觸發專案或事件時，邏輯應用引擎都會為每個項或事件創建並運行單獨的工作流實例。 如果運行失敗，請按照以下步驟查看該運行期間發生的情況，包括工作流中每個步驟的狀態以及每個步驟的輸入和輸出。

1. 通過[檢查執行歷程記錄檢查](../logic-apps/monitor-logic-apps.md#review-runs-history)工作流的運行狀態。 要查看有關失敗運行的詳細資訊，包括在其狀態下運行的所有步驟，請選擇失敗運行。

   ![查看執行歷程記錄並選擇失敗運行](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. 出現運行中的所有步驟後，展開第一個失敗步驟。

   ![展開第一個失敗步驟](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. 檢查失敗步驟的輸入，以確認它們是否按預期顯示。

1. 檢閱特定執行中每個步驟的詳細資料。 在 [執行歷程記錄]**** 下，選取您想要檢查的執行。

   ![檢閱執行歷程記錄](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![檢視邏輯應用程式執行的詳細資料](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. 若要檢查特定步驟的輸入、輸出和任何錯誤訊息，請選擇該步驟，以便圖形展開並顯示詳細資料。 例如：

   ![檢視步驟詳細資料](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>執行執行階段偵錯

為了説明調試，您可以將診斷步驟添加到邏輯應用工作流，同時查看觸發器並執行歷程記錄。 例如，您可以新增使用 [Webhook Tester](https://webhook.site/) 服務的步驟，使得您可以檢查 HTTP 要求，並判斷其確切的大小、圖形與格式。

1. 轉到[Webhook 測試程式](https://webhook.site/)網站並複製生成的唯一 URL。

1. 在邏輯應用中，添加 HTTP POST 操作以及要測試的正文內容，例如運算式或其他步驟輸出。

1. 將網址從 Webhook 測試儀粘貼到 HTTP POST 操作中。

1. 要查看從邏輯應用引擎生成請求時如何生成請求，請運行邏輯應用，然後重新訪問 Webhook 測試程式網站以瞭解更多詳細資訊。

## <a name="next-steps"></a>後續步驟

* [監視邏輯應用程式](../logic-apps/monitor-logic-apps.md)
