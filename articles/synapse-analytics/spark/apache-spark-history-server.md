---
title: 使用擴充的 Spark 歷程記錄伺服器來對應用程式進行偵錯工具-在 Azure Synapse 中 Apache Spark
description: 使用擴充的 Spark 歷程記錄伺服器，在 Azure Synapse 分析中偵測和診斷 Spark 應用程式。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: b4ee5d064d17d7b11305c6c86dc1d29ddccc642e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85194989"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>使用擴充的 Apache Spark 歷程記錄伺服器來對 Apache Spark 的應用程式進行 debug 和診斷

本文提供有關如何使用擴充的 Apache Spark 歷程記錄伺服器來偵測及診斷已完成和執行中 Spark 應用程式的指引。

此延伸模組包含 [資料] 索引標籤、[圖表] 索引標籤和 [診斷] 索引標籤。使用 [**資料**] 索引標籤可檢查 Spark 作業的輸入和輸出資料。 [**圖表**] 索引標籤會顯示作業圖形的資料流程和重新執行。 [**診斷**] 索引標籤會顯示**資料扭曲**、**時間誤差**和**執行程式使用量分析**。

## <a name="access-the-apache-spark-history-server"></a>存取 Apache Spark 歷程記錄伺服器

Apache Spark 歷程記錄伺服器是已完成和執行中 Spark 應用程式的 web 使用者介面。 您可以從 Azure Synapse 分析開啟 Apache Spark 歷程記錄伺服器 web 介面。

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>從 Apache Spark 應用程式節點開啟 Spark 歷程記錄伺服器 web UI

