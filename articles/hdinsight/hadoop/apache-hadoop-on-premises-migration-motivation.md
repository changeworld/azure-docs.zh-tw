---
title: 優點：將內部部署 Apache Hadoop 遷移至 Azure HDInsight
description: 了解將內部部署 Hadoop 叢集遷移到 Azure HDInsight 的動機和優點。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 11/15/2019
ms.openlocfilehash: b975ee8cebdac3855c3fca88b20c01145d11baeb
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86080188"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>將內部部署 Apache Hadoop 叢集遷移到 Azure HDInsight - 動機和優點

本文是系列文章中的第一篇，此系列描述將內部 Apache Hadoop 叢集遷移到 Azure HDInsight 的最佳做法。 此系列文章適合負責 Azure HDInsight 中 Apache Hadoop 解決方案的設計、部署和移轉的人員。 能從這些文章中受益的角色包括雲端架構設計人員、Hadoop 系統管理員和 DevOps 工程師。 軟體開發人員、資料工程師與資料科學家應該也能從不同類型的叢集在雲端之運作方式的說明受益。

## <a name="why-to-migrate-to-azure-hdinsight"></a>為什麼要遷移到 Azure HDInsight

Azure HDInsight 是 Hadoop 元件的雲端發行版。 Azure HDInsight 可輕鬆快速地處理大量資料，同時節省成本。 HDInsight 包含最熱門的開放原始碼架構，例如：

- Apache Hadoop
- Apache Spark
- 含 LLAP 的 Apache Hive
- Apache Kafka
- Apache Storm
- Apache HBase (英文)
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Azure HDInsight 的優勢勝過內部部署 Hadoop

- **低成本** - 可以[依照需求建立群集](../hdinsight-hadoop-create-linux-clusters-adf.md)以降低成本，且只支付您使用的部分。 分離計算和儲存體可以提供更多彈性，因為資料量是獨立於叢集大小之外。

- **自動建立叢集** - 自動建立叢集需要最少的安裝和設定。 自動化可以用於隨選叢集。

- **受控硬體和設定** - 不需要擔心 HDInsight 叢集的實體硬體或基礎結構。 只要指定的叢集的設定，Azure 就會設定它。

- **輕鬆地擴充** - HDInsight 可讓您 [調整](../hdinsight-administer-use-portal-linux.md)工作負載，將它相應增加或減少。 Azure 會負責資料轉散發和重新平衡工作負載，而不會中斷資料處理作業。

