---
title: 使用 Apache Spark 歷程記錄伺服器中的擴充功能來對應用程式進行偵錯工具-Azure HDInsight
description: 使用 Apache Spark 歷程記錄伺服器中的擴充功能，來對 Spark 應用程式進行 debug 錯和診斷-Azure HDInsight。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 1d53d9e9ee2a7fa6588ea0993b3bebdb2a287351
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941044"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>使用 Apache Spark 歷程記錄伺服器的擴充功能，來對 Spark 應用程式進行偵錯工具和診斷

本文說明如何使用 Apache Spark 歷程記錄伺服器的擴充功能，來對已完成或正在執行的 Spark 應用程式進行偵錯工具和診斷。 此延伸模組包含 [ **資料** ] 索引標籤、[ **圖形** ] 索引標籤和 [ **診斷** ] 索引標籤。在 [ **資料** ] 索引標籤中，您可以檢查 Spark 作業的輸入和輸出資料。 在 [ **圖形** ] 索引標籤上，您可以檢查資料流程，並重新執行作業圖表。 在 [ **診斷** ] 索引標籤上，您可以參考 **資料扭曲**、 **時間誤差** 和 **執行程式使用量分析** 功能。

## <a name="get-access-to-the-spark-history-server"></a>取得 Spark 歷程記錄伺服器的存取權

Spark 歷程記錄伺服器是已完成和執行中 Spark 應用程式的 web UI。 您可以從 Azure 入口網站或從 URL 來開啟它。

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>從 Azure 入口網站開啟 Spark 歷程記錄伺服器 web UI

