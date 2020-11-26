---
title: 教學課程：使用 Azure HDInsight 擷取、轉換和載入資料
description: 在本教學課程中，您將了解如何從原始 CSV 資料集擷取資料、使用 Azure HDInsight 上的 Apache Hive 加以轉換，然後使用 Sqoop 將已轉換的資料載入 Azure SQL Database 中。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 7640b7a6053532360da4c908089aecfe163bd3de
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912649"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-hdinsight"></a>教學課程：使用 Azure HDInsight 擷取、轉換和載入資料

在本教學課程中，您將會執行 ETL 作業：擷取、轉換及載入資料。 您會取用原始 CSV 資料檔案，並將其匯入 Azure HDInsight 叢集中，然後使用 Apache Hive 加以轉換，再使用 Apache Sqoop 將其載入 Azure SQL Database 中。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 擷取資料並將其上傳至 HDInsight 叢集。
> * 使用 Apache Hive 轉換資料。
> * 使用 Sqoop 將資料載入至 Azure SQL Database。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

* **針對 HDInsight 設定的 Azure Data Lake Storage Gen2 儲存體帳戶**

    請參閱[搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)。

* **HDInsight 上的 Linux 型 Hadoop 叢集**

    請參閱[快速入門：使用 Azure 入口網站在 Azure HDInsight 中開始使用 Apache Hadoop 和 Apache Hive](../../hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)。

* **Azure SQL Database**：您會使用 Azure SQL Database 做為目的地資料存放區。 如果您在 SQL Database 中沒有資料庫，請參閱[在 Azure 入口網站的 Azure SQL Database 中建立資料庫](../../azure-sql/database/single-database-create-quickstart.md)。

* **Azure CLI**：如果您未安裝 Azure CLI，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

