---
title: Azure 監視器活頁簿概觀
description: 使用預先建立及使用自訂參數之活頁簿來簡化複雜的報告
author: mrbullwinkle
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 29e675f3ae35df9211f58d45ad8450566d67a588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658229"
---
# <a name="azure-monitor-workbooks"></a>Azure 監視器活頁簿

活頁簿為數據分析和在 Azure 門戶中創建豐富的視覺化報表提供了靈活的畫布。 它們允許您從 Azure 中挖掘多個資料來源，並將它們合併到統一的互動式體驗中。 

## <a name="data-sources"></a>資料來源

活頁簿可以查詢 Azure 中的多個源的資料。 活頁簿的作者可以轉換此資料，以便深入瞭解底層元件的可用性、性能、使用方式和總體運行狀況。 例如，分析來自虛擬機器的性能日誌，以識別高 CPU 或低記憶體實例，並在互動式報表中將結果顯示為網格。
  
但活頁簿的真正力量是能夠將來自不同來源的資料合併到單個報表中。 這允許創建複合資源檢視或跨資源聯接，從而實現更豐富的資料和見解，否則將是不可能的。

活頁簿當前與以下資料來源相容：

* [日誌](workbooks-data-sources.md#logs)
* [計量](workbooks-data-sources.md#metrics)
* [Azure 資源圖](workbooks-data-sources.md#azure-resource-graph)
* [警示 (預覽)](workbooks-data-sources.md#alerts-preview)
* [工作負載運行狀況（預覽）](workbooks-data-sources.md#workload-health-preview)
* [Azure 資源運行狀況（預覽）](workbooks-data-sources.md#azure-resource-health)
* [Azure 資料資源管理器（預覽）](workbooks-data-sources.md#azure-data-explorer-preview)

## <a name="visualizations"></a>視覺效果

活頁簿提供了一組豐富的功能，用於視覺化資料。 有關每種視覺化類型的詳細示例，請參閱以下示例連結：

* [Text](workbooks-visualizations.md#text)
* [圖表](workbooks-visualizations.md#charts)
* [網 格](workbooks-visualizations.md#grids)
* [並排顯示](workbooks-visualizations.md#tiles)
* [樹木](workbooks-visualizations.md#trees)
* [圖表](workbooks-visualizations.md#graphs)

![活頁簿視覺化示例](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>開始使用

要流覽活頁簿體驗，首先導航到 Azure 監視器服務。 這可以通過在 Azure 門戶中的搜索框中鍵入**監視器**來實現。

然後選擇**活頁簿（預覽）。**

![活頁簿預覽按鈕的螢幕截圖突出顯示在紅色框中](./media/workbooks-overview/workbooks-preview.png)

### <a name="gallery"></a>主機庫

這將帶您到活頁簿庫：

![Azure 監視器活頁簿庫視圖的螢幕截圖](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>活頁簿與活頁簿範本

您可以看到綠色_活頁簿_和紫色工作_簿_範本。 範本用作精心策劃的報表，專為多個使用者和團隊靈活重用而設計。 打開範本將創建一個包含範本內容的瞬態活頁簿。 

您可以調整基於範本的活頁簿的參數並執行分析，而不必擔心破壞同事的未來報告體驗。 如果打開範本，請進行一些調整，然後選擇將範本保存為活頁簿的保存圖示，然後以綠色顯示，使原始範本保持不變。 

在引擎蓋下，範本也不同于保存的活頁簿。 保存活頁簿將創建關聯的 Azure 資源管理器資源，而剛剛打開範本時創建的瞬態活頁簿沒有與其關聯的唯一資源。 要瞭解有關如何在活頁簿中管理存取控制的更多詳細資訊，請參閱[活頁簿存取控制文章](workbooks-access-control.md)。

### <a name="exploring-a-workbook-template"></a>探索活頁簿範本

選擇**應用程式故障分析**以查看預設應用程式活頁簿範本之一。

![應用程式故障分析範本的螢幕截圖](./media/workbooks-overview/failure-analysis.png)

如前所述，打開範本將創建一個臨時活頁簿，以便您能夠與之交互。 預設情況下，活頁簿在讀取模式下打開，該模式下僅顯示原始範本作者創建的預期分析體驗的資訊。

就此特定活頁簿而言，體驗是互動式的。 您可以調整要顯示的資料的訂閱、目標應用和時間範圍。 進行這些選擇後，HTTP 要求網格也是互動式的，根據該網格，選擇單個行將更改報表底部兩個圖表中呈現的資料。

### <a name="editing-mode"></a>編輯模式

要瞭解如何將此活頁簿範本放在一起，您需要通過選擇 **"編輯**"來交換到編輯模式。 

![應用程式故障分析範本的螢幕截圖](./media/workbooks-overview/edit.png)

切換到編輯模式後，您會注意到許多**編輯**框顯示在與活頁簿的每個方面對應的正確。

![編輯按鈕的螢幕截圖](./media/workbooks-overview/edit-mode.png)

如果我們立即在請求資料網格下選擇編輯按鈕，我們可以看到，活頁簿的這一部分包含針對來自應用程式見解資源的資料的 Kusto 查詢。

![基礎 Kusto 查詢的螢幕截圖](./media/workbooks-overview/kusto.png)

按一下右側的其他 **"編輯"** 按鈕將顯示一些構成活頁簿的核心元件，如基於標記[的文字方塊](workbooks-visualizations.md#text)、[參數選擇](workbooks-parameters.md)UI 元素和其他[圖表/視覺化類型](workbooks-visualizations.md)。 

在編輯模式下流覽預構建的範本，然後修改這些範本以滿足您的需要並保存自己的自訂活頁簿，是開始瞭解 Azure 監視器活頁簿可能實現的內容的絕佳方式。

## <a name="pinning-visualizations"></a>固定視覺化效果

在活頁簿處於固定模式時，或者活頁簿作者啟用了該元素的設置以使引腳圖示可見，則可以在活頁簿上使用這些專案上的固定按鈕來固定活頁簿中的文本、查詢和指標步驟。 

要訪問引腳模式，請按一下 **"編輯"** 以進入編輯模式，然後選擇頂部欄中的藍色引腳圖示。 然後，螢幕右側每個相應活頁簿部件*的"編輯"* 框上方將顯示一個單獨的引腳圖示。

![引腳體驗](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> 活頁簿的狀態在 pin 時保存，如果修改了基礎活頁簿，儀表板上的固定活頁簿將不會更新。 為了更新固定活頁簿部件，您需要刪除並重新固定該部件。

## <a name="dashboard-time-ranges"></a>儀表板時間範圍

如果固定項配置為使用*時間範圍*參數，則固定活頁簿查詢部件將尊重儀表板的時間範圍。 儀表板的時間範圍值將用作時間範圍參數的值，儀表板時間範圍的任何更改都將導致固定項更新。 如果固定零件使用儀表板的時間範圍，您將看到固定零件更新的副標題，以便每當時間範圍更改時顯示儀表板的時間範圍。 

此外，使用時間範圍參數固定活頁簿部件將以儀表板的時間範圍確定的速率自動刷新。 上次執行查詢的時間將顯示在固定部分的副標題中。

如果固定步驟具有顯式設置的時間範圍（不使用時間範圍參數），則無論儀表板的設置如何，該時間範圍始終用於儀表板。 固定部件的副標題不會顯示儀表板的時間範圍，並且查詢不會在儀表板上自動刷新。 字幕將顯示上次執行查詢的時間。

> [!NOTE]
> 固定到儀表板時，當前不支援使用*合併*資料來源的查詢。

## <a name="sharing-workbook-templates"></a>共用活頁簿範本

開始創建自己的活頁簿範本後，您可能需要與更廣泛的社區共用它。 要瞭解更多資訊，並流覽不屬於預設 Azure 監視器庫視圖的其他範本，請訪問我們的[GitHub 存儲庫](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)。 要流覽現有活頁簿，請訪問 GitHub 上的[活頁簿庫](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks)。

## <a name="next-step"></a>後續步驟

* [開始](workbooks-visualizations.md)瞭解有關活頁簿的許多豐富視覺化選項的詳細資訊。
* [控制和](workbooks-access-control.md)共用對活頁簿資源的存取權限。
