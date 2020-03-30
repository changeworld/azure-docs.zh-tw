---
title: Azure 監視器視圖設計器到活頁簿過渡指南
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 234da921b4f0d1243ca8cfdb12ba2d851db2b43f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658688"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Azure 監視器視圖設計器到活頁簿過渡指南
[視圖設計器](view-designer.md)是 Azure 監視器的一項功能，允許您創建自訂視圖，以説明您在日誌分析工作區中視覺化資料，包括圖表、清單和時間表。 它們正在逐步淘汰，代之以提供額外功能的活頁簿。 本文概述了將現有視圖轉換為活頁簿的過程。

## <a name="workbooks-overview"></a>活頁簿概述
[活頁簿](../insights/vminsights-workbooks.md)將文本、 [日誌查詢](../log-query/query-language.md)、指標和參數合併為豐富的互動式報表。 對 Azure 資源具有相同存取權限的團隊成員也可以編輯活頁簿。

活頁簿適用于以下方案：

-   當您事先不知道感興趣的指標時，探索虛擬機器的使用：CPU 利用率、磁碟空間、記憶體、網路依賴關係等。與其他使用方式分析工具不同，活頁簿允許您組合多種視覺化和分析，使其成為這種自由形式的探索的絕佳去。
-   通過顯示關鍵計數器和其他日誌事件的指標，向您的團隊解釋最近預配的 VM 的性能。
-   與團隊的其他成員共用 VM 大小調整實驗的結果。 您可以使用文本解釋實驗的目標，然後顯示用於評估實驗的每個使用指標和分析查詢，以及每個指標是否高於或低於目標的明確呼出。
-   報告中斷對 VM 使用的影響，將資料、文本說明和討論防止將來停機的後續步驟結合起來。


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>為什麼要將視圖設計器儀表板轉換為活頁簿？

視圖設計器提供了生成不同基於查詢的視圖和視覺化效果的能力。 不過，許多高階自訂仍然會受到限制，例如格式化格線和磚配置，或選取替代圖形來代表您的資料。 視圖設計器僅限於總共九個不同的切片來表示資料。

Workbooks 是一種可讓您的資料充分發揮潛能的平臺。 活頁簿不僅保留了所有功能，而且還通過文本、指標、參數等支援其他功能。 例如，活頁簿允許使用者合併密集網格並添加搜索欄以輕鬆篩選和分析資料。 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>使用活頁簿優於視圖設計器的優勢

* 支援日誌和指標。
* 允許個人檢視用於個人存取控制和共用活頁簿視圖。
* 具有選項卡、大小調整和縮放控制項的自訂佈局選項。
* 支援跨多個日誌分析工作區、應用程式見解應用程式和訂閱進行查詢。
* 啟用動態更新關聯圖表和視覺化效果的自訂參數。
* 來自公共 GitHub 的範本庫支援。

雖然本指南提供了直接重新創建幾個常用視圖設計器視圖的簡單步驟，但活頁簿允許使用者自由創建和設計任何自己的自訂視覺化效果和指標。 以下螢幕截圖來自[工作區使用範本](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook)，顯示了活頁簿能夠創建的示例：


![活頁簿應用程式示例](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>如何開始使用活頁簿
在日誌分析工作區中，在視圖設計器位置正下方的側巡覽列中，將啟用活頁簿中的打開活頁簿作為專案。

![活頁簿導航](media/view-designer-conversion-overview/workbooks-nav.png)

選中後，將顯示一個庫，列出工作區保存的所有活頁簿和範本。

![活頁簿庫](media/view-designer-conversion-overview/workbooks-gallery.png)

要啟動新的活頁簿，您可以在 **"快速啟動"** 下選擇 **"空**"範本，或選擇頂部巡覽列中的 **"新建**"圖示。 要查看範本或返回到保存的活頁簿，請從庫中選擇專案或在搜索欄中搜索名稱。

要保存活頁簿，您需要使用特定標題、訂閱、資源組和位置保存報表。
活頁簿將自動填滿到與 LA 工作區相同的設置，使用相同的訂閱資源組，但是，使用者可以更改這些報表設置。 活頁簿預設保存到 *"我的報告"，* 僅由單個使用者訪問。 它們也可以直接保存到共用報表或稍後共用。

![活頁簿保存](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>後續步驟

- [轉換選項](view-designer-conversion-options.md)
