---
title: 大規模擷取、轉換和載入 (ETL) - Azure HDInsight
description: 了解如何在 HDInsight 中搭配 Apache Hadoop 使用擷取、轉換和載入。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: eb3c45c01b2e3ca1761e86f3ac991d67f7813856
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/07/2020
ms.locfileid: "89504310"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>大規模擷取、轉換和載入 (ETL)

擷取、轉換和載入 (ETL) 是從各種來源取得資料所使用的程序。 資料會在標準位置中收集、清除和處理。 最後，資料會載入到可供查詢的資料存放區。 傳統 ETL 會處理匯入資料、適當地清理該資料，然後將其儲存在關聯式資料引擎中。 使用 Azure HDInsight 時，各種 Apache Hadoop 環境元件都支援大規模執行 ETL。

在 ETL 程序中使用 HDInsight 由以下管線來摘要說明：

![HDInsight 大規模 ETL 概觀](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

下列各節會探索每一個 ETL 階段及其相關元件。

## <a name="orchestration"></a>協調流程

協調流程會跨 ETL 管線的所有階段。 HDInsight 中的 ETL 作業通常涉及數個彼此搭配運作的不同產品。 例如：

- 您可以使用 Apache Hive 清除部分資料，並使用 Apache Pig 清理另一部分。
- 您可以使用 Azure Data Factory 將資料從 Azure Data Lake Store 載入至 Azure SQL Database。

若要在適當的時間執行適當的作業，則需要協調流程。

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie 是一個可管理 Hadoop 作業的工作流程協調系統。 Oozie 會在 HDInsight 叢集內執行，並與 Hadoop 堆疊整合。 Oozie 支援 Apache Hadoop MapReduce、Pig、Hive 和 Sqoop 的 Hadoop 作業。 您可以使用 Oozie 排程系統專用的作業，例如 Java 程式或 Shell 指令碼。

如需詳細資訊，請參閱[使用 Apache Oozie 搭配 Apache Hadoop 在 HDInsight 上定義和執行工作流程](../hdinsight-use-oozie-linux-mac.md)。 另請參閱[使用資料管線](../hdinsight-operationalize-data-pipeline.md)。

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory 以平台即服務 (PaaS) 的形式提供協調流程功能。 Azure Data Factory 是以雲端為基礎的資料整合服務。 其可讓您建立資料驅動的工作流程，以便協調並自動進行資料移動和資料轉換。

使用 Azure Data Factory：

1. 建立和排程資料驅動的工作流程。 這些管線可從不同的資料存放區擷取資料。
1. 使用計算服務 (例如 HDInsight 或 Hadoop) 處理和轉換資料。 您也可以在此步驟中使用 Spark、Azure Data Lake Analytics、Azure Batch 或 Azure Machine Learning。
1. 將輸出資料發佈至資料存放區（例如 Azure Synapse Analytics），以供 BI 應用程式使用。

如需有關 Azure Data Factory 的詳細資訊，請參閱[文件](../../data-factory/introduction.md)。

## <a name="ingest-file-storage-and-result-storage"></a>擷取檔案儲存體和結果儲存體

來源資料檔通常會載入至 Azure 儲存體或 Azure Data Lake Storage 上的位置。 這些檔案通常是採用一般格式，例如 CSV。 但可以是任何格式。

### <a name="azure-storage"></a>Azure 儲存體

Azure 儲存體具有特定的適應性目標。 如需詳細資訊，請參閱 [Blob 儲存體的可擴縮性和效能目標](../../storage/blobs/scalability-targets.md)。 對大多數分析節點來說，當處理許多較小的檔案時，Azure 儲存體的延展性最佳。 只要是在您的帳戶限制範圍內，Azure 儲存體就可以保證不論檔案有多大，都能提供相同的效能。 您可以儲存數 TB 的資料，但仍能獲得一致的效能。 無論您使用的是資料子集還是所有資料，這個敘述皆成立。

Azure 儲存體有數種類型的 Blob。 「附加 Blob」是儲存 Web 記錄或感應器資料的絕佳選項。

您可以將多個 Blob 分散到許多伺服器，以便擴增對這些 Blob 的存取。 但是單一 Blob 只能由單一伺服器提供。 雖然 Blob 可以在 Blob 容器中邏輯分組，但這種分組方式在意涵上並非分割。

Azure 儲存體有一個用於 Blob 儲存體的 WebHDFS API 層。 所有 HDInsight 服務都可以存取 Azure Blob 儲存體中的檔案，以進行資料清除和資料處理。 這類似於那些服務使用 Hadoop 分散式檔案系統 (HDFS) 的方式。

系統通常會透過 PowerShell、Azure 儲存體 SDK 或 AZCopy，將資料擷取至 Azure 儲存體。

### <a name="azure-data-lake-storage"></a>Azure Data Lake 儲存體

Azure Data Lake Storage 是一個受管理的超大規模存放庫，適用於分析資料。 其使用類似於 HDFS 的設計範例並與其相容。 Data Lake Storage 為總容量和個別檔案的大小提供無限制的適應性。 其非常適合搭配大型檔案運作，因為大型檔案可以跨多個節點儲存。 分割 Data Lake Storage 中的資料是在幕後執行的。 您會獲得大規模輸送量，而可以使用數千個並行執行程式來執行分析作業，有效率地讀取和寫入數百 TB 的資料。

資料通常會透過 Azure Data Factory 擷取到 Data Lake Storage。 您也可以使用 Data Lake Storage SDK、AdlCopy 服務、Apache DistCp 或 Apache Sqoop。 您選擇的服務取決於資料的位置。 如果資料位於現有的 Hadoop 叢集中，您可以使用 Apache DistCp、AdlCopy 服務或 Azure Data Factory。 如果資料在 Azure Blob 儲存體中，則您可以使用 Azure Data Lake Storage .NET SDK、Azure PowerShell 或 Azure Data Factory。

Data Lake Storage 已針對事件擷取，透過 Azure 事件中樞或 Apache Storm 進行最佳化。

### <a name="considerations-for-both-storage-options"></a>兩種儲存體選項的考量

若要上傳 TB 範圍的資料集，網路延遲可能是一個主要的問題。 如果資料來自內部部署位置，這特別適用。 在此類情況下，您可以使用下列選項：

- **Azure ExpressRoute：** 在 Azure 資料中心與內部部署基礎結構之間建立私人連線。 這些連線為傳輸大量資料提供一個可靠的選項。 如需詳細資訊，請參閱 [Azure ExpressRoute 文件](../../expressroute/expressroute-introduction.md)。

- **從硬碟上傳資料：** 您可以使用 [Azure 匯入/匯出服務](../../storage/common/storage-import-export-service.md)，將含有您資料的硬碟送到 Azure 資料中心。 您的資料會先上傳到 Azure Blob 儲存體。 接下來，您可以使用 Azure Data Factory 或 AdlCopy 工具，將資料從 Azure Blob 儲存體複製到 Data Lake Storage。

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Azure Synapse Analytics 是儲存備妥結果的適當選擇。 您可以使用 Azure HDInsight 來執行這些服務，以進行 Azure z) 的 Synapse 分析。

