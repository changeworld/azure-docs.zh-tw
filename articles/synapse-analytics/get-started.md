---
title: 教學課程：開始使用 Azure Synapse Analytics
description: 在本教學課程中，您將了解設定和使用 Azure Synapse Analytics 的基本步驟。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: daa8b594b06203c7de9a9b462be469dd71ed2e49
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791855"
---
# <a name="get-started-with-azure-synapse-analytics"></a>開始使用 Azure Synapse Analytics

本教學課程將引導您完成設定和使用 Azure Synapse Analytics 的基本步驟。

## <a name="prepare-a-storage-account"></a>準備儲存體帳戶

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
1. 建立具有下列設定的新儲存體帳戶：

    |索引標籤|設定 | 建議的值 | 描述 |
    |---|---|---|---|
    |基本概念|**儲存體帳戶名稱**| 請為其指定任何名稱。|在本文件中，我們將其稱為 **contosolake**。|
    |基本概念|**帳戶類型**|必須設定為 **StorageV2**。||
    |基本概念|**位置**|選擇任何位置。| 我們建議您將 Azure Synapse Analytics 工作區和 Azure Data Lake Storage Gen2 帳戶置於相同區域。|
    |進階|**Data Lake Storage Gen2**|**已啟用**| Azure Synapse 僅適用於已啟用此設定的儲存體帳戶。|
    |||||

1. 建立儲存體帳戶之後，請選取左側窗格中的 [存取控制 (IAM)]。 然後指派下列角色，或確定其已指派：
    1. 將您自己指派給**擁有者**角色。
    1. 將您自己指派給**儲存體 Blob 資料擁有者**角色。
1. 在左側窗格中，選取 [容器] 並建立容器。
1. 您可以為容器提供任何名稱。 在本文件中，我們會將容器命名為 **users**。
1. 接受 [公用存取層級] 的預設設定，然後選取 [建立]。

在下列步驟中，您會將 Azure Synapse 工作區設定為使用此儲存體帳戶作為「主要」儲存體帳戶，以及用來儲存工作區資料的容器。 工作區會將資料儲存在 Apache Spark 資料表中。 其會將 Spark 應用程式記錄儲存在名為 **/synapse/workspacename** 的資料夾底下。

## <a name="create-a-synapse-workspace"></a>建立 Synapse 工作區

