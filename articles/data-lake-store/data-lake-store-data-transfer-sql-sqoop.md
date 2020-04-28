---
title: 在 Data Lake Storage Gen1 和 Azure SQL 之間複製資料-Sqoop |Microsoft Docs
description: 使用 Sqoop 在 Azure SQL Database 和 Azure Data Lake Storage Gen1 之間複製資料
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: cf3893706afcb4c4cc5b90dd3d2431ecedc71d0a
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "73839068"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>使用 Sqoop 在 Data Lake Storage Gen1 和 Azure SQL Database 之間複製資料

了解如何使用 Apache Sqoop 在 Azure SQL Database 和 Azure Data Lake Storage Gen1 之間匯入及匯出資料。

## <a name="what-is-sqoop"></a>什麼是 Sqoop？

巨量資料應用程式是處理非結構化和半結構化資料 (例如記錄和檔案)，很自然的一個選擇。 不過，您可能也需要處理儲存在關係資料庫中的結構化資料。

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) 是一個專門設計來在關聯式資料庫和巨量資料儲存機制 (例如 Azure Data Lake Storage Gen1) 之間傳送資料的工具。 您可以使用它從像是 Azure SQL Database 這類的關聯式資料庫管理系統 (RDBMS)，匯入資料至 Azure Data Lake Storage Gen1。 然後，您可以使用大型資料工作負載來轉換和分析資料，然後將資料匯出回 RDBMS。 在本文中，您會使用 Azure SQL 資料庫作為您的關係資料庫，以匯入/匯出。

## <a name="prerequisites"></a>Prerequisites

開始之前，您必須具備下列條件：

* **Azure 訂**用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Storage Gen1 帳戶**。 如需有關如何建立帳戶的指示，請參閱[開始使用 Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* 可存取 Data Lake Storage Gen1 帳戶的 **Azure HDInsight 叢集**。 請參閱[建立搭配 Data Lake Storage Gen1 的 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-portal.md)。 本文假設您已使用 Azure Data Lake Storage Gen1 存取 HDInsight Linux 叢集。
* **Azure SQL Database**。 如需建立方式的指示，請參閱 [建立 Azure SQL 資料庫](../sql-database/sql-database-get-started.md)

## <a name="create-sample-tables-in-the-azure-sql-database"></a>在 Azure SQL 資料庫中建立範例資料表

1. 若要開始，請在 Azure SQL 資料庫中建立兩個範例資料表。 使用[SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md)或 Visual Studio 連接到資料庫，然後執行下列查詢。

    **建立 Table1**

       CREATE TABLE [dbo].[Table1](
       [ID] [int] NOT NULL,
       [FName] [nvarchar](50) NOT NULL,
       [LName] [nvarchar](50) NOT NULL,
        CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
       ) ON [PRIMARY]
       GO

    **建立 Table2**

       CREATE TABLE [dbo].[Table2](
       [ID] [int] NOT NULL,
       [FName] [nvarchar](50) NOT NULL,
       [LName] [nvarchar](50) NOT NULL,
        CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
       ) ON [PRIMARY]
       GO

1. 執行下列命令，將一些範例資料新增至**Table1**。 保留 **Table2** 空白。 稍後，您會將資料從**Table1**匯入 Data Lake Storage Gen1。 然後，您會將資料從 Data Lake Storage Gen1 匯出至**Table2**。

       INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>從使用 Data Lake Storage Gen1 存取的 HDInsight Linux 叢集使用 Sqoop

HDInsight 叢集已有可用的 Sqoop 套件。 如果您已將 HDInsight 叢集設定為使用 Data Lake Storage Gen1 做為額外的儲存體，您可以使用 Sqoop （不需要任何設定變更）在關係資料庫（例如 Azure SQL Database）與 Data Lake Storage Gen1 帳戶之間匯入/匯出資料。

1. 在本文中，我們假設您已建立 Linux 叢集，因此您應該使用 SSH 連接到叢集。 請參閱 [連線至以 Linux 為基礎的 HDInsight 叢集](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。

1. 請確認您是否可從叢集存取 Data Lake Storage Gen1 帳戶。 從 SSH 提示字元執行下列命令：

       hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

   此命令提供 Data Lake Storage Gen1 帳戶中的檔案/資料夾清單。

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>從 Azure SQL Database 將資料匯入至 Data Lake Storage Gen1

1. 瀏覽至提供 Sqoop 封裝的目錄。 一般而言，這個位置是`/usr/hdp/<version>/sqoop/bin`。

1. 從 **Table1** 將資料匯入至 Data Lake Storage Gen1。 使用下列語法：

       sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

   [ **Sql-資料庫-伺服器名稱**] 預留位置代表執行 Azure sql 資料庫的伺服器名稱。 **sql-database-name** 預留位置代表實際的資料庫名稱。

   例如，

       sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. 請確認資料已被傳輸至 Data Lake Storage Gen1 帳戶。 執行以下命令：

       hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/

   您應該會看見下列輸出。

       -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
       -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
       -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
       -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
       -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

   每個**元件-m-*** 檔案會對應至來源資料表**Table1**中的資料列。 您可以檢視 part-m-* 檔案的內容來確認。

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>從 Data Lake Storage Gen1 將資料匯出到 Azure SQL Database

1. 從 Data Lake Storage Gen1 帳戶將資料匯出到 Azure SQL Database 中的空白資料表 **Table2**。 使用下列語法。

       sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

   例如，

       sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. 確認資料已上傳至 SQL Database 資料表。 使用 [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) 或 Visual Studio 連接至 Azure SQL Database，然後執行下列查詢。

       SELECT * FROM TABLE2

   此命令應該具有下列輸出。

        ID  FName    LName
       -------------------
       1    Neal     Kell
       2    Lila     Fulton
       3    Erna     Myers
       4    Annette  Simpson

## <a name="performance-considerations-while-using-sqoop"></a>使用 Sqoop 時的效能考量

如需調整 Sqoop 作業以將資料複製到 Data Lake Storage Gen1 之效能的相關資訊，請參閱[Sqoop 效能 blog 文章](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)。

## <a name="next-steps"></a>後續步驟

* [將資料從 Azure 儲存體 Blob 複製到 Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [保護 Data Lake Storage Gen1 中的資料](data-lake-store-secure-data.md)
* [搭配 Data Lake Storage Gen1 使用 Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [搭配 Data Lake Storage Gen1 使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)
