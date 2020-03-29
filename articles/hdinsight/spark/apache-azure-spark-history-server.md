---
title: 使用 Apache Spark 歷史記錄伺服器中的擴展功能調試應用 - Azure HDInsight
description: 使用 Apache Spark 歷史記錄伺服器中的擴展功能來調試和診斷 Spark 應用程式 - Azure HDInsight。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 5cf1986711479f7330b0cd477744d9f4e2ac6459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548929"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>使用 Apache Spark 歷史記錄伺服器的擴展功能調試和診斷 Spark 應用程式

本文介紹如何使用 Apache Spark 歷史記錄伺服器的擴展功能來調試和診斷已完成或運行的 Spark 應用程式。 副檔名包括 **"資料"** 選項卡、**圖形**選項卡和**診斷**選項卡。在"**資料"** 選項卡上，可以檢查 Spark 作業的輸入和輸出資料。 在 **"圖形"** 選項卡上，您可以檢查資料流程並重播作業圖。 在 **"診斷"** 選項卡上，您可以參考 **"資料傾斜**"、**時間偏差**和執行**器使用方式分析**功能。

## <a name="get-access-to-the-spark-history-server"></a>訪問 Spark 歷史記錄伺服器

Spark 歷史記錄伺服器是已完成和運行 Spark 應用程式的 Web UI。 可以從 Azure 門戶或 URL 打開它。

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>從 Azure 門戶打開 Spark 歷史記錄伺服器 Web UI

