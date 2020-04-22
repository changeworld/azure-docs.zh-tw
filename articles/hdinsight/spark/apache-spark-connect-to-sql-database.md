---
title: 使用 Apache Spark 將資料讀取和寫入 Azure SQL 資料庫
description: 瞭解如何在 HDInsight Spark 群集和 Azure SQL 資料庫之間建立連接。 讀取資料、寫入資料和將資料串流式傳輸到 SQL 資料庫中
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/20/2020
ms.openlocfilehash: 4e783a233bd35e012c02fbbbdc7f4223552fc734
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686855"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>使用 HDInsight Spark 叢集將資料讀取和寫入 Azure SQL 資料庫

瞭解如何將 Azure HDInsight 中的 Apache Spark 群集與 Azure SQL 資料庫連接。 然後讀取、寫入和流式傳輸數據到 SQL 資料庫中。 本文中的說明使用 Jupyter 筆記本運行 Scala 代碼段。 但是,您可以在 Scala 或 Python 中建立獨立應用程式並執行相同的任務。

## <a name="prerequisites"></a>Prerequisites

* Azure HDInsight Spark 叢集。  請依照[在 HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)中的指示操作。

* Azure SQL Database。 按照創建 Azure [SQL 資料庫](../../sql-database/sql-database-get-started-portal.md)的說明進行操作。 請確實使用範例 **AdventureWorksLT** 結構描述和資料來建立資料庫。 此外，請確實建立允許用戶端 IP 位址對伺服器上的 SQL 資料庫進行存取的伺服器層級防火牆規則。 在相同的文章中可找到新增防火牆規則的指示。 創建 Azure SQL 資料庫後,請確保保持以下值方便。 您從 Spark 叢集連線至資料庫時需要用到這些值。

    * 託管 Azure SQL 資料庫的伺服器名稱。
    * Azure SQL 資料庫名稱。
    * Azure SQL 資料庫管理員使用者名/密碼。

* SQL Server Management Studio (SSMS)。 請依照[使用 SSMS 進行連線及查詢資料](../../sql-database/sql-database-connect-query-ssms.md)中的指示操作。

## <a name="create-a-jupyter-notebook"></a>建立 Jupyter Notebook

首先創建與Spark群集關聯的猶太筆記本。 您可以使用此 Notebook 執行本文中使用的程式碼片段。

1. 從 [Azure 入口網站](https://portal.azure.com/)，開啟您的叢集。
1. 在右側選取 [叢集儀表板]**** 下方的 **Jupyter Notebook**。  如果看不到**群集儀錶板**,請從左側功能表中選擇 **「概述**」。 出現提示時，輸入叢集的系統管理員認證。

    ![阿帕奇火花上的朱派筆記本](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "火花上的朱派筆記本")

   > [!NOTE]  
   > 您也可以在瀏覽器中開啟下列 URL，以存取 Spark 叢集上的 Jupyter Notebook。 使用您叢集的名稱取代 **CLUSTERNAME** ：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. 在 Jupyter Notebook 中，按一下右上角的 [新增]****，然後按一下 [Spark]**** 以建立 Scala Notebook。 HDInsight Spark 叢集上的 Jupyter Notebook 也會提供適用於 Python2 應用程式的 **PySpark** 核心，以及適用於 Python3 應用程式的 **PySpark3** 核心。 在本文中，我們會建立 Scala Notebook。

    ![火花上朱派筆記本的內核](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "火花上朱派筆記本的內核")

    如需核心的詳細資訊，請參閱[在 HDInsight 中搭配使用 Jupyter Notebook 核心與 Apache Spark 叢集](apache-spark-jupyter-notebook-kernels.md)。

   > [!NOTE]  
   > 在本文中我們將使用 Spark (Scala) 核心，因為目前只有 Scala 和 Java 支援將 Spark 中的資料串流至 SQL 資料庫的作業。 即使對 SQL 的讀取和寫入可使用 Python 來執行，但為了本文的一致性，我們對三項作業都會使用 Scala。

1. 將打開一個預設名稱 **「無標題」 的新**筆記本。 請按一下 Notebook 名稱，並輸入您選擇的名稱。

    ![提供 Notebook 的名稱](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "提供 Notebook 的名稱")

現在您可以開始建立應用程式。

## <a name="read-data-from-azure-sql-database"></a>從 Azure SQL 資料庫讀取資料

在本節中，您會從位於 AdventureWorks 資料庫的資料表 (例如 **SalesLT.Address**) 中讀取資料。

1. 在新的 Jupyter 筆記本中,在代碼單元格中粘貼以下代碼段,並將佔位符值替換為 Azure SQL 資料庫的值。

    ```scala
    // Declare the values for your Azure SQL database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    按 **SHIFT + ENTER** 以執行程式碼單元。  

1. 使用下面的代碼段建構 JDBC URL,您可以將該網址傳遞給 Spark 資料幀 API。 代碼建立一個`Properties`物件來保存參數。 請在程式碼單元中貼上此程式碼片段，然後按 **SHIFT + ENTER** 加以執行。

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. 使用下面的代碼段創建具有 Azure SQL 資料庫中表中的數據框。 在此代碼段中,我們使用作為`SalesLT.Address` **AdventureWorksLT**資料庫一部分提供的表。 請在程式碼單元中貼上此程式碼片段，然後按 **SHIFT + ENTER** 加以執行。

    ```scala
    val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)
    ```

1. 現在,您可以對資料框執行操作,例如取得資料架構:

    ```scala
    sqlTableDF.printSchema
    ```

    您將看到類似於下圖的輸出:

    ![架構輸出](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "架構輸出")

1. 您還可以執行諸如檢索前 10 行等操作。

    ```scala
    sqlTableDF.show(10)
    ```

1. 或者，您可以從資料集擷取特定資料行。

    ```scala
    sqlTableDF.select("AddressLine1", "City").show(10)
    ```

## <a name="write-data-into-azure-sql-database"></a>將資料寫入 Azure SQL 資料庫

在本節中,我們使用群集上可用的示例 CSV 檔在 Azure SQL 資料庫中創建表,並使用數據填充表。 範例 CSV 檔案 (**HVAC.csv**) 可從所有 HDInsight 叢集取得，位置是 `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`。

1. 在新的 Jupyter 筆記本中,在代碼單元格中粘貼以下代碼段,並將佔位符值替換為 Azure SQL 資料庫的值。

    ```scala
    // Declare the values for your Azure SQL database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    按 **SHIFT + ENTER** 以執行程式碼單元。  

