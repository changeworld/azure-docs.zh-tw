---
title: 在 Data Lake Storage Gen1 和 Azure SQL 之間複製資料-Sqoop |Microsoft Docs
description: 使用 Sqoop 在 Azure SQL Database 和 Azure Data Lake Storage Gen1 之間複製資料
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: 9bb787138267fd8a9fab4dea233c1c828b457d67
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "92109182"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>使用 Sqoop 在 Data Lake Storage Gen1 和 Azure SQL Database 之間複製資料

了解如何使用 Apache Sqoop 在 Azure SQL Database 和 Azure Data Lake Storage Gen1 之間匯入及匯出資料。

## <a name="what-is-sqoop"></a>什麼是 Sqoop？

巨量資料應用程式是處理非結構化和半結構化資料 (例如記錄和檔案)，很自然的一個選擇。 不過，您可能也需要處理儲存在關係資料庫中的結構化資料。

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) 是一個專門設計來在關聯式資料庫和巨量資料儲存機制 (例如 Azure Data Lake Storage Gen1) 之間傳送資料的工具。 您可以使用它從像是 Azure SQL Database 這類的關聯式資料庫管理系統 (RDBMS)，匯入資料至 Azure Data Lake Storage Gen1。 然後，您可以使用大型資料工作負載來轉換和分析資料，然後將資料匯出回 RDBMS。 在本文中，您會使用 Azure SQL Database 中的資料庫作為要匯入/匯出來源的關係資料庫。

## <a name="prerequisites"></a>Prerequisites

開始之前，您必須具備下列條件：

* **Azure 訂用帳戶**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Storage Gen1 帳戶**。 如需有關如何建立帳戶的指示，請參閱 [開始使用 Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* 可存取 Data Lake Storage Gen1 帳戶的 **Azure HDInsight 叢集**。 請參閱[建立搭配 Data Lake Storage Gen1 的 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-portal.md)。 本文假設您已使用 Azure Data Lake Storage Gen1 存取 HDInsight Linux 叢集。
* **Azure SQL Database**。 如需有關如何在 Azure SQL Database 中建立資料庫的指示，請參閱 [在 Azure SQL Database 中建立資料庫](../azure-sql/database/single-database-create-quickstart.md)

## <a name="create-sample-tables-in-the-database"></a>在資料庫中建立範例資料表

1. 若要開始，請在資料庫中建立兩個範例資料表。 使用 [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) 或 Visual Studio 連接到資料庫，然後執行下列查詢。

    **建立 Table1**

    ```tsql
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
    ```

    **建立 Table2**

    ```tsql
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
    ```

1. 執行下列命令，以將一些範例資料新增至 **Table1**。 保留 **Table2** 空白。 稍後，您會將資料從 **Table1** 匯入 Data Lake Storage Gen1。 然後，您會將 Data Lake Storage Gen1 中的資料匯出到 **Table2**。

    ```tsql
    INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');
    ```

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>從使用 Data Lake Storage Gen1 存取的 HDInsight Linux 叢集使用 Sqoop

HDInsight 叢集已有可用的 Sqoop 套件。 如果您已將 HDInsight 叢集設定為使用 Data Lake Storage Gen1 作為額外的儲存體，您可以使用 Sqoop (，而不需要進行任何設定變更) 在關係資料庫之間匯入/匯出資料，例如 Azure SQL Database 和 Data Lake Storage Gen1 帳戶。

1. 在本文中，我們假設您已建立 Linux 叢集，因此您應該使用 SSH 來連線到叢集。 請參閱 [連線至以 Linux 為基礎的 HDInsight 叢集](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。

1. 請確認您是否可從叢集存取 Data Lake Storage Gen1 帳戶。 從 SSH 提示字元執行下列命令：

    ```console
    hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/
    ```

   此命令會提供 Data Lake Storage Gen1 帳戶中的檔案/資料夾清單。

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>從 Azure SQL Database 將資料匯入至 Data Lake Storage Gen1

1. 瀏覽至提供 Sqoop 封裝的目錄。 一般來說，這個位置是 `/usr/hdp/<version>/sqoop/bin` 。

1. 從 **Table1** 將資料匯入至 Data Lake Storage Gen1。 使用下列語法：

    ```console
    sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

   **Sql-資料庫-伺服器名稱** 預留位置代表資料庫執行所在的伺服器名稱。 **sql-database-name** 預留位置代表實際的資料庫名稱。

   例如，

    ```console
    sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

1. 請確認資料已被傳輸至 Data Lake Storage Gen1 帳戶。 執行以下命令：

    ```console
    hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/
    ```

   您應該會看見下列輸出。

    ```console
    -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
    -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
    -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
    -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
    -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003
    ```

   每個 **部分-m-** _ 檔案都會對應到來源資料表中的一個資料列，即 _ *Table1 * *。您可以查看部分 m* 檔案的內容以進行驗證。

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>從 Data Lake Storage Gen1 將資料匯出到 Azure SQL Database

1. 從 Data Lake Storage Gen1 帳戶將資料匯出到 Azure SQL Database 中的空白資料表 **Table2**。 使用下列語法。

    ```console
    sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

   例如，

    ```console
    sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

1. 確認資料已上傳至 SQL Database 資料表。 使用 [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) 或 Visual Studio 連接至 Azure SQL Database，然後執行下列查詢。

    ```tsql
    SELECT * FROM TABLE2
    ```

   此命令應該會有下列輸出。

    ```output
     ID  FName    LName
    -------------------
    1    Neal     Kell
    2    Lila     Fulton
    3    Erna     Myers
    4    Annette  Simpson
    ```

## <a name="performance-considerations-while-using-sqoop"></a>使用 Sqoop 時的效能考量

如需效能調整 Sqoop 作業以將資料複製到 Data Lake Storage Gen1 的詳細資訊，請參閱 [Sqoop 效能的 blog 文章](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)。

## <a name="next-steps"></a>後續步驟

* [將資料從 Azure 儲存體 Blob 複製到 Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [保護 Data Lake Storage Gen1 中的資料](data-lake-store-secure-data.md)
* [搭配 Data Lake Storage Gen1 使用 Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [搭配 Data Lake Storage Gen1 使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)