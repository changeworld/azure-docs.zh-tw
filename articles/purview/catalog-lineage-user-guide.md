---
title: '資料目錄歷程使用者指南 (預覽) '
description: 本文提供 Azure 範疇的目錄歷程功能概述。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/29/2020
ms.openlocfilehash: a319dbce2502f35272cf9b70da2022f581d64275
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552169"
---
# <a name="azure-purview-data-catalog-lineage-user-guide"></a>Azure 範疇資料目錄歷程使用者指南

本文概要說明 Azure 範疇資料目錄中的資料歷程功能。

## <a name="background"></a>背景

Azure 範疇的其中一個平臺功能，就是能夠顯示資料處理程式所建立資料集之間的歷程。 Data Factory、Data Share 和 Power BI 之類的系統會在資料移動時捕捉資料歷程。 自訂歷程報告也可透過 REST API 的阿特拉斯勾點來支援。

## <a name="lineage-collection"></a>歷程收集 
 從企業資料系統在 Azure 範疇中收集到的中繼資料會跨拼接，以顯示端對端資料歷程。 將歷程收集到範疇的資料系統廣泛分類為下列三種類型。

### <a name="data-processing-system"></a>資料處理系統
資料整合和 ETL 工具可以在執行時間將歷程推入 Azure 範疇。 Data Factory、Data Share、Synapse、Azure Databricks 等的工具，屬於這類的資料系統。 資料處理系統會將資料集參考為來自不同資料庫的來源，以及用來建立目標資料集的儲存體解決方案。 下表列出目前與範疇整合的資料處理系統清單。


