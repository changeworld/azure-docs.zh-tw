---
title: 建立自動化工作來管理和監視 Azure 資源
description: 設定可協助您管理 Azure 資源的自動化工作，並藉由建立在 Azure Logic Apps 上執行的工作流程來監視成本。
services: logic-apps
ms.suite: integration
ms.reviewer: deli, jonfan, logicappspm
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 1826b17a971b49fdfe8d5df02d71eb682b15db6f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91269721"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>藉由建立自動化工作 (預覽版來管理 Azure 資源和監視成本) 

> [!IMPORTANT]
> 這項功能處於公開預覽狀態，提供時沒有服務等級協定，因此不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

為協助您更輕鬆地管理 [Azure 資源](../azure-resource-manager/management/overview.md#terminology) ，您可以使用自動化工作範本為特定資源或資源群組建立自動化管理工作，這會根據資源類型而有所不同。 例如，針對 [Azure 儲存體帳戶](../storage/common/storage-account-overview.md)，您可以設定自動化工作，以將該儲存體帳戶的每月費用傳送給您。 針對 [Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)，您可以建立自動化工作，以預先定義的排程開啟或關閉該虛擬機器。

在幕後，自動化工作實際上是在 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 服務上執行的工作流程，並使用相同的 [定價費率](https://azure.microsoft.com/pricing/details/logic-apps/) 和 [計價模式](../logic-apps/logic-apps-pricing.md)來計費。 建立工作之後，您可以在邏輯應用程式設計工具中開啟工作來查看和編輯基礎工作流程。 在工作至少完成一次執行之後，您就可以檢查每次執行的狀態、歷程記錄、輸入和輸出。

以下是此預覽中目前可用的工作範本：

| 資源類型 | 自動化工作範本 |
|---------------|---------------------------|
| Azure 資源群組 | **刪除資源時** |
| 所有 Azure 資源 | **傳送資源的每月費用** |
| Azure 虛擬機器 | 此外： <p>- **關閉虛擬機器的電源** <br>- **啟動虛擬機器** |
| Azure 儲存體帳戶 | 此外： <p>- **刪除舊的 blob** |
| Azure Cosmos DB | 此外： <p>- **透過電子郵件傳送查詢結果** |
|||

本文說明如何完成下列工作：

* 針對特定的 Azure 資源[建立自動化](#create-automation-task)工作。

* [檢查工作的歷程記錄](#review-task-history)，其中包括執行狀態、輸入、輸出和其他歷程記錄資訊。

* [編輯工作](#edit-task) ，讓您可以更新工作，或在邏輯應用程式設計工具中自訂工作的基礎工作流程。

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>自動化工作與 Azure 自動化有何不同？

目前，您只能在資源層級建立自動化工作、查看工作的執行歷程記錄，以及編輯工作的基礎邏輯應用程式工作流程（由 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 服務提供支援）。 自動化工作比 [Azure 自動化](../automation/automation-intro.md)更基本且輕量。

相較之下，Azure 自動化是以雲端為基礎的自動化和設定服務，可在您的 Azure 和非 Azure 環境中支援一致的管理。 此服務包含程式自動化，可使用[runbook](../automation/automation-runbook-execution.md)、設定管理、[變更追蹤和清查](../automation/change-tracking.md)、更新管理、共用功能和異類功能[來協調處理](../automation/automation-intro.md#process-automation)程式。 自動化會在部署、作業和解除委任工作負載與資源期間，提供完整的控制權。

## <a name="prerequisites"></a>Prerequisites

* Azure 帳戶和訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 您要管理的 Azure 資源。 本文使用 Azure 儲存體帳戶作為範例。

* 如果您想要遵循此範例來使用 office 365 帳戶，這會使用 Office 365 Outlook 傳送電子郵件給您。

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>建立自動化工作

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，尋找您想要管理的資源。

1. 在 [資源] 功能表上，滾動至 [**自動化**] 區段，**然後選取 [** 工作]

   ![顯示 [自動化] 區段已選取 [工作] 功能表項目的 Azure 入口網站和儲存體帳戶資源功能表的螢幕擷取畫面。](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. **在 [工作**] 窗格中，選取 [**新增**]，讓您可以選取工作範本。

   ![顯示儲存體帳戶 [工作] 窗格的螢幕擷取畫面，其中工具列已選取 [新增]](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. 在 [ **新增工作** ] 窗格的 [ **選取範本**] 下，選取您要建立之工作的範本，然後選取 [ **下一步：驗證]**。

   此範例會繼續選取 [ **傳送資源的每月費用** ] 工作範本。

   ![顯示選取專案 [傳送資源的每月成本] 和 [下一步：驗證] 的螢幕擷取畫面](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. **在 [** **驗證**] 下的 [連線] 區段中，針對每個連線選取 [**建立**]，讓您可以提供該連接的驗證認證。 每個工作中的連線類型會根據工作而有所不同。

   ![顯示針對 Azure Resource Manager 連接所選取 [建立] 選項的螢幕擷取畫面](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. 當系統提示您時，請使用您的 Azure 帳號憑證登入。

   ![顯示 [登入] 選項的螢幕擷取畫面](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   每個成功驗證的連線看起來類似此範例：

   ![顯示已成功建立連線的螢幕擷取畫面](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. 驗證所有必要的連線之後，請選取 **[下一步：設定]**。

1. 在 [設定 **] 下，** 提供工作的名稱以及工作所需的任何其他資訊。 當您完成時，選取 [建立]。

   > [!NOTE]
   > 您無法在建立後變更工作名稱，因此，如果您 [編輯基礎工作流程](#edit-task-workflow)，請考慮仍然適用的名稱。 您對基礎工作流程所做的變更只會套用至您所建立的工作，而不會套用到工作範本。
   >
   > 例如，如果您為工作命名 `Send monthly cost` ，但稍後編輯基礎工作流程以每週執行，則無法將工作的名稱變更為 `Send weekly cost` 。

   傳送電子郵件通知的工作需要電子郵件地址。

   ![顯示所選工作所需資訊的螢幕擷取畫面](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   您建立的工作（會自動上線且正在執行）現在會出現在 [ **自動化** 工作] 清單中。

   ![顯示 [自動化工作] 清單的螢幕擷取畫面](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > 如果工作沒有立即出現，請嘗試重新整理 [工作] 清單，或稍等一下再重新整理。 在工具列上 **，選取 [** 重新整理]。

   選取的工作執行之後，您會收到類似下列範例的電子郵件：

   ![顯示工作所傳送電子郵件通知的螢幕擷取畫面](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>審核工作歷程記錄

若要查看工作的執行歷程記錄，以及它們的狀態、輸入、輸出及其他資訊，請遵循下列步驟：

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，尋找具有您想要審核之工作歷程記錄的資源。

1. 在資源功能表的 [ **設定**] 底下，選取 [ **自動化**工作]。

1. 在 [工作] 清單中，尋找您想要檢查的工作。 在該工作的 [ **執行** ] 資料行中，選取 [ **View**]。

   ![顯示工作和所選取 [View] 選項的螢幕擷取畫面](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   [ **執行歷程記錄** ] 窗格會顯示工作的所有執行，以及它們的狀態、開始時間、識別碼和執行持續時間。

   ![顯示工作執行、其狀態及其他資訊的螢幕擷取畫面](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   以下是執行的可能狀態：

   | 狀態 | 描述 |
   |--------|-------------|
   | **取消** | 工作在執行時取消。 |
   | **已失敗** | 工作至少有一個失敗的動作，但沒有任何後續的動作可處理失敗。 |
   | **執行中** | 工作目前正在執行。 |
   | **已成功** | 所有動作都已成功。 如果動作失敗，工作仍可順利完成，但在處理失敗時，會發生後續動作。 |
   | **等候中** | 尚未開始執行，因為先前的工作實例仍在執行中，所以已暫停執行。 |
   |||

   如需詳細資訊，請參閱 [審核執行歷程記錄](../logic-apps/monitor-logic-apps.md#review-runs-history)

1. 若要在執行中查看每個步驟的狀態和其他資訊，請選取該執行。

   **邏輯應用程式**的 [執行] 窗格隨即開啟，並顯示執行的基礎工作流程。

   * 工作流程一律會以 [*觸發*](../connectors/apis-list.md#triggers-actions)程式為開頭。 針對這項工作，工作流程會從 [**迴圈** 觸發](../connectors/connectors-native-recurrence.md)程式開始。

   * 每個步驟都會顯示其狀態和執行持續時間。 具有0秒持續時間的步驟花了不到1秒的時間執行。

   ![顯示執行、狀態和執行期間內每個步驟的螢幕擷取畫面](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. 若要檢查每個步驟的輸入和輸出，請選取擴充的步驟。

   這個範例會顯示迴圈觸發程式的輸入，因為此觸發程式只會在工作流程執行時指定工作流程的執行時間，而不會針對後續要處理的動作提供輸出，所以不會有輸出。

   ![顯示擴充的觸發程式和輸入的螢幕擷取畫面](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   相反地，[ **傳送電子郵件** ] 動作在工作流程和輸出中有先前動作的輸入。

   ![顯示擴充的動作、輸入和輸出的螢幕擷取畫面](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

若要瞭解如何建立您自己的自動化工作流程，讓您可以整合應用程式、資料、服務和系統，使其與 Azure 資源的自動化工作內容分開，請參閱 [快速入門：使用 Azure Logic Apps Azure 入口網站建立您的第一個整合工作流程](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="edit-task"></a>

## <a name="edit-the-task"></a>編輯工作

若要變更工作，您可以使用下列選項：

* [編輯工作「內嵌](#edit-task-inline) 」，讓您可以變更工作的屬性，例如，連接資訊或設定資訊，例如您的電子郵件地址。

* 在邏輯應用程式設計工具中[編輯工作的基礎工作流程](#edit-task-workflow)。

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>編輯內嵌工作

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，尋找包含您要更新之工作的資源。

1. 在資源功能表的 [ **自動化**] 下 **，選取 [** 工作]。

1. 在 [工作] 清單中，尋找您想要更新的工作。 開啟工作的省略號 (**...**) ] 功能表，然後選取 [ **內嵌編輯**]。

   ![螢幕擷取畫面：顯示已開啟的省略號功能表和選取的選項 [內嵌編輯]](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   依預設，[ **驗證** ] 索引標籤隨即出現，並顯示現有的連接。

1. 若要加入新的驗證認證，或為連線選取不同的現有驗證認證，請開啟連接的省略號 (**...**) 功能表，然後選取 [ **加入新連接** ] 或 [如果有的話，也可以使用不同的驗證認證]。

   ![顯示 [驗證] 索引標籤、現有連接和選取的省略號功能表的螢幕擷取畫面](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. 若要更新其他工作屬性，請選取 **[下一步：設定]**。

   針對此範例中的工作，唯一可供編輯的屬性是電子郵件地址。

   ![顯示 [設定] 索引標籤的螢幕擷取畫面](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. 完成時，選取 [儲存]。

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>編輯工作的基礎工作流程

當您變更自動化工作的基礎工作流程時，您的變更只會影響您所建立的工作實例，而不會影響建立工作的範本。 在您進行變更並儲存變更之後，您為原始工作提供的名稱可能無法正確描述此工作，因此您可能必須使用不同的名稱來重新建立工作。

> [!TIP]
> 最佳做法是複製基礎工作流程，讓您可以改為編輯複製的版本。 如此一來，您就可以在複本上進行和測試變更，而原始自動化工作會繼續運作並執行，而不會造成風險中斷或中斷現有的功能。 在您完成變更並符合新的版本成功執行之後，您可以停用或刪除原始的自動化工作，並將複製的版本用於自動化工作。 下列步驟包含如何複製工作流程的相關資訊。

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，尋找包含您要更新之工作的資源。

1. 在資源功能表的 [ **自動化**] 下 **，選取 [** 工作]。

1. 在 [工作] 清單中，尋找您想要更新的工作。 在 **[ (]**) 功能表中開啟工作的省略號，然後選取 [ **在 Logic Apps 中開啟**]。

   ![顯示開啟的省略號功能表和選取的選項 [在 Logic Apps 中開啟] 的螢幕擷取畫面](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   工作的基礎工作流程會在 Azure Logic Apps 服務中開啟，並顯示 [ **總覽** ] 窗格，您可以在其中查看工作可用的相同執行歷程記錄。

   ![螢幕擷取畫面，顯示已選取總覽窗格 Azure Logic Apps 視圖中的工作](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. 若要在邏輯應用程式設計工具中開啟基礎工作流程，請在邏輯應用程式的功能表上，選取 [ **邏輯應用程式設計**工具]。

   ![顯示已選取 [邏輯應用程式設計工具] 功能表選項的螢幕擷取畫面，以及基礎工作流程的設計工具介面](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   您現在可以編輯工作流程觸發程式和動作的屬性，以及編輯觸發程式和定義工作流程本身的動作。 不過，最佳作法是遵循下列步驟來複製您的工作流程，如此一來，當原始工作流程繼續運作並執行時，您就可以在複本上進行變更。

1. 若要複製您的工作流程並改為編輯複製的版本，請遵循下列步驟：

   1. 在 [邏輯應用程式工作流程] 功能表上，選取 **[總覽**]。

   1. 在 [總覽] 窗格的工具列上，選取 [ **複製**]。

   1. 在 [邏輯應用程式建立] 窗格的 [ **名稱**] 底下，輸入複製的邏輯應用程式工作流程的新名稱。

      除了 **邏輯應用程式狀態**以外，其他屬性無法進行編輯。 
      
   1. 在 [ **邏輯應用程式狀態**] 下選取 [ **已停用** ]，讓複製的工作流程不會在您進行變更時執行。 當您準備好要測試變更時，可以啟用工作流程。

   1. Azure 完成布建您複製的工作流程之後，請在邏輯應用程式設計工具中尋找並開啟該工作流程。

1. 若要查看觸發程式或動作的屬性，請展開該觸發程式或動作。

   例如，您可以將迴圈觸發程式變更為每週執行，而不是每月執行。

   ![顯示展開的週期觸發程式的螢幕擷取畫面，其中已開啟頻率清單來顯示可用的頻率選項](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   如需週期性觸發程式的詳細資訊，請參閱 [使用迴圈觸發程式建立、排程和執行循環性工作和工作流程](../connectors/connectors-native-recurrence.md)。 如需您可以使用之其他觸發程式和動作的詳細資訊，請參閱 [Azure Logic Apps 的連接器](../connectors/apis-list.md)。

1. 若要儲存您的變更，請在設計工具工具列上選取 [ **儲存**]。

   ![顯示設計工具工具列和所選取 [儲存] 命令的螢幕擷取畫面](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. 若要測試及執行更新的工作流程，請在設計工具的工具列上選取 [ **執行**]。

   執行完成之後，設計工具會顯示工作流程的執行詳細資料。

   ![顯示設計工具上工作流程執行詳細資料的螢幕擷取畫面](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. 若要停用工作流程，使工作無法繼續執行，請參閱 [Azure 入口網站中的管理邏輯應用程式](../logic-apps/manage-logic-apps-with-azure-portal.md)。

## <a name="provide-feedback"></a>提供意見反應

我們想要聽取您的意見！ 若要報告錯誤、提供意見反應，或詢問有關此預覽功能的問題，請 [洽詢 Azure Logic Apps 團隊](mailto:logicapps@microsoft.com)。

## <a name="next-steps"></a>後續步驟

* [管理 Azure 入口網站中的邏輯應用程式](../logic-apps/manage-logic-apps-with-azure-portal.md)
