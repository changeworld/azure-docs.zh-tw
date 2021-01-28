---
title: 教學課程：在 Azure HDInsight 的 Spark 計算內容中使用 R
description: 教學課程 - 開始在 Azure HDInsight Machine Learning 服務叢集上使用 R 和 Spark。
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: bd6015529fb521e3b157e46ee808aea43e993dee
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935666"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>教學課程：在 Azure HDInsight 的 Spark 計算內容中使用 R

此教學課程會逐步簡介如何在 Azure HDInsight Machine Learning 服務叢集上執行的 Apache Spark 中使用 R 函式。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 將範例資料下載至本機儲存體
> * 將資料複製到預設儲存體
> * 設定資料集
> * 建立資料來源
> * 建立 Spark 的計算內容
> * 進行調整以符合線性模型
> * 使用複合 XDF 檔案
> * 將 XDF 轉換為 CSV

## <a name="prerequisites"></a>Prerequisites

* Azure HDInsight Machine Learning 服務叢集。 請移至[使用 Azure 入口網站建立 Apache Hadoop 叢集](../hdinsight-hadoop-create-linux-clusters-portal.md)，然後選取 [ML 服務]  作為 [叢集類型]  。

## <a name="connect-to-rstudio-server"></a>連線到 RStudio 伺服器

RStudio Server 會在叢集的邊緣節點上執行。 移至下列網站 (其中 URL 中的 CLUSTERNAME  是您所建立的 HDInsight Machine Learning 服務叢集名稱)：

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

第一次登入時，您會進行兩次驗證。 出現第一次的驗證提示時，請提供叢集管理員使用者名稱與密碼 (預設為 admin  )。 出現第二次的驗證提示時，請提供 SSH 使用者名稱與密碼 (預設為 sshuser  )。 之後再登入時，只需要提供 SSH 認證。

## <a name="download-the-sample-data-to-local-storage"></a>將範例資料下載至本機儲存體

「2012 年班機準點資料集」  由 12 個以逗號分隔的檔案組成，包含有關美國在 2012 年境內所有民航班機的抵達和起飛詳細資料。 此資料集很大，有超過 600 萬個觀察值。

1. 初始化幾個環境變數。 在 RStudio Server 主控台中，輸入下列程式碼：

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. 在右側窗格中，選取 [環境]  索引標籤。變數會顯示在 [值]  的底下。

    ![HDInsight R studio Web 主控台](./media/ml-services-tutorial-spark-compute/hdinsight-rstudio-image.png)

1. 建立本機目錄，並下載範例資料。 在 RStudio 中輸入下列程式碼：

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    下載應會在 9.5 分鐘內完成。

## <a name="copy-the-data-to-default-storage"></a>將資料複製到預設儲存體

Hadoop 分散式檔案系統 (HDFS) 的位置會以 `airDataDir` 變數指定。 在 RStudio 中輸入下列程式碼：

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

該步驟應會在 10 秒內完成。

## <a name="set-up-a-dataset"></a>設定資料集

1. 建立使用預設值的檔案系統物件。 在 RStudio 中輸入下列程式碼：

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. 由於原始 CSV 檔案有相當難以使用的變數名稱，因此您可以提供 colInfo  清單以方便管理。 在 RStudio 中輸入下列程式碼：

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-sources"></a>建立資料來源

在 Spark 計算內容中，您可以使用下列函式建立資料來源：

|函式 | 描述 |
|---------|-------------|
|`RxTextData` | 以逗號分隔文字的資料來源。 |
|`RxXdfData` | 使用 XDF 資料檔案格式的資料。 在 RevoScaleR 中，為 Hadoop 修改 XDF 檔案格式，將資料儲存在複合的檔案集，而不是單一檔案。 |
|`RxHiveData` | 產生 Hive 資料來源物件。|
|`RxParquetData` | 產生 Parquet 資料來源物件。|
|`RxOrcData` | 產生 Orc 資料來源物件。|

使用您複製到 HDFS 的檔案，建立 [RxTextData](/machine-learning-server/r-reference/revoscaler/rxtextdata) 物件。 在 RStudio 中輸入下列程式碼：

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>建立 Spark 的計算內容

若要在背景工作節點上載入資料並執行分析，請將指令碼中的計算內容設定為 [RxSpark](/machine-learning-server/r-reference/revoscaler/rxspark)。 在此內容中，R 函式會自動將工作負載散發到所有背景工作節點，但不內建管理作業或佇列需求。 Spark 計算內容是透過 `RxSpark` 來建立的，或用 `rxSparkConnect()` 建立 Spark 計算內容，然後使用 `rxSparkDisconnect()` 傳送本機計算內容。 在 RStudio 中輸入下列程式碼：

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>進行調整以符合線性模型

1. 使用 [rxLinMod](/machine-learning-server/r-reference/revoscaler/rxlinmod) 函式進行調整以符合線性模型 (使用您的 `airDS` 資料來源)。 在 RStudio 中輸入下列程式碼：

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    此步驟應會在 2 至 3 分鐘內完成。

1. 檢視結果。 在 RStudio 中輸入下列程式碼：

    ```R
    summary(delayArr)
    ```

    您應該會看見下列結果：

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 **_ | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 _*_ | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 _*_ | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 _*_ | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 _*_ | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 _*_ | 858366
    ---
    Signif. codes:  0 ‘_*_’ 0.001 ‘_*’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    結果指出您已使用指定目錄中的所有 CSV 檔案處理所有資料 (600 萬個觀察值)。 由於您已指定 `cube = TRUE`，所以週間每天都有其估計係數 (而不是截距)。

