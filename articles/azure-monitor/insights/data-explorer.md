---
title: 適用于 Azure 資料總管 (preview 的 Azure 監視器) |Microsoft Docs
description: 本文說明 Azure 資料總管叢集的 Azure 監視器見解。
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 6f25ff02dee16812898d77d0e801f927b354dc78
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918129"
---
# <a name="azure-monitor-for-azure-data-explorer-preview"></a>適用于 Azure 資料總管 (preview 的 Azure 監視器) 

適用于 Azure 的 Azure 監視器資料總管 (preview) 提供叢集效能、作業、使用方式和失敗的統一觀點，以提供叢集的全面監視。
本文將協助您瞭解如何在 Azure 資料總管 (preview) 上架和使用 Azure 監視器。

## <a name="introduction-to-azure-monitor-for-azure-data-explorer-preview"></a>Azure 資料總管 (preview 的 Azure 監視器簡介) 

開始處理體驗之前，您應先了解其呈現和視覺化資訊的方式。
-    **大規模觀點** 顯示叢集主要計量的快照集，以輕鬆追蹤查詢、內嵌和匯出作業的效能。
-   向下切入至特定 Azure 資料總管叢集的 **深入分析**，以協助執行詳細的分析。
-    可 **自訂**，您可以在其中變更要查看的計量、修改或設定符合限制的閾值，以及儲存您自己的自訂活頁簿。 活頁簿中的圖表可釘選到 Azure 儀表板。

## <a name="view-from-azure-monitor-at-scale-perspective"></a>大規模查看 Azure 監視器 () 

您可以從 Azure 監視器查看叢集的主要效能計量，包括從訂用帳戶中的多個叢集查詢、內嵌和匯出作業的度量，以及協助找出效能問題。

若要在您的所有訂用帳戶中查看叢集的效能，請執行下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com/)

2. 從 Azure 入口網站的左側窗格中選取 [ **監視** ]，然後在 [見解中樞] 區段下，選取 [ **Azure 資料總管叢集 (預覽])**。

