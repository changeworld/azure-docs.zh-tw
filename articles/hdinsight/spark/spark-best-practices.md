---
title: Azure HDInsight Apache Spark 指導方針
description: 瞭解在 Azure HDInsight 中使用 Apache Spark 的指導方針。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: f81435abbedec25cb8abe0b958065a493c15ce4c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938810"
---
# <a name="apache-spark-guidelines"></a>Apache Spark 指導方針

本文提供在 Azure HDInsight 上使用 Apache Spark 的各種指導方針。

## <a name="how-do-i-run-or-submit-spark-jobs"></a>如何? 執行或提交 Spark 作業？

| 選項 | 文件 |
|---|---|
| VSCode | [使用 Spark & Hive Tools for Visual Studio Code](../hdinsight-for-vscode.md) |
| Jupyter Notebook | [教學課程：在 Azure HDInsight 中的 Apache Spark 叢集上載入資料和執行查詢](./apache-spark-load-data-run-query.md) |
| IntelliJ | [教學課程：使用 Azure Toolkit for IntelliJ 建立適用于 HDInsight 叢集的 Apache Spark 應用程式](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [教學課程：使用 IntelliJ 為 HDInsight 中的 Apache Spark 建立 Scala Maven 應用程式](./apache-spark-create-standalone-application.md) |
| Zeppelin Notebook | [在 Azure HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集](./apache-spark-zeppelin-notebook.md) |
| 使用 Livy 進行遠端作業提交 | [使用 Apache Spark REST API 將遠端作業提交至 HDInsight Spark 叢集](./apache-spark-livy-rest-interface.md) |
|[Apache Oozie](../hdinsight-use-oozie-linux-mac.md)|Oozie 是可管理 Hadoop 作業的工作流程和協調系統。|
|[Apache Livy](./apache-spark-livy-rest-interface.md)|您可以使用 Livy 執行互動式 Spark 殼層，或提交要在 Spark 上執行的批次作業。|
|[Apache Spark 的 Azure Data Factory](../../data-factory/transform-data-using-spark.md)|Data Factory 管線中的 Spark 活動會在您自己或 [隨選 HDInsight 叢集上執行 Spark 程式。|
|[Apache Hive 的 Azure Data Factory](../../data-factory/transform-data-using-hadoop-hive.md)|Data Factory 管線中的 HDInsight Hive 活動會在您自己或隨選 HDInsight 叢集上執行 Hive 查詢。|

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>如何? 監視和調試 Spark 作業？

| 選項 | 文件 |
|---|---|
| Azure Toolkit for IntelliJ | [使用 Azure Toolkit for IntelliJ (preview) 的 spark 作業無法進行調試 ](apache-spark-intellij-tool-failure-debug.md) |
| 透過 SSH Azure Toolkit for IntelliJ | [使用 Azure Toolkit for IntelliJ 透過 SSH 對 HDInsight 叢集上的 Apache Spark 應用程式進行本機或遠端偵錯](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| 透過 VPN Azure Toolkit for IntelliJ | [使用 Azure Toolkit for IntelliJ 來在 HDInsight 中透過 VPN 遠端偵錯 Apache Spark 應用程式](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Apache Spark 歷程記錄伺服器上的作業圖表 | [使用擴充的 Apache Spark 記錄伺服器對 Apache Spark 應用程式進行偵錯和診斷](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>如何? 讓 Spark 作業更有效率地執行？

| 選項 | 文件 |
|---|---|
| IO 快取 | [使用 Azure HDInsight IO 快取改進 Apache Spark 工作負載效能 (Preview)](./apache-spark-improve-performance-iocache.md) |
| 設定選項 | [最佳化 Apache Spark 作業](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>如何? 連接至其他 Azure 服務嗎？

| 選項 | 文件 |
|---|---|
| HDInsight 上的 Apache Hive | [整合 Apache Spark 和 Apache Hive 與 Hive Warehouse Connector](../interactive-query/apache-hive-warehouse-connector.md) |
| HDInsight 上的 Apache HBase | [使用 Apache Spark 來讀取和寫入 Apache HBase 資料](../hdinsight-using-spark-query-hbase.md) |
| HDInsight 上的 Apache Kafka | [教學課程：將 Apache Spark 結構化串流用於 HDInsight 上的 Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [適用於 Azure Cosmos DB 的 Azure Synapse Link](../../cosmos-db/synapse-link.md) |

## <a name="what-are-my-storage-options"></a>我的儲存體選項有哪些？

| 選項 | 文件 |
|---|---|
| Azure Data Lake Storage Gen2 | [搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Azure Data Lake Storage Gen1 | [使用 Azure Data Lake Storage Gen1 搭配 Azure HDInsight 叢集](../hdinsight-hadoop-use-data-lake-storage-gen1.md) |
| Azure Blob 儲存體 | [搭配使用 Azure 儲存體與 Azure HDInsight 叢集](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>後續步驟

* [設定 Apache Spark 設定](apache-spark-settings.md)
* [將 HDInsight 中的 Apache Spark 作業最佳化](apache-spark-perf.md)
