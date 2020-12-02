---
title: Azure Synapse Studio 筆記本
description: 本教學課程提供如何使用 Apache Spark 和無伺服器 SQL 集區來建立 Power BI 儀表板的概觀。
services: synapse-analytics
author: midesa
ms.author: midesa
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: ea8fcb602f49dec61187260e08d3ccd1b148cee8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95918915"
---
# <a name="tutorial-create-a-power-bi-report-using-apache-spark-and-azure-synapse-analytics"></a>教學課程：使用 Apache Spark 和 Azure Synapse Analytics 建立 Power BI 報表

通常，組織都需要先處理大量資料，然後才能為重要的商務專案關係人提供服務。 在本教學課程中，您將了解如何利用 Azure Synapse Analytics 中的整合式體驗來使用 Apache Spark 處理資料，然後透過 Power BI 和無伺服器 SQL 將資料提供給使用者。

## <a name="before-you-begin"></a>開始之前
- [Azure Synapse Analytics 工作區](../quickstart-create-workspace.md)，並將 ADLS Gen2 儲存體帳戶設定為預設儲存體。 
- 可將資料視覺化的 Power BI 工作區和 Power BI Desktop。 如需詳細資訊，請參閱[建立 Power BI 工作區](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces)和[安裝 Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- 連結的服務，用來連結您的 Azure Synapse Analytics 和 Power BI 工作區。 如需詳細資訊，請參閱 [Power BI 工作區的連結](../quickstart-power-bi.md)
- Synapse Analytics 工作區中的無伺服器 Apache Spark 集區。 如需詳細資訊，請參閱[建立無伺服器 Apache Spark 集區](../quickstart-create-apache-spark-pool-studio.md)
  
## <a name="download-and-prepare-the-data"></a>下載並準備資料
在此範例中，您將使用 Apache Spark 對紐約的計程車行程小費資料執行一些分析。 您可以透過 [Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)取得資料。 此資料集的子集包含黃色計程車行程的相關資訊，包括每個行程的相關資訊、開始和結束時間及位置、成本和其他有趣的屬性。

1. 執行下列幾行，將程式碼貼至新的儲存格來建立 Spark 資料框架。 這會透過開放資料集 API 來擷取資料。 提取所有資料會產生大約 15 億個資料列。 下列程式碼範例會使用 start_date 和 end_date 來套用篩選器，以傳回單一月份的資料。
   
   ```python
    from azureml.opendatasets import NycTlcYellow
    from dateutil import parser
    from datetime import datetime

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
   ```
2. 使用 Apache Spark SQL 時，我們將建立名為 NycTlcTutorial 的資料庫。 我們將使用此資料庫來儲存資料處理的結果。
   ```python
   %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS NycTlcTutorial")
   ```
3. 接下來，我們將使用 Spark 資料框架作業來處理資料。 在下列程式碼中，我們會執行下列轉換：
   1. 移除不需要的資料行。
   2. 透過篩選移除極端值/不正確的值。
   3. 建立 ```tripTimeSecs``` 和 ```tipped``` 等新功能，以進行額外的分析。
    ```python
    from pyspark.sql.functions import unix_timestamp, date_format, col, when

    taxi_df = filtered_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                    , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                    , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                    , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                    , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                    , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                    )\
                            .filter((filtered_df.passengerCount > 0) & (filtered_df.passengerCount < 8)\
                                    & (filtered_df.tipAmount >= 0) & (filtered_df.tipAmount <= 25)\
                                    & (filtered_df.fareAmount >= 1) & (filtered_df.fareAmount <= 250)\
                                    & (filtered_df.tipAmount < filtered_df.fareAmount)\
                                    & (filtered_df.tripDistance > 0) & (filtered_df.tripDistance <= 100)\
                                    & (filtered_df.rateCodeId <= 5)
                                    & (filtered_df.paymentType.isin({"1", "2"})))
    ```
4. 最後，我們會使用 Apache Spark ```saveAsTable``` 方法來儲存我們的資料框架。 這可讓您在稍後使用無伺服器 SQL 集區來查詢和連線到相同的資料表。
   
## <a name="query-data-using-serverless-sql-pools"></a>使用無伺服器 SQL 集區查詢資料
Azure Synapse Analytics 可讓不同的工作區計算引擎在其無伺服器 Apache Spark 集區 (預覽) 和無伺服器 SQL 集區 (預覽) 之間共用資料庫和資料表。 這是透過 Synapse 的[共用中繼資料管理](../metadata/overview.md)功能來提供支援。 因此，Spark 建立的資料庫及其受 Parquet 支援的資料表會在工作區無伺服器 SQL 集區引擎中顯示。

若要使用無伺服器 SQL 集區來查詢您的 Apache Spark 資料表：
   1. 儲存 Apache Spark 資料表之後，切換至 [資料] 索引標籤。
   
   2. 在 [工作區] 底下，尋找您剛建立的 Apache Spark 資料表，然後選取 [新的 SQL 指令碼]，然後 **選取前 100 個資料列**。 
      
      :::image type="content" source="../spark/media/apache-spark-power-bi/query-spark-table-with-sql.png" alt-text="SQL 查詢。" border="true":::

   3. 您可以使用 SQL 圖表選項進一步縮小查詢範圍，或甚至將結果視覺化。

## <a name="connect-to-power-bi"></a>連線至 Power BI
接下來，我們會將無伺服器 SQL 集區連線到 Power BI 工作區。 連線到您的工作區後，您就能夠直接從 Azure Synapse Analytics 和 Power BI Desktop 建立 Power BI 報表。

>[!Note]
> 開始之前，您必須先設定 [Power BI 工作區](../quickstart-power-bi.md)，並下載 [Power BI Desktop](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces)。  

若要將無伺服器 SQL 集區連線道 Power BI 工作區：

1.  瀏覽至 [Power BI 資料集] 索引標籤，然後選取 [+ 新增資料集] 選項。 在提示中，從您想要作為資料來源使用的 SQL Analytics 資料庫下載 .pbids 檔案。 
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-datasets.png" alt-text="Power BI 資料集。" border="true":::

2.  使用 Power BI Desktop 開啟檔案，以建立資料集。 開啟檔案後，使用 **Microsoft 帳戶** 和 [匯入] 選項來連線到 SQL 伺服器資料庫。 
   

## <a name="create-a-power-bi-report"></a>建立 Power BI 報表
1. 您現在可以從 Power BI Desktop 中，將 **關鍵影響因素** 圖表加入至報表中。
   
   1. 將平均 tipAmount 資料行拖曳至 [分析] 軸。
   
   2. 將 **weekdayString**、平均 **tripDistance** 和平均 **tripTimeSecs** 資料行拖曳至 [說明方式] 軸。 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-desktop.png" alt-text="Power BI Desktop。" border="true":::

2. 在 Power BI Desktop [首頁] 索引標籤上，選取 [發佈]，然後 **儲存** 變更。 輸入檔案名稱，並將此報表儲存至 [ 工作區]。
   
3. 此外，您也可以從 Azure Synapse Analytics 工作區中建立 Power BI 視覺效果。 若要這麼做，請瀏覽至 Azure Synapse 工作區中的 [開發] 索引標籤，然後開啟 [Power BI] 索引標籤。您可以從這裡選取報表，並繼續建立其他視覺效果。 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-synapse.png" alt-text="Azure Synapse Analytics 工作區。" border="true":::

如需有關如何透過無伺服器 SQL 建立資料集並連線到 Power BI 的詳細資訊，您可以瀏覽此教學課程來了解如何[連線到 Power BI Desktop](../../synapse-analytics/sql/tutorial-connect-power-bi-desktop.md)

## <a name="next-steps"></a>後續步驟
您可以造訪下列文件和教學課程，以繼續深入了解 Azure Synapse Analytics 中的資料視覺效果功能：
   - [使用無伺服器 Apache Spark 集區將資料視覺化](../spark/apache-spark-data-visualization-tutorial.md)
   - [使用 Apache Spark 集區的資料視覺效果概觀](../spark/apache-spark-data-visualization.md)