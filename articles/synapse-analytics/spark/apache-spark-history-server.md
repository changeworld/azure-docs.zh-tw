---
title: 使用延伸 Spark 歷史紀錄伺服器除錯應用 - Azure 突觸中的 Apache Spark
description: 使用擴展的 Spark 歷史記錄伺服器在 Azure 同步分析中調試和診斷 Spark 應用程式。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4f03033942517f4778192e0b12f84610df8fd469
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429209"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>使用延伸的 Apache Spark 歷史記錄伺服器來除錯和診斷 Apache Spark 應用程式

本文提供有關如何使用擴展的 Apache Spark 歷史記錄伺服器調試和診斷已完成和運行 Spark 應用程式的指導。

副檔名包括資料選項卡、圖形選項卡和診斷選項卡。使用 **「數據**」選項卡檢查 Spark 作業的輸入和輸出數據。 "**圖形**"選項卡顯示作業圖的數據串流和重播。 「**診斷**」選項卡顯示**資料傾斜**、**時間傾斜**與**執行器使用方式分析**。

## <a name="access-the-apache-spark-history-server"></a>存取阿帕契火花歷史記錄伺服器

Apache Spark 歷史記錄伺服器是已完成和運行Spark應用程式的Web用戶介面。 可以從 Azure 同步分析打開 Apache Spark 歷史記錄伺服器 Web 介面。

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>從 Apache 火花應用程式節點開啟火花歷史記錄伺服器 Web UI

