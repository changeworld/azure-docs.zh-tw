---
title: 在本機安裝 Jupyter 並連線到 Azure HDInsight 中的 Spark
description: 瞭解如何在本機電腦上安裝 Jupyter Notebook，並將其連線至 Apache Spark 叢集。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-python
ms.date: 04/23/2020
ms.openlocfilehash: d5915316ee9d393b2481eeca6a5da7923b271d9f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930415"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>在您的電腦上安裝 Jupyter Notebook，並連接到 HDInsight 上的 Apache Spark

在本文中，您將瞭解如何使用適用于 Python) 的自訂 PySpark (來安裝 Jupyter Notebook，以及使用 Spark 魔術 Scala (核心的 Apache Spark) 。 然後，將筆記本連接到 HDInsight 叢集。

安裝 Jupyter 並聯機到 HDInsight 上的 Apache Spark 有四個主要步驟。

* 設定 Spark 叢集。
* 安裝 Jupyter Notebook。
* 安裝含有 Spark magic 的 PySpark 和 Spark 核心。
* 設定 Spark magic 以存取 HDInsight 上的 Spark 叢集。

如需有關自訂核心和 Spark 魔術的詳細資訊，請參閱 [在 HDInsight 上使用 Apache Spark Linux 叢集的 Jupyter 筆記本可用](apache-spark-jupyter-notebook-kernels.md)核心。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。 本機筆記本會連接到 HDInsight 叢集。

* 熟悉如何搭配使用 Jupyter Notebook 和 HDInsight 上的 Spark。

## <a name="install-jupyter-notebook-on-your-computer"></a>在您的電腦上安裝 Jupyter Notebook

安裝 Jupyter 筆記本之前，請先安裝 Python。 [Anaconda 散發](https://www.anaconda.com/download/)套件將會安裝、Python 及 Jupyter Notebook。

下載您平台適用的 [Anaconda 安裝程式](https://www.anaconda.com/download/) ，然後執行安裝程式。 執行安裝精靈時，請確定您選取將 Anaconda 新增至 PATH 變數的選項。  另請參閱 [使用 Anaconda 安裝 Jupyter](https://jupyter.readthedocs.io/en/latest/install.html)。

## <a name="install-spark-magic"></a>安裝 Spark 魔術

1. 輸入命令 `pip install sparkmagic==0.13.1` 以安裝適用于 HDInsight 叢集3.6 和4.0 版的 Spark 魔術。 另請參閱 [sparkmagic 檔](https://github.com/jupyter-incubator/sparkmagic#installation)。

1. 藉 `ipywidgets` 由執行下列命令，確定已正確安裝：

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>安裝 PySpark 和 Spark 核心

1. `sparkmagic`輸入下列命令來識別安裝的位置：

    ```cmd
    pip show sparkmagic
    ```

    然後，將您的工作目錄變更為上述命令所識別的 **位置** 。

1. 在您的新工作目錄中，輸入下列一或多個命令，以安裝所需的核心 (s) ：

    |核心 | 命令 |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. 選擇性。 輸入下列命令以啟用伺服器擴充功能：

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>設定 Spark magic 以連線到 HDInsight Spark 叢集

在本節中，您會設定稍早安裝的 Spark 魔術，以連接到 Apache Spark 叢集。

1. 使用下列命令啟動 Python shell：

    ```cmd
    python
    ```

2. Jupyter 組態資訊通常儲存在使用者主目錄中。 輸入下列命令來識別 home 目錄，並建立名為 **\. sparkmagic** 的資料夾。  將會輸出完整的路徑。

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. 在資料夾內 `.sparkmagic` 建立名為 **config.js** 的檔案，並在其中新增下列 JSON 程式碼片段。  

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

4. 對檔案進行下列編輯：

    |範本值 | 新值 |
    |---|---|
    |名稱|叢集登入，預設值為 `admin` 。|
    |CLUSTERDNSNAME|叢集名稱|
    |{BASE64ENCODEDPASSWORD}|您實際密碼的 base64 編碼密碼。  您可以在上產生 base64 密碼 [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/) 。|
    |`"livy_server_heartbeat_timeout_seconds": 60`|如果使用 `sparkmagic 0.12.7` (叢集3.5 和3.6 版) ，請繼續進行。  如果使用 `sparkmagic 0.2.3` (叢集3.4 版) ，請將取代為 `"should_heartbeat": true` 。|

    您可以在 [config.js的範例中](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json)看到完整的範例檔案。

   > [!TIP]  
   > 傳送活動訊號可確保不會流失工作階段。 當電腦進入睡眠或已關機時，則不會傳送活動訊號，導致工作階段被清除。 若為叢集 3.4 版，如果想要停用此行為，您可以從 Ambari UI 將 Livy 組態 `livy.server.interactive.heartbeat.timeout` 設定為 `0`。 若為叢集 3.5 版，如果您未設定上述的 3.5 組態，則不會刪除工作階段。

5. 啟動 Jupyter。 從命令提示字元使用下列命令。

    ```cmd
    jupyter notebook
    ```

6. 確認您可以使用適用于核心的 Spark 魔術。 完成下列步驟。

    a. 建立新的 Notebook。 從右手邊選取 [ **新增**]。 您應該會看到預設的核心 **Python 2** 或 **Python 3** ，以及您安裝的核心。 實際的值可能會根據您的安裝選項而有所不同。  選取 [ **PySpark**]。

    ![Jupyter Notebook 中的可用核心](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Jupyter Notebook 中的核心")

    > [!IMPORTANT]  
    > 選取 [ **新增** ] 之後，請檢查您的 shell 是否有任何錯誤。  如果您看到錯誤， `TypeError: __init__() got an unexpected keyword argument 'io_loop'` 可能是您遇到特定版本的錯誤。  若是如此，請停止核心，然後使用下列命令將您的龍捲風安裝降級： `pip install tornado==4.5.3` 。

    b. 執行下列程式碼片段。

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    如果您可以順利擷取輸出，即表示已測試您對 HDInsight 叢集的連線。

    如果您想要更新筆記本設定以連接到不同的叢集，請使用新的值集更新 config.js，如上面的步驟3所示。

## <a name="why-should-i-install-jupyter-on-my-computer"></a>為什麼我應該在我的電腦上安裝 Jupyter？

在電腦上安裝 Jupyter，然後將它連線到 HDInsight 上 Apache Spark 叢集的原因：

* 提供選項讓您在本機建立筆記本、對執行中的叢集測試您的應用程式，然後將筆記本上傳到叢集。 若要將筆記本上傳至叢集，您可以使用執行或叢集的 Jupyter Notebook 上傳它們，或將它們儲存至與叢集 `/HdiNotebooks` 相關聯之儲存體帳戶中的資料夾。 如需有關如何將筆記本儲存在叢集上的詳細資訊，請參閱 [Jupyter 筆記本的儲存位置](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)？
* 使用本機可用的 Notebook，您可以根據您的應用程式需求，連接至不同的 Spark 叢集。
* 您可以使用 GitHub 實作來源控制系統，並且具有 Notebook 的版本控制。 您也可以有共同作業環境，其中多個使用者可以使用相同的 Notebook。
* 您甚至不需要啟用叢集，即可在本機使用 Notebook。 您只需要叢集針對 Notebook 進行測試，不必以手動方式管理您的 Notebook 或開發環境。
* 設定您自己的本機開發環境，可能會比在叢集上設定 Jupyter 安裝更容易。  您可以利用在本機安裝的所有軟體，而不需要設定一或多個遠端叢集。

> [!WARNING]  
> 將 Jupyter 安裝在本機電腦上，多個使用者可以同時在相同的 Spark 叢集上執行相同的 Notebook。 在這種情況下，會建立多個 Livy 工作階段。 如果您遇到問題，而且想要偵錯，則追蹤哪個 Livy 工作階段屬於哪個使用者會是複雜的工作。  

## <a name="next-steps"></a>後續步驟

* [概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)
* [Apache Spark 上 Jupyter Notebook 的核心](apache-spark-jupyter-notebook-kernels.md)
* [在 Apache Spark 中搭配使用外部套件與 Jupyter 筆記本](apache-spark-jupyter-notebook-use-external-packages.md)