1. 開啟 [Azure 入口網站](https://portal.azure.com)，並在頂端搜尋 **Synapse**。
1. 在 [服務] 底下的搜尋結果中，選取 [Azure Synapse Analytics (工作區預覽)]。
1. 選取 [新增]，使用這些設定來建立工作區：

    |索引標籤|設定 | 建議的值 | 描述 |
    |---|---|---|---|
    |基本概念|**工作區名稱**|您可以將其命名為任何名稱。| 在本文件中，我們將使用 **myworkspace**。|
    |基本概念|**區域**|比對儲存體帳戶的區域。|

1. 在 [選取 Data Lake Storage Gen 2] 底下，選取您先前建立的帳戶和容器。
1. 選取 [檢閱+建立] > [建立]。 您的工作區將會在幾分鐘內就緒。

## <a name="verify-access-to-the-storage-account"></a>確認儲存體帳戶的存取權

Azure Synapse 工作區的受控身分識別可能已經有儲存體帳戶的存取權。 請依照下列步驟來確認：

1. 開啟 [Azure 入口網站](https://portal.azure.com)，然後開啟為您工作區選擇的主要儲存體帳戶。
1. 從左窗格中選取 [存取控制 (IAM)]。
1. 指派下列角色，或確定其已指派。 我們使用相同名稱作為工作區身分識別和工作區名稱。
    1. 針對儲存體帳戶上的 [儲存體 Blob 資料參與者] 角色，請指派 **myworkspace** 作為工作區身分識別。
    1. 指派 **myworkspace** 作為工作區名稱。

1. 選取 [儲存]。

## <a name="open-synapse-studio"></a>開啟 Synapse Studio

建立 Azure Synapse 工作區之後，有兩種方式可以開啟 Synapse Studio：

* 在 [Azure 入口網站](https://portal.azure.com)中開啟 Synapse 工作區。 在 [概觀] 區段的頂端，選取 [啟動 Synapse Studio]。
* 移至 https://web.azuresynapse.net 並登入您的工作區。

## <a name="create-a-sql-pool"></a>建立 SQL 集區

1. 在 Synapse Studio 的左側窗格中，選取 [管理] > [SQL 集區]。
1. 選取 [新增]，然後輸入下列設定：

    |設定 | 建議的值 | 
    |---|---|---|
    |**SQL 集區名稱**| **SQLDB1**|
    |**效能等級**|**DW100C**|
    |||

1. 選取 [檢閱+建立] > [建立]。 您的 SQL 集區將會在幾分鐘內就緒。 您的 SQL 集區會與 SQL 集區資料庫 (也稱為 **SQLDB1**) 相關聯。

SQL 集區只要在作用中，就會取用計費的資源。 您可於稍後暫停集區，以降低成本。

## <a name="create-an-apache-spark-pool"></a>建立 Apache Spark 集區

1. 在 Synapse Studio 的左側窗格中，選取 [管理] > [Apache Spark 集區]。
1. 選取 [新增]，然後輸入下列設定：

    |設定 | 建議的值 | 
    |---|---|---|
    |**Apache Spark 集區名稱**|**Spark1**
    |**節點大小**| **小型**|
    |**節點數目**| 將最小值設定為 3，以及最大值設定為 3|

1. 選取 [檢閱+建立] > [建立]。 您的 Apache Spark 集區會在幾秒內準備就緒。

> [!NOTE]
> 儘管名稱為 Apache Spark 集區和 SQL 集區，但這兩者並不相同。 這只是一些基本中繼資料，可讓您用來告知 Azure Synapse 工作區如何與 Spark 互動。

由於其為中繼資料，因此無法啟動或停止 Spark 集區。

當您在 Azure Synapse 中執行 Spark 活動時，需指定要使用的 Spark 集區。 集區會通知 Azure Synapse 要使用多少 Spark 資源。 您只需就您使用的資源付費。 當您主動停止使用集區時，資源將會自動逾時並予以回收。

> [!NOTE]
> 會從 Spark 集區獨立建立 Spark 資料庫。 工作區一律會有一個名為 **default** 的 Spark 資料庫。 您可以建立其他 Spark 資料庫。

## <a name="the-sql-on-demand-pool"></a>SQL 隨選集區

每個工作區都有一個預先建立的集區，稱為 **SQL 隨選**。 您無法刪除此集區。 SQL 隨選集區可讓您使用 SQL，而不需要在 Azure Synapse 中建立或考慮管理 SQL 集區。

不同於其他類型的集區，SQL 隨選計費是根據掃描以執行查詢的資料量，而不是用於執行查詢的資源數目。

* SQL 隨選有自己的 SQL 隨選資料庫，與任何 SQL 隨選集區分開存在。
* 工作區一律只會有一個名為 **SQL 隨選**的 SQL 隨選集區。

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>將 NYC 計程車範例資料載入 SQLDB1 資料庫

1. 在 Synapse Studio 的最上方藍色功能表中，選取 [？] 圖示。
1. 選取 [開始使用] > [快速入門中樞]。
1. 在標示為 [查詢範例資料] 的卡片中，選取名為 **SQLDB1** 的 SQL 集區。
1. 選取 [查詢資料]。 「載入範例資料」通知會短暫地顯示。 靠近 Synapse Studio 頂端的淺藍色狀態列會指出正在將資料載入 SQLDB1。
1. 當狀態列變成綠色之後，請將其關閉。

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>探索 SQL 集區中的 NYC 計程車資料

1. 在 Synapse Studio 中，移至 [資料] 中樞。
1. 前往 [SQLDB1] > [資料表]。 您會看到數個已載入的資料表。
1. 以滑鼠右鍵按一下 [dbo.Trip] 資料表，然後選取 [新增 SQL 指令碼] > [選取前 100 個資料列]。
1. 請等到新 SQL 指令碼建立完成並開始執行。
1. 請注意，在 SQL 指令碼頂端的 [連線到] 會自動設定成名為 **SQLDB1** 的 SQL 集區。
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

    此查詢會顯示總行程距離和平均路程距離與乘客數的關聯。
1. 在 [SQL 指令碼結果] 視窗中，將 [視圖] 變更為 [圖表]，以折線圖形式查看結果的視覺效果。

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>將 NYC 計程車資料載入 Spark nyctaxi 資料庫

**SQLDB1** 的資料表中已有可用資料。 將其載入名為 **nyctaxi** 的 Spark 資料庫。

1. 在 Synapse Studio 中，移至 [開發] 中樞。
1. 選取 [+] > [Notebook]。
1. 在筆記本頂端，將 [附加至] 的值設定為 **Spark1**。
1. 選取 [新增程式碼] 以新增筆記本程式碼儲存格，然後貼上下列文字：

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. 移至 [資料] 中樞，以滑鼠右鍵按一下 [資料庫]，然後選取 [重新整理]。 您應該會看到這些資料庫：

    - **SQLDB1** (SQL 集區)
    - **nyctaxi** (Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>使用 Spark 和筆記本來分析 NYC 計程車資料

1. 返回筆記本。
1. 建立新的程式碼儲存格，並輸入下列文字。 然後執行儲存格，以顯示我們載入到 **nyctaxi** Spark 資料庫中的 NYC 計程車資料。

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. 執行下列程式碼，以執行我們先前對 SQL 集區 **SQLDB1** 執行的相同分析。 此程式碼會將分析的結果儲存到名為 **nyctaxi.passengercountstats** 的資料表中，並將結果視覺化。

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

1. 在資料格結果中，選取 [圖表] 可查看視覺化的資料。

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>使用 Spark 和筆記本自訂資料視覺效果

您可以使用筆記本來控制圖表的呈現方式。 下列程式碼顯示一個簡單的範例。 其使用熱門的程式庫 **matplotlib** 和 **seaborn**。 該程式碼會轉譯與稍早所執行 SQL 查詢相同類型的折線圖。

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

先前我們已將資料從 SQL 集區資料表 **SQLDB1.dbo.Trip** 複製到 Spark 資料表 **nyctaxi.trip**。 然後，我們使用 Spark 將資料匯總到 Spark 資料表 **nyctaxi.passengercountstats** 中。 現在，我們會將資料從 **nyctaxi.passengercountstats** 複製到名為 **SQLDB1.dbo.PassengerCountStats** 的 SQL 集區資料表。

在您的筆記本中執行下列儲存格。 其會將彙總的 Spark 資料表複製回 SQL 集區資料表。

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>使用 SQL 隨選分析 Spark 資料庫中的 NYC 計程車資料

Spark 資料庫中的資料表會自動顯示，且可由 SQL 隨選進行查詢。

1. 在 Synapse Studio 中，移至 [開發] 中樞，然後建立新的 SQL 指令碼。
1. 將 [連線到] 設定為 [SQL 隨選]。
1. 將下列文字貼到指令碼中，然後執行指令碼。

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > 當您第一次執行使用 SQL 隨選的查詢時，SQL 隨選需要大約 10 秒的時間，才能收集執行查詢所需的 SQL 資源。 後續查詢的速度會更快。
  
## <a name="orchestrate-activities-with-pipelines"></a>使用管線協調活動

您可以在 Azure Synapse 中協調各種不同的工作。

1. 在 Synapse Studio 中，移至 [協調] 中樞。
1. 選取 [+] > [管線]，以建立新的管線。
1. 移至 [開發] 中樞，並尋找您先前建立的筆記本。
1. 將該筆記本拖曳到管線中。
1. 在管線中，選取 [新增觸發程序] > [新增/編輯]。
1. 在 [選擇觸發程序] 中，選取 [新增]，然後在 [週期] 中，將觸發程序設定為每隔 1 小時執行一次。
1. 選取 [確定]。
1. 選取 [全部發佈]。 管線會每小時執行一次。
1. 若要讓管線立即執行，而不等到下一個小時，請選取 [新增觸發程序] > [新增/編輯]。

## <a name="work-with-data-in-a-storage-account"></a>使用儲存體帳戶中的資料

目前為止，我們已介紹資料位於工作區中資料庫的案例。 現在，我們將示範如何使用儲存體帳戶中的檔案。 在此案例中，我們將使用工作區的主要儲存體帳戶，以及我們在建立工作區時所指定的容器。

* 儲存體帳戶的名稱：**contosolake**
* 儲存體帳戶中容器的名稱：**users**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>在您的儲存體帳戶中建立 CSV 和 Parquet 檔案

在筆記本資料格中執行下列命令。 其會在儲存體帳戶中建立 CSV 檔案和 Parquet 檔案。

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>分析儲存體帳戶中的資料

1. 在 Synapse Studio 中，移至 [資料] 中樞，然後選取 [連結的項目]。
1. 移至 [儲存體帳戶] > [myworkspace (主要 - contosolake)]。
1. 選取 [使用者 (主要)]。 您應該會看到 [NYCTaxi] 資料夾。 在其中，您應該會看到名為 **PassengerCountStats.csv** 和 **PassengerCountStats.parquet** 的資料夾。
1. 開啟 [PassengerCountStats.parquet] 資料夾。 您會在其中看到 Parquet 檔案，其名稱類似 part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet。
1. 在 **.parquet** 上按一下滑鼠右鍵，然後選取 [新增筆記本]。 其會建立具有資料格的筆記本，如下所示：

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. 執行資料格。
1. 以滑鼠右鍵按一下其中的 Parquet 檔案，然後選取 [新增指令碼] > [選取前 100 個資料列]。 其會建立如下所示的 SQL 指令碼：

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

     在指令碼中，[附加至] 欄位會設定為 [SQL 隨選]。

1. 執行指令碼。

## <a name="visualize-data-with-power-bi"></a>使用 Power BI 視覺化資料

從 NYC 計程車資料中，我們在兩個資料表中建立了彙總的資料集：
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

您可以將 Power BI 工作區連結至您的 Azure Synapse 工作區。 這可讓您輕鬆地將資料放入 Power BI 工作區。 您可以直接在 Azure Synapse 工作區中編輯您的 Power BI 報表。

### <a name="create-a-power-bi-workspace"></a>建立 Power BI 工作區

1. 登入 [powerbi.microsoft.com](https://powerbi.microsoft.com/)。
1. 建立名為 **NYCTaxiWorkspace1** 的新 Power BI 工作區。

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>將您的 Azure Synapse 工作區連結至您新的 Power BI 工作區

1. 在 Synapse Studio 中，移至 [管理] > [連結服務]。
1. 選取 [新增] > [連線到 Power BI]，然後設定下列欄位：

    |設定 | 建議的值 | 
    |---|---|
    |**名稱**|**NYCTaxiWorkspace1**|
    |**工作區名稱**|**NYCTaxiWorkspace1**|

1. 選取 [建立]。

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>建立在 Azure Synapse 工作區中使用資料的 Power BI 資料集

1. 在 Synapse Studio 中，移至 [開發] > [Power BI]。
1. 移至 [NYCTaxiWorkspace1] > [Power BI 資料集]，然後選取 [新增 Power BI 資料集]。
1. 將滑鼠停留在 **SQLDB1** 資料庫上，然後選取 [下載 .pbids 檔案]。
1. 開啟下載的 **.pbids** 檔案。 Power BI 桌面會隨之開啟，並自動連線到 Azure Synapse 工作區中的 **SQLDB1**。
1. 如果您看到名為 **SQL Server 資料庫** 的對話方塊：
    1. 選取 [Microsoft 帳戶]。
    1. 選取 [登入] 並登入您的帳戶。
    1. 選取 [連接]。
1. [導覽器] 對話方塊開啟之後，請檢查 **PassengerCountStats** 資料表，然後選取 [載入]。
1. 出現 [連線設定] 對話方塊之後，請選取 [DirectQuery] > [確定]。
1. 選取左側的 [報告] 按鈕。
1. 將 [折線圖] 新增至報告中。
    1. 將 [PasssengerCount] 資料行拖曳至 [視覺效果] > [軸]。
    1. 將 [SumTripDistance] 和 [AvgTripDistance] 資料行拖曳到 [視覺效果] > [值]。
1. 在 [首頁] 索引標籤中，選取 [發佈]。
1. 選取 [儲存] 來儲存變更。
1. 選擇檔案名稱 **PassengerAnalysis.pbix**，然後選取 [儲存]。
1. 在 [選取目的地] 中，選擇 [NYCTaxiWorkspace1]，然後按一下 [選取]。
1. 等待發佈完成。

### <a name="configure-authentication-for-your-dataset"></a>設定資料集的驗證

1. 開啟 [powerbi.microsoft.com](https://powerbi.microsoft.com/)，然後**登入**。
1. 在左側的 [工作區] 下，選取 [NYCTaxiWorkspace1] 工作區。
1. 在該工作區中，找出名為 [乘客分析 (Passenger Analysis)] 的資料集，以及名為 [乘客分析 (Passenger Analysis)] 的報表。
1. 將滑鼠停留在 [乘客分析 (Passenger Analysis)] 資料集上，選取省略符號 (...) 按鈕，然後選取 [設定]。
1. 在 [資料來源認證] 中，將 [驗證方法] 設為 [OAuth2]，然後選取 [登入]。

### <a name="edit-a-report-in-synapse-studio"></a>在 Synapse Studio 中編輯報告

1. 返回 Synapse Studio，然後選取 [關閉並重新整理]。
1. 移至 [開發] 中樞。
1. 將滑鼠停留在 [Power BI] 上，然後選取重新整理 [Power BI 報告] 節點。
1. 在 **Power BI** 底下，您應該會看到：
    1. 在 [NYCTaxiWorkspace1] > [Power BI 資料集] 下，新的資料集稱為 [PassengerAnalysis]。
    1. 在 [NYCTaxiWorkspace1] > [Power BI 報告] 下，新的報告稱為 [PassengerAnalysis]。
1. 選取 [PassengerAnalysis] 報告。 報表會隨即開啟，而且您可以直接在 Synapse Studio 內進行編輯。

## <a name="monitor-activities"></a>監視器活動

1. 在 Synapse Studio 中，移至 [監視] 中樞。
1. 在此位置中，您可以查看工作區中所發生之所有活動的歷程記錄，以及目前作用中的活動。
1. 探索 [管線執行]、[Apache Spark 應用程式] 和 [SQL 要求]，即可在工作區中查看已完成的作業。

## <a name="next-steps"></a>後續步驟

深入了解 [Azure Synapse Analytics (工作區預覽)](overview-what-is.md)。

