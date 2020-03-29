---
title: Azure HDInsight 中 Spark 叢集上的 Jupyter Notebook 核心
description: 了解可透過 Azure HDInsight 上 Spark 叢集提供 Jupyter Notebook 的 PySpark、PySpark3 和 Spark 核心。
keywords: spark 上的 jupyter notebook, jupyter spark
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/20/2020
ms.openlocfilehash: a04b8fee31ffa5280bc8ad0fca35495bb87e0e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064472"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Azure HDInsight 中 Apache Spark 叢集上的 Jupyter Notebook 核心

HDInsight Spark 叢集提供的核心，可讓您用於 [Apache Spark](./apache-spark-overview.md) 上的 Jupyter Notebook 以測試應用程式。 核心是一個可執行並解譯程式碼的程式。 三個核心為︰

- **PySpark** - 適用於以 Python2 撰寫的應用程式。
- **PySpark3** - 適用於以 Python3 撰寫的應用程式。
- **Spark** - 適用於以 Scala 撰寫的應用程式。

在本文中，您將了解使用這些核心的方式及優點。

## <a name="prerequisites"></a>Prerequisites

HDInsight 中的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>在 Spark HDInsight 上建立 Jupyter Notebook

1. 從[Azure 門戶](https://portal.azure.com/)中選擇 Spark 群集。  請參閱[列出和顯示叢集](../hdinsight-administer-use-portal-linux.md#showClusters)以取得指示。 將打開 **"概述"** 視圖。

2. 在 **"概述"** 視圖中，在 **"群集儀表板"** 框中，選擇**Jupyter 筆記本**。 出現提示時，輸入叢集的系統管理員認證。

    ![阿帕奇火花上的朱派筆記本](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "火花上的朱派筆記本")
  
   > [!NOTE]  
   > 您也可以在瀏覽器中開啟下列 URL，來觸達 Spark 叢集上的 Jupyter Notebook。 使用您叢集的名稱取代 **CLUSTERNAME** ：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. 選擇 **"新建**"，然後選擇 **"PySpark"、PySpark3**或**Spark**來創建筆記本。 **PySpark3** 使用適用於 Scala 應用程式的 Spark 核心、適用於 Python2 應用程式的 PySpark 核心，以及適用於 Python3 應用程式的 PySpark3 核心。

    ![火花上朱派筆記本的內核](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "火花上朱派筆記本的內核")

4. 將以您選取的核心開啟 Notebook。

## <a name="benefits-of-using-the-kernels"></a>使用這些核心的優點

以下是在 Spark HDInsight 叢集上使用新的核心搭配 Jupyter Notebook 的幾個優點。

- **預設內容**。 使用**PySpark、PySpark3**或**Spark**內核時，無需在開始使用應用程式之前顯式設置 Spark 或 Hive 上下文。 **PySpark3** 這些依預設都可用。 這些內容包括：

  - **sc** - 代表 Spark 內容
  - **sqlContext** - 代表 Hive 內容

    因此，您**不必**運行類似下面的語句來設置上下文：

         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)

    您可以直接在您的應用程式中使用現有的內容。