![概觀體驗的螢幕擷取畫面，其中包含多個圖表](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>Overview (概觀) 索引標籤

在所選訂用帳戶的 [ **總覽** ] 索引標籤上，資料表會顯示訂用帳戶內分組的 Azure 資料總管叢集的互動式計量。 您可以根據您從下列下拉式清單中選取的選項，來篩選結果：

* 訂用帳戶–只會列出具有 Azure 資料總管叢集的訂用帳戶。

* Azure 資料總管叢集–預設只會預先選取最多五個叢集。 如果您選取範圍選取器中的所有或多個叢集，則會傳回最多200的叢集。

* 時間範圍 – 依預設會根據對應的選取項目顯示過去 24 小時的資訊。

下拉式清單下的計數器磚會匯總所選訂用帳戶中的 Azure 資料總管叢集總數，並反映選取的數目。 這些資料行有條件式色彩 codings：保持運作、CPU、內嵌使用率和快取使用率。 橙色編碼的儲存格具有對叢集而言不具持續性的值。 

若要進一步瞭解每個度量的意義，建議您閱讀 [Azure 資料總管計量](https://docs.microsoft.com/azure/data-explorer/using-metrics#cluster-metrics)的相關檔。

### <a name="query-performance-tab"></a>[查詢效能] 索引標籤

此索引標籤會顯示查詢持續時間、並行查詢總數，以及節流查詢總數。

![[查詢效能] 索引標籤的螢幕擷取畫面](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>[內嵌效能] 索引標籤

此索引標籤會顯示事件/IoT 中樞的內嵌延遲、成功的內嵌結果、失敗的內嵌結果、內嵌磁片區，以及已處理的事件。

[![[內嵌效能] 索引標籤的螢幕擷取畫面](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>串流內嵌效能索引標籤

此索引標籤提供平均資料速率、平均持續時間和要求率的相關資訊。

### <a name="export-performance-tab"></a>[匯出效能] 索引標籤

此索引標籤提供有關連續匯出作業的已匯出記錄、延遲、暫止計數和使用率百分比的資訊。

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>從 Azure 資料總管叢集資源查看 (向下切入分析) 

若要直接從 Azure 資料總管叢集中存取 Azure 資料總管叢集的 Azure 監視器：

1. 在 Azure 入口網站中，選取 [ **Azure 資料總管** 叢集]。

2. 從清單中選擇 Azure 資料總管叢集。 在 [監視] 區段中，選擇 [ **Insights (預覽])**。

您也可以從 Azure 監視器 insights 視圖中選取 Azure 資料總管叢集的資源名稱，以存取這些視圖。

適用于 Azure 資料總管的 Azure 監視器結合了記錄和度量，以提供全域監視解決方案。 包含以記錄為基礎的視覺效果需要使用者 [啟用其 Azure 資料總管叢集的診斷記錄，並將其傳送至 Log Analytics 工作區](https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs)。 應啟用的診斷記錄如下： **Command**、 **Query**、 **TableDetails** 和 **TableUsageStatistics**。

![藍色按鈕的螢幕擷取畫面，其中顯示「啟用記錄以供監視」文字](./media/data-explorer/enable-logs.png)


 [ **總覽** ] 索引標籤會顯示：

- 計量圖格會反白顯示叢集的可用性和整體狀態，以快速評估其健康情況。

- 主動式 [Advisor 建議](https://docs.microsoft.com/azure/data-explorer/azure-advisor) 和 [資源健康](https://docs.microsoft.com/azure/data-explorer/monitor-with-resource-health) 狀態的摘要。

- 顯示最高 CPU 和記憶體取用者的圖表，以及一段時間內的唯一使用者數目。


[![從 Azure 資料總管叢集資源查看的螢幕擷取畫面](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

[ **關鍵計量** ] 索引標籤會顯示叢集的一些計量（依下列方式分組）的統一觀點：一般度量、查詢相關、內嵌相關的計量，以及串流內嵌相關的計量。

[![失敗檢視的螢幕擷取畫面](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

[ **使用** 方式] 索引標籤可讓使用者深入瞭解叢集的命令和查詢效能。 在此頁面上，您可以：
 
 - 查看哪些使用者和應用程式傳送最多的查詢，或取用最多的 CPU 和記憶體 (因此，您可以瞭解哪些使用者正在提交最高的查詢，以供叢集處理) 。
 - 依失敗的查詢找出排名最高的使用者和應用程式。
 - 依使用者和應用程式，找出過去16天) 的過去16天內每日平均 (的查詢數目的最近變更。
 - 依使用者、應用程式和命令類型，識別查詢、記憶體和 CPU 耗用量數目的趨勢和尖峰。

[![工作檢視的螢幕擷取畫面，其中包含依命令和查詢計數排列的排名圖、依命令和查詢計數排列排名最高的主體，以及命令類型的最上層命令](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![工作檢視的螢幕擷取畫面，其中包含依應用程式排列的查詢計數、依應用程式的總記憶體，以及應用程式的 CPU 總計](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

[ **資料表** ] 索引標籤會顯示叢集中資料表的最新和歷程記錄屬性。 您可以查看哪些資料表耗用最多空間、依資料表大小追蹤成長歷程記錄、經常性存取資料，以及一段時間內的資料列數目。

[ **快** 取] 索引標籤可讓使用者分析其實際查詢的查詢模式，並將其與針對每個資料表) 所設定的快取原則 (進行比較。 您可以識別最多查詢和資料表所使用的資料表（完全不會查詢），並據此調整快取原則。 您可能會在 Azure Advisor 的特定資料表上取得特定的快取原則建議， (快取建議僅適用于 [主要 Azure Advisor 儀表板](https://docs.microsoft.com/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)) ，根據過去30天內的實際查詢查閱，以及至少95% 的查詢的未優化快取原則。 Azure Advisor 中的快取縮減建議適用于「以資料系結」的叢集 (這表示叢集具有低 CPU 和低內嵌使用率，但由於資料容量很高，所以叢集無法相應縮小或向下調整) 。

[![快取詳細資料的螢幕擷取畫面](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

## <a name="pin-to-azure-dashboard"></a>釘選到 Azure 儀表板

您可以選取區段右上角的圖釘圖示，將「大規模」) 觀點的任何一個度量區段 (釘選到 Azure 儀表板。

![已選取釘選圖示的螢幕擷取畫面](./media/data-explorer/pin.png)

## <a name="customize-azure-monitor-for-azure-data-explorer-cluster"></a>自訂 Azure 資料總管叢集的 Azure 監視器

本節主要說明編輯自訂活頁簿以支援資料分析需求的常見案例：
* 將活頁簿的範圍設為一律選取特定訂用帳戶或 Azure 資料總管叢集 (s) 
* 變更方格中的計量
* 變更臨界值或色彩轉譯/編碼

您可以選取頂端工具列中的 [自訂] 按鈕以啟用編輯模式，並開始進行自訂。

![自訂按鈕的螢幕擷取畫面](./media/data-explorer/customize.png)

自訂項目會儲存至自訂活頁簿，以避免覆寫已發佈活頁簿中的預設設定。 活頁簿會儲存在資源群組內，保存在您私人使用的 [我的報表] 區段中，或是可供每個具有資源群組存取權的人員存取的 [共用報表] 區段中。 儲存自訂活頁簿之後，您必須移至活頁簿資源庫加以啟動。

![活頁簿資源庫的螢幕擷取畫面](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>疑難排解

如需一般疑難排解指引，請參閱專用的以活頁簿為基礎的深入解析 [疑難排解文章](troubleshoot-workbooks.md)。

本節將協助您進行診斷，並針對 Azure 監視器使用 Azure 資料總管叢集 (preview) 時可能遇到的一些常見問題進行疑難排解。 請使用下列清單，找到與您的特定問題相關的資訊。

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>為什麼我在訂用帳戶選擇器中看不到我所有的訂用帳戶？

我們只會顯示訂用帳戶，其中包含從選取的訂用帳戶篩選器中選擇的 Azure 資料總管叢集，這些篩選準則是在 Azure 入口網站標頭中的 [目錄 + 訂用帳戶] 選取。

![訂用帳戶篩選器的螢幕擷取畫面](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>為什麼我在 [使用量]、[資料表] 或 [快取] 區段底下看不到 Azure 資料總管叢集的任何資料？

若要查看以記錄為基礎的資料，您必須為每個想要監視的 Azure 資料總管叢集 [啟用診斷記錄](https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) 。 這可在每個叢集的診斷設定下完成。 您必須將資料傳送至 Log Analytics 工作區。 應啟用的診斷記錄如下： Command、Query、TableDetails 和 TableUsageStatistics。

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>我已為 Azure 資料總管叢集啟用記錄，為什麼仍無法在命令和查詢下查看我的資料？

診斷記錄目前無法追溯運作，因此只有在您的 Azure 資料總管採取動作之後，資料才會開始出現。 因此，視您的 Azure 資料總管叢集的使用方式而定，可能需要一些時間，範圍從數小時到一天。


## <a name="next-steps"></a>後續步驟

檢閱[使用 Azure 監視器活頁簿建立互動式報表](../platform/workbooks-overview.md)，以了解設計活頁簿以提供支援的案例、如何撰寫新報表和自訂現有報表，以及其他資訊。
