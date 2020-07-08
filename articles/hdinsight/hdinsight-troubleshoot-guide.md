---
title: Azure HDInsight 疑難排解指南
description: 使用 Azure HDInsight 進行 Apache Hadoop 工作負載的疑難排解。 逐步文件會顯示如何使用 HDInsight 解決 Apache Hive、Apache Spark、Apache YARN、Apache HBase、HDFS 和 Apache Storm 的常見問題。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 4460c24bb742bff72af47954363b1051f5d9b43d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75895281"
---
# <a name="troubleshoot-by-using-azure-hdinsight"></a>使用 Azure HDInsight 進行疑難排解

| Apache 工作負載 | 最常見問題 |
|---|---|
|![hdinsight apache HBase 圖示圖示](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hbase.png)<br>[Apache HBase 疑難排解](hbase/apache-troubleshoot-hbase.md)|<br>[未指派的區域](hbase/hbase-troubleshoot-unassigned-regions.md#scenario-unassigned-regions)<br><br>[Azure HDInsight 中的 'hbase hbck' 命令逾時](hbase/hbase-troubleshoot-timeouts-hbase-hbck.md)<br><br>[Azure HDInsight 中 Apache Phoenix 連線問題](hbase/hbase-troubleshoot-phoenix-connectivity.md)<br><br>[什麼情況導致主要伺服器無法啟動？](hbase/hbase-troubleshoot-start-fails.md)<br><br>[BindException-位址已在使用中](hbase/hbase-troubleshoot-bindexception-address-use.md)|
|![hdinsight apache hdfs 圖示圖示](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hdfs.png)<br>[Apache Hadoop HDFS 疑難排解](hdinsight-troubleshoot-hdfs.md)|<br>[如何從叢集內部存取本機 HDFS？](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[本機 HDFS 在 Azure HDInsight 叢集上卡在安全模式](hadoop/hdinsight-hdfs-troubleshoot-safe-mode.md)|
|![hdinsight apache Hive 圖示圖示](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hive.png)<br>[疑難排解 Apache Hive](hdinsight-troubleshoot-hive.md)|<br>[如何匯出 Hive 中繼存放區並匯入另一個叢集？](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[如何在叢集上尋找 Apache Hive 記錄？](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[如何在叢集上以特定設定啟動 Apache Hive Shell？](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[如何分析叢集關鍵路徑上的 Apache Tez DAG 資料？](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[如何從叢集下載 Apache Tez DAG 資料？](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![hdinsight apache Spark 圖示圖示](./media/hdinsight-troubleshoot-guide/hdinsight-apache-spark.png)<br>[Apache Spark 疑難排解](hdinsight-troubleshoot-SPARK.md)|<br>[如何使用 Apache Ambari 在叢集上設定 Apache Spark 應用程式？](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters)<br><br>[如何使用 Jupyter Notebook 在叢集上設定 Apache Spark 應用程式？](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[如何使用 Apache Livy 在叢集上設定 Apache Spark 應用程式？](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters)<br><br>[如何使用 spark-submit 在叢集上設定 Apache Spark 應用程式？](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters)<br><br>[如何使用 IntelliJ 來設定 Apache Spark 應用程式？](spark/apache-spark-intellij-tool-plugin.md)<br><br>[如何使用 Eclipse 來設定 Apache Spark 應用程式？](spark/apache-spark-eclipse-tool-plugin.md)<br><br>[如何使用 VSCode 來設定 Apache Spark 應用程式？](hdinsight-for-vscode.md)<br><br>[Apache Spark 的 OutOfMemoryError 例外狀況](spark/apache-spark-troubleshoot-outofmemory.md#scenario-outofmemoryerror-exception-for-apache-spark)|
|![hdinsight apache 風暴圖示圖示](./media/hdinsight-troubleshoot-guide/hdinsight-apache-storm.png)<br>[Apache Storm 疑難排解](hdinsight-troubleshoot-STORM.md)|<br>[如何存取叢集上的 Apache Storm UI？](storm/apache-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[如何將 Apache Storm 事件中樞 Spout 檢查點資訊從一個拓撲傳輸到另一個拓撲？](storm/apache-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[如何找出叢集上的 Storm 二進位檔？](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[如何判斷 Storm 叢集的部署拓撲？](storm/apache-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[如何找出用於開發的 Apache Storm 事件中樞 Spout 二進位檔？](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)|
|![hdinsight apache YARN 圖示圖示](./media/hdinsight-troubleshoot-guide/hdinsight-apache-yarn.png)<br>[Apache Hadoop YARN 疑難排解](hdinsight-troubleshoot-YARN.md)|<br>[如何在叢集上建立新的 Apache Hadoop YARN 佇列？](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[如何從叢集下載 Apache Hadoop YARN 記錄？](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>HDInsight 疑難排解資源

| 如需下列資訊 | 請參閱以下文章 |
| --- | --- |
| Linux 上的 HDInsight 與最佳化 | - [在 Linux 上使用 HDInsight 的相關資訊](hdinsight-hadoop-linux-information.md)<br>- [Apache Hadoop 記憶體和效能疑難排解](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Apache Hive 查詢效能](https://web.archive.org/web/20190217214250/https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| 記錄和傾印 | - [存取 Linux 上的 Apache Hadoop YARN 應用程式記錄](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [為 Linux 上的 Apache Hadoop 服務啟用堆積傾印](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>- [分析 HDInsight 記錄](hdinsight-debug-jobs.md)|
| 錯誤 | - [瞭解並解決 WebHCat 錯誤](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [修正 OutofMemory 錯誤的 Apache Hive 設定](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| 工具 | - [優化 Apache Hive 查詢](hdinsight-hadoop-optimize-hive-query.md)<br>- [HDInsight IntelliJ 工具](./spark/apache-spark-intellij-tool-plugin.md)<br>- [HDInsight Eclipse 工具](./spark/apache-spark-eclipse-tool-plugin.md)<br>- [HDInsight VSCode 工具](hdinsight-for-vscode.md)<br>- [HDInsight Visual Studio 工具](./hadoop/apache-hadoop-visual-studio-tools-get-started.md) |

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。