1. 開啟 [Azure Synapse Analytics](https://web.azuresynapse.net/)。

2. 按一下 [**監視**]，然後選取 [ **Apache Spark 應用程式**]。

    ![按一下 [監視]，然後選取 [spark 應用程式]。](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. 選取應用程式，然後按一下以開啟**記錄查詢**。

    ![開啟 [記錄查詢] 視窗。](./media/apache-spark-history-server/open-application-window.png)

4. 選取 [ **spark 歷程記錄伺服器**]，就會顯示 Spark 歷程記錄伺服器 web UI。

    ![開啟 [spark 歷程記錄伺服器]。](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>從資料節點開啟 Spark 歷程記錄伺服器 web UI

1. 從您的 Azure Synapse Studio 筆記本，從 [作業執行] 輸出儲存格或從筆記本檔底部的 [狀態] 面板中選取 [ **Spark 歷程記錄伺服器**]。 選取 [工作階段詳細資料]****。

   ![啟動 Spark 歷程記錄伺服器](./media/apache-spark-history-server/launch-history-server2.png "啟動 Spark 歷程記錄伺服器")

2. 從 [滑出] 面板中選取 [ **Spark 歷程記錄伺服器**]。

   ![啟動 Spark 歷程記錄伺服器](./media/apache-spark-history-server/launch-history-server.png "啟動 Spark 歷程記錄伺服器")

## <a name="explore-the-data-tab-in-spark-history-server"></a>探索 Spark 歷程記錄伺服器中的 [資料] 索引標籤

選取您想要查看之作業的作業識別碼。 然後選取 [工具] 功能表上的 [**資料**] 來取得資料檢視。 本節說明如何在 [資料] 索引標籤中執行各種工作。

* 個別選取索引標籤，以檢查 [輸入]、[輸出] 和 [資料表作業]。

    ![Spark 應用程式索引標籤的資料](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* 選取 [**複製**] 來複製所有資料列。

    ![Spark 應用程式複製的資料](./media/apache-spark-history-server/apache-spark-data-copy.png)

* 選取 [ **csv**]，將所有資料儲存為 csv 檔案。

    ![Spark 應用程式儲存的資料](./media/apache-spark-history-server/apache-spark-data-save.png)

* 在 [欄位**搜尋**] 中輸入關鍵字來進行搜尋。 搜尋結果會立即顯示。

    ![Spark 應用程式搜尋的資料](./media/apache-spark-history-server/apache-spark-data-search.png)

* 選取要排序資料表的資料行標頭，選取加號展開資料列以顯示更多詳細資料，或選取減號來折迭列。

    ![Spark 應用程式資料表的資料](./media/apache-spark-history-server/apache-spark-data-table.png)

* 選取 [**部分下載**] 來下載單一檔案。 選取的檔案會下載到本機。 如果檔案已不存在，則會出現新的索引標籤，並顯示錯誤訊息。

    ![Spark 應用程式下載的資料列](./media/apache-spark-history-server/sparkui-data-download-row.png)

* 若要複製完整路徑或相對路徑，請選取 [**複製完整路徑**] 或 [**複製相對路徑**] 選項（從下拉式功能表中展開）。 針對 Azure Data Lake Storage 的檔案，**在中開啟 Azure 儲存體總管**會啟動 Azure 儲存體總管並在您登入時尋找資料夾。

    ![Spark 應用程式複製路徑的資料](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* 當一頁中有太多資料列要顯示時，請選取資料表下方的頁碼以流覽頁面。

    ![Spark 應用程式的資料頁面](./media/apache-spark-history-server/apache-spark-data-page.png)

* 將滑鼠停留在 [**資料**] 旁的問號，以顯示工具提示，或選取問號以取得詳細資訊。

    ![Spark 應用程式的資料詳細資訊](./media/apache-spark-history-server/sparkui-data-more-info.png)

* 選取 [**提供意見**反應] 以傳送問題的意見反應。

    ![Spark graph 再次提供意見反應給我們](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Apache Spark 歷程記錄伺服器中的 [圖表] 索引標籤

選取您想要查看之作業的作業識別碼。 然後，在工具功能表上選取 [**圖形]** ，以取得作業圖形視圖。

### <a name="overview"></a>總覽

您可以在產生的作業圖形中查看作業的總覽。 根據預設，圖表會顯示所有作業。 您可以依**工作識別碼**篩選此視圖。

![Spark 應用程式和作業圖形作業識別碼](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>顯示

預設會選取 [**進度**] 顯示。 您可以選取 [**顯示**] 下拉式清單中的 [**讀取**] 或 [**寫入**]，以檢查資料流程。

![Spark 應用程式和作業圖形顯示](./media/apache-spark-history-server/sparkui-graph-display.png)

[圖形] 節點會顯示熱度圖圖例中顯示的色彩。

![Spark 應用程式和作業圖表熱度圖](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>播放

若要播放作業，請選取 [**播放**]。 您可以隨時選取 [**停止**] 來停止。 播放時，工作色彩會顯示不同的狀態：

|Color|意義|
|-|-|
|綠色|成功：工作已順利完成。|
|橙色|已重試：失敗但不會影響作業最終結果的工作實例。 這些工作具有稍後可能會成功的重複或重試執行個體。|
|藍色|正在執行：工作正在執行。|
|白色|正在等候或略過：工作正在等候執行，或已略過該階段。|
|紅色|Failed：工作失敗。|

下圖顯示綠色、橙色和藍色狀態色彩。

![Spark 應用程式和作業圖形色彩範例，執行中](./media/apache-spark-history-server/sparkui-graph-color-running.png)

下圖顯示綠色和白色狀態色彩。

![Spark 應用程式和作業圖形色彩範例，略過](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

下圖顯示紅色和綠色的狀態色彩。

![Spark 應用程式和作業圖形色彩範例，失敗](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> 允許播放每個工作。 針對不完整的作業，不支援播放。

### <a name="zoom"></a>Zoom

使用滑鼠滾輪來放大和縮小作業圖形，或選取 [**縮放至適當比例**]，使其符合螢幕大小。

![Spark 應用程式和作業圖形縮放至適當比例](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>工具提示

當工作失敗時，將滑鼠停留在圖形節點上以查看工具提示，並選取階段以開啟其 [階段] 頁面。

![Spark 應用程式和作業圖表工具提示](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

在 [作業圖表] 索引標籤上，如果有符合下列條件的工作，階段就會顯示工具提示和小圖示：

|條件|說明|
|-|-|
|資料扭曲|資料讀取大小 > 此階段中所有工作的平均資料讀取大小 * 2 和資料讀取大小 > 10 MB|
|時間誤差|執行時間 > 此階段中所有工作的平均執行時間 * 2，而執行時間 > 2 分鐘|
   
![Spark 應用程式和作業圖形扭曲圖示](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>圖形節點描述

[作業圖形] 節點會顯示每個階段的下列資訊：

  * 識別碼。
  * 名稱或描述。
  * 總工作數。
  * 讀取的資料：輸入大小和隨機讀取大小的總和。
  * 資料寫入：輸出大小和隨機寫入大小的總和。
  * 執行時間：第一次嘗試的開始時間與最後一次嘗試的完成時間之間的時間。
  * 資料列計數：輸入記錄、輸出記錄、隨機讀取記錄和隨機寫入記錄的總和。
  * 進度。

    > [!NOTE]  
    > 根據預設，[作業圖形] 節點會顯示每個階段的最後一次嘗試的資訊（階段執行時間除外）。 不過，在播放期間，[圖形] 節點會顯示每次嘗試的資訊。
    >  
    > 讀取和寫入的資料大小為 1MB = 1000 KB = 1000 * 1000 個位元組。

### <a name="provide-feedback"></a>提供意見反應

選取 [**提供意見**反應] 以傳送問題的意見反應。

![Spark 應用程式和作業圖形意見反應](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>流覽 Apache Spark 歷程記錄伺服器中的 [診斷] 索引標籤

若要存取 [診斷] 索引標籤，請選取作業識別碼。 然後選取 [工具] 功能表上的 [**診斷**]，以取得 [作業診斷] 視圖。 [診斷] 索引標籤包括 [資料扭曲]、[時間扭曲] 和 [執行程式使用狀況分析]。

透過個別選取索引標籤，檢查 [資料扭曲]、[時間扭曲] 和 [執行程式使用狀況分析]。

![再次 SparkUI 診斷資料扭曲索引標籤](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>資料扭曲

當您選取 [**資料扭曲**] 索引標籤時，會根據指定的參數顯示對應的扭曲工作。

* **指定參數** -第一個區段會顯示用來偵測資料扭曲的參數。 預設規則為：工作資料讀取大於平均工作資料讀取的3倍，且工作資料讀取超過 10 MB。 如果您想要為扭曲的工作定義自己的規則，您可以選擇參數，**扭曲的階段**和**扭曲字元**區段會據此重新整理。

* **扭曲階段** -第二個區段會顯示階段，其中有扭曲的工作符合上面指定的準則。 如果某個階段中有一個以上的扭曲工作，扭曲階段資料表只會顯示最扭曲的工作 (例如，資料扭曲的最大資料)。

    ![sparkui 診斷資料扭曲索引標籤](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **扭曲圖表**–選取 [扭曲階段] 資料表中的資料列時，扭曲圖表會根據讀取和執行時間來顯示更多工具分佈詳細資料。 扭曲的工作會以紅色標示，而一般工作則會以藍色標示。 圖表會顯示最多100個範例工作，而且工作詳細資料會顯示在右下方面板中。

    ![階段10的 sparkui 扭曲圖](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>時間扭曲

[時間扭曲] 索引標籤會根據工作執行時間顯示扭曲的工作。

* **指定參數** - 第一個區段會顯示用來偵測時間扭曲的參數。 偵測時間扭曲的預設準則是：工作執行時間大於平均執行時間的三倍，而工作執行時間大於 30 秒。 您可以根據您的需求變更參數。 [扭曲階段] 和 [扭曲圖表] 會顯示對應的階段和工作資訊，就像上面的 [資料扭曲] 索引標籤一樣。

* 選取 [**時間誤差**]，然後根據 [**指定參數**] 區段中所設定的參數，在 [**扭曲階段**] 區段中顯示篩選的結果。 在 [**扭曲階段**] 區段中選取一個專案，然後對應的圖表會在 [section3] 中繪製，而且工作詳細資料會顯示在右下方面板中。

    ![sparkui 診斷時間誤差區段](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>執行程式使用狀況分析

執行程式使用量圖表會以視覺化方式呈現 Spark 作業執行程式的配置和執行狀態。  

1. 選取 **[執行程式使用狀況分析**]，然後繪製有關執行程式使用方式的四個類型曲線，包括已配置的執行**程式、執行**執行**程式、****閒置**的執行程式，以及**最大**程式 關於配置的執行程式，每個「加入程式」或「執行程式已移除」事件都會增加或減少配置的執行程式。 您可以檢查 [作業] 索引標籤中的 [事件時間軸] 以進行比較。

   ![sparkui 診斷執行 [] 索引標籤](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. 選取 [色彩] 圖示，以選取或取消選取所有草稿中的對應內容。

    ![sparkui 診斷選取圖表](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>已知問題

使用彈性分散式資料集（Rdd）的輸入/輸出資料不會顯示在 [資料] 索引標籤中。

## <a name="next-steps"></a>後續步驟

- [Azure Synapse Analytics](../overview-what-is.md)
- [適用於 Apache Spark 的 .NET 文件](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

