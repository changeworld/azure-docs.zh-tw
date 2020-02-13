---
title: Azure 監視器視圖設計工具至活頁簿轉換指南
description: ''
author: austonli
ms.author: aul
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 5c07d14f4a6d29b65a1ffe31fc6eaad18e8fbe26
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170899"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Azure 監視器視圖設計工具至活頁簿轉換指南
「[視圖設計](view-designer.md)工具」是 Azure 監視器的功能，可讓您建立自訂的視圖，以協助您將 Log Analytics 工作區中的資料視覺化，其中包含圖表、清單和時間軸。 它們會被淘汰，並取代為提供額外功能的活頁簿。 本文概要說明將現有的視圖轉換成活頁簿的程式。

## <a name="workbooks-overview"></a>活頁簿總覽
活頁[簿](../insights/vminsights-workbooks.md)將文字、 [記錄查詢](../log-query/query-language.md)、計量和參數結合成豐富的互動式報表。 具有相同 Azure 資源存取權的小組成員也能夠編輯活頁簿。

活頁簿有助於下列案例：

-   當您事先不知道感興趣的度量時，探索虛擬機器的使用量： CPU 使用率、磁碟空間、記憶體、網路相依性等等。不同于其他流量分析工具，活頁簿可讓您結合多種視覺效果和分析，使其更適合這種自由形式探索。
-   藉由顯示金鑰計數器和其他記錄事件的計量，向您的小組說明最近布建的 VM 的執行方式。
-   將 VM 調整大小實驗的結果與小組的其他成員共用。 您可以使用文字來說明實驗的目標，然後顯示用來評估實驗的每個使用計量和分析查詢，以及每個計量高於或低於目標的清除呼叫。
-   報告您的 VM 使用量中斷的影響、結合資料、文字說明，以及後續步驟的討論，以避免未來發生中斷。


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>為什麼要將 view designer 儀表板轉換成活頁簿？

視圖設計工具可讓您產生不同的查詢型視圖和視覺效果。 不過，許多高階自訂仍然會受到限制，例如格式化格線和磚配置，或選取替代圖形來代表您的資料。 View designer 限制為總共9個不同的磚，以代表您的資料。

Workbooks 是一種可讓您的資料充分發揮潛能的平臺。 活頁簿不僅會保留所有的功能，還支援透過文字、計量、參數和其他更多功能的其他功能。 例如，活頁簿可讓使用者合併密集的格線，並加入搜尋列，輕鬆地篩選和分析資料。 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>使用活頁簿流覽設計工具的優點

* 支援記錄和計量。
* 允許個人檢視進行個別的存取控制和共用的活頁簿流覽。
* 具有索引標籤、調整大小和縮放控制項的自訂版面配置選項。
* 跨多個 Log Analytics 工作區、Application Insights 應用程式和訂用帳戶的查詢支援。
* 啟用動態更新相關聯圖表和視覺效果的自訂參數。
* 來自公用 GitHub 的範本庫支援。

雖然本指南提供簡單的步驟來直接重新建立數個常用的視圖設計工具視圖，但活頁簿可讓使用者自由地建立及設計自己的自訂視覺效果和計量。 下列螢幕擷取畫面是來自 [[工作區使用](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook)方式] 範本，並顯示活頁簿能夠建立的範例：


![活頁簿應用程式範例](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>如何開始使用活頁簿
在 Log Analytics 工作區中，開啟活頁簿中的活頁簿，做為側邊導覽列中的專案，直接在 view designer 位置下啟用。

![活頁簿導覽](media/view-designer-conversion-overview/workbooks-nav.png)

選取之後，系統會顯示資源庫，列出您工作區的所有已儲存的活頁簿和範本。

![活頁簿圖庫](media/view-designer-conversion-overview/workbooks-gallery.png)

若要啟動新的活頁簿，您可以選取 [**快速啟動**] 底下的**空白**範本，或上方導覽列中的 [**新增**] 圖示。 若要查看範本或回到儲存的活頁簿，請從資源庫中選取專案，或在搜尋列中搜尋名稱。

若要儲存活頁簿，您必須使用特定的標題、訂用帳戶、資源群組和位置來儲存報表。
活頁簿會自動填入與 LA 工作區相同的設定，且具有相同的訂用帳戶、資源群組，但使用者可能會變更這些報表設定。 活頁簿預設會儲存到*我的報表*，只有個別使用者可以存取。 它們也可以直接儲存到共用報表，或稍後共用。

![活頁簿儲存](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>後續步驟

- [轉換選項](view-designer-conversion-options.md)
