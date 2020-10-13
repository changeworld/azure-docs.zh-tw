---
title: 將 Apache Spark 2.1 或 2.2 工作負載遷移至 2.3 或 2.4 - Azure HDInsight
description: 了解如何將 Apache Spark 2.1 和 2.2 遷移至 2.3 或 2.4。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 1ce9b0faa6636d1318871cc9ef66cfbe47908265
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89504973"
---
# <a name="migrate-apache-spark-21-and-22-workloads-to-23-and-24"></a>將 Apache Spark 2.1 和 2.2 工作負載遷移至 2.3 和 2.4

本文件說明如何將 Spark 2.1 和 2.2 上的 Apache Spark 工作負載遷移至 2.3 或 2.4。

如[版本資訊](../hdinsight-release-notes.md#upcoming-changes)所說明，從 2020 年 7 月 1 日開始即不支援下列叢集設定，且客戶將無法使用這些設定建立新的叢集：
 - HDInsight 3.6 Spark 叢集中的 Spark 2.1 和 2.2
 - HDInsight 4.0 Spark 叢集中的 Spark 2.3

這些設定中的現有叢集將會以現狀執行，Microsoft 不再加以支援。 如果您在 HDInsight 3.6 上使用 Spark 2.1 或 2.2，請於 2020 年 6 月 30 日之前在 HDInsight 3.6 上移至 Spark 2.3，以避免潛在的系統/支援中斷。 如果您在 HDInsight 4.0 叢集上使用 Spark 2.3，請於 2020 年 6 月 30 日之前在 HDInsight 4.0 上移至 Spark 2.4，以避免潛在的系統/支援中斷。

如需關於將 HDInsight 叢集從 3.6 遷移至 4.0 的一般資訊，請參閱[將 HDInsight 叢集遷移至更新的版本](../hdinsight-upgrade-cluster.md)。 如需關於遷移至較新版 Apache Spark 的一般資訊，請參閱 [Apache Spark：版本控制原則](https://spark.apache.org/versioning-policy.html)。

## <a name="guidance-on-spark-version-upgrades-on-hdinsight"></a>HDInsight 上的 Spark 版本升級指引

| 升級案例 | 機制 | 考量事項 | Spark/Hive 整合 |
|------------------|-----------|--------------------|------------------------|
|HDInsight 3.6 Spark 2.1 至 HDInsight 3.6 Spark 2.3| 使用 HDInsight Spark 2.3 重新建立叢集 | 請檢閱下列文章： <br> [Apache Spark：從 Spark SQL 2.2 升級至 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark：從 Spark SQL 2.1 升級至 2.2](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | 無變更 |
|HDInsight 3.6 Spark 2.2 至 HDInsight 3.6 Spark 2.3 | 使用 HDInsight Spark 2.3 重新建立叢集 | 請檢閱下列文章： <br> [Apache Spark：從 Spark SQL 2.2 升級至 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | 無變更 |
| HDInsight 3.6 Spark 2.1 至 HDInsight 4.0 Spark 2.4 | 使用 HDInsight 4.0 Spark 2.4 重新建立叢集 | 請檢閱下列文章： <br> [Apache Spark：從 Spark SQL 2.3 升級至 2.4](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark：從 Spark SQL 2.2 升級至 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark：從 Spark SQL 2.1 升級至 2.2](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | Spark 和 Hive 整合在 HDInsight 4.0 中已變更。 <br><br> 在 HDInsight 4.0 中，Spark 和 Hive 會使用獨立的目錄來存取 SparkSQL 或 Hive 資料表。 Spark 所建立的資料表會存放在 Spark 目錄中。 Hive 所建立的資料表會存放在 Hive 目錄中。 此行為不同於 Hive 和 Spark 會共用相同目錄的 HDInsight 3.6。 HDInsight 4.0 中的 Hive 和 Spark 整合須仰賴 Hive Warehouse Connector (HWC)。 HWC 是 Spark 與 Hive 之間的連絡管道。 了解 Hive Warehouse Connector。 <br> 在 HDInsight 4.0 中，如果您想要在 Hive 與 Spark 之間共用中繼存放區，您可以將 metastore.catalog.default 屬性變更為 Spark 叢集中的登錄區。 您可以在 Ambari 進階 spark2-hive-site-override 中找到此屬性。 請務必瞭解，中繼存放區共用僅適用于外部 hive 資料表，如果您有內部/受控 hive 資料表或 ACID 資料表，這將無法運作。 <br><br>如需詳細資訊，請參閱[將 Azure HDInsight 3.6 Hive 工作負載遷移至 HDInsight 4.0](../interactive-query/apache-hive-migrate-workloads.md)。<br><br> |
| HDInsight 3.6 Spark 2.2 至 HDInsight 4.0 Spark 2.4 | 使用 HDInsight 4.0 Spark 2.4 重新建立叢集 | 請檢閱下列文章： <br> [Apache Spark：從 Spark SQL 2.3 升級至 2.4](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark：從 Spark SQL 2.2 升級至 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | Spark 和 Hive 整合在 HDInsight 4.0 中已變更。 <br><br> 在 HDInsight 4.0 中，Spark 和 Hive 會使用獨立的目錄來存取 SparkSQL 或 Hive 資料表。 Spark 所建立的資料表會存放在 Spark 目錄中。 Hive 所建立的資料表會存放在 Hive 目錄中。 此行為不同於 Hive 和 Spark 會共用相同目錄的 HDInsight 3.6。 HDInsight 4.0 中的 Hive 和 Spark 整合須仰賴 Hive Warehouse Connector (HWC)。 HWC 是 Spark 與 Hive 之間的連絡管道。 了解 Hive Warehouse Connector。 <br> 在 HDInsight 4.0 中，如果您想要在 Hive 與 Spark 之間共用中繼存放區，您可以將 metastore.catalog.default 屬性變更為 Spark 叢集中的登錄區。 您可以在 Ambari 進階 spark2-hive-site-override 中找到此屬性。 請務必瞭解，中繼存放區共用僅適用于外部 hive 資料表，如果您有內部/受控 hive 資料表或 ACID 資料表，這將無法運作。 <br><br>如需詳細資訊，請參閱[將 Azure HDInsight 3.6 Hive 工作負載遷移至 HDInsight 4.0](../interactive-query/apache-hive-migrate-workloads.md)。|

## <a name="next-steps"></a>後續步驟

* [將 HDInsight 叢集遷移至更新的版本](../hdinsight-upgrade-cluster.md)
* [將 Azure HDInsight 3.6 Hive 工作負載遷移至 HDInsight 4.0](../interactive-query/apache-hive-migrate-workloads.md)