| 資料處理系統 | 支援的範圍 |
| ---------------------- | ------------|
| Azure Data Factory | [複製活動](how-to-link-azure-data-factory.md#data-factory-copy-data-support) <br> [資料流程活動](how-to-link-azure-data-factory.md#data-factory-data-flow-support) <br> [執行 SSIS 套件活動](how-to-link-azure-data-factory.md#data-factory-execute-ssis-package-support) |
| Azure Data Share | [共用快照集](how-to-link-azure-data-share.md) |
 
### <a name="data-storage-systems"></a>資料儲存系統
資料庫 & 儲存體解決方案（例如 SQL Server、Teradata 和 SAP）有查詢引擎可以使用指令碼語言來轉換資料。 從預存程式的資料歷程收集至範疇和拼接，以及來自其他系統的歷程。

| 資料儲存系統 | 支援的範圍 |
| ---------------------- | ------------|
| Teradata | 預存程序

### <a name="data-analytics--reporting-systems"></a>& 報表系統的資料分析
資料系統（例如 Azure ML）和 Power BI 報告歷程至 Azure 範疇。 這些系統會從儲存體系統使用資料集，並透過其中繼模型處理，以建立 BI 儀表板、ML 實驗等等。

| 資料分析 & 報表系統 | 支援的範圍 |
| ---------------------- | ------------|
| Power BI | [& 儀表板的資料集、資料流程、報表](register-scan-power-bi-tenant.md)

## <a name="get-started-with-lineage"></a>開始使用歷程

範疇中的歷程包含資料集和處理常式。 資料集也稱為節點，而進程也可以稱為邊緣：

* **資料集 (節點)**：提供給處理常式輸入的資料集 (結構化或非結構化) 。 例如，SQL 資料表、Azure blob 和檔案 (（例如 .csv 和 .xml) ）全都被視為資料集。 在範疇的 [歷程] 區段中，資料集會以矩形方塊表示。

* **進程 (Edge)**：在資料集上執行的活動或轉換稱為「處理常式」。 例如，ADF 複製活動、Data Share 快照集等等。 在範疇的 [歷程] 區段中，處理常式是以四捨五入方塊表示。

若要存取範疇中資產的歷程資訊，請遵循下列步驟：

1. 在 Azure 入口網站中，移至 [ [Azure 範疇帳戶] 頁面](https://aka.ms/purviewportal)。

1. 從清單中選取您的 Azure 範疇帳戶，然後從 [**總覽**] 頁面中選取 [**啟動範疇帳戶**]。

1. 在 Azure 範疇 **首頁** 上，搜尋資料集名稱或處理常式名稱，例如 ADF 複製或資料流程活動。 然後按 Enter 鍵。

1. 從搜尋結果中選取資產，然後 **選取其 [** 歷程] 索引標籤。

   :::image type="content" source="./media/catalog-lineage-user-guide/select-lineage-from-asset.png" alt-text="顯示如何選取 [歷程] 索引標籤的螢幕擷取畫面。" border="true":::

## <a name="asset-level-lineage"></a>資產層級歷程

Azure 範疇支援資料集和進程的資產層級歷程。 若要查看資產層級歷程，請移至目錄中目前 **資產的 [歷程] 索引** 標籤。 選取目前的資料集資產節點。 依預設，屬於資料的資料行清單會出現在左窗格中。

   :::image type="content" source="./media/catalog-lineage-user-guide/view-columns-from-lineage.png" alt-text="顯示如何在 [歷程] 頁面中選取 [View columns] 的螢幕擷取畫面" border="true":::

## <a name="column-level-lineage"></a>資料行層級歷程

Azure 範疇支援資料集的資料行層級歷程。 若要查看資料行層級歷程，請移至目錄中目前 **資產的 [歷程] 索引** 標籤，並遵循下列步驟：

1. 當您在 [歷程] 索引標籤中，在左窗格中，選取您要在資料歷程中顯示之每個資料行旁的核取方塊。

   :::image type="content" source="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png" alt-text="螢幕擷取畫面，顯示如何選取要在歷程頁面中顯示的資料行。" lightbox="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png":::

1. 將滑鼠停留在左窗格的選定資料行上，或在歷程畫布的資料集中，以查看資料行對應。 所有資料行實例都會反白顯示。

   :::image type="content" source="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png" alt-text="顯示如何將滑鼠停留在資料行名稱上，以反白顯示資料歷程路徑中之資料行流程的螢幕擷取畫面。" lightbox="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png":::

1. 如果資料行數目大於左窗格中顯示的數目，請使用篩選選項依名稱選取特定的資料行。 或者，您也可以使用滑鼠在清單中滾動。

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-columns-by-name.png" alt-text="螢幕擷取畫面，顯示如何在 [歷程] 頁面上依資料行名稱篩選資料行。" lightbox="./media/catalog-lineage-user-guide/filter-columns-by-name.png":::

1. 如果歷程畫布包含更多節點和邊緣，請使用篩選來依名稱選取資料資產或處理節點。 或者，您也可以使用滑鼠來移動歷程視窗。

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-assets-by-name.png" alt-text="顯示 [歷程] 頁面上依名稱顯示資料資產節點的螢幕擷取畫面。" lightbox="./media/catalog-lineage-user-guide/filter-assets-by-name.png":::

1. 使用左窗格中的切換來反白顯示歷程畫布中的資料集清單。 如果您關閉切換，則會顯示至少包含其中一個所選資料行的任何資產。 如果您開啟切換，則只會顯示包含所有資料行的資料集。

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png" alt-text="螢幕擷取畫面，顯示如何使用切換來篩選歷程頁面上的節點清單。" lightbox="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png":::

1. 選取 [ **切換到** 任何資產上的資產]，從歷程視圖中查看其對應的中繼資料。 這麼做是從歷程視圖流覽至目錄中另一個資產的有效方式。

   :::image type="content" source="./media/catalog-lineage-user-guide/select-switch-to-asset.png" alt-text="螢幕擷取畫面：如何選取歷程資料資產中的 [切換至資產]。" lightbox="./media/catalog-lineage-user-guide/select-switch-to-asset.png":::

1. 歷程畫布可能會變得很複雜，適用于熱門的資料集。 為了避免雜亂，預設視圖只會顯示五個焦點的資產歷程。 您可以按一下 [歷程] 畫布中的 [反升] 來展開歷程的其餘部分。 資料取用者也可以隱藏畫布中不感興趣的資產。 若要進一步減少雜亂的情況，請關閉 [歷程] 畫布頂端的 [切換 **更多** 歷程]。 此動作會隱藏曆程畫布中的所有氣泡。

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png" alt-text="顯示如何切換更多歷程的螢幕擷取畫面。" lightbox="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png":::

1. 使用歷程畫布中的智慧型按鈕，取得歷程的最佳觀點。 [自動設定]、[縮放至適當比例]、[放大/縮小]、[全螢幕] 和 [導覽對應] 可供目錄中的沉浸式歷程體驗使用。

   :::image type="content" source="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png" alt-text="顯示如何選取歷程智慧按鈕的螢幕擷取畫面。" lightbox="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png":::

## <a name="next-steps"></a>後續步驟

* [歷程的 Azure Data Factory 連結](how-to-link-azure-data-factory.md)
* [連結至 Azure Data Share 進行歷程](how-to-link-azure-data-share.md)