---
title: 使用 Azure 監視器日誌監視邏輯應用
description: 通過設置 Azure 監視器日誌和為 Azure 邏輯應用收集診斷資料來排除邏輯應用的疑難排解
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 3e41f92f9e41f7a05102e8c0e1c2edb81fa50bf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270234"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>設置 Azure 監視器日誌並收集 Azure 邏輯應用的診斷資料

要在運行時獲取有關邏輯應用的更豐富的調試資訊，可以設置和使用[Azure 監視器日誌](../azure-monitor/platform/data-platform-logs.md)來記錄和存儲有關運行時資料和事件的資訊，如日誌[分析工作區](../azure-monitor/platform/resource-logs-collect-workspace.md)中的觸發器事件、運行事件和操作事件。 [Azure 監視器](../azure-monitor/overview.md)可説明您監視雲和本地環境，以便更輕鬆地維護其可用性和性能。 通過使用 Azure 監視器日誌，您可以創建[日誌查詢](../azure-monitor/log-query/log-query-overview.md)，以説明您收集和查看此資訊。 還可以[將此診斷資料與其他 Azure 服務](#extend-data)（如 Azure 存儲和 Azure 事件中心）一起使用。

要設置邏輯應用的日誌記錄，可以在[創建邏輯應用時啟用日誌分析](#logging-for-new-logic-apps)，也可以在現有邏輯應用的日誌分析工作區中[安裝邏輯應用管理解決方案](#install-management-solution)。 此解決方案為邏輯應用運行提供聚合資訊，並包括特定詳細資訊，如狀態、執行時間、重新提交狀態和相關指示。 然後，要啟用日誌記錄和創建此資訊的查詢，[應設置 Azure 監視器日誌](#set-up-resource-logs)。

本文演示如何在創建邏輯應用時啟用日誌分析，如何安裝和設置邏輯應用管理解決方案，以及如何為 Azure 監視器日誌設置和創建查詢。

## <a name="prerequisites"></a>Prerequisites

開始之前，您需要一個[日誌分析工作區](../azure-monitor/platform/resource-logs-collect-workspace.md)。 如果沒有工作區，請[瞭解如何創建日誌分析工作區](../azure-monitor/learn/quick-create-workspace.md)。

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>為新的邏輯應用啟用日誌分析

創建邏輯應用時，可以打開日誌分析。

1. 在[Azure 門戶](https://portal.azure.com)中，在提供創建邏輯應用的資訊的窗格中，請按照以下步驟操作：

   1. 在 **"日誌分析**"下，選擇 **"打開**"。

   1. 在**日誌分析工作區**清單中，選擇要從邏輯應用運行中發送資料的工作區。

      ![提供邏輯應用程式資訊](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      完成此步驟之後，Azure 會建立您的邏輯應用程式，此應用程式現在會與您的 Log Analytics 工作區建立關聯。 此外，此步驟會自動在工作區中安裝邏輯應用管理解決方案。

1. 當您完成時，選取 [建立]****。

1. 運行邏輯應用後，要查看邏輯應用運行，[請繼續執行這些步驟](#view-logic-app-runs)。

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>安裝 Logic Apps 管理解決方案

如果在創建邏輯應用時打開日誌分析，請跳過此步驟。 您已經在日誌分析工作區中安裝了邏輯應用管理解決方案。

1. 在[Azure 門戶](https://portal.azure.com)的搜索框中，輸入`log analytics workspaces`，然後選擇**日誌分析工作區**。

   ![選擇"日誌分析工作區"](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. 在 **"日誌分析"工作區**下，選擇工作區。

   ![選取 Log Analytics 工作區](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. 在 **"概述"** 窗格中 **，在"開始使用日誌分析** > **配置監視解決方案**"下，選擇 **"查看解決方案**"。

   ![在"概覽"窗格中，選擇"查看解決方案"](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. 在 **"概述"** 下，選擇 **"添加**"。

   ![在概覽窗格上，添加新解決方案](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. **市場**打開後，在搜索框中輸入`logic apps management`，然後選擇**邏輯應用管理**。

   ![從應用商店中，選擇"邏輯應用管理"](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. 在解決方案描述窗格中，選擇 **"創建**"。

   ![選擇"創建"以添加"邏輯應用管理"解決方案](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. 查看並確認要安裝解決方案的日誌分析工作區，然後選擇"再次**創建**"。

   ![為"邏輯應用管理"選擇"創建"](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Azure 將解決方案部署到包含日誌分析工作區的 Azure 資源組後，該解決方案將顯示在工作區的摘要窗格中。

   ![工作區摘要窗格](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>設定 Azure 監視器記錄

在[Azure 監視器日誌](../azure-monitor/platform/data-platform-logs.md)中存儲有關運行時事件和資料的資訊時，可以創建[日誌查詢](../azure-monitor/log-query/log-query-overview.md)，以説明您查找和查看此資訊。

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並選取邏輯應用程式。

1. 在邏輯應用功能表中，在 **"監視**"下，選擇 **"診斷設置** > **添加診斷設置**"。

   ![在"監視"下，選擇"診斷設置">"添加診斷設置"](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. 要創建設置，請按照以下步驟操作：

   1. 提供設置的名稱。

   1. 選取 [傳送至 Log Analytics]****。

   1. 對於**訂閱**，選擇與日誌分析工作區關聯的 Azure 訂閱。

   1. 對於**日誌分析工作區**，請選擇要使用的工作區。

   1. 在**日誌**下，選擇 **"工作流執行時間**"類別，該類別指定要記錄的事件類別。

   1. 要選擇所有指標，請在**指標**下 選擇 **"所有指標**"。

   1. 完成時，選取 [儲存]****。

   例如：

   ![選取用於記錄的 Log Analytics 工作區和資料](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>查看邏輯應用運行狀態

邏輯應用運行後，您可以在日誌分析工作區中查看有關這些運行的資料。

1. 在[Azure 門戶](https://portal.azure.com)中，查找並打開日誌分析工作區。

1. 在工作區的功能表上，選擇**工作區摘要** > **邏輯應用管理**。

   ![邏輯應用程式執行狀態和計數](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > 如果邏輯應用管理磁貼在運行後沒有立即顯示結果，請嘗試選擇 **"刷新"** 或等待一小段時間，然後再重試。

   在這裡，您的邏輯應用程式執行會依名稱或執行狀態分組。 此頁面也將同時顯示邏輯應用程式執行動作或觸發程序失敗的相關詳細資料。

   ![邏輯應用程式執行的狀態摘要](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. 要查看特定邏輯應用或狀態的所有運行，請選擇該邏輯應用或狀態的行。

   以下範例顯示特定邏輯應用程式的所有執行：

   ![查看邏輯應用運行和狀態](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   對於[設置跟蹤屬性](#extend-data)的操作，還可以通過在 **"跟蹤屬性**"列中選擇 **"視圖**"來查看這些屬性。 請可以使用資料行篩選來搜尋追蹤屬性。

   ![檢視邏輯應用程式的追蹤屬性](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

   > [!NOTE]
   > 在日誌分析工作區中出現之前，跟蹤的屬性或已完成的事件可能會遇到 10-15 分鐘的延遲。
   > 此外，此頁面上的 **"重新提交**"功能當前不可用。

1. 要篩選結果，可以同時執行用戶端和伺服器端篩選。

   * **用戶端篩選器**：對於每列，選擇所需的篩選器，例如：

     ![資料行篩選範例](./media/monitor-logic-apps-log-analytics/filters.png)

   * **伺服器端篩選器**：要選擇特定的時間視窗或限制顯示的運行次數，請使用頁面頂部的範圍控制項。 根據預設，一次只能出現 1,000 筆記錄。

     ![變更時間範圍](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. 要查看特定運行的所有操作及其詳細資訊，請選擇邏輯應用運行的行。

   下面是顯示特定邏輯應用運行的所有操作和觸發器的示例：

   ![檢視邏輯應用程式執行的動作](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>將診斷資料發送到 Azure 存儲和 Azure 事件中心

除了 Azure 監視器日誌之外，還可以擴展將邏輯應用的診斷資料與其他 Azure 服務一起使用的方式，例如：

* [將 Azure 資源記錄封存到儲存體帳戶](../azure-monitor/platform/resource-logs-collect-storage.md)
* [將 Azure 平臺日誌資料流到 Azure 事件中心](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

您可以接著使用其他服務的遙測和分析來取得即時監視，例如 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) 和 [Power BI](../azure-monitor/platform/powerbi.md)。 例如：

* [將資料從事件中樞串流至串流分析](../stream-analytics/stream-analytics-define-inputs.md)
* [使用串流分析分析串流資料並在 Power BI 中建立即時分析儀表板](../stream-analytics/stream-analytics-power-bi-dashboard.md)

根據要發送診斷資料的位置，請確保首先[創建 Azure 存儲帳戶](../storage/common/storage-create-storage-account.md)或[創建 Azure 事件中心](../event-hubs/event-hubs-create.md)。 然後，您可以選擇要發送該資料的目標。 保留期僅適用于您使用存儲帳戶時。

![將資料傳送至 Azure 儲存體帳戶或事件中樞](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Azure 監視器診斷事件

每個診斷事件都會有邏輯應用程式和該事件的詳細資料，例如，狀態、開始時間、結束時間等等。 要以程式設計方式設置監視、跟蹤和日誌記錄，可以將此資訊與[Azure 邏輯應用](https://docs.microsoft.com/rest/api/logic)的 REST API 和[Azure 監視器的 REST API](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows)一起使用。 您還可以使用 和`clientTrackingId``trackedProperties`屬性，這些屬性顯示在 

* `clientTrackingId`： 如果未提供，Azure 會自動產生這個識別碼，並相互關聯邏輯應用程式執行之間的事件，包括從邏輯應用程式呼叫的任何巢狀工作流程。 通過在觸發器請求中傳遞具有自訂 ID 值的標頭`x-ms-client-tracking-id`，可以在觸發器中手動指定此 ID。 您可以使用要求觸發程序、HTTP 觸發程序或 Webhook 觸發程序。

* `trackedProperties`： 要跟蹤診斷資料中的輸入或輸出，可以使用邏輯應用設計`trackedProperties`器或直接在邏輯應用的 JSON 定義中向操作添加節。 追蹤的屬性只能追蹤單一動作的輸入和輸出，但您可以使用事件的 `correlation` 屬性來跨執行中的動作進行相互關聯。 要跟蹤多個屬性、一個或多個屬性，將`trackedProperties`節和要添加到操作定義中的屬性。

  下面是一個示例，它顯示了**初始化變數**操作定義如何包括操作輸入中的跟蹤屬性，其中輸入是陣列，而不是記錄。

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

  此示例顯示多個跟蹤屬性：

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

此示例顯示`ActionCompleted`事件如何包含 和`clientTrackingId``trackedProperties`屬性：

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
