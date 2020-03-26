---
title: 教學課程：使用互動式查詢的 ETL 作業 - Azure HDInsight
description: 教學課程 - 了解如何從原始 CSV 資料集擷取資料、使用 HDInsight 上的互動式查詢加以轉換，然後使用 Apache Sqoop 將已轉換的資料載入 Azure SQL 資料庫中。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 07/02/2019
ms.author: hrasheed
ms.custom: hdinsightactive,mvc
ms.openlocfilehash: d1136c153a529f58db1de277ec84ac332b9f78ae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "73494149"
---
# <a name="tutorial-extract-transform-and-load-data-using-interactive-query-in-azure-hdinsight"></a>教學課程：使用 Azure HDInsight 上的互動式查詢來擷取、轉換和載入資料

在本教學課程中，您將取用公開航班資料的原始 CSV 資料檔案、將其匯入 HDInsight 叢集儲存體中，然後使用 Azure HDInsight 中的互動式查詢來轉換資料。 資料轉換後，您會使用 [Apache Sqoop](https://sqoop.apache.org/) 將該資料載入 Azure SQL 資料庫中。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 下載範例航班資料
> * 將資料上傳至 HDInsight 叢集
> * 使用互動式查詢轉換資料
> * 在 Azure SQL 資料庫中建立資料表
> * 使用 Sqoop 將資料匯出至 Azure SQL 資料庫

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的互動式查詢叢集。 請參閱[使用 Azure 入口網站建立 Apache Hadoop 叢集](../hdinsight-hadoop-create-linux-clusters-portal.md)，然後選取 [互動式查詢]  作為 [叢集類型]  。

* Azure SQL Database。 您會使用 Azure SQL 資料庫做為目的地資料存放區。 如果您沒有 SQL 資料庫，請參閱[在 Azure 入口網站中建立 Azure SQL 資料庫](/azure/sql-database/sql-database-single-database-get-started)。

* SSH 用戶端。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="download-the-flight-data"></a>下載航班資料

1. 瀏覽至[創新技術研究管理部運輸統計處](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)。

2. 在此頁面上清除所有欄位，然後選取下列值：

   | 名稱 | 值 |
   | --- | --- |
   | 篩選年份 |2019 |
   | 篩選期間 |一月 |
   | 欄位 |Year、FlightDate、Reporting_Airline、DOT_ID_Reporting_Airline、Flight_Number_Reporting_Airline、OriginAirportID、Origin、OriginCityName、OriginState、DestAirportID、Dest、DestCityName、DestState、DepDelayMinutes、ArrDelay、ArrDelayMinutes、CarrierDelay、WeatherDelay、NASDelay、SecurityDelay、LateAircraftDelay。 |

3. 選取 [下載]  。 您會取得含有您所選資料欄位的 .zip 檔案。

## <a name="upload-data-to-an-hdinsight-cluster"></a>將資料上傳至 HDInsight 叢集

有許多方法可將資料上傳至與 HDInsight 叢集相關聯的儲存體。 在本節中，您會使用 `scp` 來上傳資料。 若要了解其他上傳資料的方式，請參閱[將資料上傳至 HDInsight](../hdinsight-upload-data.md)。

1. 將 .zip 檔案上傳到 HDInsight 叢集前端節點。 將 `FILENAME` 取代為 .zip 檔案的名稱，並將 `CLUSTERNAME` 取代為 HDInsight 叢集的名稱，以編輯下列命令。 然後，開啟命令提示字元，並將您的工作目錄設為檔案位置，然後輸入命令。

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

    如果系統要求您輸入 yes 或 no 以便繼續，請在命令提示字元中輸入 yes，然後按 Enter 鍵。 在輸入文字時，視窗中不會顯示出來。

2. 完成上傳之後，使用 SSH 連線至叢集。 將 `CLUSTERNAME` 取代為 HDInsight 叢集的名稱，以編輯下列命令。 然後，輸入下列命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. 在 SSH 連線建立後，請設定環境變數。 請將 `FILE_NAME`、`SQL_SERVERNAME`、`SQL_DATABASE`、`SQL_USER` 和 `SQL_PASWORD` 取代為適當的值。 然後輸入命令：

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. 輸入以下命令將 .zip 檔案解壓縮：

    ```bash
    unzip $FILENAME.zip
    ```

5. 輸入下列命令以在 HDInsight 儲存體上建立目錄，然後將 .csv 檔案複製到該目錄︰

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>使用 Hive 查詢轉換資料

有許多方法可在 HDInsight 叢集上執行 Hive 作業。 在本節中，您會使用 [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell) 來執行 Hive 作業。 若想了解其他用來執行 Hive 作業的方法，請參閱[使用 HDInsight 上的 Apache Hive](../hadoop/hdinsight-use-hive.md)。

在執行 Hive 作業的過程中，您會將 .csv 檔案中的資料匯入至名為 **Delays** 的 Hive 資料表。

1. 從 HDInsight 叢集既有的 SSH 提示字元中，使用下列命令建立並編輯名為 **flightdelays.hql** 的新檔案：

    ```bash
    nano flightdelays.hql
    ```

2. 使用下列文字做為此檔案的內容：

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. 若要儲存檔案，請依序按 **Ctrl + X**、**y** 和 Enter 鍵。

4. 若要啟動 Hive 並執行 **flightdelays.hql** 檔案，請使用下列命令：

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

5. 在 **flightdelays.hql** 指令碼執行完畢之後，請使用下列命令來開啟互動式 Beeline 工作階段：

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

6. 當您收到 `jdbc:hive2://localhost:10001/>` 提示字元時，請使用以下查詢從匯入的航班延誤資料中擷取資料：

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    此查詢會擷取因氣候因素而延誤的城市清單，以及平均延誤時間，並會儲存到 `/tutorials/flightdelays/output`。 稍後，Sqoop 會從此位置讀取該資料，並匯出到 Azure SQL Database。

7. 若要結束 Beeline，請在提示字元中輸入 `!quit` 。

## <a name="create-a-sql-database-table"></a>建立 SQL 資料庫資料表

連接至 SQL Database 並建立資料表的方法有很多種。 下列步驟會從 HDInsight 叢集使用 [FreeTDS](http://www.freetds.org/) 。

1. 若要安裝 FreeTDS，請從開啟的叢集 SSH 連線使用下列命令：

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. 安裝完成後，請使用下列命令來連線到 SQL Database 伺服器。

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    您會收到如以下文字的輸出：

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to <yourdatabase>
    1>
    ```

3. 在 `1>` 提示字元輸入下列幾行：

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
    ([origin_city_name] ASC))
    GO
    ```

    輸入 `GO` 陳述式後，將評估先前的陳述式。 此陳述式會建立名為 **delays** 的資料表 (具有叢集索引)。

    使用下列查詢來確認已建立資料表：

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    輸出大致如下：

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

4. Enter `exit` at the `1>` 以結束 tsql 公用程式。

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>使用 Apache Sqoop 將資料匯出至 SQL 資料庫

在前幾節中，您在 `/tutorials/flightdelays/output` 上複製了已轉換的資料。 在本節中，您會使用 Sqoop 將資料從 `/tutorials/flightdelays/output` 匯出至您在 Azure SQL 資料庫中建立的資料表。

1. 輸入下列命令，以確認 Sqoop 看得見您的 SQL 資料庫：

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    此命令會傳回一份資料庫清單，包含您稍早在其中建立 `delays` 資料表的資料庫。

2. 輸入下列命令，將資料從 `/tutorials/flightdelays/output` 匯出至 `delays` 資料表：

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop 會連線至包含 `delays` 資料表的資料庫，並將資料從 `/tutorials/flightdelays/output` 目錄匯出至 `delays` 資料表。

3. 在 sqoop 命令完成後，輸入下列命令以使用 tsql 公用程式連線至資料庫：

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    使用下列陳述式來確認資料已匯出到延遲資料表：

    ```sql
    SELECT * FROM delays
    GO
    ```

    您應會看到資料表中的資料清單。 此資料表包含城市名稱以及該城市的平均航班延誤時間。 

    輸入 `exit` 以結束 tsql 公用程式。

## <a name="clean-up-resources"></a>清除資源

完成本教學課程之後，您可以刪除叢集。 利用 HDInsight，您的資料會儲存在 Azure 儲存體中，以便您在未使用叢集時安全地進行刪除。 您也需支付 HDInsight 叢集的費用 (即使未使用)。 由於叢集費用是儲存體費用的許多倍，所以刪除未使用的叢集符合經濟效益。

若要刪除叢集，請參閱[使用您的瀏覽器、PowerShell 或 Azure CLI 刪除 HDInsight 叢集](../hdinsight-delete-cluster.md)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已取用原始 CSV 資料檔、將其匯入 HDInsight 叢集儲存體中，然後使用 Azure HDInsight 上的互動式查詢轉換了資料。  請前進到下一個教學課程，以了解 Apache Hive Warehouse Connector。

> [!div class="nextstepaction"]
>[整合 Apache Spark 和 Apache Hive 與 Hive Warehouse Connector](./apache-hive-warehouse-connector.md)
