---
title: 監視狀態、查看歷史記錄和設置警報
description: 通過檢查運行狀態、查看觸發器歷史記錄和啟用 Azure 邏輯應用中的警報來排除邏輯應用的故障
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 495877f1c839de2cf3583a37180054c91bd9f139
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907769"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>監視運行狀態、查看觸發器歷史記錄並為 Azure 邏輯應用設置警報

[創建並運行邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)後，可以檢查邏輯應用的運行狀態、[執行歷程記錄](#review-runs-history)、[觸發歷史記錄](#review-trigger-history)和性能。 若要取得失敗或其他可能問題的通知，請設定[警示](#add-azure-alerts)。 例如，您可以建立警示來偵測「一小時有五個以上的執行失敗時」。

要進行即時事件監視和更豐富的調試，請使用[Azure 監視器日誌](../azure-monitor/overview.md)為邏輯應用設置診斷日誌記錄。 此 Azure 服務可説明您監視雲和本地環境，以便更輕鬆地維護其可用性和性能。 然後，您可以查找和查看事件，如觸發器事件、運行事件和操作事件。 通過在[Azure 監視器日誌](../azure-monitor/platform/data-platform-logs.md)中存儲此資訊，可以創建[日誌查詢](../azure-monitor/log-query/log-query-overview.md)，以説明您查找和分析此資訊。 還可以將此診斷資料用於其他 Azure 服務，如 Azure 存儲和 Azure 事件中心。 有關詳細資訊，請參閱使用[Azure 監視器監視邏輯應用](../logic-apps/monitor-logic-apps-log-analytics.md)。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>檢閱執行歷程記錄

每次觸發專案或事件時，邏輯應用引擎都會為每個項或事件創建並運行單獨的工作流實例。 預設情況下，每個工作流實例並行運行，因此無需工作流等待才能開始運行。 您可以查看該運行期間發生的情況，包括工作流中每個步驟的狀態以及每個步驟的輸入和輸出。

1. 在[Azure 門戶](https://portal.azure.com)中，在邏輯應用設計器中查找並打開邏輯應用。

   要在主 Azure 搜索框中查找邏輯應用，請輸入`logic apps`，然後選擇**邏輯應用**。

   ![查找並選擇"邏輯應用"服務](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure 門戶顯示與 Azure 訂閱關聯的所有邏輯應用。 您可以根據名稱、訂閱、資源組、位置等篩選此清單。

   ![查看與訂閱關聯的邏輯應用](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. 選擇邏輯應用，然後選擇 **"概述**"。

   在"**執行歷程記錄"** 下的"概述"窗格中，將顯示邏輯應用的所有過去、當前和任何等待運行。 如果清單顯示多個運行，並且找不到所需的條目，請嘗試篩選清單。 如果找不到您預期的資料，請嘗試在工具列上選取 [重新整理]****。

   ![概述、執行歷程記錄和其他邏輯應用資訊](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   以下是邏輯應用運行的可能狀態：

   | 狀態 | 描述 |
   |--------|-------------|
   | **已取消** | 工作流正在運行，但收到取消請求 |
   | **失敗** | 至少有一個操作失敗，並且未設置工作流中的後續操作來處理失敗 |
   | **正在執行** | 工作流程目前正在執行中。 <p>此狀態也可能出現在受限制的工作流中，或者由於當前定價計畫。 如需詳細資訊，請參閱[定價頁面上的動作限制](https://azure.microsoft.com/pricing/details/logic-apps/)。 如果設置[診斷日誌記錄](../logic-apps/monitor-logic-apps.md)，則可以獲取有關發生的任何節流事件的資訊。 |
   | **成功** | 所有動作都已成功。 <p>**注意**：如果特定操作中發生任何失敗，工作流中的後續操作將處理該失敗。 |
   | **等候** | 工作流尚未啟動或暫停，例如，由於早期工作流仍在運行。 |
   |||

1. 要查看特定運行的步驟和其他資訊，請在 **"執行歷程記錄"** 下選擇該運行。

   ![選擇要查看的特定運行](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   **Logic 應用運行**窗格顯示所選運行中的每個步驟、每個步驟的運行狀態以及每個步驟的執行時間，例如：

   ![特定運行中的每個操作](./media/monitor-logic-apps/logic-app-run-pane.png)

   要以清單形式查看此資訊，請在**邏輯應用運行**工具列上選擇 **"運行詳細資訊**"。

   ![在工具列上，選擇"運行詳細資訊"](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   "運行詳細資訊"視圖顯示每個步驟、其狀態和其他資訊。

   ![查看有關運行中每個步驟的詳細資訊](./media/monitor-logic-apps/review-logic-app-run-details.png)

   例如，您可以獲取運行的**關聯 ID**屬性，當您[對邏輯應用](https://docs.microsoft.com/rest/api/logic)使用 REST API 時可能需要該屬性。

1. 要獲取有關特定步驟的詳細資訊，請選擇任一選項：

   * 在 **"邏輯"應用運行**窗格中選擇步驟，以便形狀展開。 現在，您可以查看輸入、輸出等資訊以及該步驟中發生的任何錯誤，例如：

     ![在邏輯應用運行窗格中，查看失敗步驟](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * 在 **"邏輯"應用運行詳細資訊**窗格中，選擇所需的步驟。

     ![在運行詳細資訊窗格中，查看失敗步驟](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     現在，您可以查看該步驟的輸入和輸出等資訊，例如：

   > [!NOTE]
   > 在 Logic Apps 服務內，會加密所有執行階段詳細資料和事件。 只有在使用者要求檢視該資料時，才對其進行解密。 您可以使用[Azure 基於角色的存取控制 （RBAC）](../role-based-access-control/overview.md)[隱藏執行歷程記錄中的輸入和輸出](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate)，或控制使用者訪問此資訊。

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>檢閱觸發程序歷程記錄

每個邏輯應用運行都以觸發器開始。 觸發器歷史記錄列出了邏輯應用進行的所有觸發器嘗試，以及有關每個觸發器嘗試的輸入和輸出的資訊。

1. 在[Azure 門戶](https://portal.azure.com)中，在邏輯應用設計器中查找並打開邏輯應用。

   要在主 Azure 搜索框中查找邏輯應用，請輸入`logic apps`，然後選擇**邏輯應用**。

   ![查找並選擇"邏輯應用"服務](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure 門戶顯示與 Azure 訂閱關聯的所有邏輯應用。 您可以根據名稱、訂閱、資源組、位置等篩選此清單。

   ![查看與訂閱關聯的邏輯應用](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. 選擇邏輯應用，然後選擇 **"概述**"。

1. 在邏輯應用的功能表上，選擇 **"概述**"。 在 **"摘要"** 部分中，在 **"評估"** 下，選擇 **"查看觸發歷史記錄**"。

   ![查看邏輯應用的觸發器歷史記錄](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   觸發器歷史記錄窗格顯示邏輯應用已進行的所有觸發器嘗試。 每次觸發專案或事件時，邏輯應用引擎都會創建運行工作流的單獨邏輯應用實例。 根據預設，每個執行個體會並行執行，使得任何工作流程不需等待即可開始執行。 因此，如果邏輯應用同時觸發多個專案，則每個專案都會顯示具有相同日期和時間的觸發器條目。

   ![不同項的多次觸發嘗試](./media/monitor-logic-apps/logic-app-trigger-history.png)

   以下是觸發程序嘗試的可能狀態：

   | 狀態 | 描述 |
   |--------|-------------|
   | **失敗** | 發生錯誤。 若要檢閱失敗的觸發程序所產生的任何錯誤訊息，請選取該觸發程序嘗試並選擇 [輸出]****。 例如，您可能會發現無效的輸入。 |
   | **跳** | 觸發程序檢查了端點，但不找到任何資料。 |
   | **成功** | 觸發程序檢查了端點，並找到可用的資料。 通常，「已引發」狀態也會隨著這個狀態一起出現。 如果沒有，則觸發程序定義可能有條件或 `SplitOn` 命令不符合要求。 <p>此狀態可以套用至手動觸發程序、循環觸發程序或輪詢觸發程序。 觸發程序可以順利執行，但執行本身在動作產生未處理的錯誤時可能會失敗。 |
   |||

   > [!TIP]
   > 您可以重新檢查觸發程序，而不需等待下一個週期。 在概覽工具列上，選擇 **"運行觸發器**"並選擇強制檢查的觸發器。 或者，在 Logic Apps 設計工具工具列上選取 [執行]****。

1. 要查看有關特定觸發器嘗試的資訊，請在觸發器窗格中選擇該觸發器事件。 如果清單顯示許多觸發器嘗試，並且找不到所需的條目，請嘗試篩選清單。 如果找不到您預期的資料，請嘗試在工具列上選取 [重新整理]****。

   ![查看特定觸發器嘗試](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   您現在可以查看有關所選觸發器事件的資訊，例如：

   ![查看特定觸發器資訊](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>設置監視警報

要根據特定指標獲取警報或邏輯應用的閾值超過，[請在 Azure 監視器 中設置警報](../azure-monitor/platform/alerts-overview.md)。 了解 [Azure 中的計量](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。 要設置警報而不使用[Azure 監視器](../log-analytics/log-analytics-overview.md)，請按照以下步驟操作。

1. 在邏輯應用功能表上，在 **"監視**"下，選擇 **"警報** > **新警報規則**"。

   ![新增邏輯應用程式的警示](./media/monitor-logic-apps/add-new-alert-rule.png)

1. 在 **"創建規則**"窗格（**在"資源**"下）上，選擇邏輯應用（如果尚未選中）。 在 **"條件**"下，選擇 **"添加**"，以便可以定義觸發警報的條件。

   ![添加規則的條件](./media/monitor-logic-apps/add-condition-for-rule.png)

1. 在 **"配置信號邏輯**窗格"上，查找並選擇要為其獲取警報的信號。 您可以使用搜索框，或者按字母順序對信號進行排序，請選擇 **"信號名稱**列"標題。

   例如，如果要在觸發器失敗時發送警報，請按照以下步驟操作：

   1. 在 **"信號名稱**"列中，查找並選擇**觸發器失敗**信號。

      ![選擇用於創建警報的信號](./media/monitor-logic-apps/find-and-select-signal.png)

   1. 在為所選信號打開的資訊窗格中，在 **"警報"邏輯**下設置您的條件，例如：

   1. 對於**運算子**，選擇**大於 或 等於**。

   1. 對於**聚合類型**，選擇 **"計數**"。

   1. 對於**閾值**，輸入`1`。

   1. 在 **"條件預覽**"下，確認您的病情看起來正確。

   1. **在"基於 評估"** 下，設置運行警報規則的間隔和頻率。 對於**聚合細微性（期間），** 選擇對資料進行分組的期間。 對於**評估頻率**，選擇要檢查條件的頻率。

   1. 準備就緒後，選擇 **"完成**"。

   下面是已完成的條件：

   ![設置警示準則](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   **"創建規則**"頁現在顯示您創建的條件和運行該警報的成本。

   !["創建規則"頁上的新警報](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. 為警報指定名稱、可選說明和嚴重性級別。 在創建設置**打開時保持啟用規則**，或關閉，直到準備好啟用該規則。

1. 完成後，選擇 **"創建警報規則**"。

> [!TIP]
> 若要從警示執行邏輯應用程式，您可以在工作流程中包含[要求觸發程序](../connectors/connectors-native-reqres.md)，以讓您執行工作，例如下列範例：
> 
> * [張貼到 Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [傳送文字](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [將訊息新增至佇列](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>後續步驟

* [使用 Azure 監視器監視邏輯應用](../logic-apps/monitor-logic-apps-log-analytics.md)