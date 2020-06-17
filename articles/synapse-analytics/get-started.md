---
title: 教學課程：開始使用 Azure Synapse Analytics
description: 步驟快速了解 Azure Synapse 中的基本概念
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 05/19/2020
ms.openlocfilehash: 00f93086fec62c08c5241d868fc5104a1197cff3
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84605403"
---
# <a name="getting-started-with-azure-synapse-analytics"></a>開始使用 Azure Synapse Analytics

本文件將引導您完成設定及使用 Azure Synapse Analytics 所需的所有基本步驟。

## <a name="prepare-a-storage-account-for-use-with-a-synapse-workspace"></a>準備與 Synapse 工作區搭配使用的儲存體帳戶

* 開啟 [Azure 入口網站](https://portal.azure.com)
* 使用下列設定建立新的儲存體帳戶：

    |索引標籤|設定 | 建議的值 | 描述 |
    |---|---|---|---|
    |基本概念|**儲存體帳戶名稱**| 請為其指定任何名稱。|在本文件中，我們將其稱為 `contosolake`。|
    |基本概念|**帳戶類型**|必須設定為 `StorageV2`||
    |基本概念|**位置**|您可以挑選任何位置| 我們建議您的 Synapse 工作區與 Azure Data Lake Storage (ADLS) Gen2 帳戶位於相同的區域。|
    |進階|**Data Lake Storage Gen2**|`Enabled`| Azure Synapse 僅適用於已啟用此設定的儲存體帳戶。|

1. 建立儲存體帳戶之後，請從左側導覽中選取 [存取控制 (IAM)]。 然後指派下列角色，或確定其已指派。 

    a. * 將您自己指派給儲存體帳戶 b 上的 [擁有者] 角色。 * 將您自己指派給儲存體帳戶上的 [儲存體 Blob 資料擁有者] 角色

1. 從左側導覽中，選取 [容器] 並建立容器。 請為其指定任何名稱。 接受預設的 [公用存取層級]。 在本文件中，我們將呼叫容器 `users`。 選取 [建立]。 

在下列步驟中，您會將 Synapse 工作區設定為使用此儲存體帳戶作為其「主要」儲存體帳戶，以及用來儲存工作區資料的容器。 工作區會將資料儲存在此帳戶的 Apache Spark 資料表和 Spark 應用程式記錄檔中，名為 `/synapse/workspacename` 的資料夾底下。

## <a name="create-a-synapse-workspace"></a>建立 Synapse 工作區

* 開啟 [Azure 入口網站](https://portal.azure.com)，並在頂端搜尋 `Synapse`。
* 在 [服務] 底下的搜尋結果中，選取 [Azure Synapse Analytics (工作區預覽)]
* 選取 [+ 新增]，使用這些設定來建立工作區

    |索引標籤|設定 | 建議的值 | 描述 |
    |---|---|---|---|
    |基本概念|**工作區名稱**|您可以將其命名為任何名稱。| 在本文件中，我們將使用 `myworkspace`|
    |基本概念|**區域**|比對儲存體帳戶的區域|

1. 在 [選取 Data Lake Storage Gen 2] 底下，選取您先前建立的帳戶和容器。

1. 選取 [檢閱 + 建立]。 選取 [建立]。 您的工作區將會在幾分鐘內就緒。

## <a name="verify-the-synapse-workspace-msi-has-access-to-the-storage-account"></a>確認 Synapse 工作區 MSI 具有儲存體帳戶的存取權

這部分可能會自動完成。 在任何情況下，您都應該進行驗證。

1. 開啟 [Azure 入口網站](https://portal.azure.com)，然後開啟為您工作區選擇的主要儲存體帳戶。
1. 從左側導覽列選取 [存取控制 (IAM)]。 然後指派下列角色，或確定其已指派。 
    a. 將工作區身分識別指派給儲存體帳戶上的 [儲存體 Blob 資料參與者 (預覽)] 角色。 工作區身分識別的名稱與工作區相同。 在本文件中，工作區名稱為 `myworkspace`，因此工作區身分識別為 `myworkspaced`
1. 選取 [儲存]。
    
## <a name="launch-synapse-studio"></a>啟動 Synapse Studio

建立 Synapse 工作區之後，有兩種方式可以開啟 Synapse Studio：
* 在 [Azure 入口網站](https://portal.azure.com)中開啟您的 Synapse 工作區，然後在 [總覽] 區段中，選取 [啟動 Synapse Studio]
* 直接移至 https://web.azuresynapse.net 並登入您的工作區。

## <a name="create-a-sql-pool"></a>建立 SQL 集區

1. 在 Synapse Studio 的左側導覽中，選取 [管理 > SQL 集區]
1. 選取 [+ 新增]，然後輸入下列設定：

    |設定 | 建議的值 | 
    |---|---|
    |**SQL 集區名稱**| `SQLDB1`|
    |**效能等級**|`DW100C`|

1. 選取 [檢閱 + 建立]，然後選取 [建立]。
1. 您的 SQL 集區將會在幾分鐘內就緒。 建立 SQL 集區時，其會與 SQL 集區資料庫相關聯 (亦稱為 **SQLDB1**)。

SQL 集區只要在作用中，就會取用計費的資源。 您可於稍後暫停集區，以降低成本。

## <a name="create-an-apache-spark-pool"></a>建立 Apache Spark 集區

1. 在 Synapse Studio 的左側選取 [管理 > Apache Spark 集區]
1. 選取 [+ 新增]，然後輸入下列設定：

    |設定 | 建議的值 | 
    |---|---|
    |**Apache Spark 集區名稱**|`Spark1`
    |**節點大小**| `Small`|
    |**節點數目**| 將最小值設定為 3，以及最大值設定為 3|

1. 選取 [檢閱 + 建立]，然後選取 [建立]。
1. 您的 Apache Spark 集區會在幾秒內準備就緒。

> [!NOTE]
> 儘管名稱為 Apache Spark 集區和 SQL 集區，但這兩者並不相同。 這只是一些基本中繼資料，可讓您用來通知 Synapse 工作區如何與 Spark 互動。 

由於其為中繼資料，因此無法啟動或停止 Spark 集區。 

當您在 Synapse 中執行任何 Spark 活動時，需指定要使用的 Spark 集區。 集區會通知 Synapse 要使用多少 Spark 資源。 您只需支付已使用的資源。 當您使用集區主動停止時，資源將會自動逾時並予以回收。

> [!NOTE]
> 會從 Spark 集區獨立建立 Spark 資料庫。 工作區一律會有一個名為**預設**的 Spark 資料庫，您可用於建立額外的 Spark 資料庫。

## <a name="the-sql-on-demand-pool"></a>SQL 隨選集區

每個工作區都有一個預先建立且無法刪除的集區，稱為 **SQL 隨選**。 SQL 隨選集區可讓您使用 SQL，而不需要建立或考慮管理 Synapse SQL 集區。 不同於其他類型的集區，SQL 隨選計費是根據掃描以執行查詢的資料量，而不是用於執行查詢的資源數目。

* SQL 隨選也有自己的 SQL 隨選資料庫，與任何 SQL 隨選集區分開存在。
* 工作區目前一律只會有一個名為 **SQL 隨選**的 SQL 隨選集區。

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>將 NYC 計程車範例資料載入 SQLDB1 資料庫

1. 在 Synapse Studio 的最上方藍色功能表中，選取 [？] 圖示。
1. 選取 [開始使用 > 快速入門中樞]
1. 在標示為 [查詢範例資料] 的卡片中，選取名為 `SQLDB1` 的 SQL 集區
1. 選取 [查詢資料]。 您會看到一則通知出現，指出「正在載入範例資料」，並且隨後消失。
1. 您會在 Synapse Studio 頂端附近看到淺藍色的通知列，指出正在將資料載入 SQLDB1 中。 等其變成綠色，然後加以關閉。

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>探索 SQL 集區中的 NYC 計程車資料

1. 在 Synapse Studio 中，瀏覽至 [資料] 中樞
1. 瀏覽至 [SQLDB1 > 資料表]。 您會看到已載入數個資料表。
1. 以滑鼠右鍵按一下 [dbo.Trip] 資料表，然後選取 [新增 SQL 指令碼 > 選取前 100 個資料列]
1. 將會建立並自動執行新的 SQL 指令碼。
1. 請注意，在 SQL 指令碼頂端的 [連接到] 會自動設定成名為 `SQLDB1` 的 SQL 集區。
1. 將 SQL 指令碼的文字取代為此程式碼並加以執行。

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

1. 此查詢會顯示總行程距離和平均路程距離與乘客數的關聯
1. 在 [SQL 指令碼結果] 視窗中，將 [視圖] 變更為 [圖表]，以折線圖形式查看結果的視覺效果

## <a name="load-the-nyc-taxi-sample-data-into-the-spark-nyctaxi-database"></a>將 NYC 計程車範例資料載入 Spark nyctaxi 資料庫

我們在 `SQLDB1` 資料表中提供資料。 我們現在會將其載入名為 `nyctaxi` 的 Spark 資料庫。

1. 在 Synapse Studio 中，瀏覽至 [開發] 中樞
1. 選取 [+]，然後選取 [筆記本]
1. 在筆記本頂端，將 [附加至] 的值設定為 `Spark1`
1. 選取 [新增程式碼] 以新增筆記本程式碼儲存格，並貼上下列文字：

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. 瀏覽至 [資料] 中樞，以滑鼠右鍵按一下 [資料庫]，然後選取 [重新整理]。
1. 現在您應該會看到這些資料庫：
    - SQLDB1 (SQL 集區)
    - nyctaxi (Spark)
      
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>使用 Spark 和筆記本來分析 NYC 計程車資料

1. 返回筆記本
1. 建立新的程式碼儲存格並輸入下列文字，然後執行該儲存格，舉例說明我們載入 `nyctaxi` Spark 資料庫的 NYC 計程車資料。

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. 執行下列程式碼，以執行與我們先前所利用 SQL 集區 `SQLDB1` 相同的分析。 這段程式碼也會將分析的結果儲存到名為 `nyctaxi.passengercountstats` 的資料表中，並將結果視覺化。

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. 在資料格結果中，選取 [圖表] 可查看視覺化的資料
 
## <a name="customize-data-visualization-data-with-spark-and-notebooks"></a>使用 Spark 和筆記本自訂資料視覺效果資料

您可以透過筆記本來控制轉譯圖表的方式。 下列程式碼會顯示使用熱門程式庫 `matplotlib` 和 `seaborn` 的簡單範例。 其會轉譯您稍早執行 SQL 查詢時所看到的相同類型折線圖。

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```
    
## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>將資料從 Spark 資料表載入 SQL 集區資料表

先前我們已將資料從 SQL 集區資料表 `SQLDB1.dbo.Trip` 複製到 Spark 資料表 `nyctaxi.trip`。 然後，我們使用 Spark，將資料彙總到 Spark 資料表 `nyctaxi.passengercountstats`。 現在，我們會將 `nyctaxi.passengercountstats` 中的資料複製到名為 `SQLDB1.dbo.PassengerCountStats` 的 SQL 集區資料表。 

在您的筆記本中執行以下儲存格。 其會將彙總的 Spark 資料表複製回 SQL 集區資料表。

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>使用 SQL 隨選分析 Spark 資料庫中的 NYC 計程車資料 

Spark 資料庫中的資料表會自動顯示，且可由 SQL 隨選查詢。

1. 在 Synapse Studio 中，瀏覽至 [開發] 中樞，然後建立新的 SQL 指令碼
1. 將 [連線到] 設定為 [SQL 隨選] 
1. 將下列文字貼到指令碼中，然後執行指令碼。

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```
    > [!NOTE]
    > 當您第一次執行使用 SQL 隨選的查詢時，SQL 隨選需要大約 10 秒的時間，才能收集執行查詢所需的 SQL 資源。 後續的查詢將不需要這個時間，而且速度會更快。
  
## <a name="orchestrate-activities-with-pipelines"></a>使用管線協調活動

您可以在 Azure Synapse 中協調各種不同的工作。 在本節中，您會看到有多麼簡單。

1. 在 Synapse Studio 中，瀏覽至 [協調] 中樞。
1. 選取 [+]，然後選取 [管線]。 將會建立新的管線。
1. 瀏覽至 [開發] 中樞，並尋找您先前建立的筆記本。
1. 將該筆記本拖曳到管線中。
1. 在管線中，選取 [新增觸發程序 > 編輯]。
1. 在 [選擇觸發程序] 中，選取 [新增]，然後在 [週期] 中，將觸發程序設定為每隔 1 小時執行一次。
1. 選取 [確定]。
1. 選取 [全部發佈]，管線就會每小時執行一次。
1. 如果您想要讓管線立即執行，而不等待下一個小時，請選取 [新增觸發程序 > 新增/編輯]。

## <a name="working-with-data-in-a-storage-account"></a>使用儲存體帳戶中的資料

目前為止，我們已涵蓋資料位於工作區中資料庫的案例。 現在，我們將示範如何使用儲存體帳戶中的檔案。 在此案例中，我們將使用工作區的主要儲存體帳戶，以及我們在建立工作區時所指定的容器。

* 儲存體帳戶的名稱：`contosolake`
* 儲存體帳戶中容器的名稱：`users`

### <a name="creating-csv-and-parquet-files-in-your-storage-account"></a>在您的儲存體帳戶中建立 CSV 和 Parquet 檔案

在筆記本資料格中執行下列命令。 其會在儲存體帳戶中建立 CSV 檔案和 Parquet 檔案

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyzing-data-in-a-storage-account"></a>分析儲存體帳戶中的資料

1. 在 Synapse Studio 中，瀏覽至 [資料] 中樞
1. 選取 [已連結]
1. 瀏覽至 [儲存體帳戶 > myworkspace (主要 - contosolake)]
1. 選取 [使用者 (主要)]
1. 您應會看到名為 `NYCTaxi` 的資料夾。 您應該會在其內看到 `PassengerCountStats.csv` 及 `PassengerCountStats.parquet` 這兩個資料夾。
1. 瀏覽至 `PassengerCountStats.parquet` 資料夾。
1. 在 `.parquet` 檔案內以滑鼠右鍵按一下，然後選取 [新增筆記本]，隨即會建立具有如下儲存格的筆記本：

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. 執行資料格。
1. 在 Parquet 檔案內以滑鼠右鍵按一下，然後選取 [新增 SQL 指令碼 > 選取前 100 個資料列]，其會建立如下的 SQL 指令碼：

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```
    
1. 在指令碼中，[附加至] 欄位將會設定為 [SQL 隨選]。
1. 執行指令碼。

## <a name="visualize-data-with-power-bi"></a>使用 Power BI 視覺化資料

從 NYX 計程車資料中，我們在兩個資料表中建立了彙總的資料集：
* `nyctaxi.passengercountstats`
* `SQLDB1.dbo.PassengerCountStats`

您可以將 Power BI 工作區連結至您的 Synapse 工作區。 如此可讓您輕鬆地將資料放入 Power BI 工作區，且您可以直接在 Synapse 工作區中編輯您的 Power BI 報告。

### <a name="create-a-power-bi-workspace"></a>建立 Power BI 工作區

1. 登入 [powerbi.microsoft.com](https://powerbi.microsoft.com/)。
1. 建立名為 `NYCTaxiWorkspace1` 的新 Power BI 工作區。

### <a name="link-your-synapse-workspace-to-your-new-power-bi-workspace"></a>將您的 Synapse 工作區連結至您新的 Power BI 工作區

1. 在 Synapse Studio 中，瀏覽至 [管理 > 連結服務]。
1. 選取 [+ 新增]，然後選取 [連線到 Power BI]，並設定下欄欄位：

    |設定 | 建議的值 | 
    |---|---|
    |**名稱**|`NYCTaxiWorkspace1`|
    |**工作區名稱**|`NYCTaxiWorkspace1`|
        
1. 選取 [建立]。

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-synapse-workspace"></a>建立在 Synapse 工作區中使用資料的 Power BI 資料集

1. 在 Synapse Studio 中，瀏覽至 [開發 > Power BI] 中樞。
1. 瀏覽至 [NYCTaxiWorkspace1 > Power BI 資料集]，然後選取 [新增 Power BI 資料集]。
1. 將滑鼠停留在 `SQLDB1` 資料庫上，然後選取 [下載 .pbids 檔案]。
1. 開啟下載的 `.pbids` 檔案。 
1. 這樣會啟動 Power BI 桌面，並自動將其連線至 Synapse 工作區中的 `SQLDB1`。
1. 如果您看到名為 **SQL Server 資料庫** 的對話方塊：a. 選取 [Microsoft 帳戶]。 
    b. 選取 [登入] 以登入。
    c. 選取 [連接]。
1. [導覽器] 對話方塊隨即開啟。 當其開啟時，請檢查 **PassengerCountStats** 資料表，然後選取 [載入]。
1. 隨即會出現 [連線設定] 對話方塊。 選取 [DirectQuery] 然後選取 [確定]
1. 選取左側的 [報告] 按鈕。
1. 將 [折線圖] 新增至報告中。
    a. 將 [PasssengerCount] 資料行拖曳至 [視覺效果 > 軸] b. 將 [SumTripDistance] 和 [AvgTripDistance] 資料行拖曳到 [視覺效果 > 值]。
1. 在 [首頁] 索引標籤中，選取 [發佈]。
1. 其會詢問您是否要儲存變更。 選取 [儲存]。
1. 其會要求您挑選檔名。 選擇 `PassengerAnalysis.pbix`，然後選取 [儲存]。
1. 其會要求您 [選取目的地]、選取 `NYCTaxiWorkspace1` 然後選取 [選取]。
1. 等待發佈完成。

### <a name="configure-authentication-for-your-dataset"></a>設定資料集的驗證

1. 開啟 [powerbi.microsoft.com](https://powerbi.microsoft.com/) 然後 [登入]
1. 在左側的 [工作區] 底下，選取 `NYCTaxiWorkspace1` 工作區。
1. 在該工作區內，您應該會看到名為 `Passenger Analysis` 的資料集，以及名為 `Passenger Analysis` 的報告。
1. 將滑鼠停留在 `PassengerAnalysis` 資料集上，並選取具有三個點的圖示，然後選取 [設定]。
1. 在 [資料來源認證] 中，將 [驗證方法] 設為 [OAuth2]，然後選取 [登入]。

### <a name="edit-a-report-in-synapse-studio"></a>在 Synapse Studio 中編輯報告

1. 返回 Synapse Studio，然後選取 [關閉並重新整理] 
1. 瀏覽至 [開發] 中樞 
1. 將滑鼠停留在 [Power BI] 上，然後按一下重新整理 [Power BI 報告] 節點。
1. 現在，您應該會在 [Power BI] 下看到：a. * 在 [NYCTaxiWorkspace1 > Power BI 資料集] 下，新的資料集稱為 [PassengerAnalysis]。
    b. * 在 [NYCTaxiWorkspace1 > Power BI 報告] 下，新的報告稱為 [PassengerAnalysis]。
1. 選取 [PassengerAnalysis] 報告。 
1. 報告隨即開啟，您現在可以直接在 Synapse Studio 內編輯報告。

## <a name="monitor-activities"></a>監視器活動

1. 在 Synapse Studio 中，瀏覽至 [監視] 中樞。
1. 在此位置中，您可以查看工作區中所發生之所有活動的歷程記錄，以及目前作用中的活動。
1. 探索 [管線執行]、[Apache Spark 應用程式] 和 [SQL 要求]，您可以在工作區中查看已完成的作業。

## <a name="next-steps"></a>後續步驟

深入了解 [Azure Synapse Analytics (預覽)](overview-what-is.md)

