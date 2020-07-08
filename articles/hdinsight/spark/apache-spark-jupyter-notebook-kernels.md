---
title: Azure HDInsight 中 Spark 叢集上的 Jupyter Notebook 核心
description: 了解可透過 Azure HDInsight 上 Spark 叢集提供 Jupyter Notebook 的 PySpark、PySpark3 和 Spark 核心。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 8c3993d8208a9a9e2ab54be44d88de0b20a2e586
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86084710"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Azure HDInsight 中 Apache Spark 叢集上的 Jupyter Notebook 核心

HDInsight Spark 叢集提供的核心，可讓您用於 [Apache Spark](./apache-spark-overview.md) 上的 Jupyter Notebook 以測試應用程式。 核心是一個可執行並解譯程式碼的程式。 三個核心為︰

- **PySpark** - 適用於以 Python2 撰寫的應用程式。
- **PySpark3** - 適用於以 Python3 撰寫的應用程式。
- **Spark** - 適用於以 Scala 撰寫的應用程式。

在本文中，您將了解使用這些核心的方式及優點。

## <a name="prerequisites"></a>必要條件

HDInsight 中的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>在 Spark HDInsight 上建立 Jupyter Notebook

1. 從 [ [Azure 入口網站](https://portal.azure.com/)] 中，選取您的 Spark 叢集。  請參閱[列出和顯示叢集](../hdinsight-administer-use-portal-linux.md#showClusters)以取得指示。 [**總覽**] 視圖隨即開啟。

2. 從 [**總覽**] 視圖的 [叢集**儀表板**] 方塊中，選取 [ **Jupyter 筆記本**]。 出現提示時，輸入叢集的系統管理員認證。

    ![Apache Spark 上的 Jupyter 筆記本](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Spark 上的 Jupyter 筆記本")
  
   > [!NOTE]  
   > 您也可以在瀏覽器中開啟下列 URL，來觸達 Spark 叢集上的 Jupyter Notebook。 使用您叢集的名稱取代 **CLUSTERNAME** ：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. 選取 [**新增**]，然後選取 [ **Pyspark**]、[ **PySpark3**] 或 [ **Spark** ] 來建立筆記本。 使用適用於 Scala 應用程式的 Spark 核心、適用於 Python2 應用程式的 PySpark 核心，以及適用於 Python3 應用程式的 PySpark3 核心。

    ![Spark 上的 Jupyter 筆記本核心](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Spark 上的 Jupyter 筆記本核心")

4. 將以您選取的核心開啟 Notebook。

## <a name="benefits-of-using-the-kernels"></a>使用這些核心的優點

以下是在 Spark HDInsight 叢集上使用新的核心搭配 Jupyter Notebook 的幾個優點。

- **預設內容**。 使用**PySpark**、 **PySpark3**或**Spark**核心時，您不需要明確地設定 Spark 或 Hive 內容，就能開始使用您的應用程式。 這些內容預設是可用的。 這些內容包括：

  - **sc** - 代表 Spark 內容
  - **sqlContext** - 代表 Hive 內容

    因此，您**不**需要執行如下所示的語句來設定內容：

    ```sql
    sc = SparkContext('yarn-client')
    sqlContext = HiveContext(sc)
    ```

    您可以直接在您的應用程式中使用現有的內容。

- **Cell magic**。 PySpark 核心提供一些預先定義的 "magic"，這是您可以使用呼叫的特殊命令 `%%` （例如 `%%MAGIC` `<args>` ）。 magic 命令必須是程式碼儲存格中的第一個字，而且允許多行的內容。 magic 這個字應該是儲存格中的第一個字。 在 magic 前面加入任何項目，甚至是註解，將會造成錯誤。     如需 magic 的詳細資訊，請參閱 [這裡](https://ipython.readthedocs.org/en/stable/interactive/magics.html)。

    下表列出透過核心而提供的不同 magic。

   | Magic | 範例 | 描述 |
   | --- | --- | --- |
   | help |`%%help` |產生所有可用 magic 的表格，其中包含範例與說明 |
   | info |`%%info` |輸出目前 Livy 端點的工作階段資訊 |
   | 設定 |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |設定用來建立工作階段的參數。 `-f`如果已經建立會話，強制旗標（）是必要的，這可確保卸載並重新建立會話。 如需有效參數的清單，請查看 [Livy 的 POST /sessions 要求本文](https://github.com/cloudera/livy#request-body) 。 參數必須以 JSON 字串傳遞，且必須在 magic 之後的下一行，如範例資料行中所示。 |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |針對 sqlContext 執行 Hive 查詢。 如果傳遞 `-o` 參數，則查詢的結果會當做 [Pandas](https://pandas.pydata.org/) 資料框架，保存在 %%local Python 內容中。 |
   | 本機 |`%%local`<br>`a=1` |後續行中的所有程式碼都是在本機執行。 無論您使用哪一種核心，程式碼都必須是有效的 Python2 程式碼。 因此，即使您在建立筆記本時選取了**PySpark3**或**Spark**核心，如果您在資料 `%%local` 格中使用魔術，該資料格必須只有有效的 Python2 程式碼。 |
   | 記錄 |`%%logs` |輸出目前 Livy 工作階段的記錄。 |
   | delete |`%%delete -f -s <session number>` |刪除目前 Livy 端點的特定工作階段。 您無法刪除針對核心本身啟動的會話。 |
   | cleanup |`%%cleanup -f` |刪除目前 Livy 端點的所有工作階段，包括此 Notebook 的工作階段。 force 旗標 -f 是必要的。 |

   > [!NOTE]  
   > 除了 PySpark 核心所新增的 Magic，您也可以使用[內建的 IPython Magic](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics) (包括 `%%sh`)。 您可以使用 `%%sh` Magic，在叢集前端節點上執行指令碼和程式碼區塊。

- **自動視覺化**。 Pyspark 核心會自動將 Hive 和 SQL 查詢的輸出視覺化。 有數種不同類型的視覺效果供您選擇，包括資料表、圓形圖、線條、區域、長條圖。

## <a name="parameters-supported-with-the-sql-magic"></a>%%sql magic 支援的參數

`%%sql` magic 支援不同的參數，可用來控制您執行查詢時收到的輸出類型。 下表列出輸出。

| 參數 | 範例 | 描述 |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |使用此參數，在 %%local Python 內容中保存查詢的結果，以做為 [Pandas](https://pandas.pydata.org/) 資料框架。 資料框架變數的名稱是您指定的變數名稱。 |
| -Q |`-q` |使用此參數來關閉儲存格的視覺效果。 如果您不想要 autovisualize 資料格的內容，而只想要將它當做資料框架來捕捉，請使用 `-q -o <VARIABLE>` 。 如果您想要關閉視覺化功能而不擷取結果 (例如，執行 SQL 查詢的 `CREATE TABLE` 陳述式)，請使用 `-q` 但不要指定 `-o` 引數。 |
| -M |`-m <METHOD>` |其中 **METHOD** 是 **take** 或 **sample** (預設值是 **take**)。 如果方法是 **`take`** ，則核心會從 MAXROWS 指定的結果資料集頂端挑選項目（如本表格稍後所述）。 如果方法是 **sample**，核心會根據 `-r` 參數隨機取樣資料集的項目，如此表稍後所述。 |
| -r |`-r <FRACTION>` |這裡的 **FRACTION** 是介於 0.0 到 1.0 之間的浮點數。 如果 SQL 查詢的範例方法是 `sample`，則核心會為您從結果集隨機取樣指定比例的項目。 例如，如果您使用 `-m sample -r 0.01` 引數執行 SQL 查詢，則會隨機取樣 1% 的結果資料列。 |
| -n |`-n <MAXROWS>` |**MAXROWS** 是整數值。 核心會將輸出資料列的數目限制為 **MAXROWS**。 如果**MAXROWS**是負數（例如 **-1**），則結果集中的資料列數目不會受到限制。 |

**範例︰**

```sql
%%sql -q -m sample -r 0.1 -n 500 -o query2
SELECT * FROM hivesampletable
```

上面的語句會執行下列動作：

- 從 **hivesampletable**選取所有記錄。
- 因為我們使用-q，所以會關閉 autovisualization。
- 因為我們使用 `-m sample -r 0.1 -n 500` ，所以它會在 hivesampletable 中隨機取樣10% 的資料列，並將結果集的大小限制為500個數據列。
- 最後，因為我們使用 `-o query2` ，所以它也會將輸出儲存成名為 **query2**的資料框架。

## <a name="considerations-while-using-the-new-kernels"></a>使用新核心的考量

無論您使用何種核心，讓 Notebook 持續執行會耗用叢集資源。  在這些核心中，由於內容是預設值，因此只會結束筆記本並不會終止內容。 因此，叢集資源會繼續使用。 當您完成使用筆記本時，最好是使用筆記本的 [檔案]**功能表中**的 [**關閉並停止**] 選項。 關閉作業會刪除內容，然後離開筆記本。

## <a name="where-are-the-notebooks-stored"></a>Notebook 會儲存在哪裡？

如果您的叢集使用 Azure 儲存體作為預設的儲存體帳戶，Jupyter 筆記本會儲存在儲存體帳戶的 **/HdiNotebooks** 資料夾底下。  您從 Jupyter 內建立的 Notebook、文字檔案和資料夾，都可從儲存體帳戶存取。  例如，如果您使用 Jupyter 來建立資料夾 **`myfolder`** 和筆記本**myfolder/稱為 mynotebook ipynb**，您可以在 `/HdiNotebooks/myfolder/mynotebook.ipynb` 儲存體帳戶中存取該筆記本。  反之亦然，也就是說，如果您直接將 Notebook 上傳至儲存體帳戶的 `/HdiNotebooks/mynotebook1.ipynb`，則從 Jupyter 也能看到該 Notebook。  即使在刪除叢集之後，Notebook 仍會保留在儲存體帳戶中。

> [!NOTE]  
> 使用 Azure Data Lake Store 作為預設儲存體帳戶的 HDInsight 叢集，不會在相關聯的儲存體中儲存筆記本。

將 Notebook 儲存到儲存體帳戶的方式與 [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) 相容。 如果您透過 SSH 連線到叢集，您可以使用檔案管理命令：

| Command | 描述 |
|---------|-------------|
| `hdfs dfs -ls /HdiNotebooks` | # 列出根目錄的所有內容–從首頁 Jupyter 可看到此目錄中的所有專案 |
| `hdfs dfs –copyToLocal /HdiNotebooks` | # 下載 HdiNotebooks 資料夾的內容|
| `hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks` | # 上傳筆記本範例. ipynb 至根資料夾，使其可從 Jupyter 中看到 |

無論叢集是使用 Azure 儲存體還是 Azure Data Lake Storage 做為預設儲存體帳戶，筆記本也會儲存在叢集前端節點上 `/var/lib/jupyter` 。

## <a name="supported-browser"></a>支援的瀏覽器

Google Chrome 上只支援 Spark HDInsight 叢集上的 Jupyter Notebook。

## <a name="suggestions"></a>建議

新的核心已在發展階段，而且經過一段時間後將會成熟。 因此，在這些核心成熟時，Api 可能會改變。 您在使用這些新核心時如有任何意見，我們都非常樂於知道。 意見反應在塑造這些核心的最終發行版本時非常有用。 您可以在本文底部的**意見**反應區段下留下您的意見/意見反應。

## <a name="next-steps"></a>後續步驟

- [概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)
- [在 HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集](apache-spark-zeppelin-notebook.md)
- [搭配 Jupyter Notebook 使用外部套件](apache-spark-jupyter-notebook-use-external-packages.md)
- [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](apache-spark-jupyter-notebook-install-locally.md)
