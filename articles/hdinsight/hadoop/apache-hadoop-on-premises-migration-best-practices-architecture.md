---
title: 架構：內部部署 Apache Hadoop 至 Azure HDInsight
description: 了解將內部部署 Hadoop 叢集遷移到 Azure HDInsight 的架構最佳做法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 7a76ac3bbe62d48de67815d09e1c8d75f03caa36
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077893"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>將內部部署 Apache Hadoop 叢集遷移到 Azure HDInsight - 架構最佳作法

本文將提供有關 Azure HDInsight 系統架構的建議。 本文是系列文章中的一篇，提供有助於將內部部署 Apache Hadoop 系統移轉至 Azure HDInsight 的最佳做法。

## <a name="use-multiple-workload-optimized-clusters"></a>使用多個已最佳化工作負載的叢集

許多內部部署的 Apache Hadoop 部署會包含支援許多工作負載的單一大型叢集。 此單一叢集可能很複雜，而且可能需要與個別服務互相妥協，才能讓每個項目正常運作。 將內部部署 Hadoop 叢集遷移到 Azure HDInsight 需要變更方法。

Azure HDInsight 叢集專為特定的計算使用類型而設計。 因為儲存體可跨多個叢集共用，所以您可以建立多個工作負載優化計算叢集，以符合不同作業的需求。 每個叢集類型都有特定工作負載適用的最佳組態。 下表列出 HDInsight 和對應工作負載中支援的叢集類型。

|工作負載|HDInsight 叢集類型|
|---|---|
|批次處理 (ETL / ELT)|Hadoop、Spark|
|資料倉儲|Hadoop、Spark、互動式查詢|
|IoT / 串流|Kafka、Storm、Spark|
|NoSQL 交易處理|hbase|
|使用記憶體內快取執行互動式及更快速的查詢|互動式查詢|
|資料科學|ML 服務、Spark|

下表顯示可用來建立 HDInsight 叢集的不同方法。

