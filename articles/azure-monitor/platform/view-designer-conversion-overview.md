---
title: Azure 監視器 view designer to 活頁簿轉換指南
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 19fd1d064a3f106d7b770481d1e456c51cf1e166
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87759422"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Azure 監視器 view designer to 活頁簿轉換指南
[View designer](view-designer.md) 是 Azure 監視器的功能，可讓您建立自訂視圖，以協助您在 Log Analytics 工作區中以圖表、清單和時程表來視覺化資料。 它們已轉換成活頁簿，以提供彈性的畫布，讓您在 Azure 入口網站內分析和建立豐富的視覺效果報表。 本文可協助您將 View designer 轉換成活頁簿。 


## <a name="workbooks-overview"></a>活頁簿總覽
活頁[簿](../insights/vminsights-workbooks.md)會將文字、 [記錄查詢](../log-query/query-language.md)、計量和參數結合成豐富的互動式報表。 具有相同 Azure 資源存取權的小組成員也可以編輯活頁簿。

活頁簿在下列案例中很有用：

-   當您事先不知道感興趣的計量時，探索虛擬機器的使用量： CPU 使用率、磁碟空間、記憶體、網路相依性等。不同于其他流量分析工具，活頁簿可讓您合併多種類型的視覺效果和分析，使其非常適合這種自由形式的探索。
-   藉由顯示金鑰計數器和其他記錄事件的計量，向您的小組說明最近布建的 VM 如何執行。
-   將 VM 調整大小實驗的結果與小組的其他成員共用。 您可以使用文字來說明實驗的目標，然後顯示用來評估實驗的每個使用計量和分析查詢，以及每個度量是否高於或低於目標的明確呼叫。
-   報告您的 VM 使用量中斷的影響、結合資料、文字說明和後續步驟的討論，以防止未來發生中斷。


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>為什麼要將 view designer 儀表板轉換成活頁簿？

View designer 可讓您產生以查詢為基礎的不同視圖和視覺效果。 不過，許多高階自訂仍然會受到限制，例如格式化格線和磚配置，或選取替代圖形來代表您的資料。 View designer 的限制為總共九個不同的磚，以代表您的資料。

Workbooks 是一種可讓您的資料充分發揮潛能的平臺。 活頁簿不僅會保留所有的功能，還可透過文字、計量、參數和其他更多功能來支援其他功能。 例如，活頁簿可讓使用者合併密集格線，並新增搜尋列以輕鬆地篩選和分析資料。 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>使用活頁簿而非 View Designer 的優點

* 支援記錄和計量。
* 允許個別存取控制和共用活頁簿的個人資料視圖。
* 具有索引標籤、調整大小和縮放控制項的自訂版面配置選項。
* 支援跨多個 Log Analytics 工作區查詢、Application Insights 應用程式和訂閱。
* 啟用可動態更新相關圖表與視覺效果的自訂參數。
* 來自公用 GitHub 的範本庫支援。

雖然本指南提供簡單的步驟來直接重新建立數個常用的視圖設計工具，但是活頁簿可讓使用者自由建立和設計任何自訂的視覺效果和度量。 下列螢幕擷取畫面是來自 [ [工作區使用](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) 方式] 範本，並顯示活頁簿能夠建立的範例：


![活頁簿應用程式範例](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>如何開始使用活頁簿
您可以在 Log Analytics 工作區中，將活頁簿中的開啟活頁簿，作為側邊導覽列中的專案，直接在 view designer 位置下啟用。

![活頁簿導覽](media/view-designer-conversion-overview/workbooks-nav.png)

一旦選取之後，就會顯示資源庫，列出您的工作區所有已儲存的活頁簿和範本。

![活頁簿資源庫](media/view-designer-conversion-overview/workbooks-gallery.png)

若要啟動新的活頁簿，您可以選取 [**快速入門**] 下的**空白**範本，或頂端導覽列中的 [**新增**] 圖示。 若要查看範本或返回已儲存的活頁簿，請從資源庫中選取該專案，或在搜尋列中搜尋該名稱。

若要儲存活頁簿，您將需要使用特定的標題、訂用帳戶、資源群組和位置來儲存報表。
活頁簿會自動填入與 LA 工作區相同的設定，但使用相同的訂用帳戶、資源群組，但使用者可能會變更這些報表設定。 活頁簿預設儲存至 *我的報表*，只有個別使用者才能存取。 它們也可以直接儲存到共用報表或稍後共用。

![活頁簿儲存](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>後續步驟

- [轉換選項](view-designer-conversion-options.md)
