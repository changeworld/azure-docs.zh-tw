---
title: 搭配 Azure Logic Apps 和電源自動化使用 Azure 監視器記錄
description: 瞭解您可以如何使用 Azure Logic Apps 和電源自動化，藉由使用 Azure 監視器連接器，快速地將可重複的進程自動化。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 6048b5f0a3702e95cef9175933041fe36f2f07bb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081554"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-flow"></a>適用于 Logic Apps 和流程的 Azure 監視器記錄連接器
[Azure Logic Apps](../../logic-apps/index.yml)和[電源自動化](https://ms.flow.microsoft.com)可讓您針對各種服務，使用數百個動作來建立自動化工作流程。 Azure 監視器 Logs 連接器可讓您建立工作流程，以從 Log Analytics 工作區或 Azure 監視器中的 Application Insights 應用程式抓取資料。 本文說明連接器所包含的動作，並提供使用此資料來建立工作流程的逐步解說。

例如，您可以建立邏輯應用程式，以在 Office 365 的電子郵件通知中使用 Azure 監視器記錄資料、在 Azure DevOps 中建立 bug，或張貼時差訊息。  您可以使用簡易排程或從連接的服務中的某個動作觸發工作流程，例如收到郵件或推文時。 

## <a name="connector-limits"></a>連接器限制
Azure 監視器記錄連接器具有下列限制：
* 資料大小上限： 16 MB
* 查詢回應大小上限為 100 MB
* 記錄的最大數目：500000
* 最大查詢超時110秒。

視您的資料大小和您使用的查詢而定，連接器可能會達到其限制並失敗。 當您調整觸發週期來執行更頻繁且查詢較少的資料時，可以解決這種情況。 您可以使用匯總資料的查詢，以傳回較少的記錄和資料行。

## <a name="actions"></a>動作
下表描述 Azure 監視器 Logs 連接器中包含的動作。 兩者都可讓您對 Log Analytics 工作區或 Application Insights 應用程式執行記錄查詢。 其差異在於傳回資料的方式。

> [!NOTE]
> Azure 監視器 Logs 連接器會取代[Azure Log Analytics 連接器](/connectors/azureloganalytics/)和[Azure 應用程式 Insights 連接器](/connectors/applicationinsights/)。 此連接器提供與其他專案相同的功能，而且是針對 Log Analytics 工作區或 Application Insights 應用程式執行查詢的慣用方法。


| 動作 | 描述 |
|:---|:---|
| [執行查詢和列出結果](/connectors/azuremonitorlogs/#run-query-and-list-results) | 傳回每個資料列做為它自己的物件。 當您想要在工作流程的其餘部分分別處理每個資料列時，請使用此動作。 動作通常會在[每個活動](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)後面加上。 |
| [執行查詢並將結果視覺化](/connectors/azuremonitorlogs/#run-query-and-visualize-results) | 以單一格式化物件的形式傳回結果集中的所有資料列。 當您想要在工作流程的其餘部分一起使用結果集，例如在郵件中傳送結果時，請使用此動作。  |

## <a name="walkthroughs"></a>逐步解說
下列教學課程說明如何在 Azure Logic Apps 中使用 Azure 監視器連接器。 您可以使用電源自動化來執行這些相同的範例，唯一的差別在於如何建立初始工作流程，並在完成時加以執行。 工作流程和動作的設定在兩者之間都相同。 請參閱[從電源自動化中的範本建立流程](/power-automate/get-started-logic-template)，以開始使用。


### <a name="create-a-logic-app"></a>建立邏輯應用程式

移至 Azure 入口網站中的**Logic Apps** ，然後按一下 [**新增**]。 選取要儲存新邏輯應用程式的**訂**用帳戶、**資源群組**和**區域**，然後為它指定唯一的名稱。 您可以開啟**Log Analytics**設定來收集有關執行時間資料和事件的資訊，如[設定 Azure 監視器記錄和收集診斷資料以進行 Azure Logic Apps](../../logic-apps/monitor-logic-apps-log-analytics.md)所述。 使用 Azure 監視器 Logs 連接器時，不需要此設定。

![建立邏輯應用程式](media/logicapp-flow-connector/create-logic-app.png)


按一下 [審核] [ **+ 建立**]，然後按一下 [**建立**]。 當部署完成時，按一下 [**移至資源**] 以開啟 [ **Logic Apps 設計**工具]。

### <a name="create-a-trigger-for-the-logic-app"></a>建立邏輯應用程式的觸發程式
在 [**從一般觸發程式開始**] 底下，選取 **[週期**]。 這會建立一個會定期自動執行的邏輯應用程式。 在動作的 [**頻率**] 方塊中，選取 [**天**]，然後在 [**間隔**] 方塊中輸入**1** ，每天執行一次工作流程。

![週期動作](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>逐步解說：以郵件視覺化的結果
下列教學課程會示範如何建立邏輯應用程式，以透過電子郵件傳送 Azure 監視器記錄查詢的結果。 

### <a name="add-azure-monitor-logs-action"></a>新增 Azure 監視器記錄檔動作
按一下 [ **+ 新增步驟**] 來新增在 [週期] 動作之後執行的動作。 在 **[選擇動作**] 底下，輸入**azure 監視器**，然後選取 [ **Azure 監視器記錄**]。

![Azure 監視器記錄檔動作](media/logicapp-flow-connector/select-azure-monitor-connector.png)

按一下 [Azure Log Analytics – Run query and visualize results] \(Azure Log Analytics – 執行查詢，並將結果視覺化) ****。

![執行查詢並視覺化結果動作](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>新增 Azure 監視器記錄檔動作

選取 Log Analytics 工作區的 [**訂**用帳戶] 和 [**資源群組**]。 針對 [**資源類型**] 選取 [ *Log Analytics 工作區*]，然後選取 [**資源名稱**] 下的工作區名稱。

在 [查詢]**** 視窗新增下列記錄查詢。  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

針對**圖表類型**，在 [查詢**時間範圍**] 和 [ **HTML 資料表**] 中選取 [*設定*]。
   
![執行查詢並視覺化結果動作](media/logicapp-flow-connector/run-query-visualize-action.png)

此郵件將由與目前連線相關聯的帳戶傳送。 您可以按一下 [**變更連接**] 來指定其他帳戶。

### <a name="add-email-action"></a>新增電子郵件動作

按一下 [ **+ 新增步驟**]，然後按一下 [ **+ 加入動作**]。 在 **[選擇動作**] 底下，輸入**outlook** ，然後選取 [ **Office 365 outlook**]。

![選取 Outlook connector](media/logicapp-flow-connector/select-outlook-connector.png)

選取 **[傳送電子郵件（V2）**]。

![Office 365 Outlook 選取視窗](media/logicapp-flow-connector/select-mail-action.png)

按一下 [本文] 方塊**中的任何**位置，即可開啟**動態內容**視窗，其中包含來自邏輯應用程式中先前動作的值。 選取 [**查看更多**]，然後選取 [**主體**]，這是 Log Analytics 動作中查詢的結果。

![選取內文](media/logicapp-flow-connector/select-body.png)

在 [收件者]**** 視窗中指定收件者的電子郵件地址，並在 [主旨]**** 指定電子郵件的主旨。 

![郵件動作](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>儲存並測試邏輯應用程式
依序按一下 [**儲存**] 和 **[執行]，以執行**邏輯應用程式的測試回合。

![儲存並執行](media/logicapp-flow-connector/save-run.png)


當邏輯應用程式完成時，請檢查您指定之收件者的郵件。  您應該會收到內文類似如下的郵件：

![範例電子郵件](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 監視器中的記錄查詢](../log-query/log-query-overview.md)。
- 深入瞭解[Logic Apps](../../logic-apps/index.yml)
- 深入了解 [Microsoft Flow](https://ms.flow.microsoft.com)。
