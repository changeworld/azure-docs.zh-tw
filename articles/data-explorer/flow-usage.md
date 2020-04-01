---
title: 微軟 Azure 資料資源管理員串流連接器(預覽)使用範例
description: 瞭解一些常見的 Microsoft 流連接器使用範例。
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 796b37f98fed7e389fa71a15b5e6697a14db1a16
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397201"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>微軟流連接器(預覽)使用範例

Azure 資料資源管理器流連接器允許 Azure 資料資源管理器使用[Microsoft Power 自動的流功能](https://flow.microsoft.com/)作為計畫或觸發任務的一部分自動執行 Kusto 查詢和命令。 本文檔包含幾個常見的 Microsoft Flow 連接器使用範例。

有關詳細資訊,請參閱[Microsoft 流連接器(預覽)。](flow.md)

* [微軟流連接器和 SQL](#microsoft-flow-connector-and-sql)
* [將資料推送到 Power BI 資料集](#push-data-to-power-bi-dataset)
* [條件查詢](#conditional-queries)
* [透過電子郵件傳送多個 Azure 資料資源管理員流程圖表](#email-multiple-azure-data-explorer-flow-charts)
* [傳送不同的聯絡人傳送不同的電子郵件](#send-a-different-email-to-different-contacts)
* [建立自訂 HTML 表](#create-a-custom-html-table)

## <a name="microsoft-flow-connector-and-sql"></a>微軟流連接器和 SQL

使用 Microsoft Flow 連接器查詢數據並將其聚合到 SQL 資料庫中。

> [!Note]
> SQL 插入針對每行單獨完成。 僅對少量輸出數據使用 Microsoft 流連接器。 

![](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> 在 *「群集名稱」* 欄位中,輸入群集 URL。

## <a name="push-data-to-power-bi-dataset"></a>將資料推送到 Power BI 資料集

Microsoft Flow 連接器可與 Power BI 連接器一起使用,將數據從 Kusto 查詢推送到 Power BI 串流資料集。

1. 創建新的"運行"查詢並列出結果操作。
1. 選擇 **「新步驟**」。
1. 選擇 **「新增操作**」並搜尋 Power BI。
1. 選取 [Power BI] ****。
1. 選擇**將行新增到資料集**。 

    ![流量功率 BI 連接器](./media/flow-usage/flow-powerbiconnector.png)
1. 輸入要推送資料的工作**區**,**資料集**與**表**格 。
1. 在動態內容對話框中,添加包含數據集架構和相關 Kusto 查詢結果的有效負載。

    ![流功率 BI 欄位](./media/flow-usage/flow-powerbifields.png)

流會自動為 Kusto 查詢結果表的每一行應用 Power BI 操作。 

![每行的流功率 BI 操作](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>條件查詢

Kusto 查詢的結果可用作下一個流操作的輸入或條件。

在下面的示例中,我們查詢 Kusto 中最後一天發生的事件。 對於每個已解決的事件,將發佈一條鬆弛消息,並創建推送通知。
對於仍處於活動狀態的每個事件,將查詢 Kusto 以瞭解有關類似事件的詳細資訊。 它將該資訊作為電子郵件發送,並打開相關的 TFS 任務。

以以下說明建立類似的流:

1. 創建新的"運行"查詢並列出結果操作。
1. 選擇 **「新步驟**」。
1. 選擇**條件控制器**。
1. 從動態內容視窗中選擇要用作下一個操作的條件的參數。
1. 選擇*關係*和*值*的類型以在給定參數上設置特定條件。

    [![](./media/flow-usage/flow-condition.png "Flow conditions")](./media/flow-usage/flow-condition.png#lightbox)

    Flow 在查詢結果表的每一行上應用此條件。
1. 添加條件為真假時的操作。

    [![](./media/flow-usage/flow-conditionactions.png "Flow condition actions")](./media/flow-usage/flow-conditionactions.png#lightbox)

您可以使用 Kusto 查詢中的結果值作為下一個操作的輸入。 從動態內容視窗中選擇結果值。
在下面的範例中,添加了"鬆弛 - 消息後"操作和可視化工作室 - 創建新的工作項操作,其中包含來自 Kusto 查詢的數據。

![鬆弛 - 訊息後操作](./media/flow-usage/flow-slack.png)

![視覺工作室操作](./media/flow-usage/flow-visualstudio.png)

在此示例中,如果事件仍然處於活動狀態,請再次查詢 Kusto 以獲取有關過去如何解決來自同一源的事件的資訊。

![串流條件查詢](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> 在 *「群集名稱」* 欄位中,輸入群集 URL。

將此資訊可視化為圓示圖,並將其透過電子郵件發送給團隊。

![串流條件電子郵件](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>透過電子郵件傳送多個 Azure 資料資源管理員流程圖表

1. 使用"重複"觸發器創建新的 Flow,並定義流的間隔和頻率。 
1. 添加新步驟,包含一個或多個 Kusto - 運行查詢並可視化結果操作。 

    ![在串流中執行多個查詢](./media/flow-usage/flow-severalqueries.png)
1. 對於每個 Kusto - 執行查詢並可視化結果,請定義以下欄位:
    * 叢集網址(在*群組名稱*)
    * 資料庫名稱
    * 查詢和圖表類型(HTML 表格/圓形圖/時程圖表/條形圖/輸入自訂值)。

    ![使用多個附件視覺化結果](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

    > [!IMPORTANT]
    > 在 *「群集名稱」* 欄位中,輸入群集 URL。

1. 添加「發送電子郵件」操作。 
    * 在 *「正文」* 欄位中,插入所需的正文,以便查詢的可視化結果包含在電子郵件正文中。
    * 要向電子郵件添加附件,請添加附件名稱和附件內容。
    
    ![透過電子郵件傳送多個附件](./media/flow-usage/flow-email-multiple-attachments.png)

    有關建立電子郵件操作的完整說明,請參閱[電子郵件 Kusto 查詢結果](flow.md#email-kusto-query-results)。 

結果：

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="send-a-different-email-to-different-contacts"></a>傳送不同的聯絡人傳送不同的電子郵件

您可以使用 Microsoft Flow 向不同的連絡人發送不同的自訂電子郵件。 電子郵件地址和電子郵件內容是 Kusto 查詢的結果。

範例：

![使用 Kusto 查詢的動態電子郵件](./media/flow-usage/flow-dynamicemailkusto.png)

> [!IMPORTANT]
> 在 *「群集名稱」* 欄位中,輸入群集 URL。

![串流操作中的動態電子郵件](./media/flow-usage/flow-dynamicemail.png)

## <a name="create-a-custom-html-table"></a>建立自訂 HTML 表

您可以使用 Microsoft Flow 創建和使用自訂 HTML 元素,如自訂 HTML 表。

下面的範例展示如何建立自訂 HTML 表。 HTML 表的行將按日誌等級著色(與 Azure 資料資源管理器中相同)。

以以下說明建立類似的流:

1. 建立新的 Kusto - 執行查詢和清單結果操作。

    ![列出 HTML 表的結果](./media/flow-usage/flow-listresultforhtmltable.png)

> [!IMPORTANT]
> 在 *「群集名稱」* 欄位中,輸入群集 URL。

1. 循環查詢結果並建立 HTML 表正文: 
    1. 要建立變數以儲存 HTML 字串,請選擇 **"新建步驟"**
    1. 選擇 **「新增操作**」並搜尋變數。 
    1. 選擇**變數 ─初始化變數**。 
    1. 初始化字串變數,如下所示:

    ![初始化變數](./media/flow-usage/flow-initializevariable.png)

1. 循環對結果:
    1. 選擇 **「新步驟**」。
    1. 選擇 **『添加操作**』」。
    1. 搜索變數。 
    1. 選擇**變數 ─新增到字串變數**。 
    1. 選擇之前初始化的變數名稱,並使用查詢結果創建 HTML 表行。 
    選擇查詢結果時,將自動添加"應用於每個查詢結果」。

    在下面的範例中,`if`表示式用於定義每行的樣式:

    ```if(equals(items('Apply_to_each')?['Level'], 'Warning'), 'Yellow', if(equals(items('Apply_to_each')?['Level'], 'Error'), 'red', 'white'))```

    [![](./media/flow-usage/flow-createhtmltableloopcontent.png "Create HTML table loop content")](./media/flow-usage/flow-createhtmltableloopcontent.png#lightbox)

1. 建立完整的 HTML 內容: 
    1. 在「應用於每個操作」之外添加新操作。 
    在下面的示例中,使用的操作是發送電子郵件。
    1. 使用上述步驟中的變數定義 HTML 表。 
    1. 如果要寄送電子郵件,請選擇 **「顯示進階選項**」,並在「是 HTML」下選擇 **「 是**」。

    ![自訂 HTML 表電子郵件](./media/flow-usage/flow-customhtmltablemail.png)

結果：

![自訂 HTML 表電子郵件結果](./media/flow-usage/flow-customhtmltableresult.png)

## <a name="next-steps"></a>後續步驟

瞭解[Microsoft Azure 資源管理員邏輯應用連接器](https://docs.microsoft.com/azure/kusto/tools/logicapps),這是作為計畫或觸發任務的一部分自動運行 Kusto 查詢和命令的另一種方法。