- **全球可用性**-HDInsight 提供的[區域](https://azure.microsoft.com/regions/services/)比其他任何海量資料分析提供的多。 Azure HDInsight 也會適用於 Azure Government、中國和德國，可讓您符合您在重要主權區域中的企業需求。

- **安全與合規性** - HDInsight 可讓您使用  [Azure 虛擬網路](../hdinsight-plan-virtual-network-deployment.md)、 [加密](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)，以及與  [Azure Active Directory](../domain-joined/hdinsight-security-overview.md) 整合來保護企業資料資產。 HDInsight 也符合最熱門的產業和政府 [合規性標準](https://azure.microsoft.com/overview/trusted-cloud)。

- **簡化版本管理**-Azure HDInsight 管理 Hadoop 生態系統元件的版本，並將其保持在最新狀態。 軟體更新對於內部部署通常是複雜的程序。

- 針對**特定工作負載優化的小型叢集，元件之間**的相依性較少-典型的內部部署 Hadoop 安裝程式會使用可提供許多用途的單一叢集。 使用 Azure HDInsight 可以建立工作負載特定的叢集。 針對特定工作負載建立叢集，可移除維護單一且複雜度不斷增加之叢集的複雜性。

- **生產力** - 您可以在慣用的開發環境中使用各種適用於 Hadoop 和 Spark 的工具。

- **使用自訂工具或協力廠商應用程式的擴充性** - HDInsight 叢集可以使用安裝的元件來擴充，並從 Azure Marketplace 使用[單鍵](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) 部署來與其他巨量資料解決方案整合。

- **輕鬆管理、管理和監視**-Azure HDInsight 與 [Azure 監視器記錄](../hdinsight-hadoop-oms-log-analytics-tutorial.md)整合，   以提供單一介面讓您監視所有的叢集。

- **與其他 Azure 服務整合** - HDInsight 可輕鬆地與其他熱門 Azure 服務整合，如下列服務：

    - Azure Data Factory (ADF)
    - Azure Blob 儲存體
    - Azure Data Lake Storage Gen2
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- **自我修復程序和元件** - HDInsight 使用自有的監視基礎結構，持續檢查基礎結構和開放原始碼元件。 它也會自動復原重大失敗，例如無法取得開放原始碼元件和節點。 如果任何 OSS 元件失敗，就會在 Ambari 中觸發警示。

如需詳細資訊，請參閱[什麼是 Azure HDInsight 和 Apache Hadoop 技術堆疊](../hadoop/apache-hadoop-introduction.md)一文。

## <a name="migration-planning-process"></a>移轉規劃程序

下列是規劃內部部署 Hadoop 叢集移轉到 Azure HDInsight 的建議步驟：

1. 了解目前的內部部署和拓撲。
2. 了解目前的專案範圍、時間表及團隊的專業知識。
3. 了解 Azure 需求。
4. 根據最佳做法建立詳細計劃。

## <a name="gathering-details-to-prepare-for-a-migration"></a>收集詳細資料準備移轉

本節提供範本問卷，以協助收集下列重要資訊：

- 內部部署
- 專案詳細資料
- Azure 需求

### <a name="on-premises-deployment-questionnaire"></a>內部部署問卷

| **問題** | **範例** | **回答** |
|---|---|---|
|**主題**：**環境**|||
|叢集散發版本|HDP 2.6.5、CDH 5.7|
|巨量資料生態系統元件|HDFS、Yarn、Hive、LLAP、Impala、Kudu、HBase、Spark、MapReduce、Kafka、Zookeeper、Solr、Sqoop、Oozie、Ranger、Atlas、Falcon、Zeppelin、R|
|叢集類型|Hadoop、Spark、Confluent Kafka、Storm、Solr|
|叢集數目|4|
|主要節點數目|2|
|背景工作節點數目|100|
|邊緣節點數目| 5|
|磁碟空間總計|100 TB|
|主節點設定|m/y、cpu、disk 等|
|資料節點設定|m/y、cpu、disk 等|
|邊緣節點設定|m/y、cpu、disk 等|
|是否使用 HDFS 加密？|是|
|高可用性|HDFS HA、中繼存放區 HA|
|嚴重損壞修復/備份|是否備份叢集？|  
|相依於叢集的系統|SQL Server、Teradata、Power BI、MongoDB|
|協力廠商整合|Tableau，GridGain、Qubole、Informatica、Splunk|
|**主題**：**安全性**|||
|周邊安全性|防火牆|
|叢集驗證與授權|Active Directory、Ambari、Cloudera Manager、沒有驗證|
|HDFS 存取控制|  手動、ssh 使用者|
|Hive 驗證與授權|Sentry、LDAP、AD 搭配 Kerberos、Ranger|
|稽核|Ambari、Cloudera Navigator、Ranger|
|監視|Graphite、collectd、statsd、Telegraf、InfluxDB|
|警示|Kapacitor、Prometheus、Datadog|
|資料保留期間| 3 年、5 年|
|叢集系統管理員|單一系統管理員、多個系統管理員|

### <a name="project-details-questionnaire"></a>專案詳細資料問卷

|**問題**|**範例**|**回答**|
|---|---|---|
|**主題**：**工作負載和頻率**|||
|MapReduce 工作|10 個作業 -- 每天兩次||
|Hive 作業|100 個作業 -- 每小時||
|Spark 批次作業|50 個作業 -- 每 15 分鐘||
|Spark 串流作業|5 個作業 -- 每 3 分鐘||
|結構化串流作業|5 個工作 -- 每分鐘||
|ML 模型定型作業|2 個工作 -- 每週一次||
|程式語言：|Python、Scala、Java||
|指令碼|殼層、Python||
|**主題**：**資料**|||
|資料來源|一般檔案、Json、Kafka、RDBMS||
|資料協調流程|Oozie 工作流程、Airflow||
|記憶體內查閱|Apache Ignite、Redis||
|資料目的地|HDFS、RDBMS、Kafka、MPP ||
|**主題**：**中繼資料**|||
|Hive DB 類型|Mysql、Postgres||
|Hive 中繼存放區的數目|2||
|Hive 資料表的數目|100||
|Ranger 原則的數目|20||
|Oozie 工作流程的數目|100||
|**主題**：**調整**|||
|包含複寫的資料磁碟區|100 TB||
|每日擷取量|50 GB||
|資料成長率|每年 10%||
|叢集節點的成長率|每年 5%
|**主題**：**叢集使用量**|||
|平均 CPU 使用量 %|60%||
|平均記憶體使用量 %|75%||
|磁碟空間使用量|75%||
|平均網路使用量 %|25%
|**主題**：**員工**|||
|系統管理員數目|2||
|開發人員人數|10||
|終端使用者數目|100||
|技術|Hadoop、Spark||
|適用于遷移工作的可用資源數目|2||
|**主題**：**限制**|||
|目前的限制|高延遲||
|目前的挑戰|並行處理的問題||

### <a name="azure-requirements-questionnaire"></a>Azure 需求問卷

|**主題**：**基礎結構** |||
|---|---|---|
|**問題**|**範例**|**回答**|
| 慣用區域|美國東部||
|是否慣用 VNet？|是||
|是否需要 HA / DR？|是||
|是否與其他雲端服務整合？|ADF、CosmosDB||
|**主題**：**資料移動**  |||
|初始載入喜好設定|DistCp、Data box、ADF、WANDisco||
|資料傳輸差異|DistCp、AzCopy||
|持續增量的資料傳輸|DistCp、Sqoop||
|**主題**：**監視與警示** |||
|使用 Azure 監視器與警示或整合協力廠商監視|使用 Azure 監視器與警示||
|**主題**：**安全性喜好設定** |||
|私人且受保護的資料管線？|是||
|是否使用網域加入叢集 (ESP)？|     是||
|是否將內部部署 AD 同步處理至雲端？|     是||
|要同步處理的 AD 使用者數目？|          100||
|是否將密碼同步套雲端？|    是||
|僅雲端使用者？|                 是||
|是否需要 MFA？|                       No|| 
|是否符合資料授權需求？|  是||
|是否使用角色型存取控制？|        是||
|是否需要稽核？|                  是||
|是否使用待用資料加密？|          是||
|是否使用傳輸中資料加密？|       是||
|**主題**：**架構重新設計喜好設定** |||
|單一叢集或特定叢集類型|特定叢集類型||
|共置儲存體或遠端儲存體？|遠端儲存體||
|是否因資料儲存在遠端，所以叢集大小較小？|較小的叢集大小||
|是否使用多個較小的叢集而不是單一大型叢集？|使用多個較小的叢集||
|是否使用遠端中繼存放區？|是||
|是否在不同叢集之間共用中繼存放區？|是||
|是否將工作負載解構？|使用 Spark 作業取代 Hive 作業||
|是否使用 ADF 資料協調流程？|否||

## <a name="next-steps"></a>後續步驟

閱讀此系列中的下一篇文章：

- [從內部部署移轉至 Azure HDInsight Hadoop 的架構最佳做法](apache-hadoop-on-premises-migration-best-practices-architecture.md)