|工具|以瀏覽器為基礎|命令列|REST API|SDK|
|---|---|---|---|---|
|[Azure 入口網站](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Azure CLI (1.0 版)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)||||X|
|[Python SDK](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)||||X|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)||||X|
|[Azure 資源管理員範本](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

如需詳細資訊，請參閱 [HDInsight 中的叢集類型](../hadoop/apache-hadoop-introduction.md)一文。

## <a name="use-transient-on-demand-clusters"></a>使用暫時性隨選叢集

HDInsight 叢集可能會有很長一段時間未使用。 為了節省資源成本，HDInsight 可支援暫時性隨選叢集，當工作負載成功完成後，即可刪除這些叢集。

當您刪除叢集時，不會移除相關聯的儲存體帳戶和外部中繼資料。 您之後可以使用相同的儲存體帳戶和中繼存放區重新建立叢集。

Azure Data Factory 可用來排定隨選 HDInsight 叢集的建立。 如需詳細資訊，請參閱[使用 Azure Data Factory 在 HDInsight 中建立隨選 Apache Hadoop 叢集](../hdinsight-hadoop-create-linux-clusters-adf.md)一文。

## <a name="decouple-storage-from-compute"></a>分離計算和儲存體

一般內部部署的 Hadoop 部署會使用同一組機器進行資料儲存和資料處理。 因為它們是共置的，所以計算和儲存體必須一起調整。

在 HDInsight 叢集上，儲存體不需要與計算共存，而且可以在 Azure 儲存體中 Azure Data Lake Storage 或兩者。 分離儲存體和計算有下列優點：

- 在叢集之間共用資料。
- 可使用暫時性叢集，因為資料不相依於叢集。
- 降低儲存成本。
- 可各別調整儲存體與計算。
- 跨區域複寫資料。

所建立的計算叢集會靠近 Azure 區域中的儲存體帳戶資源，以減少分開計算和儲存體而產生的效能成本。 高速網路會讓計算節點有效率地存取 Azure 儲存體內的資料。

## <a name="use-external-metadata-stores"></a>使用外部中繼資料存放區

與 HDInsight 叢集搭配使用的主要中繼存放區有兩種： [Apache Hive](https://hive.apache.org/)和[Apache Oozie](https://oozie.apache.org/)。 Hive 中繼存放區是主要的結構描述存放庫，可供資料處理引擎 (包括 Hadoop、Spark、LLAP、Presto 和 Apache Pig) 使用。 Oozie 中繼存放區會為進行中和已完成的 Hadoop 作業儲存排程和狀態的詳細資料。

HDInsight 會使用 Azure SQL Database 作為 Hive 和 Oozie 中繼存放區。 在 HDInsight 叢集中設定中繼存放區有兩種方式：

1. 預設中繼存放區

    - 沒有其他成本。
    - 刪除叢集時，中繼存放區也會一併刪除。
    - 無法在不同叢集之間共用中繼存放區。
    - 使用基本的 Azure SQL DB，其中有 5 個 DTU 的限制。

1. 自訂外部中繼存放區

    - 將外部 Azure SQL Database 指定為中繼存放區。
    - 建立和刪除叢集時，不會遺失中繼資料和其中的 Hive 結構 Oozie 作業詳細資料。
    - 可以與不同類型的叢集共用單一中繼存放區資料庫。
    - 中繼存放區可以視需要相應增加。
    - 如需詳細資訊，請參閱[在 Azure HDInsight 中使用外部中繼資料存放區](../hdinsight-use-external-metadata-stores.md)。

## <a name="best-practices-for-hive-metastore"></a>Hive 中繼存放區的最佳做法

以下是一些 HDInsight Hive 中繼存放區的最佳做法：

- 使用自訂的外部中繼存放區來分開計算資源和中繼資料。
- 從 S2 層的 Azure SQL 執行個體開始，其提供 50 個 DTU 和 250 GB 的儲存體。 如果您看到瓶頸，您可以相應增加資料庫。
- 不讓專為某個 HDInsight 叢集版本建立的中繼存放區與不同版本的叢集共用。 不同的 Hive 版本會使用不同的結構描述。 例如，Hive 1.2 和 Hive 2.1 叢集無法共用中繼存放區。
- 定期備份自訂中繼存放區。
- 將中繼存放區與 HDInsight 叢集保存在相同區域。
- 使用 Azure SQL Database 監視工具（例如 Azure 入口網站或 Azure 監視器記錄），監視中繼存放區的效能和可用性。
- `ANALYZE TABLE`視需要執行命令，以產生資料表和資料行的統計資料。 例如： `ANALYZE TABLE [table_name] COMPUTE STATISTICS` 。

## <a name="best-practices-for-different-workloads"></a>不同工作負載的最佳做法

- 請考慮使用 LLAP 叢集來進行互動式 Hive 查詢，並改善回應時間[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP)   是 Hive 2.0 中的新功能，可允許查詢的記憶體內部快取。 LLAP 讓 Hive 查詢的速讀變快，在某些情況下可達到 [比 Hive 1.x 快 26 倍](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/)。
- 請考慮使用 Spark 作業取代 Hive 作業。
- 請考慮將以 impala 為基礎的查詢取代為 LLAP 查詢。
- 請考慮將 MapReduce 作業取代為 Spark 作業。
- 請考慮使用 Spark 結構化串流作業取代低延遲的 Spark 批次作業。
- 請考慮在資料協調流程中使用 Azure Data Factory (ADF) 2.0。
- 請考慮使用 Ambari 來管理叢集。
- 將資料儲存體從內部部署 HDFS 變更為 WASB 或 ADLS 或 ADFS，以處理指令碼。
- 請考慮在 Hive 資料表和稽核上使用 Ranger RBAC。
- 請考慮使用 CosmosDB 取代 MongoDB 或 Cassandra。

## <a name="next-steps"></a>下一步

閱讀此系列中的下一篇文章：

- [從內部部署移轉至 Azure HDInsight Hadoop 的基礎架構最佳作法](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)
