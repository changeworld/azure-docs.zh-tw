---
title: 微軟 Azure 資料資源管理器流連接器（預覽）使用示例
description: 瞭解一些常見的 Microsoft 流連接器使用示例。
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 759ea9800a0ed051fed887adfb10b06f7a53b72f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529198"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>微軟流連接器（預覽）使用示例

Azure 資料資源管理器流連接器允許 Azure 資料資源管理器使用[Microsoft Power 自動的流功能](https://flow.microsoft.com/)作為計畫或觸發任務的一部分自動運行 Kusto 查詢和命令。 本文檔包含幾個常見的 Microsoft Flow 連接器使用示例。

有關詳細資訊，請參閱[Microsoft 流連接器（預覽）。](flow.md)

* [微軟流連接器和 SQL](#microsoft-flow-connector-and-sql)
* [將資料推送到 Power BI 資料集](#push-data-to-power-bi-dataset)
* [條件查詢](#conditional-queries)
* [通過電子郵件發送多個 Azure 資料資源管理器流程圖表](#email-multiple-azure-data-explorer-flow-charts)
* [向不同的連絡人發送不同的電子郵件](#send-a-different-email-to-different-contacts)
* [創建自訂 HTML 表](#create-a-custom-html-table)

## <a name="microsoft-flow-connector-and-sql"></a>微軟流連接器和 SQL

使用 Microsoft Flow 連接器查詢資料並將其聚合到 SQL 資料庫中。

> [!Note]
> SQL 插入針對每行單獨完成。 僅對少量輸出資料使用 Microsoft 流連接器。 

![](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> 在 *"群集名稱"* 欄位中，輸入群集 URL。

## <a name="push-data-to-power-bi-dataset"></a>將資料推送到 Power BI 資料集

Microsoft Flow 連接器可與 Power BI 連接器一起使用，將資料從 Kusto 查詢推送到 Power BI 流資料集。

1. 創建新的"運行"查詢並列出結果操作。
1. 選擇 **"新步驟**"。
1. 選擇 **"添加操作**"並搜索 Power BI。
1. 選取 [Power BI] ****。
1. 選擇**將行添加到資料集**。 

    ![流量功率 BI 連接器](./media/flow-usage/flow-powerbiconnector.png)
1. 輸入要推送資料的**工作區**、**資料集**和**表**。
1. 在動態內容對話方塊中，添加包含資料集架構和相關 Kusto 查詢結果的有效負載。

    ![流功率 BI 欄位](./media/flow-usage/flow-powerbifields.png)

流會自動為 Kusto 查詢結果表的每一行應用 Power BI 操作。 

![每行的流功率 BI 操作](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>條件查詢

Kusto 查詢的結果可用作下一個流操作的輸入或條件。

在下面的示例中，我們查詢 Kusto 中最後一天發生的事件。 對於每個已解決的事件，將發佈一條鬆弛消息，並創建推送通知。
對於仍處於活動狀態的每個事件，將查詢 Kusto 以瞭解有關類似事件的詳細資訊。 它將該資訊作為電子郵件發送，並打開相關的 TFS 任務。

按照以下說明創建類似的流：

1. 創建新的"運行"查詢並列出結果操作。
1. 選擇 **"新步驟**"。
1. 選擇**條件控制項**。
1. 從動態內容視窗中選擇要用作下一個操作的條件的參數。
1. 選擇*關係*和*值*的類型以在給定參數上設置特定條件。

    [![](./media/flow-usage/flow-condition.png "Flow conditions")](./media/flow-usage/flow-condition.png#lightbox)

    Flow 在查詢結果表的每一行上應用此條件。
1. 添加條件為真假時的操作。

    [![](./media/flow-usage/flow-conditionactions.png "Flow condition actions")](./media/flow-usage/flow-conditionactions.png#lightbox)

您可以使用 Kusto 查詢中的結果值作為下一個操作的輸入。 從動態內容視窗中選擇結果值。
在下面的示例中，添加了"鬆弛 - 消息後"操作和視覺化工作室 - 創建新的工作項操作，其中包含來自 Kusto 查詢的資料。

![鬆弛 - 消息後操作](./media/flow-usage/flow-slack.png)

![視覺工作室操作](./media/flow-usage/flow-visualstudio.png)

在此示例中，如果事件仍然處於活動狀態，請重新查詢 Kusto 以獲取有關過去如何解決來自同一源的事件的資訊。

![流條件查詢](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> 在 *"群集名稱"* 欄位中，輸入群集 URL。

將此資訊視覺化為圓形圖，並將其通過電子郵件發送給團隊。

![流條件電子郵件](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>通過電子郵件發送多個 Azure 資料資源管理器流程圖表

1. 使用"重複"觸發器創建新的 Flow，並定義流的間隔和頻率。 
1. 添加新步驟，包含一個或多個 Kusto - 執行查詢並視覺化結果操作。 

    ![在流中運行多個查詢](./media/flow-usage/flow-severalqueries.png)
1. 對於每個 Kusto - 執行查詢並視覺化結果，請定義以下欄位：
    * 群集 URL（在*群集名稱*欄位中）
    * 資料庫名稱
    * 查詢和圖表類型（HTML 表格/圓形圖/時間圖表/橫條圖/輸入自訂值）。

    ![使用多個附件視覺化結果](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

    > [!IMPORTANT]
    > 在 *"群集名稱"* 欄位中，輸入群集 URL。

1. 添加"發送電子郵件"操作。 
    * 在 *"正文"* 欄位中，插入所需的正文，以便查詢的視覺化結果包含在電子郵件正文中。
    * 要向電子郵件添加附件，請添加附件名稱和附件內容。
    
    ![通過電子郵件發送多個附件](./media/flow-usage/flow-emailmultipleattachments.png)

結果：

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="send-a-different-email-to-different-contacts"></a>向不同的連絡人發送不同的電子郵件

您可以使用 Microsoft Flow 向不同的連絡人發送不同的自訂電子郵件。 電子郵件地址和電子郵件內容是 Kusto 查詢的結果。

範例：

![使用 Kusto 查詢的動態電子郵件](./media/flow-usage/flow-dynamicemailkusto.png)

> [!IMPORTANT]
> 在 *"群集名稱"* 欄位中，輸入群集 URL。

![流操作中的動態電子郵件](./media/flow-usage/flow-dynamicemail.png)

## <a name="create-a-custom-html-table"></a>創建自訂 HTML 表

您可以使用 Microsoft Flow 創建和使用自訂 HTML 元素，如自訂 HTML 表。

下面的示例演示如何創建自訂 HTML 表。 HTML 表的行將按日誌級別著色（與 Azure 資料資源管理器中相同）。

按照以下說明創建類似的流：

1. 創建新的 Kusto - 執行查詢和清單結果操作。

    ![列出 HTML 表的結果](./media/flow-usage/flow-listresultforhtmltable.png)

> [!IMPORTANT]
> 在 *"群集名稱"* 欄位中，輸入群集 URL。

1. 迴圈查詢結果並創建 HTML 表正文： 
    1. 要創建變數以保存 HTML 字串，請選擇 **"新建步驟"**
    1. 選擇 **"添加操作**"並搜索變數。 
    1. 選擇**變數 - 初始化變數**。 
    1. 初始化字串變數，如下所示：

    ![初始化變數](./media/flow-usage/flow-initializevariable.png)

1. 迴圈對結果：
    1. 選擇 **"新步驟**"。
    1. 選擇 **"添加操作**"。
    1. 搜索變數。 
    1. 選擇**變數 - 追加到字串變數**。 
    1. 選擇之前初始化的變數名稱，並使用查詢結果創建 HTML 表行。 
    選取查詢結果時，將自動添加"應用於每個查詢結果"。

    在下面的示例中，`if`運算式用於定義每行的樣式：

    ```if(equals(items('Apply_to_each')?['Level'], 'Warning'), 'Yellow', if(equals(items('Apply_to_each')?['Level'], 'Error'), 'red', 'white'))```

    [![](./media/flow-usage/flow-createhtmltableloopcontent.png "Create HTML table loop content")](./media/flow-usage/flow-createhtmltableloopcontent.png#lightbox)

1. 創建完整的 HTML 內容： 
    1. 在"應用於每個操作"之外添加新操作。 
    在下面的示例中，使用的操作是發送電子郵件。
    1. 使用上述步驟中的變數定義 HTML 表。 
    1. 如果要發送電子郵件，請選擇 **"顯示高級選項**"，並在"是 HTML"下選擇"**是**"。

    ![自訂 HTML 表電子郵件](./media/flow-usage/flow-customhtmltablemail.png)

結果：

![自訂 HTML 表電子郵件結果](./media/flow-usage/flow-customhtmltableresult.png)

## <a name="next-steps"></a>後續步驟

瞭解[Microsoft Azure 資源管理器邏輯應用連接器](https://docs.microsoft.com/azure/kusto/tools/logicapps)，這是作為計畫或觸發任務的一部分自動運行 Kusto 查詢和命令的另一種方法。