1. 從 [Azure 入口網站](https://portal.azure.com/)，開啟 Spark 叢集。 如需詳細資訊，請參閱[列出和顯示叢集](../hdinsight-administer-use-portal-linux.md#showClusters)。
2. 從**群集儀表板**中，選擇**Spark 歷史記錄伺服器**。 出現提示時，輸入 Spark 叢集的系統管理員認證。

    ![從 Azure 門戶啟動 Spark 歷史記錄伺服器。](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Spark 歷程記錄伺服器")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>按 URL 打開 Spark 歷史記錄伺服器 Web UI

通過流覽打開`https://CLUSTERNAME.azurehdinsight.net/sparkhistory`Spark 歷史記錄伺服器，其中**CLUSTERNAME**是 Spark 群集的名稱。

Spark 歷史記錄伺服器 Web UI 可能類似于此映射：

!["火花歷史記錄伺服器"頁。](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>使用 Spark 歷史記錄伺服器中的資料選項卡

選擇作業 ID，然後選擇工具功能表上**的資料**以查看資料檢視。

+ 通過選擇單個選項卡來查看**輸入**、**輸出**和**表操作**。

    !["火花應用程式的資料"頁上的資料選項卡。](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ 通過選擇 **"複製**"按鈕複製所有行。

    ![複製 Spark 應用程式頁上的資料。](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ 將所有資料另存為 。通過選擇**csv**按鈕來 CSV 檔。

    ![將資料另存為 。來自"火花應用程式資料"頁的 CSV 檔。](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ 通過在 **"搜索"** 欄位中輸入關鍵字來搜索資料。 搜尋結果將立即顯示。

    ![在"火花應用程式資料"頁上搜索資料。](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ 選擇列標題對表進行排序。 選擇加號以展開行以顯示更多詳細資訊。 選擇減號以折疊行。

    ![Spark 應用程式資料頁上的資料表。](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ 通過選擇右側的 **"部分下載"** 按鈕下載單個檔。 所選檔將在本地下載。 如果該檔不再存在，這將打開一個新選項卡以顯示錯誤訊息。

    ![Spark 應用程式資料頁上的資料下載行。](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ 通過選擇"**複製完整路徑**"或 **"複製相對路徑**"選項（從下載功能表展開）複製完整路徑或相對路徑。 對於 Azure 資料湖存儲檔，請在**Azure 存儲資源管理器中選擇"打開**"以啟動 Azure 存儲資源管理器，並在登錄後找到資料夾。

    ![在 Spark 應用程式的資料頁面上複製完整路徑和複製相對路徑選項。](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ 如果單個頁面上顯示的行太多，請選擇表底部的頁碼以進行導航。

    !["火花應用程式資料"頁上的頁碼。](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ 有關詳細資訊，請將滑鼠懸停在**Spark 應用程式的資料**旁邊或選擇問號以顯示工具提示。

    ![從"火花應用資料"頁面獲取更多資訊。](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  要發送有關問題的回饋，請選擇"**向我們提供回饋**"。

    ![提供來自"火花應用資料"頁面的回饋。](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>使用 Spark 歷史記錄伺服器中的圖形選項卡

+ 選擇作業 ID，然後在工具功能表上選擇 **"圖形**"以查看作業圖。 預設情況下，圖形將顯示所有作業。 使用**作業 ID**下拉式功能表篩選結果。

    !["火花應用程式"&作業圖形頁上的工作 ID 下拉式功能表。](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ 預設情況下選擇**進度**。 通過在 **"顯示"** 下拉式功能表中選擇 **"讀取**"或 **"寫入"** 來檢查資料流程。

    ![檢查 Spark 應用程式&作業圖形頁上的資料流程。](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ 每個任務的背景顏色對應于熱圖。

   !["火花應用程式"&作業圖頁上的熱圖。](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |Color |描述 |
    |---|---|
    |綠色|作業已成功完成。|
    |橙色|任務失敗，但這不會影響作業的最終結果。 這些任務具有重複或重試實例，以後可能會成功。|
    |藍色|工作正在執行。|
    |白色|工作正在等候執行，或已略過該階段。|
    |紅色|工作失敗。|

     ![在"火花應用程式"上運行任務&作業圖形頁。](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     跳過的階段以白色顯示。
    ![Spark 應用程式&作業圖形頁上跳過的任務。](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Spark 應用程式&作業圖形頁上的失敗任務。](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > 重播可用於已完成的工作。 選擇 **"播放"** 按鈕以播放作業。 通過選擇停止按鈕，隨時停止作業。 重播作業時，每個任務將按顏色顯示其狀態。 不完全作業不支援播放。

+ 滾動以放大或縮小作業圖，或選擇 **"縮放以使其適合**"以使其適合螢幕。

    ![選擇"縮放"以適合"火花應用程式&作業圖"頁。](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ 當任務失敗時，將滑鼠懸停在圖形節點上以查看工具提示，然後選擇階段在新頁面中打開它。

    ![在"火花應用程式&作業圖頁上查看工具提示。](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ 在 Spark 應用程式&作業圖頁上，如果任務滿足以下條件，這些階段將顯示工具提示和小圖示：
  + 資料偏斜：資料讀取大小>此階段內所有任務的平均資料讀取大小 = 2*和*資料讀取大小> 10 MB。
  + 時間偏差：執行時間>此階段內所有任務的平均執行時間 = *2，* 執行時間> 2 分鐘。

    ![Spark 應用程式上的傾斜任務圖示&作業圖頁。](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ 作業圖節點將顯示有關每個階段的以下資訊：
  + ID
  + 名稱或說明
  + 任務總數
  + 資料讀取：輸入大小和隨機讀取大小的總和
  + 資料寫入：輸出大小和隨機寫入大小的總和
  + 執行時間：第一次嘗試的開始時間和上次嘗試的完成時間之間的時間
  + 行計數：輸入記錄、輸出記錄、隨機讀取記錄和隨機寫入記錄的總和
  + 進度

    > [!NOTE]  
    > 預設情況下，作業圖形節點將顯示每個階段上次嘗試的資訊（階段執行時間除外）。 但在播放過程中，作業圖形節點將顯示有關每次嘗試的資訊。

    > [!NOTE]  
    > 對於資料讀取和資料寫入大小，我們使用 1MB = 1000 KB = 1000 = 1000 位元組。

+ 通過選擇 **"向我們提供回饋**"發送有關問題的回饋。

    ![Spark 應用程式上的回饋選項&作業圖頁上。](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>使用 Spark 歷史記錄伺服器中的診斷選項卡

選擇作業 ID，然後在工具功能表上選擇 **"診斷"** 以查看作業診斷視圖。 診斷**選項卡**包括**資料傾斜**、**時間偏差**和執行**器使用方式分析**。

+ 通過分別選擇選項卡，查看**資料傾斜**、**時間偏斜**和執行**器使用方式分析**。

    ![診斷選項卡中的"資料傾斜"選項卡。](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>資料扭曲

選擇"**資料傾斜"** 選項卡。基於指定參數顯示相應的傾斜任務。

#### <a name="specify-parameters"></a>指定參數

"**指定參數"** 部分顯示用於檢測資料偏差的參數。 預設規則是：讀取的任務資料大於平均任務資料讀取的三倍，並且任務資料讀取超過 10 MB。 如果要為傾斜任務定義自己的規則，可以選擇參數。 **傾斜階段**和**傾斜圖**部分將相應地更新。

#### <a name="skewed-stage"></a>傾斜階段

**"傾斜階段"** 部分顯示具有扭曲任務以達到指定條件的各個階段。 如果一個階段中有多個偏斜任務，**則"傾斜階段"** 部分僅顯示最偏斜的任務（即資料偏斜的最大資料）。

!["診斷"選項卡中"資料傾斜"選項卡的較大視圖。](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>傾斜圖表

在 **"傾斜階段"** 表中選擇一行時，**偏斜圖**會根據資料讀取和執行時間顯示更多工分佈詳細資訊。 傾斜的任務以紅色標記，正常任務以藍色標記。 出於性能考慮，圖表最多顯示 100 個示例任務。 任務詳細資訊顯示在右下角的面板中。

![Spark UI 中階段 10 的傾斜圖。](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>時間扭曲

[時間扭曲]**** 索引標籤會根據工作執行時間顯示扭曲的工作。

#### <a name="specify-parameters"></a>指定參數

"**指定參數"** 部分顯示用於檢測時間偏斜的參數。 預設規則是：任務執行時間大於平均執行時間的三倍，任務執行時間大於 30 秒。 您可以根據您的需求變更參數。 **傾斜階段**和**傾斜圖**顯示相應的階段和任務資訊，就像在 **"資料傾斜"** 選項卡中一樣。

選擇**時間傾斜**時，根據 **"指定參數"** 部分中設置的參數，篩選的結果將顯示在 **"傾斜階段"** 部分中。 當您在 **"傾斜階段"** 部分中選擇一個專案時，相應的圖表將在第三部分中繪製，任務詳細資訊顯示在右下角的面板中。

!["診斷"選項卡中的"時間傾斜"選項卡。](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>執行器使用方式分析圖

**執行器使用方式圖**顯示作業的實際執行器分配和運行狀態。  

當您選擇**執行器流量分析**時，將起草關於執行器用法的四條不同曲線：**已分配執行器**、**正在運行的執行器**、**空閒執行器**和**最大執行器實例**。 **每個添加執行器**或刪除**執行者**的事件將增加或減少已分配執行器。 您可以在 **"作業"** 選項卡中查看**事件時間表**以進行更多比較。

![診斷選項卡中的"執行器使用方式分析"選項卡。](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

選擇顏色圖示以選擇或取消選擇所有草稿中的相應內容。

 ![在"執行器使用方式分析"選項卡中選擇圖表。](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>常見問題集

### <a name="how-do-i-revert-to-the-community-version"></a>如何恢復社區版本？

要還原到社區版本，請執行以下步驟。

1. 打開安巴里的群集。
1. 導航到**Spark2** > **配置**。
1. 選擇**自訂 spark2 預設值**。
1. 選擇 **"添加屬性..."。**
1. 添加**spark.ui.增強.啟用\false，** 然後保存它。
1. 屬性現在會設定為 **false**。
1. 選取 [儲存]**** 以儲存組態。

    ![關閉阿帕奇安巴里中的功能。](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. 在左側面板中選擇**Spark2。** 然後，在 **"摘要"** 選項卡上，選擇**Spark2 歷史記錄伺服器**。

    ![阿帕奇·安巴里中的摘要視圖。](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. 要重新開機 Spark 歷史記錄伺服器，請選擇**Spark2 歷史記錄伺服器**右側的 **"已啟動"** 按鈕，然後從下拉式功能表中選擇 **"重新開機**"。

    ![重新開機阿帕奇 Ambari 中的火花歷史記錄伺服器。](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. 刷新 Spark 歷史記錄伺服器 Web UI。 它將恢復到社區版本。

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>如何上傳 Spark 歷史記錄伺服器事件以將其報告為問題？

如果在 Spark 歷史記錄伺服器中遇到錯誤，執行以下步驟來報告事件。

1. 通過在 Spark 歷史記錄伺服器 Web UI 中選擇 **"下載**"來下載事件。

    ![在 Spark 歷史記錄伺服器 UI 中下載事件。](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. 選擇"從 Spark**應用程式&作業圖**頁面**向我們提供回饋**。

    ![在 Spark 應用程式&作業圖頁上提供回饋](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. 提供錯誤的標題和說明。 然後，將 .ZIP 檔案拖動到編輯欄位，然後選擇 **"提交新問題**"。

    ![上傳並提交新問題。](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>如何在修補程式方案中升級 .jar 檔？

如果要使用修補程式進行升級，請使用以下腳本，這將升級`spark-enhancement.jar*`。

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

#### <a name="usage"></a>使用量

`upgrade_spark_enhancement.sh https://${jar_path}`

#### <a name="example"></a>範例

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

#### <a name="use-the-bash-file-from-the-azure-portal"></a>使用 Azure 門戶中的 bash 檔

1. 啟動[Azure 門戶](https://ms.portal.azure.com)，然後選擇群集。
2. 使用以下參數完成[腳本操作](../hdinsight-hadoop-customize-cluster-linux.md)。

    |屬性 |值 |
    |---|---|
    |指令碼類型|- 自訂|
    |名稱|升級Jar|
    |Bash 指令碼 URI|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |節點類型|頭， 工人|
    |參數|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Azure 門戶提交腳本操作](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>已知問題

+ 目前，Spark 歷史記錄伺服器僅適用于 Spark 2.3 和 2.4。

+ 使用 RDD 的輸入和輸出資料不會顯示在 **"資料"** 選項卡中。

## <a name="next-steps"></a>後續步驟

+ [在 HDInsight 上管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
+ [設定 Apache Spark 設定](apache-spark-settings.md)

## <a name="feedback"></a>意見反應

如果您在使用此工具時有任何回饋或遇到任何問題，請發送電子郵件至 （[hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)。
