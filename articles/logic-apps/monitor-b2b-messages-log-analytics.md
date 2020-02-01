---
title: 使用 Azure 監視器監視 B2B 訊息
description: 藉由設定 Azure 監視器記錄並收集診斷資料，來針對 AS2、X12 和 EDIFACT 訊息進行疑難排解 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: e9ba5a516293eb72a715dc9d0df7db4d5a4ea3c5
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907977"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>在 Azure Logic Apps 中設定 Azure 監視器記錄並收集 B2B 訊息的診斷資料

在您的整合帳戶中設定交易夥伴之間的 B2B 通訊之後，這些合作夥伴可以使用 AS2、X12 和 EDIFACT 等通訊協定來交換訊息。 若要檢查此通訊是否以您預期的方式運作，您可以設定整合帳戶的[Azure 監視器記錄](../azure-monitor/platform/data-platform-logs.md)。 [Azure 監視器](../azure-monitor/overview.md)可協助您監視您的雲端和內部部署環境，以便更輕鬆地維護其可用性和效能。 藉由使用 Azure 監視器記錄，您可以記錄和儲存有關執行時間資料和事件的資料，例如觸發程式事件、執行事件和[Log Analytics 工作區](../azure-monitor/platform/resource-logs-collect-workspace.md)中的動作事件。 對於訊息，記錄也會收集下列資訊：

* 訊息計數和狀態
* 通知狀態
* 訊息和通知之間的相互關聯
* 失敗的詳細錯誤描述

Azure 監視器可讓您建立[記錄查詢](../azure-monitor/log-query/log-query-overview.md)，以協助您尋找並查看此資訊。 您也可以[將此診斷資料與其他 Azure 服務搭配使用](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)，例如 Azure 儲存體和 Azure 事件中樞。

