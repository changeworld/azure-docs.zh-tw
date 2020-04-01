---
title: 微軟 Azure 資料資源管理員串流連接器(預覽版)
description: 瞭解如何使用 Microsoft 流連接器創建自動計畫或觸發任務的流。
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 198b78d5bab15057fdb6c7f6d4e8fff9f77d496e
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397120"
---
# <a name="microsoft-flow-connector-preview"></a>微軟流連接器(預覽版)

Azure 資料資源管理器流連接器允許 Azure 資料資源管理器使用[Microsoft Power 自動的流功能](https://flow.microsoft.com/)作為計畫或觸發任務的一部分自動執行 Kusto 查詢和命令。

常見使用機制包括:

* 傳送包含表格和圖表的每日報告
* 依查詢結果設定通知
* 在叢集上調度控制命令
* Azure 資料資源管理員與資料庫之間匯出及匯入資料 

有關詳細資訊,請參閱[Microsoft Flow 連接器使用範例](flow-usage.md)。

##  <a name="log-in"></a>登入 

1. 登錄到[微軟電源自動。](https://flow.microsoft.com/)

1. 首次連接時,系統將提示您登錄。

1. 選取 [登入]****，然後輸入您的認證。

![登入對話方塊](./media/flow/flow-signin.png)

## <a name="authentication"></a>驗證

您可以使用使用者認證或 AAD 應用程式進行身分驗證。

> [!Note]
> 確保您的應用程式是[AAD 應用程式](https://docs.microsoft.com/azure/kusto/management/access-control/how-to-provision-aad-app),並有權在群集上執行查詢。

1. 選擇 Microsoft 串流連線器右上角的三![個點: 加入連線](./media/flow/flow-addconnection.png)

1. 選擇 **"添加新連接**",然後選擇"**與服務主體連接**"。
![登入對話框](./media/flow/flow-signin.png)

1. 輸入必要資訊：
    * 連接名稱:新連線的描述性和有意義的名稱
    * 用戶端識別碼:您的應用程式識別碼
    * 用戶端金鑰:您的應用程式金鑰
    * 租戶:在其中創建應用程式的 AAD 目錄的 ID。 例如,Microsoft 租戶 ID 是:72f988bf-86f1-41af-91ab-2d7cd011db47

![應用程式驗證](./media/flow/flow-appauth.png)

身份驗證完成後,您將看到流使用新添加的連接。

![已完成應用程式身份驗證](./media/flow/flow-appauthcomplete.png)

從現在起,此流將使用這些應用程式憑據運行。

## <a name="find-the-azure-kusto-connector"></a>尋找 Azure 函式庫斯托連接器

要使用 Microsoft Flow 連接器,您需要首先添加觸發器。 可以基於定期時間段或對前一個流操作的回應定義觸發器。

1. [建立新流](https://flow.microsoft.com/manage/flows/new),或者從主頁選擇 **"我的流"** 操作,然後選擇 **"新建**"。

    ![建立新流程](./media/flow/flow-newflow.png)

1. 從空白添加計劃。

    ![新的計畫串流](./media/flow/flow-scheduled-from-blank.png)

1. 在「生成計畫流」頁上輸入所需資訊。
    ![組建計畫流](./media/flow/flow-build-scheduled-flow.png)
1. 選取 [建立]  。
1. 選取 [+ 新步驟] ****。
1. 在搜索框中,輸入"庫斯托"。

    ![選擇串流操作](./media/flow/flow-actions.png)

1. 選擇**Azure 資料資源管理員**。

## <a name="flow-actions"></a>流操作

打開 Azure 資料資源管理器連接器時,可以向流添加三種可能的操作。

本節介紹每個 Microsoft Flow 操作的功能和參數。

![串流 Azure 資料資源管理員操作](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>執行控制命令並視覺化結果

使用「執行控制」指令並可視化結果操作來執行[控制命令](https://docs.microsoft.com/azure/kusto/management/index)。

1. 指定群集 URL。 例如， https://clusterName.eastus.kusto.windows.net
1. 輸入資料庫的名稱。
1. 指定控制命令:
    * 從串流使用的應用程式與連接器中選擇動態內容
    * 新增表示式以存取、轉換和比較值
1. 要透過電子郵件傳送此操作的結果作為表格或圖表,請指定圖表類型,該類型可以是:
    * HTML 表
    * 餅圖
    * 時間圖表
    * 條形圖

![執行流控制命令](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> 在 *「群集名稱」* 欄位中,輸入群集 URL。

### <a name="run-query-and-list-results"></a>執行查詢與清單結果

> [!Note]
> 如果查詢以點開頭(表示它是[控制項指令](https://docs.microsoft.com/azure/kusto/management/index)),請使用[「執行「控制」控制器, 並可視化結果](#run-control-command-and-visualize-results)。

此操作向 Kusto 群集發送查詢。 之後添加的操作會反覆運算查詢結果的每一行。

以下示例每分鐘觸發查詢,並根據查詢結果發送電子郵件。 查詢檢查資料庫中的行數,然後僅在行數大於 0 時發送電子郵件。 

![執行查詢清單結果](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> 如果列有多個行,則連接器將針對列中的每一行運行。

### <a name="run-query-and-visualize-results"></a>執行查詢並視覺化結果
        
> [!Note]
> 如果查詢以點開頭(表示它是[控制項指令](https://docs.microsoft.com/azure/kusto/management/index)),請使用[「執行「控制」控制器, 並可視化結果](#run-control-command-and-visualize-results)。
        
使用「執行」查詢並可視化結果操作,將 Kusto 查詢結果可視化為表或圖表。 例如,使用此流通過電子郵件接收每日ICM報告。 
    
在此範例中,查詢的結果將作為 HTML 表返回。
            
![執行查詢並視覺化結果](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> 在 *「群集名稱」* 欄位中,輸入群集 URL。

## <a name="email-kusto-query-results"></a>透過電子郵件傳送函式庫斯托查詢結果

您可以在任何流中包括一個步驟,以便透過電子郵件將報告發送到任何電子郵件位址。 

1. 選擇 **「新步驟**」可向流添加新步驟。
1. 在搜尋欄位中,輸入 Office 365 並選擇**Office 365 Outlook**。
1. 選擇**傳送電子郵件 (V2)。**
1. 輸入要發送電子郵件報告的電子郵件位址。
1. 輸入電子郵件的主題。
1. 選擇 **「代碼」 檢視**。
1. 將游標放在 *「正文」* 欄位中,然後選擇 **「添加動態內容**」 。。
1. 選擇**正文Html**。
    ![傳送電子郵件](./media/flow/flow-send-email.png)
1. 選取 [顯示進階選項]****。
1. 附*件名稱 -1*欄位中,選擇**附件名稱**。
1. 在 *'附件內容'* 欄位中,選擇**附件內容**。
1. 如有必要,添加更多附件。 
1. 如有必要,設置重要性級別。
1. 選取 [儲存]  。

![傳送電子郵件](./media/flow/flow-add-attachments.png)

## <a name="check-if-your-flow-succeeded"></a>檢查您的流是否成功

要檢查串流是否成功,請參閱流的執行歷史記錄:
1. 跳到[Microsoft 主頁](https://flow.microsoft.com/)。
1. 從主菜單中,選擇["我的流](https://flow.microsoft.com/manage/flows)"。
    ![我的流頁](./media/flow/flow-myflows.png)
1. 在要調查的流行上,選擇更多命令圖示,然後**執行歷史紀錄**。

    ![執行歷程記錄選單](./media/flow//flow-runhistory.png)

    所有流運行都列出啟動時間、持續時間和狀態。
    ![執行歷程記錄結果頁](./media/flow/flow-runhistoryresults.png)

    有關流的完整詳細資訊,請在"[我的流"](https://flow.microsoft.com/manage/flows)頁上選擇要調查的流。

    ![執行歷程記錄完整結果頁](./media/flow/flow-fulldetails.png) 

要查看運行失敗的原因,請選擇運行開始時間。 此時將顯示流,失敗流的步驟由紅色感嘆號指示。 展開失敗步驟以查看其詳細資訊。 右側窗格包含有關故障的資訊,以便您可以排除故障。

![流錯誤](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>超時異常

如果流運行超過 7 分鐘,則可能會失敗並返回"請求超時"異常。

瞭解有關[微軟流量限制](#limitations)的更多。
    
同一查詢可能在 Azure 資料資源管理器中成功運行,因為時間不受限制,可以更改。
            
下圖顯示了「請求超時」異常:
    
![流要求逾時異常錯誤](./media/flow/flow-requesttimeout.png)
    
要修復超時問題,請嘗試提高查詢的效率,使其運行得更快,或將其劃分為區塊。 每個區塊都可以在查詢的不同部分上運行。

有關詳細資訊,請閱讀[查詢最佳實務](https://docs.microsoft.com/azure/kusto/query/best-practices)。

## <a name="limitations"></a>限制

* 返回給客戶端的結果僅限於 500,000 條記錄。 這些記錄的總記憶體不能超過 64 MB 和 7 分鐘執行時間。
* 連接器不支援[分叉](https://docs.microsoft.com/azure/kusto/query/forkoperator)和[分面](https://docs.microsoft.com/azure/kusto/query/facetoperator)運算符。
* 流量在微軟邊緣和Chrome上效果最好。

## <a name="next-steps"></a>後續步驟

瞭解[Microsoft Azure 資源管理員邏輯應用連接器](https://docs.microsoft.com/azure/kusto/tools/logicapps),這是作為計畫或觸發任務的一部分自動運行 Kusto 查詢和命令的另一種方法。