1. 從 [Azure 入口網站](https://portal.azure.com/)，開啟 Spark 叢集。 如需詳細資訊，請參閱[列出和顯示叢集](../hdinsight-administer-use-portal-linux.md#showClusters)。
2. 從叢集 **儀表板** 選取 [  **Spark 歷程記錄伺服器**]。 出現提示時，輸入 Spark 叢集的系統管理員認證。

    ![從 Azure 入口網站啟動 Spark 歷程記錄伺服器。](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Spark 歷程記錄伺服器")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>依 URL 開啟 Spark 歷程記錄伺服器 web UI

流覽至以開啟 Spark 歷程記錄伺服器 `https://CLUSTERNAME.azurehdinsight.net/sparkhistory` ，其中 **CLUSTERNAME** 是您 Spark 叢集的名稱。

Spark 歷程記錄伺服器 web UI 看起來可能類似下圖：

![[Spark 歷程記錄伺服器] 頁面。](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>使用 Spark 歷程記錄伺服器中的 [資料] 索引標籤

選取作業識別碼，然後選取工具功能表上的 [ **資料** ]，以查看資料檢視。

+ 選取個別的索引標籤，以檢查 **輸入**、 **輸出** 和 **資料表作業** 。

    ![Spark 應用程式的資料頁面上的資料索引標籤。](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ 選取 [ **複製** ] 按鈕來複製所有資料列。

    ![在 Spark 應用程式頁面上複製資料。](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ 將所有資料儲存為。CSV 檔案，方法是選取 **csv** 按鈕。

    ![將資料儲存為。來自 Spark 應用程式的資料頁面的 CSV 檔案。](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ 在 [ **搜尋** ] 欄位中輸入關鍵字來搜尋資料。 搜尋結果會立即顯示。

    ![在 Spark 應用程式的資料頁面上搜尋資料。](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ 選取資料行標題來排序資料表。 選取加號展開資料列，以顯示更多詳細資料。 選取減號以折迭資料列。

    ![Spark 應用程式的資料頁面上的資料表。](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ 選取右側的 [ **部分下載** ] 按鈕以下載單一檔案。 選取的檔案會在本機下載。 如果檔案不再存在，這將會開啟新的索引標籤以顯示錯誤訊息。

    ![Spark 應用程式的資料頁面上的資料下載資料列。](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ 選取 [ **複製完整路徑** ] 或 [ **複製相對路徑** ] 選項，以複製完整路徑或相對路徑，這是從 [下載] 功能表展開的選項。 針對 Azure Data Lake Storage 檔案，請選取 [ **在 Azure 儲存體總管開啟** ] 以啟動 Azure 儲存體總管並在登入後找出資料夾。

    ![在 [Spark 應用程式的資料] 頁面上複製 [完整路徑] 和 [複製相對路徑] 選項。](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ 如果有太多資料列要顯示在單一頁面上，請選取資料表底部的頁碼以進行導覽。

    ![Spark 應用程式的資料頁面的頁碼。](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ 如需詳細資訊，請將滑鼠游標移到 [ **Spark 應用程式的資料** ] 旁的問號，以顯示工具提示。

    ![從 Spark 應用程式的資料頁面取得詳細資訊。](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  若要傳送有關問題的意見反應，請選取 [ **提供意見** 反應]。

    ![從 Spark 應用程式頁面的資料提供意見反應。](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>使用 Spark 歷程記錄伺服器中的 [圖形] 索引標籤

+ 選取作業識別碼，然後選取工具功能表上的 [ **圖形]** ，以查看作業圖表。 根據預設，圖表會顯示所有的工作。 使用 [ **作業識別碼** ] 下拉式功能表來篩選結果。

    ![Spark 應用程式 & 作業圖形頁面上的 [作業識別碼] 下拉式功能表。](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ 預設會選取 **進度**。 選取 [**顯示**] 下拉式功能表中的 [**讀取**] 或 [**寫入**]，以檢查資料流程。

    ![檢查 Spark 應用程式 & 作業圖形頁面上的資料流程。](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ 每個工作的背景色彩都會對應到熱度圖。

   ![Spark 應用程式 & 作業圖形頁面上的熱度圖。](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |Color |描述 |
    |---|---|
    |綠色|作業已成功完成。|
    |橙色|工作失敗，但這並不會影響作業的最終結果。 這些工作有稍後可能會成功的重複或重試實例。|
    |藍色|工作正在執行。|
    |白色|工作正在等候執行，或已略過該階段。|
    |紅色|工作失敗。|

     ![在 Spark 應用程式上執行工作 & 作業圖形頁面。](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     略過的階段會以白色顯示。
    ![在 Spark 應用程式 & 作業圖形頁面上略過的工作。](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Spark 應用程式 & 作業圖形頁面上有失敗的工作。](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > 已完成的工作可使用播放。 選取 [ **播放** ] 按鈕以播放工作。 選取 [停止] 按鈕，隨時停止作業。 播放工作時，每個工作都會依色彩顯示其狀態。 未完成的作業不支援播放。

+ 在作業圖表上滾動至放大或縮小，或選取 [ **縮放至適當比例** ]，使其符合螢幕。

    ![選取 [縮放] 以符合 Spark 應用程式 & 作業圖形頁面。](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ 當工作失敗時，將滑鼠停留在圖形節點上方以查看工具提示，然後選取要在新頁面中開啟的階段。

    ![在 Spark 應用程式 & 作業圖形頁面上查看工具提示。](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ 在 Spark 應用程式 & 作業圖形] 頁面上，如果工作符合下列條件，階段會顯示工具提示和小圖示：
  + 資料扭曲：資料讀取大小 > 此階段中所有工作的平均資料讀取大小 * 2 *和* 資料讀取大小 > 10 MB。
  + 時間誤差：執行時間 > 這個階段中所有工作的平均執行時間 * 2 *，而* 執行時間 > 2 分鐘。

    ![Spark 應用程式 & 作業圖形頁面上的扭曲工作圖示。](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ 作業圖形節點會顯示每個階段的下列資訊：
  + 識別碼
  + 名稱或描述
  + 工作數總計
  + 讀取的資料：輸入大小和隨機讀取大小的總和
  + 資料寫入：輸出大小和隨機寫入大小的總和
  + 執行時間：第一次嘗試的開始時間與上次嘗試的完成時間之間的時間
  + 資料列計數：輸入記錄的總和、輸出記錄、隨機讀取記錄，以及隨機寫入記錄
  + 進度

    > [!NOTE]  
    > 依預設，[作業圖表] 節點會顯示每個階段上一次嘗試的資訊 (但階段執行時間) 除外。 但在播放期間，作業圖形節點會顯示每次嘗試的相關資訊。

    > [!NOTE]  
    > 針對資料讀取和資料寫入大小，我們使用 1MB = 1000 KB = 1000 * 1000 個位元組。

+ 選取 [ **提供意見** 反應] 來傳送有關問題的意見反應。

    ![Spark 應用程式 & 作業圖形頁面上的 [意見] 選項。](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>使用 Spark 歷程記錄伺服器中的 [診斷] 索引標籤

選取作業識別碼，然後選取工具功能表上的 [ **診斷** ]，以查看作業診斷視圖。 [ **診斷** ] 索引標籤包含 **資料扭曲**、 **時間誤差** 和 **執行程式使用量分析**。

+ 分別選取索引標籤，以查看 **資料扭曲**、 **時間誤差** 和 **執行程式使用量分析** 。

    ![[診斷] 索引標籤中的 [資料扭曲] 索引標籤。](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>資料扭曲

選取 [ **資料扭曲** ] 索引標籤。對應的扭曲工作會根據指定的參數顯示。

#### <a name="specify-parameters"></a>指定參數

[ **指定參數** ] 區段會顯示用來偵測資料扭曲的參數。 預設規則為：工作資料讀取大於平均工作資料讀取的三倍，且工作資料讀取超過 10 MB。 如果您想要為扭曲的工作定義自己的規則，您可以選擇您的參數。 **扭曲階段** 和 **扭曲圖表** 區段將會據以更新。

#### <a name="skewed-stage"></a>扭曲階段

**扭曲階段** 區段會顯示具有扭曲工作符合指定準則的階段。 如果階段中有一個以上的扭曲工作， **扭曲階段** 區段只會顯示最扭曲的工作 (也就是資料扭曲的最大資料) 。

![[診斷] 索引標籤中的 [資料扭曲] 索引標籤更大的觀點。](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>扭曲圖表

當您在 [ **扭曲階段** ] 資料表中選取資料列時， **扭曲圖表** 會根據資料讀取和執行時間顯示更多工分佈詳細資料。 扭曲的工作會以紅色標示，而一般工作則會以藍色標示。 基於效能考慮，圖表最多會顯示100個範例工作。 工作詳細資料會顯示在右下角面板中。

![Spark UI 中階段10的扭曲圖表。](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>時間扭曲

[時間扭曲] 索引標籤會根據工作執行時間顯示扭曲的工作。

#### <a name="specify-parameters"></a>指定參數

[ **指定參數** ] 區段會顯示用來偵測時間扭曲的參數。 預設規則是：工作執行時間大於平均執行時間的三倍，而工作執行時間大於30秒。 您可以根據您的需求變更參數。 **扭曲階段** 和 **扭曲圖表** 會顯示對應的階段和工作資訊，就像在 [**資料扭曲**] 索引標籤中一樣。

當您選取 [**時間誤差**] 時，根據 [**指定參數**] 區段中設定的參數，篩選的結果會顯示在 [**扭曲階段**] 區段中。 當您在 [ **扭曲階段** ] 區段中選取一個專案時，對應的圖表會在第三個區段中繪製，而工作詳細資料則會顯示在右下角面板中。

![[診斷] 索引標籤內的 [時間扭曲] 索引標籤。](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>執行程式流量分析圖表

**執行程式使用圖表** 會顯示作業的實際執行程式配置和執行狀態。  

當您選取 **[執行程式使用狀況分析**] 時，會繪製四個關於執行程式使用方式的不同曲線：已 **配置的執行****程式、執行** 的執行程式、**閒置** 執行程式和 **最大** 新增的每個執行程式或 **執行程式移除** 的事件將會增加或減少已配置的 **執行** 程式。 您可以在 [**工作**] 索引標籤中檢查 **事件時間軸**，以取得更多比較。

![[診斷] 索引標籤中的 [執行程式使用量分析] 索引標籤](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

選取 [色彩] 圖示，以選取或取消選取所有草稿中的對應內容。

 ![在 [執行程式使用狀況分析] 索引標籤中選取圖表。](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>常見問題集

### <a name="how-do-i-revert-to-the-community-version"></a>如何? 還原為該社區版本？

若要還原為該社區版本，請執行下列步驟。

1. 在 Ambari 中開啟叢集。
1. 流覽至 **Spark2** 的 [進行]  >  ****。
1. 選取 [ **自訂 spark2-預設值**]。
1. 選取 [ **加入屬性**]。
1. 加入 **spark. enabled = false**，然後加以儲存。
1. 屬性現在會設定為 **false**。
1. 選取 [ **儲存** ] 以儲存設定。

    ![關閉 Apache Ambari 中的功能。](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. 選取左面板中的 [ **Spark2** ]。 然後，在 [ **摘要** ] 索引標籤上，選取 [ **Spark2 歷程記錄伺服器**]。

    ![Apache Ambari 中的摘要視圖。](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. 若要重新開機 Spark 歷程記錄伺服器，請選取 [ **Spark2 歷程記錄伺服器**] 右邊的 [**已啟動**] 按鈕，然後從下拉式功能表中選取 [**重新開機**]。

    ![在 Apache Ambari 中重新開機 Spark 歷程記錄伺服器。](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. 重新整理 Spark 歷程記錄伺服器 web UI。 它會還原為該社區版本。

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>如何? 上傳 Spark 歷程記錄伺服器事件，將它報告為問題嗎？

如果您在 Spark 歷程記錄伺服器中遇到錯誤，請執行下列步驟來報告事件。

1. 在 Spark 歷程記錄伺服器 web UI 中選取 [ **下載** ]，以下載事件。

    ![下載 Spark 歷程記錄伺服器 UI 中的事件。](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. 選取 [從 **Spark 應用程式 & 作業圖形]** 頁面 **提供意見反應給我們**。

    ![在 Spark 應用程式 & 作業圖形頁面上提供意見反應](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. 提供錯誤的標題和描述。 然後，將 .zip 檔案拖曳至 [編輯] 欄位，然後選取 [ **提交新的問題**]。

    ![上傳並提交新的問題。](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>如何? 升級在修正程式案例中的 .jar 檔案？

如果您想要使用修正程式進行升級，請使用下列腳本來升級 `spark-enhancement.jar*` 。

**upgrade_spark_enhancement.sh**：

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

#### <a name="usage"></a>使用狀況

`upgrade_spark_enhancement.sh https://${jar_path}`

#### <a name="example"></a>範例

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

#### <a name="use-the-bash-file-from-the-azure-portal"></a>使用 Azure 入口網站中的 bash 檔案

1. 啟動 [Azure 入口網站](https://ms.portal.azure.com)，然後選取您的叢集。
2. 使用下列參數來完成 [腳本動作](../hdinsight-hadoop-customize-cluster-linux.md) 。

    |屬性 |值 |
    |---|---|
    |指令碼類型|- 自訂|
    |名稱|UpgradeJar|
    |Bash 指令碼 URI|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |節點類型|Head、Worker|
    |參數|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Azure 入口網站提交腳本動作](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>已知問題

+ Spark 歷程記錄伺服器目前僅適用于 Spark 2.3 和2.4。

+ 使用 RDD 的輸入和輸出資料不會顯示在 [ **資料** ] 索引標籤中。

## <a name="next-steps"></a>後續步驟

+ [在 HDInsight 上管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
+ [設定 Apache Spark 設定](apache-spark-settings.md)

## <a name="suggestions"></a>建議

如果您有任何意見反應，或在使用此工具時遇到任何問題，請傳送電子郵件給 ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)) 。
