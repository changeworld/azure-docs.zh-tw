---
title: 在資料存儲庫第 1 代和 Azure SQL 之間複製資料 - Sqoop |微軟文檔
description: 使用 Sqoop 在 Azure SQL Database 和 Azure Data Lake Storage Gen1 之間複製資料
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: cf3893706afcb4c4cc5b90dd3d2431ecedc71d0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839068"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>使用 Sqoop 在資料存儲庫第 1 代和 Azure SQL 資料庫之間複製資料

了解如何使用 Apache Sqoop 在 Azure SQL Database 和 Azure Data Lake Storage Gen1 之間匯入及匯出資料。

## <a name="what-is-sqoop"></a>什麼是 Sqoop？

巨量資料應用程式是處理非結構化和半結構化資料 (例如記錄和檔案)，很自然的一個選擇。 但是，您可能還需要處理存儲在關係資料庫中的結構化資料。

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) 是一個專門設計來在關聯式資料庫和巨量資料儲存機制 (例如 Azure Data Lake Storage Gen1) 之間傳送資料的工具。 您可以使用它從像是 Azure SQL Database 這類的關聯式資料庫管理系統 (RDBMS)，匯入資料至 Azure Data Lake Storage Gen1。 然後，您可以使用大資料工作負載轉換和分析資料，然後將資料匯出回 RDBMS。 在本文中，您將 Azure SQL 資料庫用作關係資料庫，以便從中導入/匯出。

## <a name="prerequisites"></a>Prerequisites

開始之前，您必須具備下列條件：

* **Azure 訂閱**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Storage Gen1 帳戶**。 有關如何創建帳戶的說明，請參閱[使用 Azure 資料存儲第 1 代](data-lake-store-get-started-portal.md)
* 可存取 Data Lake Storage Gen1 帳戶的 **Azure HDInsight 叢集**。 請參閱[建立搭配 Data Lake Storage Gen1 的 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-portal.md)。 本文假設您已使用 Azure Data Lake Storage Gen1 存取 HDInsight Linux 叢集。
* **Azure SQL 資料庫**。 如需建立方式的指示，請參閱 [建立 Azure SQL 資料庫](../sql-database/sql-database-get-started.md)

## <a name="create-sample-tables-in-the-azure-sql-database"></a>在 Azure SQL 資料庫中創建示例表

1. 首先，在 Azure SQL 資料庫中創建兩個示例表。 使用[SQL 伺服器管理工作室](../sql-database/sql-database-connect-query-ssms.md)或視覺化工作室連接到資料庫，然後運行以下查詢。

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

1. 運行以下命令以將一些示例資料添加到**表 1**。 保留 **Table2** 空白。 稍後，您將將資料從**表 1**導入到資料湖存儲 Gen1 中。 然後，您將將資料從資料存儲第 1 代匯出到**表 2**中。

       INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>從使用 Data Lake Storage Gen1 存取的 HDInsight Linux 叢集使用 Sqoop

HDInsight 叢集已有可用的 Sqoop 套件。 如果將 HDInsight 群集配置為使用 Data Lake 存儲 Gen1 作為附加存儲，則可以使用 Sqoop（無需任何配置更改）在關係資料庫（如 Azure SQL 資料庫）和資料存儲庫第 1 代帳戶之間導入/匯出資料。

1. 在本文中，我們假設您創建了一個 Linux 群集，因此您應該使用 SSH 連接到群集。 請參閱 [連線至以 Linux 為基礎的 HDInsight 叢集](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。

1. 請確認您是否可從叢集存取 Data Lake Storage Gen1 帳戶。 從 SSH 提示字元執行下列命令：

       hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

   此命令提供資料湖存儲 Gen1 帳戶中的檔/資料夾的清單。

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>從 Azure SQL Database 將資料匯入至 Data Lake Storage Gen1

1. 瀏覽至提供 Sqoop 封裝的目錄。 通常，此位置為`/usr/hdp/<version>/sqoop/bin`。

1. 從 **Table1** 將資料匯入至 Data Lake Storage Gen1。 使用下列語法：

       sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

   **sql-資料庫-伺服器名稱**預留位置表示運行 Azure SQL 資料庫的伺服器的名稱。 **sql-database-name** 預留位置代表實際的資料庫名稱。

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

   每個**部件 m-*** 檔對應于源表中的一行，**表 1**。 您可以檢視 part-m-* 檔案的內容來確認。

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>從 Data Lake Storage Gen1 將資料匯出到 Azure SQL Database

1. 從 Data Lake Storage Gen1 帳戶將資料匯出到 Azure SQL Database 中的空白資料表 **Table2**。 使用下列語法。

       sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

   例如，

       sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. 確認資料已上傳至 SQL Database 資料表。 使用 [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) 或 Visual Studio 連接至 Azure SQL Database，然後執行下列查詢。

       SELECT * FROM TABLE2

   此命令應具有以下輸出。

        ID  FName    LName
       -------------------
       1    Neal     Kell
       2    Lila     Fulton
       3    Erna     Myers
       4    Annette  Simpson

## <a name="performance-considerations-while-using-sqoop"></a>使用 Sqoop 時的效能考量

有關性能調整您的 Sqoop 作業將資料複製到資料存儲第 1 代，請參閱[Sqoop 性能博客文章](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)。

## <a name="next-steps"></a>後續步驟

* [將資料從 Azure 儲存體 Blob 複製到 Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [保護 Data Lake Storage Gen1 中的資料](data-lake-store-secure-data.md)
* [搭配 Data Lake Storage Gen1 使用 Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [搭配 Data Lake Storage Gen1 使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)