- **Cell magic**。 PySpark 內核提供一些預定義的"魔術"，它們是可以使用調用的特殊命令`%%`（例如）。 `%%MAGIC` `<args>` magic 命令必須是程式碼儲存格中的第一個字，而且允許多行的內容。 magic 這個字應該是儲存格中的第一個字。 在 magic 前面加入任何項目，甚至是註解，將會造成錯誤。     如需 magic 的詳細資訊，請參閱 [這裡](https://ipython.readthedocs.org/en/stable/interactive/magics.html)。

    下表列出透過核心而提供的不同 magic。

   | Magic | 範例 | 描述 |
   | --- | --- | --- |
   | help |`%%help` |產生所有可用 magic 的表格，其中包含範例與說明 |
   | info |`%%info` |輸出目前 Livy 端點的工作階段資訊 |
   | 設定 |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |設定用來建立工作階段的參數。 如果已建立工作階段，則強制旗標 (-f) 是必要的，可確保卸除並重新建立該工作階段。 如需有效參數的清單，請查看 [Livy 的 POST /sessions 要求本文](https://github.com/cloudera/livy#request-body) 。 參數必須以 JSON 字串傳遞，且必須在 magic 之後的下一行，如範例資料行中所示。 |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |針對 sqlContext 執行 Hive 查詢。 如果傳遞 `-o` 參數，則查詢的結果會當做 [Pandas](https://pandas.pydata.org/) 資料框架，保存在 %%local Python 內容中。 |
   | local |`%%local`<br>`a=1` |接下來幾行的程式碼全部在本機執行。 代碼必須是有效的 Python2 代碼，即使您使用的內核如何。 因此，即使您在創建筆記本時選擇了**PySpark3**或**Spark**內核，如果您在儲存格中使用`%%local`魔術，該儲存格也必須只有有效的 Python2 代碼。 |
   | 記錄 |`%%logs` |輸出目前 Livy 工作階段的記錄。 |
   | delete |`%%delete -f -s <session number>` |刪除目前 Livy 端點的特定工作階段。 不能刪除為內核本身啟動的會話。 |
   | cleanup |`%%cleanup -f` |刪除目前 Livy 端點的所有工作階段，包括此 Notebook 的工作階段。 force 旗標 -f 是必要的。 |

   > [!NOTE]  
   > 除了 PySpark 核心所新增的 Magic，您也可以使用[內建的 IPython Magic](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics) (包括 `%%sh`)。 您可以使用 `%%sh` Magic，在叢集前端節點上執行指令碼和程式碼區塊。

- **自動視覺化**。 Pyspark 核心會自動將 Hive 和 SQL 查詢的輸出視覺化。 有數種不同類型的視覺效果供您選擇，包括資料表、圓形圖、線條、區域、長條圖。

## <a name="parameters-supported-with-the-sql-magic"></a>%%sql magic 支援的參數

`%%sql` magic 支援不同的參數，可用來控制您執行查詢時收到的輸出類型。 下表列出輸出。

| 參數 | 範例 | 描述 |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |使用此參數，在 %%local Python 內容中保存查詢的結果，以做為 [Pandas](https://pandas.pydata.org/) 資料框架。 資料框架變數的名稱是您指定的變數名稱。 |
| -Q |`-q` |使用此項關閉儲存格的視覺效果。 如果不想自動視覺化儲存格的內容，並且只想將其捕獲為數據框，請使用`-q -o <VARIABLE>`。 如果您想要關閉視覺化功能而不擷取結果 (例如，執行 SQL 查詢的 `CREATE TABLE` 陳述式)，請使用 `-q` 但不要指定 `-o` 引數。 |
| -M |`-m <METHOD>` |其中 **METHOD** 是 **take** 或 **sample** (預設值是 **take**)。 如果方法是 **take**，核心會從 MAXROWS 指定的結果資料集頂端挑選項目 (如此表稍後所述)。 如果方法是 **sample**，核心會根據 `-r` 參數隨機取樣資料集的項目，如此表稍後所述。 |
| -r |`-r <FRACTION>` |這裡的 **FRACTION** 是介於 0.0 到 1.0 之間的浮點數。 如果 SQL 查詢的範例方法是 `sample`，則核心會為您從結果集隨機取樣指定比例的項目。 例如，如果您使用 `-m sample -r 0.01` 引數執行 SQL 查詢，則會隨機取樣 1% 的結果資料列。 |
| -n |`-n <MAXROWS>` |**MAXROWS** 是整數值。 核心會將輸出資料列的數目限制為 **MAXROWS**。 如果**MAXROWS**是負數，如 **-1**，則結果集中的行數不受限制。 |

**例子：**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

上面的陳述式會執行下列動作︰

- 從 **hivesampletable**選取所有記錄。
- 因為我們使用 -q，它關閉自動視覺化。
- 因為我們使用 `-m sample -r 0.1 -n 500` ，所以它會從 hivesampletable 的資料列中隨機取樣 10%，並將結果集的大小限制為 500 個資料列。
- 最後，因為我們使用 `-o query2` ，所以它也會將輸出儲存成名為 **query2**的資料框架。

## <a name="considerations-while-using-the-new-kernels"></a>使用新核心的考量

無論您使用何種核心，讓 Notebook 持續執行會耗用叢集資源。  有了這些內核，因為上下文是預設的，因此只需退出筆記本不會殺死上下文，因此群集資源仍在使用中。 一個很好的做法是在使用完筆記本後使用筆記本 **"檔"** 功能表中的 **"關閉"和"停止"** 選項，該筆記本將終止上下文，然後退出筆記本。

## <a name="where-are-the-notebooks-stored"></a>Notebook 會儲存在哪裡？

如果您的叢集使用 Azure 儲存體作為預設的儲存體帳戶，Jupyter 筆記本會儲存在儲存體帳戶的 **/HdiNotebooks** 資料夾底下。  您從 Jupyter 內建立的 Notebook、文字檔案和資料夾，都可從儲存體帳戶存取。  例如，如果您使用 Jupyter 建立資料夾 **myfolder** 和 Notebook **myfolder/mynotebook.ipynb**，您可以在儲存體帳戶內從 `/HdiNotebooks/myfolder/mynotebook.ipynb` 存取該 Notebook。  反之亦然，也就是說，如果您直接將 Notebook 上傳至儲存體帳戶的 `/HdiNotebooks/mynotebook1.ipynb`，則從 Jupyter 也能看到該 Notebook。  即使在刪除叢集之後，Notebook 仍會保留在儲存體帳戶中。

> [!NOTE]  
> 使用 Azure Data Lake Store 作為預設儲存體帳戶的 HDInsight 叢集，不會在相關聯的儲存體中儲存筆記本。

將 Notebook 儲存到儲存體帳戶的方式與 [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) 相容。 因此，如果對叢集執行 SSH，您可以使用檔案管理命令，如下列程式碼片段所示︰

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                   # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it's visible from Jupyter

無論叢集是使用 Azure 儲存體或 Azure Data Lake Store 作為預設的儲存體帳戶，筆記本也會儲存在叢集前端節點的 `/var/lib/jupyter` 上。

## <a name="supported-browser"></a>支援的瀏覽器

Google Chrome 上只支援 Spark HDInsight 叢集上的 Jupyter Notebook。

## <a name="feedback"></a>意見反應

新的核心已在發展階段，而且經過一段時間後將會成熟。 這或許也意味著，API 可能會隨著這些核心的成熟而改變。 您在使用這些新核心時如有任何意見，我們都非常樂於知道。 這對於這些核心最終版本的定調很有幫助。 您可以在本文底部的 **"回饋"** 部分下留下您的評論/回饋。

## <a name="see-also"></a>另請參閱

- [概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>案例

- [Apache Spark 和 BI：在 HDInsight 中搭配 BI 工具使用 Spark 執行互動式資料分析](apache-spark-use-bi-tools.md)
- [Apache Spark 和機器學習服務：使用 HDInsight 中的 Spark，使用 HVAC 資料來分析建築物溫度](apache-spark-ipython-notebook-machine-learning.md)
- [Apache Spark 和機器學習服務：在 HDInsight 中使用 Spark 預測食品檢查結果](apache-spark-machine-learning-mllib-ipython.md)
- [在 HDInsight 中使用 Apache Spark 進行網站記錄分析](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>建立及執行應用程式

- [使用 Scala 建立獨立應用程式](apache-spark-create-standalone-application.md)
- [利用 Apache Livy 在 Apache Spark 叢集上遠端執行作業](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和擴充功能

- [使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式來建立和提交 Spark Scala 應用程式](apache-spark-intellij-tool-plugin.md)
- [使用適用於 IntelliJ IDEA 的 HDInsight 工具外掛程式遠端偵錯 Apache Spark 應用程式](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
- [在 HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集](apache-spark-zeppelin-notebook.md)
- [搭配 Jupyter Notebook 使用外部套件](apache-spark-jupyter-notebook-use-external-packages.md)
- [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理資源

- [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
- [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](apache-spark-job-debugging.md)