Azure Synapse Analytics 是針對分析工作負載優化的關係資料庫存放區。 其會根據分割資料表進行調整。 資料表可以跨多個節點進行分割。 在建立時便會選取節點。 您可以在事後調整節點規模，但這是一個可能需要移動資料的作用中程序。 如需詳細資訊，請參閱 [管理 Azure Synapse Analytics 中的計算](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)。

### <a name="apache-hbase"></a>Apache HBase (英文)

Apache HBase 是 Azure HDInsight 中提供的索引鍵/值存放區。 其為開放原始碼的 NoSQL 資料庫，建置在 Hadoop 的基礎之上，並仿照 Google BigTable 建立模型。 HBase 可針對大量非結構化及半結構化資料，提供高效能隨機存取功能和強式一致性。

HBase 是無結構描述資料庫，因此您不需要先定義資料行和資料類型，就可以使用。 資料儲存在資料表的資料列中，並依據資料行系列進行分組。

開放原始碼的程式碼會以線性方式延展，以處理數千個節點上的 PB 資料。 HBase 依賴 Hadoop 環境中分散式應用程式所提供的資料備援、批次處理及其他功能。

HBase 是要日後進行分析之感應器和記錄資料的絕佳目的地。

HBase 適應性取決於 HDInsight 叢集中的節點數目。

### <a name="azure-sql-databases"></a>Azure SQL 資料庫

Azure 提供三個 PaaS 關聯式資料庫：

* [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md) 是 Microsoft SQL Server 的實作。 如需有關效能的詳細資訊，請參閱[微調 Azure SQL Database 中的資料庫效能](../../azure-sql/database/performance-guidance.md)。
* [適用於 MySQL 的 Azure 資料庫](../../mysql/overview.md) 是 Oracle MySQL 的實作。
* [適用於 PostgreSQL 的 Azure 資料庫](../../postgresql/quickstart-create-server-database-portal.md) 是 PostgreSQL 的實作。

新增更多 CPU 和記憶體，以擴大這些產品。  您也可以選擇使用進階磁碟搭配這些產品來獲得較佳的 I/O 效能。

## <a name="azure-analysis-services"></a>Azure Analysis Services

Azure Analysis Services 是用於決策支援和商務分析的分析資料引擎。 其針對商務報表和用戶端應用程式 (例如 Power BI) 提供分析資料。 分析資料也可以搭配 Excel、SQL Server Reporting Services 報表和其他資料視覺效果工具使用。

變更每個個別分析 Cube 的階層可調整這些 Cube。 如需詳細資訊，請參閱 [Azure Analysis Services 價格](https://azure.microsoft.com/pricing/details/analysis-services/)。

## <a name="extract-and-load"></a>擷取和載入

在資料存在於 Azure 中之後，您便可以使用許多服務加以擷取，並將其載入至其他產品。 HDInsight 支援 Sqoop 和 Flume。

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop 是一個專為在結構化、半結構化及非結構化資料來源之間有效率地傳輸資料而設計的工具。

Sqoop 使用 MapReduce 來匯入和匯出資料，可提供平行作業和容錯功能。

### <a name="apache-flume"></a>Apache Flume

Apache Flume 是一個分散式、可靠且可用的服務，可有效率地收集、彙總及移動大量的記錄資料。 其彈性架構是以串流資料流程為基礎。 Flume 提供可微調的可靠性機制，既健全又能容錯。 其有許多容錯移轉和復原機制。 Flume 使用已將線上分析應用程式納入考量的簡單可延伸資料模型。

Apache Flume 無法搭配 Azure HDInsight 使用。 但是，內部部署 Hadoop 安裝可以使用 Flume，將資料傳送至 Azure Blob 儲存體或 Azure Data Lake Storage。 如需詳細資訊，請參閱[搭配 HDInsight 使用 Apache Flume](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/) \(英文\)。

## <a name="transform"></a>轉換

在資料存在於所選擇的位置中之後，您必須加以清理、結合或準備，才能用於特定使用模式。 Hive、Pig 及 Spark SQL 都是適用於該類工作的絕佳選擇。 HDInsight 支援所有這些產品。

## <a name="next-steps"></a>後續步驟

- [使用 Apache Hive 作為 ETL 工具](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
- [搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
- [將資料從 Azure SQL Database 移至 Apache Hive 資料表](./apache-hadoop-use-sqoop-mac-linux.md)
