---
title: 教學課程：Azure Data Lake Storage Gen2、Azure Databricks 和 Spark | Microsoft Docs
description: 本教學課程顯示如何在 Azure Databricks 叢集上執行 Spark 查詢，以存取 Azure Data Lake Storage Gen2 儲存體帳戶中的資料。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.custom: tracking-python
ms.openlocfilehash: 12f5f8b1e1a20675fd08f753e515faef2f7a97ce
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560042"
---
# <a name="tutorial-azure-data-lake-storage-gen2-azure-databricks--spark"></a>教學課程：Azure Data Lake Storage Gen2、Azure Databricks 和 Spark

本教學課程說明如何將 Azure Databricks 叢集連線至已啟用 Azure Data Lake Storage Gen2 功能之 Azure 儲存體帳戶中所儲存的資料。 此連線可讓您以原生方式從叢集對資料執行查詢和分析。

在本教學課程中，您將：

> [!div class="checklist"]
> * 建立 Databricks 叢集
> * 將非結構化的資料內嵌到儲存體帳戶
> * 在 Blob 儲存體中對資料執行分析

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>Prerequisites

* 建立 Azure Data Lake Storage Gen2 帳戶。

  請參閱[建立 Azure Data Lake Storage Gen2 帳戶](data-lake-storage-quickstart-create-account.md)。

* 確定您已對使用者帳戶指派[儲存體 Blob 資料參與者角色](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)。

