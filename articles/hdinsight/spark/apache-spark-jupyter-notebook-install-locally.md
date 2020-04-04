---
title: 在本機安裝 Jupyter 並連線到 Azure HDInsight 中的 Spark
description: 了解如何在電腦本機安裝 Jupyter Notebook，並連線到 Apache Spark 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/02/2020
ms.openlocfilehash: 1d044ddaea0a2c7a1d489523cc9aa4515df0728a
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632665"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>在電腦上安裝 Jupyter Notebook，並連線到 HDInsight 上的 Apache Spark

在本文中,您將學習如何使用自定義 PySpark(用於 Python)和 Apache Spark(用於 Scala)內核安裝具有 Spark 魔法的 Jupyter 筆記型電腦。 然後,將筆記本連接到 HDInsight 群集。

在 HDInsight 上安裝 Jupyter 和連接到 Apache Spark 涉及四個關鍵步驟。

* 配置 Spark 群集。
* 安裝 Jupyter Notebook。
* 安裝含有 Spark magic 的 PySpark 和 Spark 核心。
* 設定 Spark magic 以存取 HDInsight 上的 Spark 叢集。

有關自訂內核和 Spark 魔法的詳細資訊,請參閱[HDInsight 上帶有 Apache Spark Linux 群集的猶太筆記本的內核](apache-spark-jupyter-notebook-kernels.md)。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。 本地筆記本連接到 HDInsight 群集。

* 熟悉如何搭配使用 Jupyter Notebook 和 HDInsight 上的 Spark。

## <a name="install-jupyter-notebook-on-your-computer"></a>在電腦上安裝 Jupyter Notebook

