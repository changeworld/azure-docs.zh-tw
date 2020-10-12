---
title: 使用 Spark 連接器搭配 Microsoft Azure SQL 和 SQL Server
description: 瞭解如何搭配 Azure SQL Database、Azure SQL 受控執行個體和 SQL Server 來使用 Spark 連接器。
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 09/02/2020
ms.openlocfilehash: 36010ff0206ddf9dae08391eb6e4c3dd7762cc10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319328"
---
# <a name="accelerate-real-time-big-data-analytics-using-the-spark-connector"></a>使用 Spark 連接器加速即時的大型資料分析
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 從2020年9月，此連接器不會主動維護。 不過， [適用于 SQL Server 和 AZURE SQL 的 Apache Spark 連接器](https://docs.microsoft.com/sql/connect/spark/connector) 現在已可供使用，並支援 Python 和 R 系結、更容易使用的介面來大量插入資料，以及其他許多改進。 強烈建議您評估並使用新的連接器，而不是這種連接器。 此頁面 (的舊連接器相關資訊) 只會保留供封存之用。

Spark 連接器可讓 Azure SQL Database、Azure SQL 受控執行個體和 SQL Server 中的資料庫作為 Spark 作業的輸入資料來源或輸出資料接收器。 它可讓您在大型資料分析中利用即時交易資料，並保存特定查詢或報告的結果。 相較于內建 JDBC 連接器，此連接器可讓您在資料庫中大量插入資料。 它可以優於逐列插入和10倍，以20倍較快的效能。 Spark 連接器支援 Azure Active Directory (Azure AD) authentication 來連線至 Azure SQL Database 和 Azure SQL 受控執行個體，可讓您使用 Azure Databricks 帳戶從 Azure AD 連接您的資料庫。 它提供類似內建 JDBC 連接器的介面。 您可以輕鬆移轉現有的 Spark 作業以使用此新的連接器。

## <a name="download-and-build-a-spark-connector"></a>下載並建立 Spark 連接器

先前從這個頁面連結的舊連接器的 GitHub 存放庫不會主動維護。 相反地，我們強烈建議您評估和使用 [新的連接器](https://github.com/microsoft/sql-spark-connector)。

### <a name="official-supported-versions"></a>官方支援的版本

| 元件                             | 版本                  |
| :-----------------------------------  | :----------------------- |
| Apache Spark                          | 2.0.2 或更新版本           |
| Scala                                 | 2.10 或更新版本            |
| Microsoft JDBC Driver for SQL Server  | 6.2 或更新版本             |
| Microsoft SQL Server                  | SQL Server 2008 或更新版本 |
| Azure SQL Database                    | 支援                |
| Azure SQL 受控執行個體            | 支援                |

Spark 連接器利用適用于 SQL Server 的 Microsoft JDBC Driver 來移動 Spark 背景工作角色節點和資料庫之間的資料：

資料流程如下：

1. Spark 主要節點會連接到 SQL Database 中的資料庫或 SQL Server，然後從特定資料表或使用特定的 SQL 查詢載入資料。
2. Spark 主要節點將資料散發到背景工作節點以供轉換。
3. 背景工作節點會連接到連接到 SQL Database 的資料庫，並 SQL Server 並將資料寫入資料庫中。 使用者可以選擇使用逐列插入或大量插入。

下圖說明此資料流程。

   ![圖表會顯示描述的流程，其中的主要節點會直接連接到資料庫，並連接到連接至資料庫的三個背景工作節點。](./media/spark-connector/architecture.png)

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

您可以使用 Azure AD 驗證連接到 Azure SQL Database 和 SQL 受控執行個體。 使用 Azure AD 驗證集中管理資料庫使用者的身分識別，並作為 SQL Server 的替代驗證。

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

請參閱 [使用 Azure Active Directory 驗證進行驗證](authentication-aad-overview.md) ，以瞭解如何在 Azure SQL Database 或 Azure SQL 受控執行個體中取得資料庫的存取權杖。

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

傳統 jdbc 連接器會使用逐列插入的方式將資料寫入您的資料庫。 您可以使用 Spark 連接器，將資料寫入 Azure SQL，並使用 bulk insert SQL Server。 在載入大型資料集或將資料載入使用資料行存放區索引的資料表時，它會大幅改善寫入效能。

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

如果您還沒有這麼做，請從 [azure sqldb-Spark GitHub 存放庫](https://github.com/Azure/azure-sqldb-spark) 下載 spark 連接器，並探索存放庫中的其他資源：

- [Azure Databricks Notebook 範例](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [指令碼範例 (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

您也可以檢閱 [Apache Spark SQL、DataFrame 和 Dataset 指南](https://spark.apache.org/docs/latest/sql-programming-guide.html) (英文) 和 [Azure Databricks 文件](https://docs.microsoft.com/azure/azure-databricks/)。
