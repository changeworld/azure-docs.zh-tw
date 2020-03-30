---
title: 使用 Azure 監視器監視 B2B 消息
description: 通過設置 Azure 監視器日誌和收集 Azure 邏輯應用的診斷資料，對 AS2、X12 和 EDIFACT 消息進行故障排除
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: e9ba5a516293eb72a715dc9d0df7db4d5a4ea3c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907977"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>在 Azure 邏輯應用中設置 Azure 監視器日誌並收集 B2B 消息的診斷資料

在集成帳戶中設置交易夥伴之間的 B2B 通信後，這些合作夥伴可以使用 AS2、X12 和 EDIFACT 等協定交換消息。 要檢查此通信是否可以按預期方式工作，可以為集成帳戶設置[Azure 監視器日誌](../azure-monitor/platform/data-platform-logs.md)。 [Azure 監視器](../azure-monitor/overview.md)可説明您監視雲和本地環境，以便更輕鬆地維護其可用性和性能。 通過使用 Azure 監視器日誌，可以在[日誌分析工作區](../azure-monitor/platform/resource-logs-collect-workspace.md)中記錄和存儲有關運行時資料和事件的資料，例如觸發器事件、運行事件和操作事件。 對於消息，日誌記錄還會收集資訊，例如：

* 訊息計數和狀態
* 通知狀態
* 消息和確認之間的關聯
* 失敗的詳細錯誤描述

Azure 監視器允許您創建[日誌查詢](../azure-monitor/log-query/log-query-overview.md)，以説明您查找和查看此資訊。 還可以[將此診斷資料與其他 Azure 服務](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)（如 Azure 存儲和 Azure 事件中心）一起使用。

