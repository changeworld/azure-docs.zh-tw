---
title: 使用 Microsoft Flow 自動化 Azure 監視器記錄流程
description: 了解如何利用 Azure Log Analytics Connector，使用 Microsoft Flow 來快速自動化可重複的程序。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/29/2017
ms.openlocfilehash: a6097d38d3335be356ca75f5a9d0eadeed414b03
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166942"
---
# <a name="automate-azure-monitor-log-processes-with-the-connector-for-microsoft-flow"></a>使用 Microsoft Flow 的連接器自動化 Azure 監視器記錄流程
[Microsoft Flow](https://ms.flow.microsoft.com) 可讓您使用數百個動作建立各種不同服務的自動化工作流程。 從一個動作的輸出可用來作為另一個動作的輸入，讓您建立不同服務之間的整合。  適用於 Microsoft Flow 的 Azure Log Analytics 連接器可讓您建立工作流程，包含 Azure 監視器中的 Log Analytics 工作區之中的記錄搜尋所擷取的資料。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

例如，您可以使用 Microsoft Flow，在 Office 365 的電子郵件通知中使用 Azure 監視器記錄資料、在 Azure DevOps 中建立 bug，或張貼時差訊息。  您可以使用簡易排程或從連接的服務中的某個動作觸發工作流程，例如收到郵件或推文時。  

這篇文章中的教學課程會示範如何建立流程，自動透過電子郵件傳送 Azure 監視器記錄查詢結果，這只是如何在 Microsoft Flow 中使用 Log Analytics 連接器的一個範例。 


## <a name="step-1-create-a-flow"></a>步驟 1：建立流程
1. 登入 [Microsoft Flow](https://flow.microsoft.com)，然後選取 [我的流程]。
2. 按一下 [+ 從空白建立]。

## <a name="step-2-create-a-trigger-for-your-flow"></a>步驟 2：建立流程的觸發程序
1. 按一下 [Search hundreds of connectors and triggers] \(搜尋數以百計的連接器和觸發程序)。
2. 在搜尋方塊中鍵入**排程**。
3. 依序選取 [排程] 和 [排程 - 重複]。
4. 在 [頻率] 方塊中選取 [天]，然後在 [間隔] 方塊中輸入 **1**。<br><br>![Microsoft Flow 觸發程序對話方塊](media/flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>步驟 3：新增 Log Analytics 動作
1. 按一下 [+ 新增步驟]，然後按一下 [新增動作]。
2. 搜尋 **Log Analytics**。
3. 按一下 [Azure Log Analytics – Run query and visualize results] \(Azure Log Analytics – 執行查詢，並將結果視覺化)。<br><br>![Log Analytics 執行查詢視窗](media/flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>步驟 4：設定 Log Analytics 動作

1. 指定工作區的詳細資料，包括訂用帳戶識別碼、資源群組和工作區名稱。
2. 在 [查詢] 視窗新增下列記錄查詢。  這只是範例查詢，您可以使用任何其他可傳回資料的查詢取代。
   ```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
   ```

2. 針對 [圖表類型] 選取 [HTML 表格]。<br><br>![Log Analytics 動作](media/flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>步驟 5：設定傳送電子郵件的流程

1. 按一下 [新增步驟]，然後按一下 [+ 新增動作]。
2. 搜尋 **Office 365 Outlook**。
3. 按一下 [Office 365 Outlook – 傳送電子郵件]。<br><br>![Office 365 Outlook 選取視窗](media/flow-tutorial/flow04.png)

4. 在 [收件者] 視窗中指定收件者的電子郵件地址，並在 [主旨] 指定電子郵件的主旨。
5. 在 [內文] 方塊的任何地方按一下。  隨即開啟 [動態內容] 視窗，並具有來自先前動作的值。  
6. 選取 [內文]。  這是 Log Analytics 動作中的查詢結果。
6. 按一下 [顯示進階選項]。
7. 在 [為 HTML] 方塊中選取 [是]。<br><br>![Office 365 電子郵件設定畫面](media/flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>步驟 6：儲存並測試流程
1. 在 [流程名稱] 方塊中，新增您的流程名稱，然後按一下 [建立流程]。<br><br>![儲存流程](media/flow-tutorial/flow06.png)
2. 現在已建立流程，並且會在一天之後，也就是您指定的排程時執行。 
3. 若要立即測試流程，請依序按一下 [立即執行]、[執行流程]。<br><br>![執行流程](media/flow-tutorial/flow07.png)
3. 流程完成時，請檢查您指定的收件者郵件。  您應該會收到內文類似如下的郵件：<br><br>![範例電子郵件](media/flow-tutorial/flow08.png)


## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 監視器中的記錄查詢](../log-query/log-query-overview.md)。
- 深入了解 [Microsoft Flow](https://ms.flow.microsoft.com)。