* **安全殼層 (SSH) 用戶端**：如需詳細資訊，請參閱 [使用 SSH 連線至 HDInsight (Hadoop)](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="download-the-flight-data"></a>下載航班資料

1. 瀏覽至[創新技術研究管理部運輸統計處](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)。

2. 在此頁面上選取下列值：

   | 名稱 | 值 |
   | --- | --- |
   | 篩選年份 |2013 |
   | 篩選期間 |一月 |
   | 欄位 |Year、FlightDate、Reporting_Airline、IATA_CODE_Reporting_Airline、Flight_Number_Reporting_Airline、OriginAirportID、Origin、OriginCityName、OriginState、DestAirportID、Dest、DestCityName、DestState、DepDelayMinutes、ArrDelay、ArrDelayMinutes、CarrierDelay、WeatherDelay、NASDelay、SecurityDelay、LateAircraftDelay。 |
   
   清除所有其他欄位。

3. 選取 [下載]。 您會取得含有您所選資料欄位的 .zip 檔案。

## <a name="extract-and-upload-the-data"></a>擷取並上傳資料

在本節中，您會將資料上傳至 HDInsight 叢集，然後將該資料複製到您的 Data Lake Storage Gen2 帳戶。

1. 開啟命令提示字元，並使用下列安全複製 (Scp) 命令將 .zip 檔案上傳至 HDInsight 叢集前端節點：

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   * 使用 .zip 檔案的名稱取代 `<file-name>` 預留位置。
   * 使用 HDInsight 叢集的 SSH 登入取代 `<ssh-user-name>` 預留位置。
   * 使用 HDInsight 叢集的名稱取代 `<cluster-name>` 預留位置。

   如果您使用密碼來驗證您的 SSH 登入，系統會提示您輸入密碼。

   如果您使用的是公用金鑰，您可能必須使用 `-i` 參數並指定對應的私密金鑰路徑。 例如： `scp -i ~/.ssh/id_rsa <file_name>.zip <user-name>@<cluster-name>-ssh.azurehdinsight.net:` 。

2. 完成上傳之後，使用 SSH 連線至叢集。 在命令提示字元中輸入下列命令：

   ```bash
   ssh <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net
   ```

3. 使用以下命令解壓縮 .zip 檔案：

   ```bash
   unzip <file-name>.zip
   ```

   此命令會解壓縮 **.csv** 檔案。

4. 使用下列命令建立 Data Lake Storage Gen2 容器。

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   請將 `<container-name>` 預留位置取代為您要為容器指定的名稱。

   使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置。

5. 使用以下命令建立目錄。

   ```bash
   hdfs dfs -mkdir -p abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. 使用以下命令將 *.csv* 檔案複製到目錄：

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   如果檔案名稱包含空格或特殊字元，請使用引號括住檔案名稱。

## <a name="transform-the-data"></a>轉換資料

在本節中，您會使用 Beeline 來執行 Apache Hive 作業。

在執行 Apache Hive 作業的過程中，您會將 .csv 檔案中的資料匯入至名為 **delays** 的 Apache Hive 資料表。

1. 從 HDInsight 叢集既有的 SSH 提示字元中，使用下列命令建立並編輯名為 **flightdelays.hql** 的新檔案：

   ```bash
   nano flightdelays.hql
   ```

2. 以您的容器和儲存體帳戶名稱取代 `<container-name>` 和 `<storage-account-name>` 預留位置，修改下列文字。 然後按 SHIFT 鍵以及滑鼠右鍵按鈕，將文字複製並貼到 nano 主控台中。

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
    LOCATION 'abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION 'abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/processed'
    AS
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

3. 使用 CTRL + X 儲存檔案，然後在出現提示時鍵入 `Y`。

4. 若要啟動 Hive 並執行 **flightdelays.hql** 檔案，請使用下列命令：

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
   ```

5. 在 __flightdelays.hql__ 指令碼執行完畢之後，請使用下列命令來開啟互動式 Beeline 工作階段：

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

   此查詢會擷取因氣候因素而延誤的城市清單，以及平均延誤時間，並會儲存到 `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`。 稍後，Sqoop 會從此位置讀取該資料，並匯出到 Azure SQL Database。

7. 若要結束 Beeline，請在提示字元中輸入 `!quit` 。

## <a name="create-a-sql-database-table"></a>建立 SQL 資料庫資料表

執行這項作業時，需要您在 SQL Database 中的伺服器名稱。 請完成下列步驟以找出您的伺服器名稱。

1. 移至 [Azure 入口網站](https://portal.azure.com)。

2. 選取 [SQL 資料庫]。

3. 篩選您選擇要使用的資料庫名稱。 伺服器名稱會列在 [伺服器名稱] 資料行中。

4. 篩選您要使用的資料庫名稱。 伺服器名稱會列在 [伺服器名稱] 資料行中。

    ![取得 Azure SQL 伺服器詳細資料](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "取得 Azure SQL 伺服器詳細資料")

    連接至 SQL Database 並建立資料表的方法有很多種。 下列步驟會從 HDInsight 叢集使用 [FreeTDS](https://www.freetds.org/) 。

5. 若要安裝 FreeTDS，請從 SSH 連線對叢集使用下列命令：

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. 安裝完成後，請使用下列命令連線至 SQL Database。

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```
   * 以邏輯 SQL 伺服器名稱取代 `<server-name>` 預留位置。

   * 以 SQL 資料庫的管理員登入取代 `<admin-login>` 預留位置。

   * 以資料庫名稱取代 `<database-name>` 預留位置

   出現提示時，請輸入 SQL 資料庫管理員的登入密碼。

   您會收到如以下文字的輸出：

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. 出現 `1>` 提示時，請輸入下列陳述式：

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. 輸入 `GO` 陳述式後，將評估先前的陳述式。

   此查詢會建立名為 **delays** 的資料表 (具有叢集索引)。

9. 使用下列查詢來確認資料表已建立：

   ```hiveql
   SELECT * FROM information_schema.tables
   GO
   ```

   輸出大致如下：

   ```
   TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
   databaseName       dbo             delays        BASE TABLE
   ```

10. Enter `exit` at the `1>` 以結束 tsql 公用程式。

## <a name="export-and-load-the-data"></a>匯出和載入資料

在前幾節中，您在位置 `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` 上複製了已轉換的資料。 在本節中，您將使用 Sqoop 將資料從 `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` 匯出至您在 Azure SQL Database 中建立的資料表。

1. 使用下列命令以確認 Sqoop 看得見您的 SQL 資料庫：

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   此命令會傳回一份資料庫清單，包括您在其中建立 **delays** 資料表的資料庫。

2. 使用下列命令，將資料從 **hivesampletable** 資料表匯出至 **delays** 資料表：

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<container-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   Sqoop 會連線至包含 **delays** 資料表的資料庫，並將資料從 `/tutorials/flightdelays/output` 目錄匯出至 **delays** 資料表。

3. 在 `sqoop` 命令完成後，請使用 tsql 公用程式連線至資料庫：

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. 使用下列陳述式來確認資料已匯出到 **delays** 資料表：

   ```sql
   SELECT * FROM delays
   GO
   ```

   您應會看到資料表中的資料清單。 此資料表包含城市名稱以及該城市的平均航班延誤時間。

5. 輸入 `exit` 以結束 tsql 公用程式。

## <a name="clean-up-resources"></a>清除資源

本教學課程中使用的所有資源都是既有的。 不需要執行清除。

## <a name="next-steps"></a>後續步驟

若要深入了解如何使用 HDInsight 中的資料，請參閱下列文章：

> [!div class="nextstepaction"]
> [搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)