1. 開啟[Azure 同步分析](https://web.azuresynapse.net/)。

2. 點選**螢幕**,然後選擇**阿帕契火花應用程式**。

    ![單擊監視器,然後選擇火花應用程式。](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. 選擇應用程式,然後按下開啟**紀錄查詢**。

    ![打開日誌查詢視窗。](./media/apache-spark-history-server/open-application-window.png)

4. 選擇**Spark 歷史記錄伺服器**,然後將顯示 Spark 歷史記錄伺服器 Web UI。

    ![打開火花歷史記錄伺服器。](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>從資料節點開啟 Spark 歷史記錄伺服器 Web UI

1. 從 Azure Synapse Studio 筆記本中,從工作執行輸出單元或筆記型文件底部的狀態面板中選擇**Spark 歷史紀錄伺服器**。 選取 [工作階段詳細資料]****。

   ![啟動火花歷史記錄伺服器](./media/apache-spark-history-server/launch-history-server2.png "啟動火花歷史記錄伺服器")

2. 從滑出面板中選擇**Spark 歷史紀錄伺服器**。

   ![啟動火花歷史記錄伺服器](./media/apache-spark-history-server/launch-history-server.png "啟動火花歷史記錄伺服器")

## <a name="explore-the-data-tab-in-spark-history-server"></a>瀏覽 Spark 歷史記錄伺服器中的資料選項卡

選擇要查看的作業的作業 ID。 然後選擇工具功能表上**的數據**以獲取數據檢視。 本節介紹如何在"數據"選項卡中執行各種任務。

* 個別選取索引標籤，以檢查 [輸入]****、[輸出]**** 和 [資料表作業]****。

    ![Spark 應用程式選項卡的資料](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* 通過選擇 **「複製**」來複製所有行。

    ![Spark 應用程式複本資料](./media/apache-spark-history-server/apache-spark-data-copy.png)

* 通過選擇**csv**將所有資料儲存為 CSV 檔。

    ![Spark 應用程式儲存的資料](./media/apache-spark-history-server/apache-spark-data-save.png)

* 以欄位搜尋中輸入關鍵字**搜尋**。 搜尋結果立即顯示。

    ![Spark 應用程式搜尋的資料](./media/apache-spark-history-server/apache-spark-data-search.png)

* 選擇要對表進行排序的列標題,選擇加號以展開行以顯示更多詳細資訊,或選擇減號以摺疊一行。

    ![Spark 應用程式表的資料](./media/apache-spark-history-server/apache-spark-data-table.png)

* 通過選擇 **「部分下載」下載**單個檔。 所選檔將下載到本地。 如果該檔不再存在,則會顯示一個帶有錯誤消息的新選項卡。

    ![Spark 應用程式下載列的資料](./media/apache-spark-history-server/sparkui-data-download-row.png)

* 要複製完整路徑或相對路徑,請選擇從下拉菜單展開的 **「複製完整路徑**」或 **「複製相對路徑**」選項。 對於 Azure 資料湖儲存檔案,**在 Azure 儲存資源管理器中打開**將啟動 Azure 儲存資源管理員,並在登錄時找到資料夾。

    ![Spark 應用程式複製路徑的資料](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* 選擇表下方的頁碼,在一個頁面中顯示的行太多時導航頁面。

    ![Spark 應用程式頁資料](./media/apache-spark-history-server/apache-spark-data-page.png)

* 將滑鼠懸停在 **「數據」** 旁邊的問號上以顯示工具提示,或選擇問號以獲取更多資訊。

    ![Spark 應用程式資料更多資訊](./media/apache-spark-history-server/sparkui-data-more-info.png)

* 通過選擇 **「向我們提供反饋**」發送有關問題的反饋。

    ![火花圖再次向我們提供回饋](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>阿帕奇火花歷史伺服器中的圖形選項卡

選擇要查看的作業的作業 ID。 然後,在工具功能表上選擇 **「圖形**」以獲取作業圖形檢視。

### <a name="overview"></a>概觀

您可以在生成的作業圖中查看作業的概述。 預設情況下,圖形顯示所有作業。 您可以按**作業 ID**篩選此檢視。

![火花應用程式與工作圖工作 ID](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>顯示器

預設情況下,選擇 **「進度」** 顯示。 您可以透過在 **「顯示」** 下拉清單中選擇 **「讀取**」或 **「寫入」** 來檢查資料流。

![火花應用程式與工作圖顯示](./media/apache-spark-history-server/sparkui-graph-display.png)

圖形節點顯示熱圖圖例中顯示的顏色。

![火花應用和作業圖熱圖](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>播放

要播放作業,請選擇 **"播放**"。 您可以隨時選擇 **"停止**」。。 彈時的工作顏色顯示不同的狀態:

|Color|意義|
|-|-|
|綠色|成功:作業已成功完成。|
|橙色|重試:失敗但不會影響作業最終結果的任務實例。 這些工作具有稍後可能會成功的重複或重試執行個體。|
|藍色|正在運行:任務正在運行。|
|白色|等待或跳過:任務正在等待運行,或者階段已跳過。|
|紅色|失敗:任務失敗。|

下圖顯示了綠色、橙色和藍色狀態顏色。

![火花應用程式和作業圖顏色範例,執行](./media/apache-spark-history-server/sparkui-graph-color-running.png)

下圖顯示了綠色和白色狀態顏色。

![火花應用程式和作業圖顏色範例,跳過](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

下圖顯示了紅色和綠色狀態顏色。

![火花應用程式和作業圖顏色範例,失敗](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> 允許播放每個工作。 對於未完成的作業,不支援播放。

### <a name="zoom"></a>Zoom

使用滑鼠滾動在作業圖上放大和縮小,或選擇 **「縮放」** 以使其適合螢幕。

![火花應用程式與工作圖縮放適合](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>工具提示

將滑鼠懸停在圖形節點上,以便查看任務失敗時的工具提示,並選擇一個階段以打開其舞臺頁。

![程式應用程式與工作圖工具提示](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

在作業圖選項卡上,如果階段的任務滿足以下條件,則顯示工具提示和一個小圖示:

|條件|描述|
|-|-|
|資料位斜|資料讀取大小>此階段內所有任務的平均資料讀取大小 = 2 和資料讀取大小> 10 MB|
|時間斜斜|執行時間>此階段內所有任務的平均執行時間 = 2,執行時間> 2 分鐘|
   
![火花應用程式與工作圖示圖示](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>圖像節點描述

工作圖節點顯示每個階段的以下資訊:

  * 識別碼。
  * 名稱或描述。
  * 總工作數。
  * 讀取的資料：輸入大小和隨機讀取大小的總和。
  * 數據寫入:輸出大小和隨機寫入大小的總和。
  * 執行時間：第一次嘗試的開始時間與最後一次嘗試的完成時間之間的時間。
  * 資料列計數：輸入記錄、輸出記錄、隨機讀取記錄和隨機寫入記錄的總和。
  * 進度。

    > [!NOTE]  
    > 默認情況下,作業圖節點顯示每個階段最後一次嘗試的資訊(階段執行時間除外)。 但是,在播放過程中,圖形節點顯示每次嘗試的資訊。
    >  
    > 讀寫的資料大小為 1MB = 1000 KB = 1000 = 1000 位元組。

### <a name="provide-feedback"></a>提供意見反應

通過選擇 **「向我們提供反饋**」發送有關問題的反饋。

![火花應用程式和工作圖回饋](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>瀏覽 Apache Spark 歷史記錄伺服器中的診斷選項卡

要造訪"診斷"選項卡,請選擇作業ID。 然後在工具選單上選擇 **「診斷」** 以獲取作業診斷檢視。 [診斷] 索引標籤包括 [資料扭曲]****、[時間扭曲]**** 和 [執行程式使用狀況分析]****。

透過個別選取索引標籤，檢查 [資料扭曲]****、[時間扭曲]**** 和 [執行程式使用狀況分析]****。

![SparkUI 診斷資料再次傾斜選項卡](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>資料扭曲

選擇 **「資料傾斜」** 選項卡時,將根據指定的參數顯示相應的傾斜任務。

* **指定參數** -第一個區段會顯示用來偵測資料扭曲的參數。 默認規則是:任務數據讀取大於平均任務數據讀取的三倍,並且任務數據讀取超過 10 MB。 如果要為傾斜任務定義自己的規則,可以選擇參數,將相應地刷新**傾斜階段**和**傾斜字元**部分。

* **扭曲階段** -第二個區段會顯示階段，其中有扭曲的工作符合上面指定的準則。 如果某個階段中有一個以上的扭曲工作，扭曲階段資料表只會顯示最扭曲的工作 (例如，資料扭曲的最大資料)。

    ![sparkui 診斷資料偏斜選項卡](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **傾斜圖表**– 當選擇傾斜階段表中的行時,偏斜圖根據數據讀取和執行時間顯示更多任務分佈詳細資訊。 扭曲的工作會以紅色標示，而一般工作則會以藍色標示。 圖表最多顯示 100 個範例任務,任務詳細信息顯示在右底部面板中。

    ![階段 10 的火花偏斜圖](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>時間扭曲

[時間扭曲]**** 索引標籤會根據工作執行時間顯示扭曲的工作。

* **指定參數**- 第一部分顯示用於檢測時間偏差的參數。 偵測時間扭曲的預設準則是：工作執行時間大於平均執行時間的三倍，而工作執行時間大於 30 秒。 您可以根據您的需求變更參數。 [扭曲階段] **** 和 [扭曲圖表]**** 會顯示對應的階段和工作資訊，就像上面的 [資料扭曲]**** 索引標籤一樣。

* 選擇**時間偏斜**,然後根據指定**參數**部分設定的參數,在**偏斜階段**部分顯示篩選結果。 在**傾斜階段**部分中選擇一個項目,然後在第 3 節中起草相應的圖表,任務詳細信息顯示在右底部面板中。

    ![火花診斷時間偏斜部份](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>執行程式使用狀況分析

執行器使用圖可視化 Spark 作業執行器的分配和運行狀態。  

1. 選擇**執行器使用情況分析**,然後起草有關執行器用法的四種類型曲線,包括**已分配執行器**,**正在執行的執行器**、**閒置執行器**與**最大執行器實例**。 對於已分配執行器,每個「已添加執行器」或「已刪除執行器」事件都會增加或減少已分配執行器。 您可以檢查 [作業] 索引標籤中的 [事件時間軸] 以進行比較。

   ![sparkui 診斷執行器選項卡](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. 選擇顏色圖示以選擇或取消選擇所有草稿中的相應內容。

    ![sparkui 診斷選擇圖表](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>已知問題

使用彈性分散式資料集 (RDD) 的輸入/輸出數據不會顯示在數據選項卡中。

## <a name="next-steps"></a>後續步驟

- [Azure Synapse Analytics](../overview-what-is.md)
- [阿帕奇火花文檔的 .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

