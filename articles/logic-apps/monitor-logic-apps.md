---
title: 監視狀態、查看歷程記錄，以及設定警示
description: 藉由檢查執行狀態、查看觸發程式歷程記錄，以及在 Azure Logic Apps 中啟用警示，對邏輯應用程式進行疑難排解
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 69d018db26a42c331ff41d242eae54d6fcc43990
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536244"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>監視執行狀態、查看觸發程式歷程記錄，以及設定 Azure Logic Apps 的警示

[建立並執行邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)之後，您可以檢查邏輯應用程式的執行狀態、[執行歷程記錄](#review-runs-history)、[觸發記錄](#review-trigger-history)和效能。 若要取得有關失敗或其他可能問題的通知，請設定[警示](#add-azure-alerts)。 例如，您可以建立警示來偵測「一小時有五個以上的執行失敗時」。

如需即時事件監控和更豐富的偵錯工具，請使用[Azure 監視器記錄](../azure-monitor/overview.md)來設定邏輯應用程式的診斷記錄。 此 Azure 服務可協助您監視您的雲端和內部部署環境，以便更輕鬆地維護其可用性和效能。 接著，您可以尋找並查看事件，例如觸發程式事件、執行事件和動作事件。 藉由將此資訊儲存在[Azure 監視器記錄](../azure-monitor/platform/data-platform-logs.md)中，您可以建立[記錄查詢](../azure-monitor/log-query/log-query-overview.md)，協助您尋找及分析此資訊。 您也可以將此診斷資料與其他 Azure 服務搭配使用，例如 Azure 儲存體和 Azure 事件中樞。 如需詳細資訊，請參閱[使用 Azure 監視器監視邏輯應用程式](../logic-apps/monitor-logic-apps-log-analytics.md)。

> [!NOTE]
> 如果您的邏輯應用程式在已建立以使用[內部存取端點](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)的[整合服務環境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中執行，您只能*從虛擬網路內部*查看並存取邏輯應用程式執行歷程記錄的輸入和輸出。 請確定您在私人端點和您想要存取其執行歷程記錄的電腦之間具有網路連線能力。 例如，您的用戶端電腦可以存在於 ISE 的虛擬網路內，或位於連線到 ISE 虛擬網路的虛擬網路內部，例如透過對等互連或虛擬私人網路。 如需詳細資訊，請參閱 [ISE 端點存取](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)。 

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>檢閱執行歷程記錄

每次引發專案或事件的觸發程式時，Logic Apps 引擎都會針對每個專案或事件建立並執行個別的工作流程實例。 根據預設，每個工作流程實例會以平行方式執行，因此在開始執行之前，工作流程都不需要等待。 您可以檢查在執行期間發生的狀況，包括工作流程中每個步驟的狀態，以及每個步驟的輸入和輸出。

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，尋找並開啟邏輯應用程式。

   若要尋找您的邏輯應用程式，請在主要 Azure 搜尋方塊中輸入 `logic apps` ，然後選取 [ **Logic Apps**]。

   ![尋找並選取 [Logic Apps] 服務](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure 入口網站會顯示與您的 Azure 訂用帳戶相關聯的所有邏輯應用程式。 您可以根據名稱、訂用帳戶、資源群組、位置等來篩選這份清單。

   ![查看與訂閱相關聯的邏輯應用程式](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. 選取您的邏輯應用程式，然後選取 **[總覽**]。

   在 [總覽] 窗格的 [**執行歷程記錄**] 底下，會顯示邏輯應用程式的所有過去、目前和等候執行。 如果清單顯示許多回合，但您找不到所需的專案，請嘗試篩選清單。 如果找不到您預期的資料，請嘗試在工具列上選取 [重新整理]****。

   ![總覽、執行歷程記錄，以及其他邏輯應用程式資訊](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   以下是邏輯應用程式執行的可能狀態：

   | 狀態 | 描述 |
   |--------|-------------|
   | **取消** | 工作流程正在執行，但收到取消要求 |
   | **已失敗** | 至少有一個動作失敗，且未設定工作流程中的後續動作來處理失敗 |
   | **執行中** | 工作流程目前正在執行中。 <p>此狀態也會顯示在節流的工作流程中，或因為目前的定價方案。 如需詳細資訊，請參閱[定價頁面上的動作限制](https://azure.microsoft.com/pricing/details/logic-apps/)。 如果您設定[診斷記錄](../logic-apps/monitor-logic-apps.md)，您可以取得任何發生之節流事件的相關資訊。 |
   | **已成功** | 所有動作都已成功。 <p>**注意**：如果特定動作中發生任何失敗，則工作流程中的後續動作會處理該失敗。 |
   | **正在等待** | 工作流程尚未啟動或暫停（例如，因為先前仍在執行的工作流程）。 |
   |||

1. 若要檢查特定執行的步驟和其他資訊，請在 [**執行歷程記錄**] 下選取該執行。

   ![選取要審查的特定執行](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   [**邏輯應用程式執行**] 窗格會顯示所選執行中的每個步驟、每個步驟的執行狀態，以及執行每個步驟所花費的時間，例如：

   ![特定執行中的每個動作](./media/monitor-logic-apps/logic-app-run-pane.png)

   若要以清單形式查看此資訊，請在**邏輯應用程式**的 [執行] 工具列上，選取 [**執行詳細資料**]。

   ![在工具列上，選取 [執行詳細資料]。](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   [執行詳細資料] 視圖會顯示每個步驟、其狀態和其他資訊。

   ![查看執行中每個步驟的詳細資料](./media/monitor-logic-apps/review-logic-app-run-details.png)

   例如，您可以取得執行的相互**關聯識別碼**屬性，當您使用[Logic Apps 的 REST API](/rest/api/logic)時，可能會需要它。

1. 若要取得特定步驟的詳細資訊，請選取其中一個選項：

   * 在 [**邏輯應用程式執行**] 窗格中，選取步驟使圖形展開。 您現在可以查看輸入、輸出和該步驟中發生的任何錯誤等資訊，例如：

     ![在 [邏輯應用程式執行] 窗格中，view failed step](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * 在**邏輯應用程式的 [執行詳細資料**] 窗格中，選取您想要的步驟。

     ![在 [執行詳細資料] 窗格中，view failed step](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     您現在可以查看該步驟的輸入和輸出之類的資訊，例如：

   > [!NOTE]
   > 在 Logic Apps 服務內，會加密所有執行階段詳細資料和事件。 只有在使用者要求檢視該資料時，才對其進行解密。 您可以[隱藏執行歷程記錄中的輸入和輸出，](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate)或使用[Azure 角色型存取控制（RBAC）](../role-based-access-control/overview.md)來控制使用者對此資訊的存取權。

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>檢閱觸發程序歷程記錄

每個邏輯應用程式的執行都會以觸發程式為開頭。 觸發歷程記錄會列出您的邏輯應用程式所做的所有觸發程式嘗試，以及每個觸發程式嘗試的輸入和輸出的相關資訊。

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，尋找並開啟邏輯應用程式。

   若要尋找您的邏輯應用程式，請在主要 Azure 搜尋方塊中輸入 `logic apps` ，然後選取 [ **Logic Apps**]。

   ![尋找並選取 [Logic Apps] 服務](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure 入口網站會顯示與您的 Azure 訂用帳戶相關聯的所有邏輯應用程式。 您可以根據名稱、訂用帳戶、資源群組、位置等來篩選這份清單。

   ![查看與訂閱相關聯的邏輯應用程式](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. 選取您的邏輯應用程式，然後選取 **[總覽**]。

1. 在邏輯應用程式的功能表上，選取 **[總覽**]。 在 [**摘要**] 區段的 [**評估**] 底下，選取 [**查看觸發**程式歷程記錄]。

   ![查看邏輯應用程式的觸發歷程記錄](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   [觸發程式歷程記錄] 窗格會顯示邏輯應用程式所做的所有觸發程式嘗試。 每當觸發程式針對專案或事件引發時，Logic Apps 引擎會建立個別的邏輯應用程式實例，以執行工作流程。 根據預設，每個執行個體會並行執行，使得任何工作流程不需等待即可開始執行。 因此，如果您的邏輯應用程式同時在多個專案上觸發，每個專案都會出現具有相同日期和時間的觸發程式專案。

   ![不同專案的多個觸發程式嘗試](./media/monitor-logic-apps/logic-app-trigger-history.png)

   以下是觸發程序嘗試的可能狀態：

   | 狀態 | 描述 |
   |--------|-------------|
   | **已失敗** | 發生錯誤。 若要檢閱失敗的觸發程序所產生的任何錯誤訊息，請選取該觸發程序嘗試並選擇 [輸出]****。 例如，您可能會發現無效的輸入。 |
   | **已略過** | 觸發程序檢查了端點，但不找到任何資料。 |
   | **已成功** | 觸發程序檢查了端點，並找到可用的資料。 通常，「已引發」狀態也會隨著這個狀態一起出現。 如果沒有，則觸發程序定義可能有條件或 `SplitOn` 命令不符合要求。 <p>此狀態可以套用至手動觸發程序、循環觸發程序或輪詢觸發程序。 觸發程序可以順利執行，但執行本身在動作產生未處理的錯誤時可能會失敗。 |
   |||

   > [!TIP]
   > 您可以重新檢查觸發程序，而不需等待下一個週期。 在 [總覽] 工具列上，選取 [**執行觸發**程式]，然後選取觸發程式來強制執行檢查。 或者，在 Logic Apps 設計工具工具列上選取 [執行]****。

1. 若要查看有關特定觸發程式嘗試的資訊，請在 [觸發程式] 窗格上選取該觸發事件。 如果清單顯示許多觸發程式嘗試，而您找不到想要的專案，請嘗試篩選清單。 如果找不到您預期的資料，請嘗試在工具列上選取 [重新整理]****。

   ![查看特定的觸發程式嘗試](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   您現在可以查看所選觸發程式事件的相關資訊，例如：

   ![查看特定的觸發程式資訊](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>設定監視警示

若要根據特定計量或邏輯應用程式超過閾值來取得警示，請[在 Azure 監視器中設定警示](../azure-monitor/platform/alerts-overview.md)。 了解 [Azure 中的計量](../azure-monitor/platform/data-platform.md)。 若要在不使用[Azure 監視器](../azure-monitor/log-query/log-query-overview.md)的情況下設定警示，請遵循下列步驟。

1. 在邏輯應用程式功能表的 [**監視**] 底下，選取 [**警示**] [  >  **新增警示規則**]。

   ![新增邏輯應用程式的警示](./media/monitor-logic-apps/add-new-alert-rule.png)

1. 在 [**建立規則**] 窗格的 [**資源**] 底下，選取您的邏輯應用程式（如果尚未選取）。 在 [**條件**] 底下，選取 [**新增**]，讓您可以定義觸發警示的條件。

   ![新增規則的條件](./media/monitor-logic-apps/add-condition-for-rule.png)

1. 在 [**設定信號邏輯**] 窗格中，尋找並選取您想要取得警示的信號。 您可以使用 [搜尋] 方塊，或依字母順序排序信號，選取 [**信號名稱**] 欄標題。

   例如，如果您想要在觸發程式失敗時傳送警示，請遵循下列步驟：

   1. 在 [**信號名稱**] 欄中，尋找並選取 [**觸發程式失敗**] 信號。

      ![選取要建立警示的信號](./media/monitor-logic-apps/find-and-select-signal.png)

   1. 在針對所選信號開啟的資訊窗格中，于 [**警示邏輯**] 下設定您的條件，例如：

   1. 針對 [**運算子**]，選取 [**大於或等於**]。

   1. 針對 [**匯總類型**]，選取 [**計數**]。

   1. 在 [**臨界值**] 中，輸入 `1` 。

   1. 在 [**條件預覽**] 底下，確認您的條件是否正確。

   1. 在 [**評估依據**] 底下，設定執行警示規則的間隔和頻率。 針對 [**匯總細微性（期間）**]，選取分組資料的期間。 在 [**評估頻率**] 中，選取您想要檢查條件的頻率。

   1. 當您準備好時，請選取 [**完成**]。

   以下是完成的條件：

   ![設定警示的條件](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   [**建立規則**] 頁面現在會顯示您所建立的條件以及執行該警示的成本。

   ![[建立規則] 頁面上的 [新增警示]](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. 為您的警示指定 [名稱]、[選擇性描述] 和 [嚴重性] 層級。 請在開啟 [**建立時啟用規則**] 設定，或關閉直到您準備好啟用規則為止。

1. 當您完成時，請選取 [**建立警示規則**]。

> [!TIP]
> 若要從警示執行邏輯應用程式，您可以在工作流程中包含[要求觸發程序](../connectors/connectors-native-reqres.md)，以讓您執行工作，例如下列範例：
> 
> * [張貼到 Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [傳送文字](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [將訊息新增至佇列](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>後續步驟

* [使用 Azure 監視器監視邏輯應用程式](../logic-apps/monitor-logic-apps-log-analytics.md)
