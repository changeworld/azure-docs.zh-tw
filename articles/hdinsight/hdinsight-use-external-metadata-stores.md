---
title: 使用外部中繼資料存放區 - Azure HDInsight
description: 搭配 Azure HDInsight 叢集使用外部中繼資料存放區。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 08/06/2020
ms.openlocfilehash: 78c0526ac750977115a88e96bb5f7d5cb4e9803f
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87873087"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>在 Azure HDInsight 中使用外部中繼資料存放區

HDInsight 可讓您利用外部資料存放區來控制您的資料和中繼資料。 這項功能適用于[Apache Hive 中繼存放區](#custom-metastore)、 [apache Oozie 中繼存放區](#apache-oozie-metastore)和[apache Ambari 資料庫](#custom-ambari-db)。

HDInsight 中的 Apache Hive 中繼存放區是 Apache Hadoop 架構不可或缺的一部分。 中繼存放區是中央架構存放庫。 中繼存放區是由其他海量資料存取工具（例如 Apache Spark、互動式查詢 (LLAP) 、Presto 或 Apache Pig）所使用。 HDInsight 使用 Azure SQL Database 作為 Hive 中繼存放區。

![HDInsight Hive 中繼資料存放區架構](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

您有兩種方式可以為您的 HDInsight 叢集設定中繼存放區：

* [預設中繼存放區](#default-metastore)
* [自訂中繼存放區](#custom-metastore)

## <a name="default-metastore"></a>預設中繼存放區

根據預設，HDInsight 會以每個叢集類型來建立中繼存放區。 您可以改為指定自訂中繼存放區。 預設中繼存放區包含下列考量：

* 沒有其他成本。 HDInsight 會以每個叢集類型來建立中繼存放區，但不會對您產生額外成本。

* 每個預設中繼存放區是叢集生命週期的一部分。 當您刪除叢集時，相應的中繼存放區和中繼資料會一併刪除。

* 您無法與其他叢集共用預設中繼存放區。

* 建議僅針對簡單的工作負載使用預設中繼存放區。 不需要多個叢集，且不需要保留超過叢集生命週期的中繼資料的工作負載。

> [!IMPORTANT]
> 預設中繼存放區會提供具有**基本第5層 DTU 限制的 Azure SQL Database， (無法升級) **！ 適用于基本測試用途。 針對大型或生產工作負載，我們建議您遷移至外部中繼存放區。

## <a name="custom-metastore"></a>自訂中繼存放區

HDInsight 也支援自訂中繼存放區，這是針對生產叢集建議的中繼存放區：

* 您將自己的 Azure SQL Database 指定為中繼存放區。

* 中繼存放區的生命週期不會系結至叢集生命週期，因此您可以建立和刪除叢集，而不會遺失中繼資料。 即使您刪除並重新建立 HDInsight 叢集之後，中繼資料 (例如您的 Hive 結構描述) 仍會保存。

* 自訂中繼存放區可讓您將多個叢集與叢集類型連結至該中繼存放區。 例如，單一中繼存放區可以在 HDInsight 中的互動式查詢、Hive 和 Spark 叢集之間共用。

* 根據您選擇的效能層級，您需要支付中繼存放區 (Azure SQL Database) 費用。

* 您可以視需要相應增加中繼存放區。

* 叢集和外部中繼存放區必須裝載在相同的區域中。

![HDInsight Hive 中繼資料存放區使用案例](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>建立和設定自訂中繼存放區的 Azure SQL Database

在設定 HDInsight 叢集的自訂 Hive 中繼存放區之前，建立或擁有現有的 Azure SQL Database。  如需詳細資訊，請參閱[快速入門：在 Azure SQL Database 中建立單一資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)。

建立叢集時，HDInsight 服務必須連接到外部中繼存放區並驗證您的認證。 設定 Azure SQL Database 防火牆規則，以允許 Azure 服務和資源存取伺服器。 選取 [**設定伺服器防火牆**]，在 Azure 入口網站中啟用此選項。 然後在 [**拒絕公用網路存取**] 底下選取 **[** **否**]，然後在 [**允許 Azure 服務和資源存取此伺服器**以進行 Azure SQL Database] 底下。 如需詳細資訊，請參閱[建立和管理 IP 防火牆規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

不支援 SQL 存放區的私用端點。

![[設定伺服器防火牆] 按鈕](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![允許 azure 服務存取](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>在叢集建立期間選取自訂中繼存放區

您可以隨時將叢集指向先前建立的 Azure SQL Database。 若要透過入口網站建立叢集，請從**儲存體 > 中繼存放區設定**指定選項。

![HDInsight Hive 中繼資料存放區 Azure 入口網站](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>Hive 中繼存放區方針

> [!NOTE]
> 盡可能使用自訂中繼存放區，這樣有助於個別計算資源 (您的執行中叢集) 和中繼資料 (儲存在中繼存放區)。 從 S2 層開始，它會提供 50 DTU 和 250 GB 的儲存空間。 如果您看到瓶頸，您可以相應增加資料庫。

* 如果您想要讓多個 HDInsight 叢集存取不同的資料，請針對每個叢集上的中繼存放區使用不同資料庫。 如果您在多個 HDInsight 叢集間共用中繼存放區，則表示這些叢集會使用相同的中繼資料和基礎使用者資料檔案。

* 定期備份您的自訂中繼存放區。 Azure SQL Database 會自動產生備份，但備份保留時間範圍有所不同。 如需詳細資訊，請參閱[了解自動 SQL Database 備份](../azure-sql/database/automated-backups-overview.md)。

* 在相同的區域中找出您的中繼存放區和 HDInsight 叢集。 此設定會提供最高效能和最低的網路輸出費用。

* 使用 Azure SQL Database 監視工具或 Azure 監視器記錄，監視中繼存放區的效能和可用性。

* 針對現有的自訂中繼存放區資料庫建立新的較高版本的 Azure HDInsight 時，系統會升級中繼存放區的架構。 升級無法復原，而不需要從備份還原資料庫。

* 如果您在多個叢集間共用中繼存放區，請確定所有叢集都是相同的 HDInsight 版本。 不同的 Hive 版本會使用不同的中繼存放區資料庫結構描述。 例如，您無法在 Hive 2.1 和 Hive 3.1 版本的叢集間共用中繼存放區。

* 在 HDInsight 4.0 中，Spark 和 Hive 會使用獨立的目錄來存取 SparkSQL 或 Hive 資料表。 Spark 所建立的資料表會存放在 Spark 目錄中。 Hive 所建立的資料表會存放在 Hive 目錄中。 此行為不同於 Hive 和 Spark 會共用相同目錄的 HDInsight 3.6。 HDInsight 4.0 中的 Hive 和 Spark 整合須仰賴 Hive Warehouse Connector (HWC)。 HWC 是 Spark 與 Hive 之間的連絡管道。 [瞭解 Hive 倉儲連接器](../hdinsight/interactive-query/apache-hive-warehouse-connector.md)。

* 在 HDInsight 4.0 中，如果您想要在 Hive 與 Spark 之間共用中繼存放區，您可以將 metastore.catalog.default 屬性變更為 Spark 叢集中的登錄區。 您可以在 Ambari 進階 spark2-hive-site-override 中找到此屬性。 請務必了解，中繼存放區的共用僅適用於外部登錄區資料表，如果您具有內部/受控登錄區資料表或 ACID 資料表，則無法共用。  

## <a name="apache-oozie-metastore"></a>Apache Oozie 中繼存放區

Apache Oozie 是一個可管理 Hadoop 作業的工作流程協調系統。 Oozie 支援 Apache MapReduce、Pig、Hive 等等的 Hadoop 作業。  Oozie 會使用中繼存放區來儲存工作流程的相關詳細資料。 為提升使用 Oozie 時的效能，您可以使用 Azure SQL Database 作為自訂中繼存放區。 中繼存放區會在您刪除叢集之後，提供 Oozie 作業資料的存取權。

如需有關使用 Azure SQL Database 來建立 Oozie 中繼存放區的指示，請參閱[使用 Apache Oozie 來處理工作流程](hdinsight-use-oozie-linux-mac.md)。

## <a name="custom-ambari-db"></a>自訂 Ambari DB

若要使用您自己的外部資料庫搭配 Apache Ambari on HDInsight，請參閱[自訂 Apache Ambari 資料庫](hdinsight-custom-ambari-db.md)。

## <a name="next-steps"></a>後續步驟

* [使用 Apache Hadoop、Apache Spark、Apache Kafka 及其他工具在 HDInsight 中設定叢集](./hdinsight-hadoop-provision-linux-clusters.md)
