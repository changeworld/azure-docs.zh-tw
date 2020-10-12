---
title: 使用 Azure 監視器記錄搭配 Azure Logic Apps 和 Power Automate
description: 瞭解如何使用 Azure 監視器連接器，利用 Azure Logic Apps 和 Power Automate 快速自動化可重複的進程。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: d06501abe69ce9b06656cfa8949c42bb53a03983
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91439250"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-power-automate"></a>適用于 Logic Apps 和 Power Automate 的 Azure 監視器記錄連接器
[Azure Logic Apps](../../logic-apps/index.yml) 和 [Power Automate](https://flow.microsoft.com) 可讓您針對各種服務使用數百個動作來建立自動化工作流程。 Azure 監視器 Logs connector 可讓您建立工作流程，以從 Log Analytics 工作區或 Azure 監視器中的 Application Insights 應用程式取出資料。 本文說明連接器所包含的動作，並提供使用此資料來建立工作流程的逐步解說。

例如，您可以建立邏輯應用程式，以在 Office 365 的電子郵件通知中使用 Azure 監視器記錄資料、在 Azure DevOps 中建立 bug 或張貼一則延時間訊息。  您可以使用簡易排程或從連接的服務中的某個動作觸發工作流程，例如收到郵件或推文時。 

## <a name="connector-limits"></a>連接器限制
Azure 監視器記錄連接器有下列限制：
* 資料大小上限： 16 MB
* 最大查詢回應大小 100 MB
* 記錄數目上限：500000
* 最大查詢超時110秒。

根據您的資料大小和您使用的查詢，連接器可能會達到其限制，而且會失敗。 當您調整觸發程式週期以更頻繁地執行和查詢較少的資料時，可以解決這種情況。 您可以使用匯總資料的查詢，以傳回較少的記錄和資料行。

## <a name="actions"></a>動作
下表說明 Azure 監視器記錄連接器所包含的動作。 兩者都可讓您對 Log Analytics 工作區或 Application Insights 應用程式執行記錄查詢。 不同之處在于傳回資料的方式。

> [!NOTE]
> Azure 監視器 Logs connector 取代了 [Azure Log Analytics 連接器](/connectors/azureloganalytics/) 和 [Azure 應用程式 Insights connector](/connectors/applicationinsights/)。 此連接器提供的功能與其他連接器相同，而且是對 Log Analytics 工作區或 Application Insights 應用程式執行查詢的慣用方法。


| 動作 | 描述 |
|:---|:---|
| [執行查詢並列出結果](/connectors/azuremonitorlogs/#run-query-and-list-results) | 傳回每一個資料列做為它自己的物件。 當您想要在工作流程的其餘部分個別使用每個資料列時，請使用此動作。 動作通常會在 [每個活動](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)的後面加上。 |
| [執行查詢並將結果視覺化](/connectors/azuremonitorlogs/#run-query-and-visualize-results) | 傳回結果集中的所有資料列，做為單一格式化的物件。 如果您想要在工作流程的其餘部分一起使用結果集，例如在郵件中傳送結果，請使用此動作。  |

## <a name="walkthroughs"></a>逐步解說
下列教學課程說明如何在 Azure Logic Apps 中使用 Azure 監視器連接器。 您可以使用 Power Automate 來執行相同的範例，唯一的差異在於如何建立初始工作流程，並在完成時執行。 工作流程和動作的設定在兩者之間都相同。 若要開始使用，請參閱 [Power Automate 中的範本建立流程](/power-automate/get-started-logic-template) 。


### <a name="create-a-logic-app"></a>建立邏輯應用程式

移至 Azure 入口網站中的 **Logic Apps** ，然後按一下 [ **新增**]。 選取 **訂**用帳戶、 **資源群組**和 **區域** 以儲存新的邏輯應用程式，然後為其指定唯一的名稱。 您可以開啟 **Log Analytics** 設定，以收集有關執行時間資料和事件的資訊，如 [設定 Azure 監視器記錄和收集診斷資料以供 Azure Logic Apps](../../logic-apps/monitor-logic-apps-log-analytics.md)所述。 使用 Azure 監視器記錄連接器不需要此設定。

![建立邏輯應用程式](media/logicapp-flow-connector/create-logic-app.png)


按一下 [ **審核 + 建立** ]，然後 **建立**。 當部署完成時，按一下 [ **移至資源** ] 以開啟 **Logic Apps 設計**工具。

### <a name="create-a-trigger-for-the-logic-app"></a>建立邏輯應用程式的觸發程式
在 [ **開始使用一般觸發**程式] 下，選取 **[週期**]。 這會建立可定期自動執行的邏輯應用程式。 在動作的 [ **頻率** ] 方塊中，選取 [ **天** ]，然後在 [ **間隔** ] 方塊中輸入 **1** ，每天執行一次工作流程。

![週期動作](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>逐步解說：郵件視覺化結果
下列教學課程會示範如何建立邏輯應用程式，以透過電子郵件傳送 Azure 監視器記錄查詢的結果。 

### <a name="add-azure-monitor-logs-action"></a>新增 Azure 監視器記錄檔動作
按一下 [ **+ 新增步驟** ]，加入在迴圈動作之後執行的動作。 在 **[選擇動作**] 下，輸入 **azure 監視器** ，然後選取 [ **Azure 監視器記錄**]。

![Azure 監視器記錄檔動作](media/logicapp-flow-connector/select-azure-monitor-connector.png)

按一下 [Azure Log Analytics – Run query and visualize results] \(Azure Log Analytics – 執行查詢，並將結果視覺化) ****。

![在邏輯應用程式設計工具的步驟中新增動作的螢幕擷取畫面。 在 [選擇動作] 下，會反白顯示 Azure 監視器記錄。](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>新增 Azure 監視器記錄檔動作

選取 Log Analytics 工作區的 **訂** 用帳戶和 **資源群組** 。 選取**資源類型**的*Log Analytics 工作區*，然後在 [**資源名稱**] 底下選取工作區的名稱。

在 [查詢]**** 視窗新增下列記錄查詢。  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

針對**圖表類型**，選取 [*在查詢中設定***時間範圍**] 和 [ **HTML] 資料表**。
   
![新 Azure 監視器記錄檔的設定的螢幕擷取畫面，名為 [執行查詢]，並以視覺化方式呈現結果。](media/logicapp-flow-connector/run-query-visualize-action.png)

郵件將會由與目前連接相關聯的帳戶傳送。 您可以按一下 [ **變更連接**] 來指定其他帳戶。

### <a name="add-email-action"></a>新增電子郵件動作

按一下 [ **+ 新增步驟**]，然後按一下 [ **+ 新增動作**]。 在 **[選擇動作**] 下，輸入 **outlook** ，然後選取 [ **Office 365 outlook**]。

![選取 Outlook 連接器](media/logicapp-flow-connector/select-outlook-connector.png)

選取 [ **傳送電子郵件 (V2) **。

![Office 365 Outlook 選取視窗](media/logicapp-flow-connector/select-mail-action.png)

按一下 [內文] 方塊 **中的任意** 位置，以開啟 **動態內容** 視窗，其中包含邏輯應用程式中先前動作的值。 選取 [ **查看更多** ]，然後選取 [ **主體** ]，這是 Log Analytics 動作中的查詢結果。

![選取內文](media/logicapp-flow-connector/select-body.png)

在 [收件者]**** 視窗中指定收件者的電子郵件地址，並在 [主旨]**** 指定電子郵件的主旨。 

![郵件動作](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>儲存並測試邏輯應用程式
按一下 [ **儲存** ] **，然後執行以執行** 邏輯應用程式的測試回合。

![儲存並執行](media/logicapp-flow-connector/save-run.png)


當邏輯應用程式完成時，請檢查您指定之收件者的郵件。  您應該會收到內文類似如下的郵件：

![範例電子郵件](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 監視器中的記錄查詢](../log-query/log-query-overview.md)。
- 深入瞭解 [Logic Apps](../../logic-apps/index.yml)
- 深入瞭解 [Power Automate](https://flow.microsoft.com)。