* 安裝 AzCopy v10。 請參閱[使用 AzCopy v10 轉送資料](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

* 建立服務主體。 請參閱[如何：使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

  在執行該文章中的步驟時，您必須執行幾個特定動作。

  :heavy_check_mark:在執行該文章的[將應用程式指派給角色](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application)一節中的步驟時，請確實將 [儲存體 Blob 資料參與者]  角色指派給服務主體。

  > [!IMPORTANT]
  > 請務必在 Data Lake Storage Gen2 儲存體帳戶的範圍中指派該角色。 您可以將角色指派給父資源群組或訂用帳戶，但在這些角色指派傳播至儲存體帳戶之前，您將會收到與權限有關的錯誤。

  :heavy_check_mark:在執行該文章的[取得值以便登入](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)一節中的步驟時，請將租用戶識別碼、應用程式識別碼和用戶端密碼值貼到文字檔中。 您很快就會用到這些資料。

### <a name="download-the-flight-data"></a>下載航班資料

本教學課程將使用來自運輸統計處的航班資料示範如何執行 ETL 作業。 您必須下載這項資料，才能完成本教學課程。

1. 移至[創新技術研究管理部運輸統計處](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)。

2. 選取 [預先壓縮的檔案]  核取方塊以選取所有資料欄位。

3. 選取 [下載]  按鈕，然後將結果儲存到您的電腦。 

4. 將 ZIP 檔案的內容解壓縮，並記下檔案名稱和檔案路徑。 稍後的步驟將會需要這項資訊。

## <a name="create-an-azure-databricks-service"></a>建立 Azure Databricks 服務

在本節中，您將使用 Azure 入口網站建立 Azure Databricks 服務。

1. 在 Azure 入口網站中，選取 [建立資源]   > [分析]   > [Azure Databricks]  。

    ![Azure 入口網站上的 Databricks](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Azure 入口網站上的 Databricks")

2. 在 [Azure Databricks 服務]  下方提供下列值，以建立 Databricks 服務：

    |屬性  |描述  |
    |---------|---------|
    |**工作區名稱**     | 提供您 Databricks 工作區的名稱。  |
    |**訂用帳戶**     | 從下拉式清單中選取您的 Azure 訂用帳戶。        |
    |**資源群組**     | 指定您是要建立新的資源群組，還是使用現有資源群組。 資源群組是存放 Azure 方案相關資源的容器。 如需詳細資訊，請參閱 [Azure 資源群組概觀](../../azure-resource-manager/management/overview.md)。 |
    |**位置**     | 選取 [美國西部 2]  。 如需其他可用的區域，請參閱[依區域提供的 Azure 服務](https://azure.microsoft.com/regions/services/)。       |
    |定價層      |  選取 [標準]  。     |

    ![建立 Azure Databricks 工作區](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "建立 Azure Databricks 服務")

3. 建立帳戶需要幾分鐘的時間。 若要監視作業狀態，請檢視頂端的進度列。

4. 選取 [釘選到儀表板]  ，然後選取 [建立]  。

## <a name="create-a-spark-cluster-in-azure-databricks"></a>在 Azure Databricks 中建立 Spark 叢集

1. 在 Azure 入口網站中，移至您所建立的 Databricks 服務，然後選取 [啟動工作區]  。

2. 系統會將您重新導向至 Azure Databricks 入口網站。 在入口網站中選取 [叢集]  。

    ![Azure 上的 Databricks](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Azure 上的 Databricks")

3. 在 [新增叢集]  頁面上，提供值以建立叢集。

    ![在 Azure 上建立 Databricks Spark 叢集](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "在 Azure 上建立 Databricks Spark 叢集")

    填寫下列欄位的值，然後接受其他欄位的預設值：

    - 輸入叢集的名稱。
     
    - 務必要選取 [在停止活動 120 分鐘後終止]  核取方塊。 請提供用來終止叢集的叢集未使用持續時間 (以分鐘為單位)。

4. 選取 [建立叢集]  。 叢集執行後，您就可以將 Notebook 連結至叢集，並執行 Spark 作業。

## <a name="ingest-data"></a>擷取資料

### <a name="copy-source-data-into-the-storage-account"></a>將來源資料複製到儲存體帳戶

使用 AzCopy 從 .csv  檔案複製資料到 Data Lake Storage Gen2 帳戶。

1. 開啟命令提示字元視窗，並輸入下列命令以登入您的儲存體帳戶。

   ```bash
   azcopy login
   ```

   請依照命令提示字元視窗中顯示的指示，來驗證您的使用者帳戶。

2. 若要從 .csv  帳戶複製資料，請輸入下列命令。

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<container-name>/folder1/On_Time.csv
   ```

   * 將 `<csv-folder-path>` 預留位置值更換為 *.csv* 檔案的名稱。

   * 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

   * 使用您儲存體帳戶中的容器名稱取代 `<container-name>` 預留位置。

## <a name="create-a-container-and-mount-it"></a>建立容器和掛接容器

在本節中，您將會在儲存體帳戶中建立容器和資料夾。

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您所建立的 Azure Databricks 服務，然後選取 [啟動工作區]  。

2. 在左側選取 [工作區]  。 從 [工作區]  下拉式清單選取 [建立]   > [Notebook]  。

    ![在 Databricks 中建立筆記本](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "在 Databricks 中建立筆記本")

3. 在 [建立 Notebook]  對話方塊中，輸入 Notebook 的名稱。 選取 [Python]  作為語言，然後選取您先前建立的 Spark 叢集。

4. 選取 [建立]  。

5. 將下列程式碼區塊複製並貼到第一個資料格中，但先不要執行此程式碼。

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<appId>",
           "fs.azure.account.oauth2.client.secret": "<clientSecret>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```

18. 在此程式碼區塊中，請將此程式碼區塊中的 `appId`、`clientSecret`、`tenant` 和 `storage-account-name` 預留位置值取代為您在執行本教學課程的必要條件時所收集到的值。 使用容器名稱取代 `container-name` 預留位置值。

19. 按 **SHIFT + ENTER** 鍵以執行此區塊中的程式碼。

   讓此筆記本保持開啟，以便稍後在其中新增命令。

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>使用 Databricks Notebook 將 CSV 轉換成 Parquet

在先前建立的筆記本中新增資料格，然後將下列程式碼貼到該資料格。 

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
print("Done")
```

## <a name="explore-data"></a>探索資料

在新的資料格中貼上下列程式碼，以取得透過 AzCopy 所上傳 CSV 檔案的清單。

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata"))
```

若要建立新檔案，並列出 *parquet/flights* 資料夾中的檔案，請執行此指令碼：

```python
dbutils.fs.put("/mnt/flightdata/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/parquet/flights")
```

藉由這些程式碼範例，您已使用具有 Data Lake Storage Gen2 功能的儲存體帳戶所儲存的資料，探索了 HDFS 的階層式本質。

## <a name="query-the-data"></a>查詢資料

接下來，您可以開始查詢您上傳到儲存體帳戶的資料。 在 **Cmd 1** 中輸入下列每個程式碼區塊，然後按 **Cmd + Enter** 來執行 Python 指令碼。

若要建立資料來源的資料框架，請執行下列指令碼：

* 將 `<csv-folder-path>` 預留位置值更換為 *.csv* 檔案的名稱。

```python
# Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/On_Time.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

# read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(
    header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

# print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

# print the flight database size
print("Number of flights in the database: ", flightDF.count())

# show the first 20 rows (20 is the default)
# to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

# Display to run visualizations
# preferably run this in a separate cmd cell
display(flightDF)
```

輸入此指令碼來針對資料執行一些基本的分析查詢。

```python
# Run each of these queries, preferably in a separate cmd cell for separate analysis
# create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

# create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

# using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights = out2.count()
print("Jan 2016: ", NumJan2016Flights, " Feb 2016: ", NumFeb2016Flights)
Total = NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql(
    "SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'")
print('Airports in Texas: ', out.show(100))

# find all airlines that fly from Texas
out1 = spark.sql(
    "SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>清除資源

當已不再需要資源時，請刪除資源群組及所有相關資源。 若要這麼做，請選取儲存體帳戶的資源群組，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [使用 Azure HDInsight 上的 Apache Hive 來擷取、轉換和載入資料](data-lake-storage-tutorial-extract-transform-load-hive.md)