1. 以下代碼段生成一個 JDBC URL,您可以將該 URL 傳遞給 Spark 資料幀 API。 代碼建立一個`Properties`物件來保存參數。 請在程式碼單元中貼上此程式碼片段，然後按 **SHIFT + ENTER** 加以執行。

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. 使用下列程式碼片段來擷取 HVAC.csv 中資料的結構描述，並使用該結構描述，從資料框架 `readDf` 中的 CSV 載入資料。 請在程式碼單元中貼上此程式碼片段，然後按 **SHIFT + ENTER** 加以執行。

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. 使用 `readDf` 資料框架建立暫存資料表 `temphvactable`。 然後，使用該暫存資料表建立登錄區資料表 `hvactable_hive`。

    ```scala
    readDf.createOrReplaceTempView("temphvactable")
    spark.sql("create table hvactable_hive as select * from temphvactable")
    ```

1. 最後,使用配置單元表在 Azure SQL 資料庫中創建表。 以下代碼段在`hvactable`Azure SQL 資料庫中創建。

    ```scala
    spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)
    ```

1. 使用 SSMS 連接到 Azure SQL`dbo.hvactable`資料庫,並驗證 您是否在那裡看到。

    a. 通過提供連接詳細資訊(如下圖所示)啟動 SSMS 並連接到 Azure SQL 資料庫。

    ![使用 SSMS1 連線到 SQL 資料庫](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "使用 SSMS1 連線到 SQL 資料庫")

    b. 從**物件資源管理器**中,展開 Azure SQL 資料庫和表節點以查看創建的**dbo.hvac。"**

    ![使用 SSMS2 連線到 SQL 資料庫](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "使用 SSMS2 連線到 SQL 資料庫")

1. 在 SSMS 中執行查詢，以查看資料表中的資料行。

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>將資料串流式傳輸到 Azure SQL 資料庫

在本節中,我們將數據流式傳輸到`hvactable`上一節中已在 Azure SQL 資料庫中創建的。

1. 作為第一步,請確保 中沒有`hvactable`記錄。 請使用 SSMS 對資料表執行下列查詢。

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. 在 HDInsight Spark 叢集上建立新的 Jupyter Notebook。 在程式碼單元中貼上下列程式碼片段，然後按 **SHIFT + ENTER**：

    ```scala
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.streaming._
    import java.sql.{Connection,DriverManager,ResultSet}
    ```

1. 我們將資料從**HVAC.csv**串流`hvactable`式傳輸到 。 HVAC.csv 檔在群集`/HdiSamples/HdiSamples/SensorSampleData/HVAC/`上 可用。 在下列程式碼片段中，我們會先取得要串流處理之資料的結構描述。 接著，我們會使用該結構描述建立串流資料框架。 請在程式碼單元中貼上此程式碼片段，然後按 **SHIFT + ENTER** 加以執行。

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
    readStreamDf.printSchema
    ```

1. 輸出會顯示 **HVAC.csv** 的結構描述。 也有`hvactable`相同的架構。 輸出會列出資料表中的資料行。

    !["hdinsight 阿帕奇火花架構表"](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "表的架構")

1. 最後,使用以下代碼段從 HVAC.csv 讀取數據並將其流式`hvactable`傳輸到 Azure SQL 資料庫中。 將代碼段貼上到代碼單元格中,將占位符值替換為 Azure SQL 資料庫的值,然後按**SHIFT + ENTER**執行。

    ```scala
    val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
        var connection:java.sql.Connection = _
        var statement:java.sql.Statement = _

        val jdbcUsername = "<SQL DB ADMIN USER>"
        val jdbcPassword = "<SQL DB ADMIN PWD>"
        val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
        val jdbcPort = 1433
        val jdbcDatabase ="<AZURE SQL DB NAME>"
        val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
        val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"

        def open(partitionId: Long, version: Long):Boolean = {
        Class.forName(driver)
        connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
        statement = connection.createStatement
        true
        }

        def process(value: Row): Unit = {
        val Date  = value(0)
        val Time = value(1)
        val TargetTemp = value(2)
        val ActualTemp = value(3)
        val System = value(4)
        val SystemAge = value(5)
        val BuildingID = value(6)  

        val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
        statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")
        }

        def close(errorOrNull: Throwable): Unit = {
        connection.close
        }
        })

    var streamingQuery = WriteToSQLQuery.start()
    ```

1. 透過 SQL 伺服器管理工作室 (SSMS) 中執行以下查詢,驗證資料是否流式`hvactable`傳輸到 。 每當您執行此查詢時，都會顯示資料表中增加的資料列數。

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>後續步驟

* [使用 HDInsight Spark 叢集分析 Data Lake Storage 中的資料](apache-spark-use-with-data-lake-store.md)
* [在 Azure HDInsight 中的 Apache Spark 叢集上載入資料和執行查詢](apache-spark-load-data-run-query.md)
* [將 Apache Spark 結構化串流用於 HDInsight 上的 Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
