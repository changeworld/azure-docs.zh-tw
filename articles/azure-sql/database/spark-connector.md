---
title: 搭配 Microsoft Azure SQL 和 SQL Server 使用 Spark 連接器
description: 瞭解如何搭配 Azure SQL Database、Azure SQL 受控執行個體和 SQL Server 使用 Spark 連接器。
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: cb7fb7f6c44f9e1c4a9b073c666543a2e892582a
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985494"
---
# <a name="accelerate-real-time-big-data-analytics-using-the-spark-connector"></a>使用 Spark 連接器加速即時海量資料分析 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Spark 連接器可讓 Azure SQL Database、Azure SQL 受控執行個體和 SQL Server 中的資料庫作為 Spark 作業的輸入資料來源或輸出資料接收器。 它可讓您在海量資料分析中利用即時交易資料，並保存特定查詢或報告的結果。 相較于內建的 JDBC 連接器，此連接器可讓您將資料大量插入資料庫中。 它可以優於每個資料列插入10倍，以20倍更快的效能。 Spark 連接器支援 Azure Active Directory （Azure AD）驗證，以連線至 Azure SQL Database 和 Azure SQL 受控執行個體，可讓您使用 Azure Databricks 帳戶從 Azure AD 連接您的資料庫。 它提供類似內建 JDBC 連接器的介面。 您可以輕鬆移轉現有的 Spark 作業以使用此新的連接器。

## <a name="download-and-build-a-spark-connector"></a>下載並建立 Spark 連接器

若要開始使用，請從 GitHub 上的[azure sqldb-spark 儲存](https://github.com/Azure/azure-sqldb-spark)機制下載 Spark 連接器。

### <a name="official-supported-versions"></a>正式支援的版本

| 元件                             | 版本                  |
| :-----------------------------------  | :----------------------- |
| Apache Spark                          | 2.0.2 或更新版本           |
| Scala                                 | 2.10 或更新版本            |
| Microsoft JDBC Driver for SQL Server  | 6.2 或更新版本             |
| Microsoft SQL Server                  | SQL Server 2008 或更新版本 |
| Azure SQL Database                    | 支援                |
| Azure SQL 受控執行個體            | 支援                |

Spark 連接器會利用 Microsoft JDBC Driver for SQL Server 在 Spark 背景工作角色節點與資料庫之間移動資料：

資料流程如下：

1. Spark 主要節點會在 SQL Database 或 SQL Server 中連接到資料庫，並從特定的資料表或使用特定的 SQL 查詢載入資料。
2. Spark 主要節點將資料散發到背景工作節點以供轉換。
3. 背景工作節點會連接到連接到 SQL Database 的資料庫，並 SQL Server 並將資料寫入資料庫。 使用者可以選擇使用逐列插入或大量插入。

下圖說明此資料流程。

   ![架構](./media/spark-connector/architecture.png)

### <a name="build-the-spark-connector"></a>建立 Spark 連接器

目前，連接器專案會使用 maven。 若要建置沒有相依性的連接器，您可以執行︰

- mvn 全新套件
- 從發行資料夾下載最新版 JAR
- 包含 SQL Database Spark JAR

## <a name="connect-and-read-data-using-the-spark-connector"></a>使用 Spark 連接器連接和讀取資料

您可以連接到 SQL Database 中的資料庫，並從 Spark 作業 SQL Server 以讀取或寫入資料。 您也可以在 SQL Database 和 SQL Server 的資料庫中執行 DML 或 DDL 查詢。

### <a name="read-data-from-azure-sql-and-sql-server"></a>從 Azure SQL 和 SQL Server 讀取資料

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients",
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="read-data-from-azure-sql-and-sql-server-with-specified-sql-query"></a>使用指定的 SQL 查詢從 Azure SQL 和 SQL Server 讀取資料

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="write-data-to-azure-sql-and-sql-server"></a>將資料寫入 Azure SQL 和 SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-and-sql-server"></a>在 Azure SQL 和 SQL Server 中執行 DML 或 DDL 查詢

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.sqlDBQuery(config)
```

## <a name="connect-from-spark-using-azure-ad-authentication"></a>使用 Azure AD authentication 從 Spark 連接

您可以使用 Azure AD 驗證，連接到 Azure SQL Database 和 SQL 受控執行個體。 使用 Azure AD 驗證集中管理資料庫使用者的身分識別，並作為 SQL Server 的替代驗證。

### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>使用 ActiveDirectoryPassword 驗證模式來連線

#### <a name="setup-requirement"></a>安裝需求

如果您使用 ActiveDirectoryPassword 驗證模式，必須下載 [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) 及其相依項目，並將它們包含在 Java 建置路徑中。

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="connecting-using-an-access-token"></a>使用存取權杖連接

#### <a name="setup-requirement"></a>安裝需求

如果您使用存取權杖型驗證模式，必須下載 [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java)及其相依項目，並將它們包含在 Java 建置路徑中。

請參閱[使用 Azure Active Directory 驗證進行驗證](authentication-aad-overview.md)，以瞭解如何在 Azure SQL Database 或 Azure SQL 受控執行個體中取得您資料庫的存取權杖。

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

## <a name="write-data-using-bulk-insert"></a>使用 bulk insert 寫入資料

傳統 jdbc 連接器會使用逐列插入，將資料寫入您的資料庫。 您可以使用 Spark 連接器，將資料寫入 Azure SQL，並使用 bulk insert SQL Server。 在載入大型資料集或將資料載入使用資料行存放區索引的資料表時，它會大幅改善寫入效能。

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/**
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>後續步驟

如果您還沒有這麼做，請從[azure sqldb-Spark GitHub 儲存](https://github.com/Azure/azure-sqldb-spark)機制下載 Spark 連接器，並探索存放庫中的其他資源：

- [Azure Databricks Notebook 範例](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [指令碼範例 (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

您也可以檢閱 [Apache Spark SQL、DataFrame 和 Dataset 指南](https://spark.apache.org/docs/latest/sql-programming-guide.html) (英文) 和 [Azure Databricks 文件](https://docs.microsoft.com/azure/azure-databricks/)。