## <a name="use-composite-xdf-files"></a>使用複合 XDF 檔案

如您所見，您可以直接在 Hadoop 上使用 R 來分析 CSV 檔案。 但是，如果您以更有效率的格式儲存資料，就可以更快速地進行分析。 R XDF 就是有效率的檔案格式，但已為 HDFS 稍微修改，以便讓個別檔案保留在單一 HDFS 區塊內。 (HDFS 區塊大小因不同安裝而異，但通常是 64 MB 或 128 MB。) 

當您使用 Hadoop 上的 [rxImport](/machine-learning-server/r-reference/revoscaler/rximport) 來建立一組複合 XDF 檔案時，可以將 `AirDS` 這類 `RxTextData` 資料來源指定為 inData 與 `RxXdfData` 資料來源，搭配將設定為 HDFS 檔案系統的 fileSystem 作為 outFile 引數。 接著，您可以使用 `RxXdfData` 物件作為後續 R 分析中的資料引數。

1. 定義 `RxXdfData` 物件。 在 RStudio 中輸入下列程式碼：

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. 設定有 250000 個資料列的區塊大小，並指出我們會讀取所有資料。 在 RStudio 中輸入下列程式碼：

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. 使用 `rxImport` 匯入資料。 在 RStudio 中輸入下列程式碼：

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    此步驟應會在幾分鐘內完成。

1. 使用較快速的新資料來源，重新估計相同的線性模型。 在 RStudio 中輸入下列程式碼：

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    該步驟應會在一分鐘內完成。

1. 檢視結果。 結果應該和 CSV 檔案提供的相同。 在 RStudio 中輸入下列程式碼：

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>將 XDF 轉換為 CSV

### <a name="in-a-spark-context"></a>在 Spark 內容中

如果您為了更有效率地執行分析，已將 CSV 檔案轉換為 XDF 檔案格式，但現在想要將資料轉換回 CSV，您可以使用 [rxDataStep](/machine-learning-server/r-reference/revoscaler/rxdatastep)。

若要建立 CSV 檔案的資料夾，請先使用目錄名稱作為檔案引數來建立 `RxTextData` 物件。 此物件代表要在其中建立 CSV 檔案的資料夾。 當您執行 `rxDataStep` 時，就會建立此目錄。 然後，在 `rxDataStep` 的 `outFile` 引數中，指向這個 `RxTextData` 物件。 建立的每個 CSV 都會依照目錄名稱來命名，並在後面加上數字。

假設您要在執行羅吉斯迴歸與預測後，從 `airDataXdf` 複合 XDF 在 HDFS 中寫出 CSV 檔案的資料夾，以讓新的 CSV 檔案包含預測值與殘差。 在 RStudio 中輸入下列程式碼：

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

此步驟應會在 2.5 分鐘內完成。

`rxDataStep` 會為輸入複合 XDF 檔案中的每個 XDFD 檔案寫出 一個 CSV 檔案。 當計算內容設定為 `RxSpark`，將 CSV 檔案從複合 XDF 檔案寫入至 HDFS 時，這是預設行為。

### <a name="in-a-local-context"></a>在本機內容中

或者，當您完成執行分析時，您可以將計算內容切換回 `local`，以充分利用 `RxTextData` 內的兩個引數：`createFileSet` 與 `rowsPerOutFile`，讓您在將 CSV 檔案寫出到 HDFS 時可多一些控制。 當您將 `createFileSet` 設定為 `TRUE` 時，CSV 檔案的資料夾會寫入您指定的目錄。 當您將 `createFileSet` 設定為 `FALSE` 時，會寫入單一 CSV 檔案。 您可以將第二個引數 (`rowsPerOutFile`) 設定為整數，表示當 `createFileSet` 為 `TRUE` 時要將多少列寫入至每個 CSV 檔案。

在 RStudio 中輸入下列程式碼：

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

此步驟應會在 10 分鐘內完成。

當您使用 `RxSpark` 計算內容時，預設為 `TRUE` 和 `rowsPerOutFile` 的 `createFileSet` 沒有任何作用。 因此，如果您想要建立單一 CSV，或自訂每個檔案的列數，請在 `local` 計算內容中執行 `rxDataStep` (資料仍可放在 HDFS 中)。

## <a name="final-steps"></a>最後步驟

1. 清除資料。 在 RStudio 中輸入下列程式碼：

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. 停止遠端 Spark 應用程式。 在 RStudio 中輸入下列程式碼：

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. 結束 R 工作階段。 在 RStudio 中輸入下列程式碼：

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>清除資源

完成本教學課程之後，您可以刪除叢集。 利用 HDInsight，您的資料會儲存在 Azure 儲存體中，以便您在未使用叢集時安全地進行刪除。 您也需支付 HDInsight 叢集的費用 (即使未使用)。 由於叢集費用是儲存體費用的許多倍，所以刪除未使用的叢集符合經濟效益。

若要刪除叢集，請參閱[使用您的瀏覽器、PowerShell 或 Azure CLI 刪除 HDInsight 叢集](../hdinsight-delete-cluster.md)。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已了解如何在 HDInsight Machine Learning 服務叢集上執行的 Apache Spark 中使用 R 函式。 如需詳細資訊，請參閱下列文章：

* [Azure HDInsight Machine Learning 服務叢集的計算內容選項](r-server-compute-contexts.md)
* [Hadoop 上 Spark 的 R 函式](/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)