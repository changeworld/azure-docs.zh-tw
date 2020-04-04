---
title: 使用外部中繼資料存放區 - Azure HDInsight
description: 使用具有 Azure HDInsight 群集的外部元數據存儲。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/03/2020
ms.openlocfilehash: 0cadf3930008868fe223e6e1024a2d14d17d8131
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657112"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>在 Azure HDInsight 中使用外部中繼資料存放區

HDInsight 允許您透過外部資料儲存控制數據和中繼資料。 此功能可用於[阿帕契蜂巢元儲存](#custom-metastore),[阿帕奇烏齊元儲存](#apache-oozie-metastore),和[阿帕契巴里資料庫](#custom-ambari-db)。

HDInsight 中的 Apache Hive 中繼存放區是 Apache Hadoop 架構不可或缺的一部分。 元存儲是中央架構存儲庫。 元存儲由其他大數據訪問工具(如 Apache Spark、互動式查詢 (LLAP)、Presto 或 Apache Pig)使用。 HDInsight 使用 Azure SQL Database 作為 Hive 中繼存放區。

![HDInsight Hive 中繼資料存放區架構](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

您有兩種方式可以為您的 HDInsight 叢集設定中繼存放區：

* [預設中繼存放區](#default-metastore)
* [自訂中繼存放區](#custom-metastore)

## <a name="default-metastore"></a>預設中繼存放區

根據預設，HDInsight 會以每個叢集類型來建立中繼存放區。 您可以改為指定自訂中繼存放區。 預設中繼存放區包含下列考量：

* 沒有其他成本。 HDInsight 會以每個叢集類型來建立中繼存放區，但不會對您產生額外成本。

* 每個預設中繼存放區是叢集生命週期的一部分。 當您刪除叢集時，相應的中繼存放區和中繼資料會一併刪除。

* 不能與其他群集共用預設元存儲。

* 預設中繼存放區會使用基本的 Azure SQL DB，它具有五個 DTU (資料庫交易單位) 限制。
此預設元存儲通常用於相對簡單的工作負載。 不需要多個群集且不需要在群集生命周期之後保留元數據的工作負載。

## <a name="custom-metastore"></a>自訂中繼存放區

HDInsight 也支援自訂中繼存放區，這是針對生產叢集建議的中繼存放區：

* 您將自己的 Azure SQL Database 指定為中繼存放區。

* 元存儲的生命週期不綁定到群集生命週期,因此您可以創建和刪除群集,而不會丟失元數據。 即使您刪除並重新建立 HDInsight 叢集之後，中繼資料 (例如您的 Hive 結構描述) 仍會保存。

* 自訂中繼存放區可讓您將多個叢集與叢集類型連結至該中繼存放區。 例如，單一中繼存放區可以在 HDInsight 中的互動式查詢、Hive 和 Spark 叢集之間共用。

* 您需要根據您選擇的效能層級支付中繼存放區 (Azure SQL DB) 的成本。

* 您可以視需要相應增加中繼存放區。

* 群集和外部元存儲必須託管在同一區域中。

![HDInsight Hive 中繼資料存放區使用案例](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>為自訂中儲存建立及設定 Azure SQL 資料庫

在為 HDInsight 群集設置自訂 Hive 儲存之前,創建或擁有現有的 Azure SQL 資料庫。  有關詳細資訊,請參閱[快速入門:在 Azure SQL DB 建立單個資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)。

配置 Azure SQL 資料庫防火牆規則以允許 Azure 服務和資源存取伺服器。 通過選擇 **「設定伺服器防火牆」** 在 Azure 門戶中啟用此選項。 然後選擇 **「在下面打開****允許 Azure 服務和資源存取 Azure** SQL 資料庫伺服器或資料庫的此伺服器」。 有關詳細資訊,請參閱[建立及管理 IP 防火牆規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

![設定伺服器防火牆按鈕](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![允許 Azure 服務存取](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>在叢集建立期間選取自訂中繼存放區

您可以隨時將群集指向以前創建的 Azure SQL 資料庫。 對於通過門戶創建群集,將從**存儲>元存儲設置中**指定該選項。

![HDInsight Hive 中繼資料存放區 Azure 入口網站](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>蜂巢元儲存指南

* 盡可能使用自訂中繼存放區，這樣有助於個別計算資源 (您的執行中叢集) 和中繼資料 (儲存在中繼存放區)。

* 從 S2 層開始，該層提供 50 個 DTU 和 250 GB 的儲存體。 如果您看到瓶頸，您可以相應增加資料庫。

* 如果您想要讓多個 HDInsight 叢集存取不同的資料，請針對每個叢集上的中繼存放區使用不同資料庫。 如果您在多個 HDInsight 叢集間共用中繼存放區，則表示這些叢集會使用相同的中繼資料和基礎使用者資料檔案。

* 定期備份您的自訂中繼存放區。 Azure SQL Database 會自動產生備份，但備份保留時間範圍有所不同。 如需詳細資訊，請參閱[了解自動 SQL Database 備份](../sql-database/sql-database-automated-backups.md)。

* 在同一區域中尋找您的元儲存和 HDInsight 叢集。 此配置將提供最高性能和最低的網路出口費用。

* 使用 Azure SQL 資料庫監視工具或 Azure 監視器日誌監視元儲存的性能和可用性。

* 當針對現有自定義元儲存資料庫創建新的更高版本的 Azure HDInsight 時,系統將升級元儲存的架構。 升級是不可逆的,無需從備份還原資料庫。

* 如果您在多個叢集間共用中繼存放區，請確定所有叢集都是相同的 HDInsight 版本。 不同的 Hive 版本會使用不同的中繼存放區資料庫結構描述。 例如,不能跨 Hive 2.1 和 Hive 3.1 版本群集共用元存儲。

* 在 HDInsight 4.0 中,Spark 和 Hive 使用獨立的目錄來訪問 SparkSQL 或 Hive 表。 Spark 創建的表位於Spark目錄中。 Hive 創建的表位於 Hive 目錄中。 此行為不同於 HDInsight 3.6,其中 Hive 和 Spark 共用通用目錄。 HDInsight 4.0 中的蜂巢和火花集成依賴於蜂巢倉庫連接器 (HWC)。 HWC是火花和蜂巢之間的橋樑。 [瞭解蜂巢主目錄連接器](../hdinsight/interactive-query/apache-hive-warehouse-connector.md)。

## <a name="apache-oozie-metastore"></a>阿帕奇烏齊元存儲

Apache Oozie 是一個可管理 Hadoop 作業的工作流程協調系統。 Oozie 支援 Apache MapReduce、Pig、Hive 等等的 Hadoop 作業。  Oozie 使用元存儲來存儲有關工作流的詳細資訊。 為提升使用 Oozie 時的效能，您可以使用 Azure SQL Database 作為自訂中繼存放區。 刪除群集后,元存儲提供對 Oozie 作業數據的訪問。

如需有關使用 Azure SQL Database 來建立 Oozie 中繼存放區的指示，請參閱[使用 Apache Oozie 來處理工作流程](hdinsight-use-oozie-linux-mac.md)。

## <a name="custom-ambari-db"></a>自訂 Ambari DB

要在 HDInsight 上使用自己的外部資料庫,請參閱[自訂阿帕奇 Ambari 資料庫](hdinsight-custom-ambari-db.md)。

## <a name="next-steps"></a>後續步驟

* [使用 Apache Hadoop、Apache Spark、Apache Kafka 及其他工具在 HDInsight 中設定叢集](./hdinsight-hadoop-provision-linux-clusters.md)