要為集成帳戶設置日誌記錄，請在 Azure 門戶中[安裝邏輯應用 B2B 解決方案](#install-b2b-solution)。 此解決方案提供 B2B 消息事件的聚合資訊。 然後，要啟用日誌記錄和創建此資訊的查詢，應設置[Azure 監視器日誌](#set-up-resource-logs)。

本文演示如何為集成帳戶啟用 Azure 監視器日誌記錄。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisites

* Log Analytics 工作區。 如果您沒有 Log Analytics 工作區，請了解[如何建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。

* 使用 Azure 監視器日誌記錄設置並將該資訊發送到日誌分析工作區的邏輯應用。 [瞭解如何為邏輯應用設置 Azure 監視器日誌](../logic-apps/monitor-logic-apps.md)。

* 連結至邏輯應用程式的整合帳戶。 [瞭解如何將集成帳戶連結到邏輯應用](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>安裝 Logic Apps B2B 解決方案

在 Azure 監視器日誌可以跟蹤邏輯應用的 B2B 消息之前，請將**邏輯應用 B2B**解決方案添加到日誌分析工作區。

1. 在[Azure 門戶](https://portal.azure.com)的搜索框中，輸入`log analytics workspaces`，然後選擇**日誌分析工作區**。

   ![選擇"日誌分析工作區"](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. 在 **"日誌分析"工作區**下，選擇工作區。

   ![選取 Log Analytics 工作區](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. 在"概述"窗格中，**在"開始使用日誌分析** > **配置監視解決方案**"下，選擇 **"查看解決方案**"。

   ![在"概述"窗格中，選擇"查看解決方案"](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. 在"概述"窗格中，選擇 **"添加**"。

   ![在概覽窗格上，添加新解決方案](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. **市場**打開後，在搜索框中輸入`logic apps b2b`，然後選擇**邏輯應用 B2B**。

   ![從應用商店中，選擇"邏輯應用管理"](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. 在解決方案描述窗格中，選擇 **"創建**"。

   ![選擇"創建"以添加"邏輯應用 B2B"解決方案](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. 查看並確認要安裝解決方案的日誌分析工作區，然後選擇"再次**創建**"。

   ![為"邏輯應用 B2B"選擇"創建"](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   Azure 將解決方案部署到包含日誌分析工作區的 Azure 資源組後，該解決方案將顯示在工作區的摘要窗格中。 處理 B2B 消息時，將更新此窗格上的消息計數。

   ![工作區摘要窗格](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>設定 Azure 監視器記錄

可以直接從集成帳戶啟用 Azure 監視器日誌記錄。

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並選取整合帳戶。

   ![尋找並選取您的整合帳戶](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. 在集成帳戶的功能表上，在 **"監視"** 下，選擇 **"診斷設置**"。 選擇 **"添加診斷設置**"。

   ![在"監視"下，選擇"診斷設置"](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. 要創建設置，請按照以下步驟操作：

   1. 提供設置的名稱。

   1. 選取 [傳送至 Log Analytics]****。

   1. 對於**訂閱**，選擇與日誌分析工作區關聯的 Azure 訂閱。

   1. 對於**日誌分析工作區**，請選擇要使用的工作區。

   1. 在**日誌**下，選擇 **"集成帳戶跟蹤事件**"類別，該類別指定要記錄的事件類別。

   1. 完成時，選取 [儲存]****。

   例如： 

   ![設置 Azure 監視器日誌以收集診斷資料](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>檢視訊息狀態

運行邏輯應用後，您可以在日誌分析工作區中查看有關這些消息的狀態和資料。

1. 在[Azure 門戶](https://portal.azure.com)搜索框中，查找並打開日誌分析工作區。

1. 在工作區的功能表上，選擇**工作區摘要** > **邏輯應用 B2B**。

   ![工作區摘要窗格](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > 如果邏輯應用 B2B 磁貼在運行後沒有立即顯示結果，請嘗試選擇 **"刷新"** 或等待一小段時間，然後再重試。

   [Logic Apps B2B]**** 圖格預設會顯示以一天為基礎的資料。 要將資料範圍更改為不同的間隔，請選擇頁面頂部的範圍控制項：

   ![變更間隔](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. 訊息狀態儀表板出現之後，即可檢視特定訊息類型的其他詳細資料，這會顯示以一天為基礎的資料。 選擇**AS2** **、X12**或**EDIFACT**的磁貼。

   ![查看郵件的狀態](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   會針對您所選擇的磚顯示訊息清單。 例如，AS2 訊息清單可能如下：

   ![AS2 消息的狀態和詳細資訊](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   若要深入了解每種訊息類型的屬性，請參閱這些訊息屬性描述：

   * [AS2 訊息屬性](#as2-message-properties)
   * [X12 訊息屬性](#x12-message-properties)
   * [EDIFACT 訊息屬性](#EDIFACT-message-properties)

<!--
1. To view or export the inputs and outputs for specific messages, select those messages, and select **Download**. When you're prompted, save the .zip file to your local computer, and then extract that file.

   The extracted folder includes a folder for each selected message. If you set up acknowledgements, the message folder also includes files with acknowledgement details. Each message folder has at least these files:
   
   * Human-readable files with the input payload and output payload details
   * Encoded files with the inputs and outputs

   For each message type, you can find the folder and file name formats here:

   * [AS2 folder and file name formats](#as2-folder-file-names)
   * [X12 folder and file name formats](#x12-folder-file-names)
   * [EDIFACT folder and file name formats](#edifact-folder-file-names)

   ![Download message files](./media/monitor-b2b-messages-log-analytics/download-messages.png)

1. To view all actions that have the same run ID, on the **Log Search** page, select a message from the message list.

   You can sort these actions by column, or search for specific results.

   * To search results with prebuilt queries, select **Favorites**.

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../log-analytics/log-analytics-log-searches.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>AS2、X12 和 EDIFACT 訊息的屬性描述和名稱格式

對於每種訊息類型，以下是所下載訊息檔案的屬性描述和名稱格式。

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>AS2 訊息屬性描述

以下是每個 AS2 訊息的屬性描述。

| 屬性 | 描述 |
|----------|-------------|
| **發送** | 針對 AS2 協議的 [接收設定]**** 中所指定的來賓合作夥伴，或 [傳送設定]**** 中所指定的主機合作夥伴 |
| **接收機** | 針對 AS2 協議的 [接收設定]**** 中所指定的主機合作夥伴，或 [傳送設定]**** 中所指定的來賓合作夥伴 |
| **邏輯應用** | 已設定 AS2 動作的邏輯應用程式 |
| **狀態** | AS2 訊息狀態 <br>成功 = 已接收或傳送有效的 AS2 訊息。 未設定 MDN。 <br>成功 = 已接收或傳送有效的 AS2 訊息。 已設定並接收 MDN，或傳送 MDN。 <br>失敗 = 已接收無效的 AS2 訊息。 未設定 MDN。 <br>暫止 = 已接收或傳送有效的 AS2 訊息。 已設定 MDN，並預期要有 MDN。 |
| **Ack** | MDN 訊息狀態 <br>接受 = 已接收或傳送正值的 MDN。 <br>暫止 = 等候接收或傳送 MDN。 <br>拒絕 = 已接收或傳送負值的 MDN。 <br>不需要 = 未在協議中設定 MDN。 |
| **方向** | AS2 訊息方向 |
| **追蹤識別碼** | 與邏輯應用程式中所有觸發程序和動作相關聯的識別碼 |
| **訊息識別碼** | 來自 AS2 訊息標頭的 AS2 訊息識別碼 |
| **時間 戳** | AS2 動作處理訊息的時間 |
|||

<!--
<a name="as2-folder-file-names"></a>

### AS2 name formats for downloaded message files

Here are the name formats for each downloaded AS2 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|||
-->

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>X12 訊息屬性描述

以下是每個 X12 訊息的屬性描述。

| 屬性 | 描述 |
|----------|-------------|
| **發送** | 針對 X12 協議的 [接收設定]**** 中所指定的來賓合作夥伴，或 [傳送設定]**** 中所指定的主機合作夥伴 |
| **接收機** | 針對 X12 協議的 [接收設定]**** 中所指定的主機合作夥伴，或 [傳送設定]**** 中所指定的來賓合作夥伴 |
| **邏輯應用** | 已設定 X12 動作的邏輯應用程式 |
| **狀態** | X12 訊息狀態 <br>成功 = 已接收或傳送有效的 X12 訊息。 未設定任何功能通知。 <br>成功 = 已接收或傳送有效的 X12 訊息。 已設定和接收功能通知，或傳送功能通知。 <br>失敗 = 已接收或傳送有效的 X12 訊息。 <br>暫止 = 已接收或傳送有效的 X12 訊息。 已設定功能通知，並預期要有功能通知。 |
| **Ack** | 功能認可 (997) 狀態 <br>接受 = 已接收或傳送正值的功能通知。 <br>拒絕 = 已接收或傳送負值的功能通知。 <br>暫止 = 預期要有功能通知但未收到。 <br>暫止 = 已產生功能通知，但無法傳送給合作夥伴。 <br>不需要 = 未設定功能通知。 |
| **方向** | X12 訊息方向 |
| **追蹤識別碼** | 與邏輯應用程式中所有觸發程序和動作相關聯的識別碼 |
| **訊息類型** | EDI X12 訊息類型 |
| **ICN** | X12 訊息的交換控制編號 |
| **TSCN** | X12 訊息的交易集控制編號 |
| **時間 戳** | X12 動作處理訊息的時間 |
|||

<!--
<a name="x12-folder-file-names"></a>

### X12 name formats for downloaded message files

Here are the name formats for each downloaded X12 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|||
-->

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>EDIFACT 訊息屬性描述

以下是每個 EDIFACT 訊息的屬性描述。

| 屬性 | 描述 |
|----------|-------------|
| **發送** | 針對 EDIFACT 協議的 [接收設定]**** 中所指定的來賓合作夥伴，或 [傳送設定]**** 中所指定的主機合作夥伴 |
| **接收機** | 針對 EDIFACT 協議的 [接收設定]**** 中所指定的主機合作夥伴，或 [傳送設定]**** 中所指定的來賓合作夥伴 |
| **邏輯應用** | 已設定 EDIFACT 動作的邏輯應用程式 |
| **狀態** | EDIFACT 訊息狀態 <br>成功 = 已接收或傳送有效的 EDIFACT 訊息。 未設定任何功能通知。 <br>成功 = 已接收或傳送有效的 EDIFACT 訊息。 已設定和接收功能通知，或傳送功能通知。 <br>失敗 = 已接收或傳送有效的 EDIFACT 訊息。 <br>暫止 = 已接收或傳送有效的 EDIFACT 訊息。 已設定功能通知，並預期要有功能通知。 |
| **Ack** | 功能確認 （CONTRL） 狀態 <br>接受 = 已接收或傳送正值的功能通知。 <br>拒絕 = 已接收或傳送負值的功能通知。 <br>暫止 = 預期要有功能通知但未收到。 <br>暫止 = 已產生功能通知，但無法傳送給合作夥伴。 <br>不需要 = 未設定功能通知。 |
| **方向** | EDIFACT 訊息方向 |
| **追蹤識別碼** | 與邏輯應用程式中所有觸發程序和動作相關聯的識別碼 |
| **訊息類型** | EDIFACT 訊息類型 |
| **ICN** | EDIFACT 訊息的交換控制編號 |
| **TSCN** | EDIFACT 訊息的交易集控制編號 |
| **時間 戳** | EDIFACT 動作處理訊息的時間 |
|||

<!--
<a name="edifact-folder-file-names"></a>

### EDIFACT name formats for downloaded message files

Here are the name formats for each downloaded EDIFACT message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|||
-->

## <a name="next-steps"></a>後續步驟

* [建立監視和追蹤查詢](../logic-apps/create-monitoring-tracking-queries.md)