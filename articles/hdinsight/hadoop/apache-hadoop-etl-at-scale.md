---
title: 大規模擷取、轉換和載入 (ETL) - Azure HDInsight
description: 瞭解如何在 HDInsight 中搭配 Apache Hadoop 使用解壓縮、轉換和載入。
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: ee989ccbb2e441256bec71781c538c7761fc7b88
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232235"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>大規模擷取、轉換和載入 (ETL)

「解壓縮」、「轉換」和「載入」（ETL）是從各種來源取得資料的程式。 在標準位置中收集、清除和處理。 最後會載入到可供查詢的資料存放區。 傳統 ETL 會處理匯入資料、適當地清理該資料，然後將其儲存在關聯式資料引擎中。 有了 HDInsight，各種 Apache Hadoop 環境元件都支援大規模的 ETL。

ETL 程序中的 HDInsight 使用可以由以下管線來摘要說明：

![HDInsight ETL 大規模總覽](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

下列各節會探索每一個 ETL 階段及其相關元件。

## <a name="orchestration"></a>協調流程

協調流程會跨 ETL 管線的所有階段。 HDInsight 中的 ETL 作業通常涉及數個彼此搭配運作的不同產品。  您可以使用 Hive 來清理資料的某個部分，而使用 Pig 來清理另一個部分。  您可以使用 Azure Data Factory 將資料從 Azure Data Lake Store 載入至 Azure SQL Database。

若要在適當的時間執行適當的作業，則需要協調流程。

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie 是一個可管理 Hadoop 作業的工作流程協調系統。 Oozie 會在 HDInsight 叢集內執行，並與 Hadoop 堆疊整合。 Oozie 支援 Apache Hadoop MapReduce、Apache Pig、Apache Hive 及 Apache Sqoop 的 Hadoop 作業。 Oozie 也可用來排定系統特定作業，例如 Java 程式或 Shell 指令碼。

如需詳細資訊，請參閱[在 HDInsight 上搭配 Apache Hadoop 使用 Apache Oozie 來定義和執行工作流程](../hdinsight-use-oozie-linux-mac.md)。 另請參閱[讓資料管線](../hdinsight-operationalize-data-pipeline.md)。

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory 以平台即服務的形式提供協調流程功能。 這是一項雲端式資料整合服務，可讓您在雲端中建立資料驅動的工作流程。 協調和自動化資料移動和資料轉換的工作流程。

使用 Azure Data Factory 時，您可以：

1. 建立並排定資料驅動工作流程 (稱為管線)，以從不同的資料存放區擷取資料。
2. 使用計算服務（例如 Azure HDInsight Hadoop）來處理和轉換資料。 或 Spark、Azure Data Lake Analytics、Azure Batch 和 Azure Machine Learning。
3. 將輸出資料發佈至資料存放區，例如 Azure SQL 資料倉儲，讓商業智慧 (BI) 應用程式取用。

如需有關 Azure Data Factory 的詳細資訊，請參閱[文件](../../data-factory/introduction.md)。

## <a name="ingest-file-storage-and-result-storage"></a>擷取檔案儲存體和結果儲存體

來源資料檔通常會載入至 Azure 儲存體或 Azure Data Lake Storage 中的位置。 檔案可以是任何格式，但通常是一般檔案，例如 Csv。

### <a name="azure-storage"></a>Azure 儲存體

Azure 儲存體具有特定的適應性目標。 請參閱[Blob 儲存體的擴充性和效能目標](../../storage/blobs/scalability-targets.md)。 對大多數分析節點來說，當處理許多較小的檔案時，Azure 儲存體的延展性最佳。  Azure 儲存體保證相同的效能，無論檔案有多大（只要您在限制內）。  這表示您可以儲存數 tb，而且仍然會取得一致的效能，不論您是使用資料的子集或所有資料。

Azure 儲存體有數種不同類型的 Blob。  「附加 Blob」** 是儲存 Web 記錄或感應器資料的絕佳選項。  

多個 blob 可以分散到多部伺服器，以相應放大它們的存取權。 但是單一的 blob 只能由單一伺服器提供服務。 雖然 Blog 可以在 Blog 容器中邏輯分組，但這種分組方式的意涵卻非分割。

Azure 儲存體也有一個用於 Blob 儲存體的 WebHDFS API 層。  HDInsight 中的所有服務都可以存取 Azure Blob 儲存體中的檔案，以進行資料清理和資料處理。 與這些服務使用 Hadoop 分散式檔案系統（HDFS）的方式類似。

通常會使用 PowerShell、Azure 儲存體 SDK 或 AZCopy 將資料擷取至 Azure 儲存體。

### <a name="azure-data-lake-storage"></a>Azure Data Lake 儲存體

Azure Data Lake Storage （ADLS）是受控的超大規模資料庫存放庫。 與 HDFS 相容之分析資料的存放庫。  ADLS 會使用類似于 HDFS 的設計範例。 ADLS 為總容量和個別檔案的大小提供無限制的適應性。 ADLS 在處理大型檔案時很好用，因為大型檔案可以儲存在多個節點上。  分割 ADLS 中的資料是在幕後執行的。  您會獲得大規模輸送量，而可以使用數千個並行執行程式來執行分析作業，有效率地讀取和寫入數百 TB 的資料。

資料通常會使用 Azure Data Factory 內嵌到 ADLS 中。 或 ADLS Sdk、AdlCopy Service、Apache DistCp 或 Apache Sqoop。  要使用這當中的哪些服務大部分取決於資料的所在位置。  如果資料目前在現有的 Hadoop 叢集中，您可以使用 Apache DistCp、AdlCopy 服務或 Azure Data Factory。  對於 Azure Blob 儲存體中的資料，您可以使用 Azure Data Lake Storage .NET SDK、Azure PowerShell 或 Azure Data Factory。

ADLS 還針對使用「Azure 事件中樞」或 Apache Storm 來執行的事件擷取進行了最佳化。

#### <a name="considerations-for-both-storage-options"></a>兩種儲存體選項的考量

如果上傳的資料集達到數 TB，網路延遲可能會是主要問題，尤其是當資料來自內部部署位置時。  在這類情況下，您可以使用下列選項：

* Azure ExpressRoute：Azure ExpressRoute 可讓您在 Azure 資料中心與內部部署的基礎結構之間建立私人連線。 這些連線為傳輸大量資料提供一個可靠的選項。 如需詳細資訊，請參閱 [Azure ExpressRoute 文件](../../expressroute/expressroute-introduction.md)。

* 「離線」上傳資料。 您可以使用 [Azure 匯入/匯出服務](../../storage/common/storage-import-export-service.md)，將含有您資料的硬碟送到 Azure 資料中心。 您的資料會先上傳到 Azure 儲存體 Blob。 接下來，您可以使用 Azure Data Factory 或 AdlCopy 工具，將資料從 Azure 儲存體 Blob 複製到 Data Lake Storage。

### <a name="azure-sql-data-warehouse"></a>Azure SQL 資料倉儲

Azure SQL DW 是儲存備妥結果的絕佳選擇。  Azure HDInsight 可用來針對 Azure SQL DW 執行這些服務。

「Azure SQL 資料倉儲」(SQL DW) 是一個已針對分析工作負載最佳化的關聯式資料庫。  Azure SQL DW 會根據分割資料表調整規模。  資料表可以跨多個節點進行分割。  在建立 Azure SQL DW 節點時便會選取節點。  您可以在事後調整節點規模，但這是一個可能需要移動資料的作用中程序。 如需詳細資訊，請參閱[SQL 資料倉儲-管理計算](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)。

### <a name="apache-hbase"></a>Apache HBase (英文)

Apache HBase 是 Azure HDInsight 中提供的索引鍵/值存放區。  Apache HBase 是開放原始碼的 NoSQL 資料庫，其建置於 Hadoop 上並模仿 Google BigTable。 HBase 針對大量非結構化和半結構化資料提供高效能的隨機存取和強式一致性。 依資料行系列組織的無架構資料庫中的資料。

資料儲存在資料表的資料列中，而資料列中的資料會依據資料行系列進行分組。 HBase 是無結構描述資料庫。 儲存在其中的資料行和資料類型不需要先定義，就能使用它們。 開放原始碼的程式碼會以線性方式延展，以處理數千個節點上的 PB 資料。 HBase 可以依賴 Hadoop 環境中的分散式應用程式所提供的資料冗余、批次處理和其他功能。

HBase 是要日後進行分析之感應器和記錄資料絕佳目的地。

HBase 適應性取決於 HDInsight 叢集中的節點數目。

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL Database 與 Azure 資料庫

Azure 以平台即服務 (PAAS) 的形式提供三種不同的關聯式資料庫。

* [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) 是 Microsoft SQL Server 的實作。 如需有關效能的詳細資訊，請參閱[微調 Azure SQL Database 中的資料庫效能](../../sql-database/sql-database-performance-guidance.md)。
* [適用於 MySQL 的 Azure 資料庫](../../mysql/overview.md) 是 Oracle MySQL 的實作。
* [適用於 PostgreSQL 的 Azure 資料庫](../../postgresql/quickstart-create-server-database-portal.md) 是 PostgreSQL 的實作。

這些產品會相應增加，這表示它們是藉由新增更多 CPU 和記憶體來進行調整。  您也可以選擇使用進階磁碟搭配這些產品來獲得較佳的 I/O 效能。

## <a name="azure-analysis-services"></a>Azure Analysis Services

Azure Analysis Services （.AAS）是用於決策支援和商務分析的分析資料引擎。 .AAS 供應商務報表和用戶端應用程式（例如 Power BI）的分析資料。 此外，Excel、Reporting Services 報表和其他資料視覺效果工具。

您可以變更每個個別分析 Cube 的階層來調整這些 Cube。  如需詳細資訊，請參閱 [Azure Analysis Services 價格](https://azure.microsoft.com/pricing/details/analysis-services/)。

## <a name="extract-and-load"></a>擷取和載入

在資料存在於 Azure 中之後，您便可以使用許多服務來擷取它並載入至其他產品。  HDInsight 支援 Sqoop 和 Flume。

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop 是一個專為在結構化、半結構化及非結構化資料來源之間有效率地傳輸資料而設計的工具。

Sqoop 使用 MapReduce 來匯入和匯出資料，可提供平行作業和容錯功能。

### <a name="apache-flume"></a>Apache Flume

`Apache Flume`是一種分散式、可靠且可用的服務，可有效率地收集、匯總和移動大量記錄資料。 Flume 具有以資料流程資料流程為基礎的彈性架構。 Flume 提供可微調的可靠性機制及許多容錯移轉與復原機制，既健全又能容錯。 Flume 使用已將線上分析應用程式納入考量的簡單可延伸資料模型。

Apache Flume 不能與 Azure HDInsight 搭配使用。  內部部署 Hadoop 安裝可以使用 Flume，將資料傳送至 Azure 儲存體 Blob 或 Azure Data Lake Storage。  如需詳細資訊，請參閱[搭配 HDInsight 使用 Apache Flume](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/) \(英文\)。

## <a name="transform"></a>轉換

在資料存在於所選擇的位置中之後，您必須加以清理、結合或準備，才能用於特定使用模式。  Hive、Pig 及 Spark SQL 都是適用於該類工作的絕佳選擇。  這些都是 HDInsight 支援的。

## <a name="next-steps"></a>後續步驟

* [使用 Apache Hive 作為 ETL 工具](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [將資料從 Azure SQL Database 移至 Apache Hive 資料表](./apache-hadoop-use-sqoop-mac-linux.md)
