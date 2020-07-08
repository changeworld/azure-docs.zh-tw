---
title: 在 Spark 中搭配 Python 程式庫分析網站記錄 - Azure
description: 此 Notebook 示範如何使用自訂程式庫搭配 Azure HDInsight 上的 Spark 來分析記錄資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: c6bf26d8f3a73db6ee69b2aa0de73872911893bf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75552707"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>使用自訂 Python 程式庫搭配 HDInsight 上的 Apache Spark 叢集來分析網站記錄

此 Notebook 示範如何使用自訂程式庫搭配 HDInsight 上的 Apache Spark 來分析記錄資料。 我們使用的自訂程式庫是名為 **iislogparser.py**的 Python 程式庫。

## <a name="prerequisites"></a>Prerequisites

HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。

## <a name="save-raw-data-as-an-rdd"></a>將原始資料儲存為 RDD

在本節中，我們會使用與 HDInsight 中的 Apache Spark 叢集相關聯的 [Jupyter](https://jupyter.org) Notebook，來執行會處理原始範例資料，並將這些資料儲存成 Hive 資料表的作業。 範例資料是所有叢集在預設情況下均有的 .csv 檔案 (hvac.csv)。

當您的資料儲存為 Apache Hive 資料表之後，下一節會使用 Power BI 和 Tableau 等 BI 工具連接到 Hive 資料表。

1. 從網頁瀏覽器瀏覽至 `https://CLUSTERNAME.azurehdinsight.net/jupyter`，其中 `CLUSTERNAME` 是叢集的名稱。

1. 建立新的 Notebook。 依序選取 [**新增**] 和 [ **PySpark**]。

    ![建立新的 Apache Jupyter 筆記本](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "建立新的 Jupyter Notebook")

1. 系統隨即會建立新 Notebook，並以 Untitled.pynb 的名稱開啟。 選取頂端的 [筆記本名稱]，然後輸入好記的名稱。

    ![提供 Notebook 的名稱](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "提供 Notebook 的名稱")

1. 因為您是使用 PySpark 核心建立筆記本，所以您不需要明確建立任何內容。 當您執行第一個程式碼儲存格時，系統會自動為您建立 Spark 和 Hive 內容。 首先，您可以匯入此案例需要的類型。 將下列程式碼片段貼到空白儲存格中，然後按下**Shift + enter**。

    ```pyspark
    from pyspark.sql import Row
    from pyspark.sql.types import *
    ```

1. 使用叢集上已有的範例記錄資料來建立 RDD。 您可以在與叢集相關聯的預設儲存體帳戶中存取資料 `\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log` 。 執行以下程式碼：

    ```pyspark
    logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')
    ```

1. 擷取範例記錄組，以確認先前的步驟已順利完成。

    ```pyspark
    logs.take(5)
    ```

    您應該會看到類似下列文字的輸出：

    ```output
    [u'#Software: Microsoft Internet Information Services 8.0',
    u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']
    ```

## <a name="analyze-log-data-using-a-custom-python-library"></a>使用自訂 Python 程式庫來分析記錄資料

1. 在上述輸出中，前幾行包含標頭資訊，其餘各行則符合該標頭中所說明的結構描述。 剖析這類記錄可能是複雜的作業。 因此，我們使用自訂 Python 程式庫 (**iislogparser.py**)，它可大幅簡化這類記錄的剖析。 根據預設，此程式庫會隨附于 HDInsight 上的 Spark 叢集，網址為 `/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py` 。

    不過，此程式庫不在中， `PYTHONPATH` 因此無法使用匯入語句（例如） `import iislogparser` 。 若要使用此程式庫，我們必須將它散發到所有的背景工作角色節點。 執行下列程式碼片段。

    ```pyspark
    sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')
    ```

1. `iislogparser` 提供一個函數 `parse_log_line`，在記錄行為標頭資料列時會傳回 `None`，在發現記錄行時則傳回 `LogLine` 類別的執行個體。 使用 `LogLine` 類別，僅從 RDD 擷取記錄行：

    ```pyspark
    def parse_line(l):
        import iislogparser
        return iislogparser.parse_log_line(l)
    logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
    ```

1. 擷取幾個記錄行，以確認步驟已順利完成。

    ```pyspark
    logLines.take(2)
    ```

   輸出應該類似下列文字：

    ```output
    [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
    2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
    ```

1. `LogLine` 類別也有一些有用的方法，像是 `is_error()`，它會傳回記錄項目是否有錯誤碼。 使用此類別來計算已解壓縮記錄行中的錯誤數目，然後將所有錯誤記錄到不同的檔案。

    ```pyspark
    errors = logLines.filter(lambda p: p.is_error())
    numLines = logLines.count()
    numErrors = errors.count()
    print 'There are', numErrors, 'errors and', numLines, 'log entries'
    errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')
    ```

    輸出應該會是狀態 `There are 30 errors and 646 log entries` 。

1. 您也可以使用 **Matplotlib** 來建構資料的視覺效果。 例如，如果您想要找出要求長時間執行的原因，您可以尋找平均佔用最多服務資源的檔案。 下列程式碼片段會擷取耗費最多時間為要求提供服務的前 25 個資源。

    ```pyspark
    def avgTimeTakenByKey(rdd):
        return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                    .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

    avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])
    ```

   您應該會看到類似下列文字的輸出：

    ```output
    [(u'/blogposts/mvc4/step13.png', 197.5),
    (u'/blogposts/mvc2/step10.jpg', 179.5),
    (u'/blogposts/extractusercontrol/step5.png', 170.0),
    (u'/blogposts/mvc4/step8.png', 159.0),
    (u'/blogposts/mvcrouting/step22.jpg', 155.0),
    (u'/blogposts/mvcrouting/step3.jpg', 152.0),
    (u'/blogposts/linqsproc1/step16.jpg', 138.75),
    (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
    (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
    (u'/blogposts/nested/step2.jpg', 126.0),
    (u'/blogposts/adminpack/step1.png', 124.0),
    (u'/BlogPosts/datalistpaging/step2.png', 118.0),
    (u'/blogposts/mvc4/step35.png', 117.0),
    (u'/blogposts/mvcrouting/step2.jpg', 116.5),
    (u'/blogposts/aboutme/basketball.jpg', 109.0),
    (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
    (u'/blogposts/mvc4/step12.png', 106.0),
    (u'/blogposts/linq8/step0.jpg', 105.5),
    (u'/blogposts/mvc2/step18.jpg', 104.0),
    (u'/blogposts/mvc2/step11.jpg', 104.0),
    (u'/blogposts/mvcrouting/step1.jpg', 104.0),
    (u'/blogposts/extractusercontrol/step1.png', 103.0),
    (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
    (u'/blogposts/mvcrouting/step21.jpg', 101.0),
    (u'/blogposts/mvc4/step1.png', 98.0)]
    ```

1. 您也可以用繪圖的形式呈現這項資訊。 建立繪圖的第一個步驟是建立暫存資料表 **AverageTime**。 此資料表會依時間將記錄分組，以查看在任何特定時間中是否出現異常的延遲尖峰。

    ```pyspark
    avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
    schema = StructType([StructField('Minutes', IntegerType(), True),
                        StructField('Time', FloatType(), True)])

    avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
    avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
    ```

1. 接著，您可以執行下列 SQL 查詢取得 **AverageTime** 資料表中的所有記錄。

    ```pyspark
    %%sql -o averagetime
    SELECT * FROM AverageTime
    ```

   `%%sql` magic 後面緊接著 `-o averagetime` 可確保查詢的輸出會保存在 Jupyter 伺服器的本機上 (通常是叢集的前端節點)。 輸出內容會以具有指定名稱 [averagetime](https://pandas.pydata.org/) 的 **Pandas**資料框架保存。

   您應該會看到如下圖所示的輸出：

   ![hdinsight jupyter SQL 查詢輸出](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "SQL 查詢輸出")

   如需 `%%sql` magic 的詳細資訊，請參閱 [%%sql magic 支援的參數](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)。

1. 現在您可以使用 Matplotlib (用於建構資料視覺效果的程式庫) 建立繪圖。 因為必須從保存在本機上的 **averagetime** 資料框架建立繪圖，所以程式碼片段的開頭必須為 `%%local` magic。 這可確保程式碼是在 Jupyter 伺服器的本機上執行。

    ```pyspark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
    plt.xlabel('Time (min)')
    plt.ylabel('Average time taken for request (ms)')
    ```

   您應該會看到如下圖所示的輸出：

   ![apache spark web 記錄分析繪圖](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Matplotlib 輸出")

1. 應用程式執行完畢之後，您應該關閉 Notebook 以釋放資源。 若要這麼做，請從 Notebook 的 [檔案]  功能表中，選取 [關閉並終止]  。 此動作將會關閉並關閉筆記本。

## <a name="next-steps"></a>後續步驟

探索下列文章：

* [概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)
* [搭配 Jupyter Notebook 使用外部套件](apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](apache-spark-jupyter-notebook-install-locally.md)