若要設定整合帳戶的記錄，請在 Azure 入口網站中[安裝 Logic Apps B2B 解決方案](#install-b2b-solution)。 此解決方案提供 B2B 訊息事件的匯總資訊。 然後，若要啟用記錄並建立此資訊的查詢，請設定[Azure 監視器記錄](#set-up-resource-logs)。

本文說明如何為您的整合帳戶啟用 Azure 監視器記錄。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>必要條件

* Log Analytics 工作區。 如果您沒有 Log Analytics 工作區，請了解[如何建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。

* 使用 Azure 監視器記錄設定的邏輯應用程式，並將該資訊傳送至 Log Analytics 工作區。 瞭解[如何為您的邏輯應用程式設定 Azure 監視器記錄](../logic-apps/monitor-logic-apps.md)。

* 連結至邏輯應用程式的整合帳戶。 瞭解[如何將整合帳戶連結至邏輯應用程式](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>安裝 Logic Apps B2B 解決方案

在 Azure 監視器記錄可以追蹤邏輯應用程式的 B2B 訊息之前，請將**Logic Apps B2B**解決方案新增至您的 Log Analytics 工作區。

1. 在[Azure 入口網站](https://portal.azure.com)的搜尋方塊中，輸入 `log analytics workspaces`，然後選取 [ **Log Analytics 工作區**]。

   ![選取 [Log Analytics 工作區]](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. 在 [ **Log Analytics 工作區**] 底下，選取您的工作區。

   ![選取 Log Analytics 工作區](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. 在 [總覽] 窗格的 [**開始使用 Log Analytics** > **設定監視解決方案**] 下，選取 [ **View 解決方案**]。

   ![在 [總覽] 窗格中，選取 [View 解決方案]](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. 在 [總覽] 窗格中，選取 [**新增**]。

   ![在 [總覽] 窗格中，加入新的方案](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. 在**Marketplace**開啟之後，請在搜尋方塊中輸入 `logic apps b2b`，然後選取 [ **Logic Apps B2B**]。

   ![從 Marketplace 選取 [Logic Apps 管理]](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. 在 [解決方案描述] 窗格上，選取 [**建立**]。

   ![選取 [建立] 以新增「Logic Apps B2B」方案](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. 請檢查並確認您要在其中安裝解決方案的 Log Analytics 工作區，然後再次選取 [**建立**]。

   ![針對 [Logic Apps B2B] 選取 [建立]](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   在 Azure 將解決方案部署至包含 Log Analytics 工作區的 Azure 資源群組之後，解決方案會出現在工作區的 [摘要] 窗格上。 處理 B2B 訊息時，會更新此窗格上的訊息計數。

   ![工作區摘要窗格](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>設定 Azure 監視器記錄

您可以直接從您的整合帳戶啟用 Azure 監視器記錄。

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並選取整合帳戶。

   ![尋找並選取您的整合帳戶](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. 在整合帳戶的功能表上，選取 [**監視**] 底下的 [**診斷設定**]。 選取 [**新增診斷設定**]。

   ![在 [監視] 底下，選取 [診斷設定]](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. 若要建立設定，請遵循下列步驟：

   1. 提供設定的名稱。

   1. 選取 [傳送至 Log Analytics]。

   1. 針對 [**訂**用帳戶]，選取與您的 Log Analytics 工作區相關聯的 Azure 訂用帳戶。

   1. 針對 [ **Log Analytics 工作區**]，選取您要使用的工作區。

   1. 在 **記錄** 下，選取  **integrationaccounttrackingevents**  類別，以指定您想要記錄的事件類別目錄。

   1. 完成時，選取 [儲存]。

   例如： 

   ![設定 Azure 監視器記錄來收集診斷資料](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>檢視訊息狀態

執行邏輯應用程式之後，您可以在 Log Analytics 工作區中查看這些訊息的狀態和相關資料。

1. 在 [ [Azure 入口網站](https://portal.azure.com)搜尋] 方塊中，尋找並開啟您的 Log Analytics 工作區。

1. 在工作區的功能表上，選取 **工作區摘要** > **Logic Apps B2B**。

   ![工作區摘要窗格](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > 如果 [Logic Apps B2B] 磚不會在執行後立即顯示結果，請**嘗試選取 [** 重新整理] 或稍待片刻後再試一次。

   [Logic Apps B2B] 圖格預設會顯示以一天為基礎的資料。 若要將資料範圍變更為不同的間隔，請選取頁面頂端的範圍控制項：

   ![變更間隔](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. 訊息狀態儀表板出現之後，即可檢視特定訊息類型的其他詳細資料，這會顯示以一天為基礎的資料。 選取**AS2**、 **X12**或**EDIFACT**的磚。

   ![訊息的檢視狀態](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   會針對您所選擇的磚顯示訊息清單。 例如，AS2 訊息清單可能如下：

   ![AS2 訊息的狀態和詳細資料](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

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

| 屬性 | 說明 |
|----------|-------------|
| **發送者** | 針對 AS2 協議的 [接收設定] 中所指定的來賓合作夥伴，或 [傳送設定] 中所指定的主機合作夥伴 |
| **放** | 針對 AS2 協議的 [接收設定] 中所指定的主機合作夥伴，或 [傳送設定] 中所指定的來賓合作夥伴 |
| **邏輯應用程式** | 已設定 AS2 動作的邏輯應用程式 |
| **狀態** | AS2 訊息狀態 <br>成功 = 已接收或傳送有效的 AS2 訊息。 未設定 MDN。 <br>成功 = 已接收或傳送有效的 AS2 訊息。 已設定並接收 MDN，或傳送 MDN。 <br>失敗 = 已接收無效的 AS2 訊息。 未設定 MDN。 <br>暫止 = 已接收或傳送有效的 AS2 訊息。 已設定 MDN，並預期要有 MDN。 |
| **認可** | MDN 訊息狀態 <br>接受 = 已接收或傳送正值的 MDN。 <br>暫止 = 等候接收或傳送 MDN。 <br>拒絕 = 已接收或傳送負值的 MDN。 <br>不需要 = 未在協議中設定 MDN。 |
| **方向** | AS2 訊息方向 |
| **追蹤識別碼** | 與邏輯應用程式中所有觸發程序和動作相關聯的識別碼 |
| **訊息識別碼** | 來自 AS2 訊息標頭的 AS2 訊息識別碼 |
| **Timestamp** | AS2 動作處理訊息的時間 |
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

| 屬性 | 說明 |
|----------|-------------|
| **發送者** | 針對 X12 協議的 [接收設定] 中所指定的來賓合作夥伴，或 [傳送設定] 中所指定的主機合作夥伴 |
| **放** | 針對 X12 協議的 [接收設定] 中所指定的主機合作夥伴，或 [傳送設定] 中所指定的來賓合作夥伴 |
| **邏輯應用程式** | 已設定 X12 動作的邏輯應用程式 |
| **狀態** | X12 訊息狀態 <br>成功 = 已接收或傳送有效的 X12 訊息。 未設定任何功能通知。 <br>成功 = 已接收或傳送有效的 X12 訊息。 已設定和接收功能通知，或傳送功能通知。 <br>失敗 = 已接收或傳送有效的 X12 訊息。 <br>暫止 = 已接收或傳送有效的 X12 訊息。 已設定功能通知，並預期要有功能通知。 |
| **認可** | 功能認可 (997) 狀態 <br>接受 = 已接收或傳送正值的功能通知。 <br>拒絕 = 已接收或傳送負值的功能通知。 <br>暫止 = 預期要有功能通知但未收到。 <br>暫止 = 已產生功能通知，但無法傳送給合作夥伴。 <br>不需要 = 未設定功能通知。 |
| **方向** | X12 訊息方向 |
| **追蹤識別碼** | 與邏輯應用程式中所有觸發程序和動作相關聯的識別碼 |
| **Msg 類型** | EDI X12 訊息類型 |
| **ICN** | X12 訊息的交換控制編號 |
| **TSCN** | X12 訊息的交易集控制編號 |
| **Timestamp** | X12 動作處理訊息的時間 |
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

| 屬性 | 說明 |
|----------|-------------|
| **發送者** | 針對 EDIFACT 協議的 [接收設定] 中所指定的來賓合作夥伴，或 [傳送設定] 中所指定的主機合作夥伴 |
| **放** | 針對 EDIFACT 協議的 [接收設定] 中所指定的主機合作夥伴，或 [傳送設定] 中所指定的來賓合作夥伴 |
| **邏輯應用程式** | 已設定 EDIFACT 動作的邏輯應用程式 |
| **狀態** | EDIFACT 訊息狀態 <br>成功 = 已接收或傳送有效的 EDIFACT 訊息。 未設定任何功能通知。 <br>成功 = 已接收或傳送有效的 EDIFACT 訊息。 已設定和接收功能通知，或傳送功能通知。 <br>失敗 = 已接收或傳送有效的 EDIFACT 訊息。 <br>暫止 = 已接收或傳送有效的 EDIFACT 訊息。 已設定功能通知，並預期要有功能通知。 |
| **認可** | 功能通知（CONTRL）狀態 <br>接受 = 已接收或傳送正值的功能通知。 <br>拒絕 = 已接收或傳送負值的功能通知。 <br>暫止 = 預期要有功能通知但未收到。 <br>暫止 = 已產生功能通知，但無法傳送給合作夥伴。 <br>不需要 = 未設定功能通知。 |
| **方向** | EDIFACT 訊息方向 |
| **追蹤識別碼** | 與邏輯應用程式中所有觸發程序和動作相關聯的識別碼 |
| **Msg 類型** | EDIFACT 訊息類型 |
| **ICN** | EDIFACT 訊息的交換控制編號 |
| **TSCN** | EDIFACT 訊息的交易集控制編號 |
| **Timestamp** | EDIFACT 動作處理訊息的時間 |
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