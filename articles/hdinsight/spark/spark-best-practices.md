---
title: 在 Azure HDInsight 上使用 Apache Spark 最佳實踐
description: 瞭解在 Azure HDInsight 中使用 Apache Spark 的最佳做法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71106121"
---
# <a name="apache-spark-best-practices"></a>阿帕奇火花最佳實踐

本文提供了在 Azure HDInsight 上使用 Apache Spark 的各種最佳實踐。

## <a name="how-do-i-run-or-submit-spark-jobs"></a>如何運行或提交 Spark 作業？

| 選項 | 文件 |
|---|---|
| VSCode | [使用火花&蜂巢工具視覺化工作室代碼](../hdinsight-for-vscode.md) |
| Jupyter Notebook | [教學課程：在 Azure HDInsight 中的 Apache Spark 叢集上載入資料和執行查詢](./apache-spark-load-data-run-query.md) |
| IntelliJ | [教程：使用 IntelliJ 的 Azure 工具組為 HDInsight 群集創建 Apache Spark 應用程式](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [教學課程：使用 IntelliJ 為 HDInsight 上的 Apache Spark 建立 Scala Maven 應用程式](./apache-spark-create-standalone-application.md) |
| Zeppelin Notebook | [在 Azure HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集](./apache-spark-zeppelin-notebook.md) |
| 與利維一起提交遠端作業 | [使用 Apache Spark REST API 將遠端作業提交至 HDInsight Spark 叢集](./apache-spark-livy-rest-interface.md) |

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>如何監視和調試 Spark 作業？

| 選項 | 文件 |
|---|---|
| Azure Toolkit for IntelliJ | [使用用於 IntelliJ 的 Azure 工具組（預覽）失敗引發作業調試](apache-spark-intellij-tool-failure-debug.md) |
| 通過 SSH 用於 IntelliJ 的 Azure 工具組 | [使用 Azure Toolkit for IntelliJ 透過 SSH 對 HDInsight 叢集上的 Apache Spark 應用程式進行本機或遠端偵錯](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| 通過 VPN 為 IntelliJ 的 Azure 工具組 | [使用 Azure Toolkit for IntelliJ 來在 HDInsight 中透過 VPN 遠端偵錯 Apache Spark 應用程式](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Apache Spark 歷史伺服器上的工作圖 | [使用擴充的 Apache Spark 記錄伺服器對 Apache Spark 應用程式進行偵錯和診斷](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>如何提高 Spark 作業的運行效率？

| 選項 | 文件 |
|---|---|
| IO 緩存 | [使用 Azure HDInsight IO 快取改進 Apache Spark 工作負載效能 (Preview)](./apache-spark-improve-performance-iocache.md) |
| 設定選項 | [最佳化 Apache Spark 作業](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>如何連接到其他 Azure 服務？

| 選項 | 文件 |
|---|---|
| HDInsight 上的 Apache Hive | [整合 Apache Spark 和 Apache Hive 與 Hive Warehouse Connector](../interactive-query/apache-hive-warehouse-connector.md) |
| HDInsight 上的 Apache HBase | [使用 Apache Spark 來讀取和寫入 Apache HBase 資料](../hdinsight-using-spark-query-hbase.md) |
| HDInsight 上的 Apache Kafka | [教學課程：將 Apache Spark 結構化串流用於 HDInsight 上的 Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB：在 Azure 平台上實作 Lambda 架構](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>我的存儲選項是什麼？

| 選項 | 文件 |
|---|---|
| Data Lake Storage Gen2 | [搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Data Lake Storage Gen1 | [搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen1](../hdinsight-hadoop-use-data-lake-store.md) |
| Azure Blob 儲存體 | [搭配使用 Azure 儲存體與 Azure HDInsight 叢集](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>後續步驟

* [設定 Apache Spark 設定](apache-spark-settings.md)
* [在 HDInsight 中優化阿帕奇火花作業](apache-spark-perf.md)
