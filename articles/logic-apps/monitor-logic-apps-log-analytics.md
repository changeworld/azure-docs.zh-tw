---
title: 使用 Azure 監視器記錄來監視邏輯應用程式
description: 藉由設定 Azure 監視器記錄並收集診斷資料，來針對邏輯應用程式進行疑難排解 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 3e41f92f9e41f7a05102e8c0e1c2edb81fa50bf3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79270234"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>設定 Azure 監視器記錄和收集診斷資料以進行 Azure Logic Apps

若要在執行時間期間取得有關邏輯應用程式的更豐富詳細資料，您可以設定及使用[Azure 監視器記錄](../azure-monitor/platform/data-platform-logs.md)來記錄和儲存執行時間資料和事件的相關資訊，例如觸發程式事件、執行事件和[Log Analytics 工作區](../azure-monitor/platform/resource-logs-collect-workspace.md)中的動作事件。 [Azure 監視器](../azure-monitor/overview.md)可協助您監視您的雲端和內部部署環境，以便更輕鬆地維護其可用性和效能。 藉由使用 Azure 監視器記錄，您可以建立[記錄查詢](../azure-monitor/log-query/log-query-overview.md)，協助您收集及審查這項資訊。 您也可以[將此診斷資料與其他 Azure 服務搭配使用](#extend-data)，例如 Azure 儲存體和 Azure 事件中樞。

若要設定邏輯應用程式的記錄，您可以在[建立邏輯應用程式時啟用 Log analytics](#logging-for-new-logic-apps)，也可以在現有邏輯應用程式的 Log Analytics 工作區中[安裝 Logic Apps 管理解決方案](#install-management-solution)。 此解決方案會提供邏輯應用程式執行的匯總資訊，並包含特定詳細資料，例如狀態、執行時間、重新提交狀態和相互關聯識別碼。 然後，若要啟用記錄並建立此資訊的查詢，請[設定 Azure 監視器記錄](#set-up-resource-logs)。

本文說明如何在您建立邏輯應用程式、如何安裝和設定 Logic Apps 管理解決方案，以及如何設定和建立 Azure 監視器記錄的查詢時，啟用 Log Analytics。

## <a name="prerequisites"></a>Prerequisites

開始之前，您需要[Log Analytics 工作區](../azure-monitor/platform/resource-logs-collect-workspace.md)。 如果您沒有工作區，請瞭解[如何建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>為新的邏輯應用程式啟用 Log Analytics

當您建立邏輯應用程式時，可以開啟 Log Analytics。

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，于您提供用來建立邏輯應用程式之資訊的窗格上，依照下列步驟執行：

   1. 在 [ **Log Analytics**] 底下，選取 [**開啟**]。

   1. 從 [ **Log Analytics 工作區**] 清單中，選取您想要從邏輯應用程式執行傳送資料的工作區。

      ![提供邏輯應用程式資訊](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      完成此步驟之後，Azure 會建立您的邏輯應用程式，此應用程式現在會與您的 Log Analytics 工作區建立關聯。 此外，此步驟會自動在您的工作區中安裝 Logic Apps 管理解決方案。

1. 當您完成時，選取 [建立]  。

1. 執行邏輯應用程式之後，若要查看您的邏輯應用程式執行，請[繼續進行這些步驟](#view-logic-app-runs)。

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>安裝 Logic Apps 管理解決方案

如果您在建立邏輯應用程式時開啟 Log Analytics，請略過此步驟。 您已在 Log Analytics 工作區中安裝 Logic Apps 管理解決方案。

1. 在[Azure 入口網站](https://portal.azure.com)的搜尋方塊中，輸入`log analytics workspaces`，然後選取 [ **Log Analytics 工作區**]。

   ![選取 [Log Analytics 工作區]](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. 在 [ **Log Analytics 工作區**] 底下，選取您的工作區。

   ![選取 Log Analytics 工作區](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. 在 [**總覽**] 窗格的 [**開始使用 Log Analytics** > **設定監視解決方案**] 底下，選取 [ **View 解決方案**]。

   ![在 [總覽] 窗格中，選取 [View 解決方案]](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. 在 **[總覽**] 底下，選取 [**新增**]。

   ![在 [總覽] 窗格中，加入新的方案](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. 在**Marketplace**開啟之後，請在搜尋方塊中輸入`logic apps management`，然後選取 [ **Logic Apps 管理**]。

   ![從 Marketplace 選取 [Logic Apps 管理]](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. 在 [解決方案描述] 窗格上，選取 [**建立**]。

   ![選取 [建立] 以新增「Logic Apps 管理」解決方案](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. 請檢查並確認您要在其中安裝解決方案的 Log Analytics 工作區，然後再次選取 [**建立**]。

   ![針對 [Logic Apps 管理] 選取 [建立]](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   在 Azure 將解決方案部署至包含 Log Analytics 工作區的 Azure 資源群組之後，解決方案會出現在工作區的 [摘要] 窗格上。

   ![工作區摘要窗格](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>設定 Azure 監視器記錄

當您將運行時間事件和資料的相關資訊儲存在[Azure 監視器記錄](../azure-monitor/platform/data-platform-logs.md)檔中時，您可以建立[記錄查詢](../azure-monitor/log-query/log-query-overview.md)，協助您尋找並查看這項資訊。

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並選取邏輯應用程式。

1. 在邏輯應用程式功能表的 [**監視**] 底下，選取 [**診斷設定** > ] [**新增診斷設定**]。

   ![在 [監視] 底下，選取 [診斷設定] > [新增診斷設定]](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. 若要建立設定，請遵循下列步驟：

   1. 提供設定的名稱。

   1. 選取 [傳送至 Log Analytics]****。

   1. 針對 [**訂**用帳戶]，選取與您的 Log Analytics 工作區相關聯的 Azure 訂用帳戶。

   1. 針對 [ **Log Analytics 工作區**]，選取您要使用的工作區。

   1. 在 [**記錄**] 下，選取 [ **WorkflowRuntime** ] 類別，以指定您想要記錄的事件類別目錄。

   1. 若要選取所有計量，請在 [**度量**] 底下選取 [ **AllMetrics**]。

   1. 完成時，選取 [儲存]****。

   例如：

   ![選取用於記錄的 Log Analytics 工作區和資料](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>查看邏輯應用程式執行狀態

執行邏輯應用程式之後，您可以在 Log Analytics 工作區中查看這些執行的相關資料。

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，尋找並開啟您的 Log Analytics 工作區。

1. 在工作區的功能表上，選取 [**工作區摘要** > ] [**Logic Apps 管理**]。

   ![邏輯應用程式執行狀態和計數](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > 如果 [Logic Apps 管理] 圖格未在執行後立即顯示結果，請**嘗試選取 [** 重新整理] 或稍待片刻，然後再試一次。

   在這裡，您的邏輯應用程式執行會依名稱或執行狀態分組。 此頁面也將同時顯示邏輯應用程式執行動作或觸發程序失敗的相關詳細資料。

   ![邏輯應用程式執行的狀態摘要](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. 若要查看特定邏輯應用程式或狀態的所有執行，請選取該邏輯應用程式或狀態的資料列。

   以下範例顯示特定邏輯應用程式的所有執行：

   ![查看邏輯應用程式的執行和狀態](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   針對您[設定追蹤屬性](#extend-data)的動作，您也可以在 [**追蹤的屬性**] 資料行中選取 [ **view** ] 來查看這些屬性。 請可以使用資料行篩選來搜尋追蹤屬性。

   ![檢視邏輯應用程式的追蹤屬性](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

   > [!NOTE]
   > 追蹤的屬性或已完成的事件在出現于 Log Analytics 工作區之前，可能會遇到10-15 分鐘的延遲。
   > 此外，此頁面上的**重新提交**功能目前無法使用。

1. 若要篩選您的結果，您可以執行用戶端和伺服器端的篩選。

   * **用戶端篩選**：針對每個資料行，選取您想要的篩選準則，例如：

     ![資料行篩選範例](./media/monitor-logic-apps-log-analytics/filters.png)

   * **伺服器端篩選**：若要選取特定時間範圍或限制出現的執行次數，請使用頁面頂端的 [範圍] 控制項。 根據預設，一次只能出現 1,000 筆記錄。

     ![變更時間範圍](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. 若要查看特定執行的所有動作及其詳細資料，請選取邏輯應用程式執行的列。

   以下範例顯示特定邏輯應用程式執行的所有動作和觸發程式：

   ![檢視邏輯應用程式執行的動作](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>將診斷資料傳送至 Azure 儲存體並 Azure 事件中樞

除了 Azure 監視器記錄之外，您還可以擴充將邏輯應用程式的診斷資料與其他 Azure 服務搭配使用的方式，例如：

* [將 Azure 資源記錄封存到儲存體帳戶](../azure-monitor/platform/resource-logs-collect-storage.md)
* [將 Azure 平臺記錄串流至 Azure 事件中樞](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

您可以接著使用其他服務的遙測和分析來取得即時監視，例如 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) 和 [Power BI](../azure-monitor/platform/powerbi.md)。 例如：

* [將資料從事件中樞串流至串流分析](../stream-analytics/stream-analytics-define-inputs.md)
* [使用串流分析分析串流資料並在 Power BI 中建立即時分析儀表板](../stream-analytics/stream-analytics-power-bi-dashboard.md)

根據您想要傳送診斷資料的位置，請確定您先[建立 azure 儲存體帳戶](../storage/common/storage-create-storage-account.md)，或[建立 azure 事件中樞](../event-hubs/event-hubs-create.md)。 然後，您可以選取要傳送該資料的目的地。 只有當您使用儲存體帳戶時，才適用保留週期。

![將資料傳送至 Azure 儲存體帳戶或事件中樞](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Azure 監視器診斷事件

每個診斷事件都會有邏輯應用程式和該事件的詳細資料，例如，狀態、開始時間、結束時間等等。 若要以程式設計方式設定監視、追蹤和記錄，您可以使用此資訊搭配適用于[Azure Logic Apps 的 REST API](https://docs.microsoft.com/rest/api/logic)和[Azure 監視器的 REST API](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows)。 您也可以使用出現`clientTrackingId`在`trackedProperties`中的和屬性。 

* `clientTrackingId`： 如果未提供，Azure 會自動產生這個識別碼，並相互關聯邏輯應用程式執行之間的事件，包括從邏輯應用程式呼叫的任何巢狀工作流程。 您可以藉由在觸發程式要求中傳遞`x-ms-client-tracking-id`標頭與您的自訂識別碼值，在觸發程式中手動指定此識別碼。 您可以使用要求觸發程序、HTTP 觸發程序或 Webhook 觸發程序。

* `trackedProperties`：若要追蹤診斷資料中的輸入或輸出，您可以`trackedProperties`使用邏輯應用程式設計工具或直接在邏輯應用程式的 JSON 定義中，將區段新增至動作。 追蹤的屬性只能追蹤單一動作的輸入和輸出，但您可以使用事件的 `correlation` 屬性來跨執行中的動作進行相互關聯。 若要追蹤一個以上的屬性，一或多個屬性， `trackedProperties`請將區段和您想要的屬性加入至動作定義。

  以下範例顯示「**初始化變數**」動作定義如何包含動作輸入中的追蹤屬性，其中輸入是陣列，而不是記錄。

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  這個範例會顯示多個追蹤的屬性：

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

這個範例會顯示`ActionCompleted`事件如何包含`clientTrackingId`和`trackedProperties`屬性：

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
   "category": "WorkflowRuntime",
   "level": "Information",
   "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
   "properties": {
      "$schema": "2016-06-01",
      "startTime": "2016-07-09T17:09:53.4336305Z",
      "endTime": "2016-07-09T17:09:53.5430281Z",
      "status": "Succeeded",
      "code": "OK",
      "resource": {
         "subscriptionId": "<subscription-ID>",
         "resourceGroupName": "MyResourceGroup",
         "workflowId": "<logic-app-workflow-ID>",
         "workflowName": "MyLogicApp",
         "runId": "08587361146922712057",
         "location": "westus",
         "actionName": "Http"
      },
      "correlation": {
         "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
         "clientTrackingId": "<my-custom-tracking-ID>"
      },
      "trackedProperties": {
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>後續步驟

* [建立監視和追蹤查詢](../logic-apps/create-monitoring-tracking-queries.md)
* [使用 Azure 監視器記錄監視 B2B 訊息](../logic-apps/monitor-b2b-messages-log-analytics.md)