安裝 Jupyter 筆記型電腦之前安裝 Python。 [Anaconda 發行版](https://www.anaconda.com/download/)將同時安裝 Python 和 Jupyter 筆記本。

下載您平台適用的 [Anaconda 安裝程式](https://www.anaconda.com/download/) ，然後執行安裝程式。 執行安裝精靈時，請確定您選取將 Anaconda 新增至 PATH 變數的選項。  另請參閱,[使用阿納康達安裝朱皮](https://jupyter.readthedocs.io/en/latest/install.html)特 。

## <a name="install-spark-magic"></a>安裝火花魔法

1. 輸入以下命令之一以安裝 Spark 魔法。 另請參閱[,火花文件](https://github.com/jupyter-incubator/sparkmagic#installation)。

    |叢集版本 | 安裝命令 |
    |---|---|
    |v3.6 和 v3.5 |`pip install sparkmagic==0.13.1`|
    |v3.4|`pip install sparkmagic==0.2.3`|

1. 以`ipywidgets`執行以下指令的完整安裝:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>安裝 PySpark 與 Spark 內核

1. 以輸入`sparkmagic`以下指令確定安裝位置:

    ```cmd
    pip show sparkmagic
    ```

    然後,將工作目錄更改為使用上述命令識別**的位置**。

1. 從新的工作目錄中,輸入以下一個或多個指令以安裝想要的內核:

    |核心 | Command |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |皮斯帕克3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. 選擇性。 輸入下面的指令以啟用伺服器延伸:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>設定 Spark magic 以連線到 HDInsight Spark 叢集

在本節中,您可以配置之前安裝的 Spark 魔術以連接到 Apache Spark 群集。

1. 使用以下指令啟動 Python 外殼:

    ```cmd
    python
    ```

2. Jupyter 組態資訊通常儲存在使用者主目錄中。 輸入以下命令以標識主目錄,並創建一個名為**\.Sparkmagic**的資料夾。  將輸出完整路徑。

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. 在資料夾中`.sparkmagic`,創建名為**config.json**的檔,並在其中添加以下 JSON 程式碼段。  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "custom_headers" : {
        "X-Requested-By": "livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. 對檔案進行以下編輯:

    |樣本值 | 新值 |
    |---|---|
    |[使用者名稱]|叢集登入,預設值`admin`為 。|
    |[群集 DNS 名稱]|叢集名稱|
    |[BASE64編碼密碼]|基本密碼為實際密碼。  您可以在生成[https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/)base64 密碼。|
    |`"livy_server_heartbeat_timeout_seconds": 60`|如果使用`sparkmagic 0.12.7`(群集 v3.5 和 v3.6),請保留。  如果使用`sparkmagic 0.2.3`(群集 v3.4),則`"should_heartbeat": true`取代為 。|

    您可以在[範例 config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json)上看到完整的範例檔。

   > [!TIP]  
   > 傳送活動訊號可確保不會流失工作階段。 當電腦進入睡眠或已關機時，則不會傳送活動訊號，導致工作階段被清除。 若為叢集 3.4 版，如果想要停用此行為，您可以從 Ambari UI 將 Livy 組態 `livy.server.interactive.heartbeat.timeout` 設定為 `0`。 若為叢集 3.5 版，如果您未設定上述的 3.5 組態，則不會刪除工作階段。

5. 啟動 Jupyter。 從命令提示字元使用下列命令。

    ```cmd
    jupyter notebook
    ```

6. 驗證是否可以將可用的 Spark 魔術與內核一起使用。 完成下列步驟。

    a. 建立新的 Notebook。 從右側角選擇 **"新建**"。 您應該會看到預設內核**Python 2**或**Python 3**以及您安裝的內核。 實際值可能因安裝選擇而異。  選擇**PySpark**。

    ![猶太筆記筆記本中的可用內核](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "聚居器筆記本中的內核")

    > [!IMPORTANT]  
    > 選擇 **"新建"** 後,請審核 shell 是否存在任何錯誤。  如果您看到錯誤`TypeError: __init__() got an unexpected keyword argument 'io_loop'`,您可能遇到某些版本的龍捲風的已知問題。  如果是這樣,請停止內核,然後使用以下命令降級您的龍捲風安裝: `pip install tornado==4.5.3`。

    b. 執行下列程式碼片段。

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    如果您可以順利擷取輸出，即表示已測試您對 HDInsight 叢集的連線。

    如果要更新筆記本配置以連接到其他群集,請使用新值集更新 config.json,如上文步驟 3 所示。

## <a name="why-should-i-install-jupyter-on-my-computer"></a>為什麼我應該在我的電腦上安裝 Jupyter？

在電腦上安裝 Jupyter,然後將其連接到 HDInsight 上的 Apache Spark 群集的原因:

* 提供在本地創建筆記本、根據正在運行的群集測試應用程式,然後將筆記本上載到群集的選項。 要將筆記本上載到群集,可以使用正在運行的 Jupyter 筆記本或群集上傳筆記本,也可以將其保存到與群集關聯的存儲帳戶中的`/HdiNotebooks`資料夾中。 如需有關 Notebook 在叢集上的儲存方式詳細資訊，請參閱 [Jupyter Notebook 會儲存在哪裡](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)？
* 使用本機可用的 Notebook，您可以根據您的應用程式需求，連接至不同的 Spark 叢集。
* 您可以使用 GitHub 實作來源控制系統，並且具有 Notebook 的版本控制。 您也可以有共同作業環境，其中多個使用者可以使用相同的 Notebook。
* 您甚至不需要啟用叢集，即可在本機使用 Notebook。 您只需要叢集針對 Notebook 進行測試，不必以手動方式管理您的 Notebook 或開發環境。
* 配置自己的本地開發環境可能比在群集上配置 Jupyter 安裝更容易。  您可以利用您在本地安裝的所有軟體,而無需配置一個或多個遠端群集。

> [!WARNING]  
> 將 Jupyter 安裝在本機電腦上，多個使用者可以同時在相同的 Spark 叢集上執行相同的 Notebook。 在這種情況下，會建立多個 Livy 工作階段。 如果您遇到問題，而且想要偵錯，則追蹤哪個 Livy 工作階段屬於哪個使用者會是複雜的工作。  

## <a name="next-steps"></a>後續步驟

* [概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)
* [使用 BI 的 Apache Spark:在 HDInsight 中使用 Power BI 分析阿帕契火花數據](apache-spark-use-bi-tools.md)
* [Apache Spark 和機器學習服務：使用 HDInsight 中的 Spark，使用 HVAC 資料來分析建築物溫度](apache-spark-ipython-notebook-machine-learning.md)
