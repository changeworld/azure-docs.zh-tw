---
title: 建立要在 Azure 監視器中分析記錄資料的視圖 |Microsoft Docs
description: 藉由使用 Azure 監視器中的 [View Designer]，您可以建立 Azure 入口網站中顯示的自訂視圖，並在 Log Analytics 工作區中包含資料的各種視覺效果。 本文包含檢視設計工具的概觀，並提供建立和編輯自訂檢視的程序。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/04/2020
ms.openlocfilehash: f3ae081c8657a3ee6a27b0f9208c3eb4e4e745cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87759728"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>在 Azure 監視器中使用 View Designer 建立自訂視圖
藉由使用 Azure 監視器中的 View Designer，您可以在 Azure 入口網站中建立各種自訂視圖，以協助您將 Log Analytics 工作區中的資料視覺化。 本文提供檢視設計工具的概觀以及建立和編輯自訂檢視的程序。

> [!IMPORTANT]
> Azure 監視器中的視圖已轉換成可提供額外功能的活頁 [簿](workbooks-overview.md) 。 如需將現有的視圖轉換成活頁簿的詳細資訊，請參閱 [Azure 監視器 view designer to 活頁簿轉換指南](view-designer-conversion-overview.md) 。
 


如需檢視設計工具的詳細資訊，請參閱：

* [圖格參考](view-designer-tiles.md)：提供您的自訂檢視中每個可用圖格設定的參考指南。
* [視覺效果組件參考](view-designer-parts.md)：提供自訂檢視中可用的視覺效果組件設定的參考指南。


## <a name="concepts"></a>概念
視圖會顯示在 Azure 入口網站的 Azure 監視器 **總覽** ] 頁面中。 按一下 [深入解析]**** 區段下方的 [更多]****，即可在 [Azure 監視器]**** 功能表開啟此頁面。 每個自訂視圖中的圖格會依字母順序顯示，而監視解決方案的磚會安裝在相同的工作區。

![概觀分頁](media/view-designer/overview-page.png)

下表說明您使用檢視設計工具所建立檢視的元素：

| 部分 | 描述 |
|:--- |:--- |
| Tiles | 會顯示在您的 Azure 監視器 **總覽** 頁面上。 每個圖格會顯示它所呈現自訂檢視的視覺化摘要。 每個圖格類型提供記錄的不同的視覺效果。 您可以選取圖格來顯示自訂檢視。 |
| 自訂檢視 | 當您選取圖格時顯示。 每個檢視包含一或多個視覺效果組件。 |
| 視覺效果組件 | 根據一或多個 [記錄查詢](../log-query/log-query-overview.md)，呈現 Log Analytics 工作區中資料的視覺效果。 大部分組件包含標頭 (提供高階的視覺效果)，以及清單 (顯示前幾名搜尋結果)。 每個組件類型提供 Log Analytics 工作區中不同記錄的視覺效果。 您可以選取元件中的專案，以執行提供詳細記錄的記錄查詢。 |

## <a name="required-permissions"></a>所需的權限
您至少需要 Log Analytics 工作區中的 [參與者層級許可權](manage-access.md#manage-access-using-azure-permissions) ，才能建立或修改視圖。 如果您沒有此許可權，則 [View Designer] 選項不會顯示在功能表中。


## <a name="work-with-an-existing-view"></a>使用現有的檢視
使用檢視設計工具建立的檢視會顯示下列選項：

![概觀功能表](media/view-designer/overview-menu.png)

下表說明這些選項：

| 選項 | 描述 |
|:--|:--|
| 重新整理   | 使用最新資料重新整理檢視。 | 
| 記錄      | 開啟 [Log Analytics](../log-query/log-query-overview.md) ，以利用記錄查詢來分析資料。 |
| 編輯       | 在檢視表設計工具中開啟檢視以編輯其內容和設定。  |
| 複製      | 建立新的檢視並且在檢視表設計工具中開啟。 新檢視的名稱與原始檢視的名稱相同，只是再附加*複製*字樣。 |
| 日期範圍 | 為檢視中包含的資料設定日期和時間範圍篩選條件。 在檢視中設定查詢的任何日期範圍之前，會套用此日期範圍。  |
| +          | 定義針對檢視所定義的自訂篩選條件。 |


## <a name="create-a-new-view"></a>建立新的檢視
您可以在檢視設計工具中建立新的檢視，方法是選取 Log Analytics 工作區功能表中的 [檢視設計工具]****。

![檢視設計工具圖格](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>使用檢視設計工具
您可以使用檢視設計工具來建立新的檢視或編輯現有的檢視。 

檢視設計工具有三個窗格： 
* **設計**：包含您要建立或編輯的自訂檢視。 
* **控制項**：包含您新增至**設計**窗格的圖格和組件。 
* **屬性**：顯示圖格或所選組件的屬性。

![檢視設計工具](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>設定檢視圖格
自訂檢視可以只有單一圖格。 若要檢視目前圖格或選取另一個圖格，請選取 [控制]**** 窗格中的 [圖格]**** 索引標籤。 [屬性]**** 窗格會顯示目前圖格的屬性。 

您可以根據 [圖格參考](view-designer-tiles.md) 中的資訊來設定圖格屬性，然後按一下 [套用]**** 儲存變更。

### <a name="configure-the-visualization-parts"></a>設定視覺效果組件
檢視可以包含任意數目的視覺效果組件。 若要將組件新增至檢視，請選取 [檢視]**** 索引標籤，然後選取視覺效果組件。 [屬性]**** 窗格會顯示所選取組件的屬性。 

您可以根據 [視覺效果組件參考](view-designer-parts.md) 中的資訊來設定檢視的屬性，然後按一下 [套用]**** 儲存變更。

檢視只有一列視覺效果組件。 您可以將它們拖曳至新位置來重新安排現有組件的位置。

選取組件右上角的 **X**，即可從檢視中移除視覺效果組件。


### <a name="menu-options"></a>功能表選項
下表說明在編輯模式中處理檢視的選項。

![編輯功能表](media/view-designer/edit-menu.png)

| 選項 | 描述 |
|:--|:--|
| 儲存        | 儲存變更並關閉檢視。 |
| 取消      | 捨棄變更並關閉檢視。 |
| 刪除檢視 | 刪除檢視。 |
| 匯出      | 將檢視匯出至 [Azure Resource Manager 範本](../../azure-resource-manager/templates/template-syntax.md)，您可以將該範本匯入至其他工作區。 檔案的名稱是檢視的名稱，副檔名為 *omsview*。 |
| 匯入      | 將您從其他工作區匯出的 *omsview* 檔案匯入。 此動作會覆寫現有檢視的設定。 |
| 複製       | 建立新的檢視並且在檢視表設計工具中開啟。 新檢視的名稱與原始檢視的名稱相同，只是再附加*複製*字樣。 |

## <a name="next-steps"></a>後續步驟
* 將[圖格](view-designer-tiles.md)新增至您的自訂檢視。
* 將 [視覺效果元件](view-designer-parts.md) 新增至您的自訂視圖。
