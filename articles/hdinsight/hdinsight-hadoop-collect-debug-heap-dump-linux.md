---
title: 在 HDInsight 上啟用 Apache Hadoop 服務的堆積傾印 - Azure
description: 在以 Linux 為基礎的 HDInsight 叢集上啟用 Apache Hadoop 服務的堆積傾印，以進行偵錯和分析。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 1ef52d74f7ae6e7e0d8c58e3b1972a0a1227c6b5
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85962198"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>在以 Linux 為基礎的 HDInsight 上啟用 Apache Hadoop 服務的堆積傾印

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

堆積傾印含有應用程式記憶體的快照，其中包括建立傾印時的變數值。 因此，它們很適合用來診斷執行時間發生的問題。

## <a name="services"></a>服務

您可以啟用下列服務的堆積傾印：

* **Apache hcatalog** - tempelton
* **Apache hive** - hiveserver2、metastore、derbyserver
* **mapreduce** - jobhistoryserver
* **Apache yarn** - resourcemanager、nodemanager、timelineserver
* **Apache hdfs** - datanode、secondarynamenode、namenode

您也可以針對 HDInsight 所執行的 map 和 reduce 處理序來啟用堆積傾印。

## <a name="understanding-heap-dump-configuration"></a>了解堆積傾印組態

堆積傾印的啟用方式，是在服務啟動時將選項 (有時稱為參數) 傳遞至 JVM。 就大部分的 [Apache Hadoop](https://hadoop.apache.org/) 服務而言，您可以修改用於啟動服務的殼層指令碼以略過這些選項。

在每個腳本中，會有 [選擇** \* \_ ] 的匯出，其中**包含傳遞至 JVM 的選項。 例如，在 **hadoop-env.sh** 指令碼中，以 `export HADOOP_NAMENODE_OPTS=` 為開頭的那一行即含有 NameNode 服務的選項。

map 和 reduce 處理序會稍有不同，因為這些作業是 MapReduce 服務的子處理序。 每個 map 或 reduce 處理序都會在一個子容器中執行，且有兩個含有 JVM 選項的項目。 兩者均包含在 **mapred-site.xml** 中：

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]  
> 我們建議使用[Apache Ambari](https://ambari.apache.org/)來修改腳本和 mapred-site.xml 設定，因為 Ambari 會處理叢集中跨節點的複寫變更。 如需特定的步驟，請參閱 [使用 Apache Ambari](#using-apache-ambari) 一節。

### <a name="enable-heap-dumps"></a>啟用堆積傾印

以下選項可在發生 OutOfMemoryError 時啟用堆積傾印：

`-XX:+HeapDumpOnOutOfMemoryError`

**+** 表示已啟用此選項。 預設值為停用。

> [!WARNING]  
> 根據預設不會在 HDInsight 上啟用 Hadoop 服務的堆積傾印，因為傾印檔案可能會很龐大。 如果您為了進行疑難排解而啟用它們，請記得在重現問題並收集傾印檔案之後將其停用。

### <a name="dump-location"></a>傾印位置

傾印檔案的預設位置是目前的工作目錄。 您可以使用以下選項，控制檔案的儲存位置：

`-XX:HeapDumpPath=/path`

例如，使用 `-XX:HeapDumpPath=/tmp` 會使傾印儲存在 /tmp 目錄中。

### <a name="scripts"></a>指令碼

您也可以在發生 **OutOfMemoryError** 時觸發指令碼。 例如觸發通知，讓您知道發生了錯誤。 使用下列選項可在發生 __OutOfMemoryError__ 時觸發指令碼：

`-XX:OnOutOfMemoryError=/path/to/script`

> [!NOTE]  
> 由於 Apache Hadoop 是分散式系統，因此所有使用的指令碼都必須放置在服務執行所在叢集中的所有節點上。
> 
> 指令碼也必須位於服務所屬的帳戶可以存取的位置中，而且必須提供執行權限。 例如，您可能想要在 `/usr/local/bin` 中存放指令碼，並用 `chmod go+rx /usr/local/bin/filename.sh` 授與讀取和執行權限。

## <a name="using-apache-ambari"></a>使用 Apache Ambari

若要修改服務的組態，請依照下列步驟進行：

1. 從網頁瀏覽器瀏覽至 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是叢集的名稱。

2. 使用左側的清單，選取您想要修改的服務區域。 例如 **HDFS**。 在中間區域內，選取 [設定] **** 索引標籤。

    ![Ambari Web 圖片 (已選取 HDFS 設定索引標籤)](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-service-config-tab.png)

3. 在 [篩選...]**** 項目中輸入 **opts**。 只會顯示包含此文字的項目。

    ![Apache Ambari 設定篩選清單](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdinsight-filter-list.png)

4. 針對您想要啟用堆積傾** \* \_ 印的服務尋找選擇專案，** 並新增您想要啟用的選項。 在下圖中，我將 `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` 新增至 **HADOOP\_NAMENODE\_OPTS** 項目：

    ![Apache Ambari hadoop-namenode-選擇](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hadoop-namenode-opts.png)

   > [!NOTE]  
   > 啟用 map 或 reduce 子處理序的堆積傾印時，請尋找名為 **mapreduce.admin.map.child.java.opts** 和 **mapreduce.admin.reduce.child.java.opts** 的欄位。

    使用 [儲存]**** 按鈕來儲存變更。 您可以輸入簡短的附註來說明所做的變更。

5. 套用變更後，一或多個服務旁邊就會出現**必須重新啟動**圖示。

    ![必須重新啟動圖示和重新啟動按鈕](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/restart-required-icon.png)

6. 選取每個需要重新啟動的服務，並使用 [服務動作]**** 按鈕**開啟維護模式**。 維護模式可避免您在重新啟動服務產生警示。

    ![開啟 hdi 維護模式功能表](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-maintenance-mode.png)

7. 啟用維護模式後，請使用服務的 [重新啟動]**** 按鈕來**重新啟動所有受影響的項目**。

    ![Apache Ambari 重新開機所有受影響的專案](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-restart-all-button.png)

   > [!NOTE]  
   > 其他服務的 [**重新開機**] 按鈕專案可能會有所不同。

8. 重新啟動服務後，請使用 [服務動作]**** 按鈕**關閉維護模式**。 這麼做可讓 Ambari 繼續監視服務是否有警示。