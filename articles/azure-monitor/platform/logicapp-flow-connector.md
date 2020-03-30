---
title: 將 Azure 監視器日誌與 Azure 邏輯應用一起使用，並自動供電
description: 瞭解如何使用 Azure 邏輯應用和電源自動功能，使用 Azure 監視器連接器快速自動執行可重複的進程。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 6961b7bd94c9b3fe70365055851c488efa2cbeca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480006"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-flow"></a>用於邏輯應用和流的 Azure 監視器日誌連接器
[Azure 邏輯應用](/azure/logic-apps/)和[電源自動功能](https://ms.flow.microsoft.com)允許您使用數百個操作為各種服務創建自動化工作流。 Azure 監視器日誌連接器允許您構建從日誌分析工作區或 Azure 監視器中的應用程式見解應用程式檢索資料的工作流。 本文介紹了連接器附帶的操作，並提供了使用此資料構建工作流的演練。

例如，您可以創建邏輯應用，用於在 Office 365 的電子郵件通知中使用 Azure 監視器日誌資料、在 Azure DevOps 中創建 Bug 或發佈 Slack 消息。  您可以使用簡易排程或從連接的服務中的某個動作觸發工作流程，例如收到郵件或推文時。 

## <a name="actions"></a>動作
下表描述了 Azure 監視器日誌連接器中包含的操作。 兩者都允許您針對日誌分析工作區或應用程式見解應用程式運行日誌查詢。 區別在於資料的返回方式。

> [!NOTE]
> Azure 監視器日誌連接器將替換[Azure 日誌分析連接器](https://docs.microsoft.com/connectors/azureloganalytics/)和[Azure 應用程式見解連接器](https://docs.microsoft.com/connectors/applicationinsights/)。 此連接器提供與其他連接器相同的功能，是針對日誌分析工作區或應用程式見解應用程式執行查詢的首選方法。


| 動作 | 描述 |
|:---|:---|
| [執行查詢和清單結果](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-list-results) | 返回每行作為其自己的物件。 如果要在工作流的其餘部分中單獨處理每一行，請使用此操作。 操作後跟["對於每個活動](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)"。 |
| [執行查詢並視覺化結果](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-visualize-results) | 將結果集中的所有行作為單個格式化物件返回。 如果要在工作流的其餘部分（如在郵件中發送結果）中一起使用結果集，請使用此操作。  |

## <a name="walkthroughs"></a>逐步解說
以下教程演示了 Azure 邏輯應用中 Azure 監視器連接器的使用。 您可以使用 Power 自動執行相同的示例，唯一的區別是如何創建初始工作流並在完成後運行它。 工作流和操作的配置在兩者之間是相同的。 請參閱[從"電源自動"中的範本創建流](https://docs.microsoft.com/power-automate/get-started-logic-template)以開始。


### <a name="create-a-logic-app"></a>建立邏輯應用程式

轉到 Azure 門戶中的**邏輯應用**，然後按一下"**添加**"。 選擇**訂閱**、**資源組**和**區域**以存儲新邏輯應用，然後為它指定唯一名稱。 可以打開**日誌分析**設置以收集有關運行時資料和事件的資訊，如[設置 Azure 監視器日誌和為 Azure 邏輯應用收集診斷資料](../../logic-apps/monitor-logic-apps-log-analytics.md)所述。 使用 Azure 監視器日誌連接器不需要此設置。

![建立邏輯應用程式](media/logicapp-flow-connector/create-logic-app.png)


按一下 **"審閱 + 創建**"，然後**創建**。 部署完成後，按一下"**轉到資源"** 以打開**邏輯應用設計器**。

### <a name="create-a-trigger-for-the-logic-app"></a>為邏輯應用創建觸發器
在 **"從公共觸發器開始"** 下，選擇 **"重複**"。 這將創建自動以常規時間間隔運行的邏輯應用。 在操作**的"頻率"** 框中，選擇 **"天"** 和"**間隔"** 框中，輸入**1**以每天運行一次工作流。

![重複操作](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>演練：郵件視覺化結果
以下教程演示如何創建邏輯應用，該應用通過電子郵件發送 Azure 監視器日誌查詢的結果。 

### <a name="add-azure-monitor-logs-action"></a>添加 Azure 監視器日誌操作
按一下 **" 新建步驟**"以添加在重複操作後運行的操作。 在 **"選擇操作**"下，鍵入**azure 監視器**，然後選擇**Azure 監視器日誌**。

![Azure 監視器日誌操作](media/logicapp-flow-connector/select-azure-monitor-connector.png)

按一下 [Azure Log Analytics – Run query and visualize results] \(Azure Log Analytics – 執行查詢，並將結果視覺化) ****。

![執行查詢並視覺化結果操作](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>添加 Azure 監視器日誌操作

選擇日誌分析工作區的**訂閱****和資源組**。 為 **"資源類型***"選擇日誌分析工作區*，然後在 **"資源名稱**"下選擇工作區的名稱。

在 [查詢]**** 視窗新增下列記錄查詢。  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

選擇"時間**範圍**"和"**圖表類型**"的**HTML 表***的查詢集*。
   
![執行查詢並視覺化結果操作](media/logicapp-flow-connector/run-query-visualize-action.png)

郵件將由與當前連接關聯的帳戶發送。 您可以通過按一下 **"更改連接**"來指定其他帳戶。

### <a name="add-email-action"></a>添加電子郵件操作

按一下 **" 新建步驟**"，然後按一下 **" 添加操作**"。 在 **"選擇操作**"下，鍵入**Outlook，** 然後選擇**Office 365 Outlook**。

![選擇 Outlook 連接器](media/logicapp-flow-connector/select-outlook-connector.png)

選擇**發送電子郵件 （V2）。**

![Office 365 Outlook 選取視窗](media/logicapp-flow-connector/select-mail-action.png)

按一下 **"正文"** 框中的任意位置以打開 **"動態內容**"視窗，該視窗將打開邏輯應用中以前操作的值。 選擇 **"查看更多****"，然後選擇"在**日誌分析"操作中查看查詢的結果。然後選擇"正文"。

![選取內文](media/logicapp-flow-connector/select-body.png)

在 [收件者]**** 視窗中指定收件者的電子郵件地址，並在 [主旨]**** 指定電子郵件的主旨。 

![郵件操作](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>儲存並測試邏輯應用程式
按一下 **"保存****"，然後按一下"運行"** 以執行邏輯應用的測試回合。

![儲存並執行](media/logicapp-flow-connector/save-run.png)


邏輯應用完成後，請檢查指定收件者的郵件。  您應該會收到內文類似如下的郵件：

![範例電子郵件](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 監視器中的記錄查詢](../log-query/log-query-overview.md)。
- 瞭解有關[邏輯應用](/azure/logic-apps/)的更多資訊
- 深入了解 [Microsoft Flow](https://ms.flow.microsoft.com)。

