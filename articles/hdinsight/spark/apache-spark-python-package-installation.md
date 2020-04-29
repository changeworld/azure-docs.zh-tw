---
title: Azure HDInsight 上具有 Jupyter 之 Python 套件的腳本動作
description: 說明如何使用指令碼動作以設定讓 HDInsight Spark 叢集隨附之 Jupyter Notebook 使用外部 Python 套件的逐步指示。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 659af8b85cb3736d663e79676b04af8041aeabfb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80129634"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>使用指令碼動作在 Azure HDInsight 上安全地管理 Python 環境

> [!div class="op_single_selector"]
> * [使用資料格魔術](apache-spark-jupyter-notebook-use-external-packages.md)
> * [使用指令碼動作](apache-spark-python-package-installation.md)

HDInsight 在 Spark 叢集中有兩個內建的 Python 安裝，Anaconda Python 2.7 和 Python 3.5。 在某些情況下，客戶必須自訂 Python 環境，例如安裝外部 Python 套件或其他 Python 版本。 在本文中，我們會示範在 HDInsight 上安全地管理[Apache Spark](./apache-spark-overview.md)叢集之 Python 環境的最佳作法。

## <a name="prerequisites"></a>Prerequisites

HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。 如果您在 HDInsight 上還沒有 Spark 叢集，您可以在叢集建立期間執行腳本動作。 。請瀏覽有關[如何使用自訂指令碼動作](../hdinsight-hadoop-customize-cluster-linux.md)的文件。

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>支援在 HDInsight 叢集上使用開放原始碼軟體

Microsoft Azure HDInsight 服務會使用以 Apache Hadoop 為中心的開放原始碼技術生態系統。 Microsoft Azure 可為開放原始碼技術提供一般層級的支援。 如需詳細資訊，請參閱[Azure 支援常見問題網站](https://azure.microsoft.com/support/faq/)。 HDInsight 服務針對內建元件提供額外等級的支援。

HDInsight 服務中有兩種類型的開放原始碼元件可用：

|元件 |描述 |
|---|---|
|內建|這些元件會預先安裝在 HDInsight 叢集上，並提供叢集的核心功能。 例如，Apache Hadoop YARN Resource Manager、Apache Hive 查詢語言（HiveQL）和 Mahout 程式庫都屬於此類別。 叢集元件的完整清單可於 [HDInsight 所提供 Apache Hadoop 叢集版本的新功能](../hdinsight-component-versioning.md)中取得。|
|自訂|身為叢集使用者的您，可以在您的工作負載中安裝或使用任何元件，或由您所建立。|

> [!IMPORTANT]
> 對隨 HDInsight 叢集提供的元件會有完整支援。 Microsoft 支援服務可協助隔離和解決這些元件的相關問題。
>
> 自訂元件則獲得商務上合理的支援，協助您進一步疑難排解問題。 Microsoft 支援服務可能可以解決問題，也可能要求您利用可用的開放原始碼技術管道，找到該技術的深度專業知識。 例如，有許多可以使用的社區網站，例如：[適用于 HDInsight 的 MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) [https://stackoverflow.com](https://stackoverflow.com)。 此外，Apache 專案在上[https://apache.org](https://apache.org)也有專案網站，例如： [Hadoop](https://hadoop.apache.org/)。

## <a name="understand-default-python-installation"></a>瞭解預設的 Python 安裝

HDInsight Spark 叢集是使用 Anaconda 安裝所建立。 叢集中有兩個 Python 安裝，Anaconda Python 2.7 和 Python 3.5。 下表顯示 Spark、Livy 和 Jupyter 的預設 Python 設定。

| |Python 2.7|Python 3.5|
|----|----|----|
|Path|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|預設值設定為2。7|N/A|
|Livy|預設值設定為2。7|N/A|
|Jupyter|PySpark 核心|PySpark3 核心|

## <a name="safely-install-external-python-packages"></a>安全地安裝外部 Python 套件

HDInsight 叢集取決於內建的 Python 環境，也就是 Python 2.7 和 Python 3.5。 直接在這些預設的內建環境中安裝自訂套件可能會導致未預期的程式庫版本變更，並進一步中斷叢集。 若要安全地為您的 Spark 應用程式安裝自訂外部 Python 套件，請遵循下列步驟。

1. 使用 conda 建立 Python 虛擬環境。 虛擬環境會為您的專案提供隔離的空間，而不會中斷其他工作。 建立 Python 虛擬環境時，您可以指定您想要使用的 python 版本。 請注意，即使您想要使用 Python 2.7 和3.5，仍然需要建立虛擬環境。 這是為了確保叢集的預設環境不會中斷。 針對具有下列腳本的所有節點，在您的叢集上執行腳本動作，以建立 Python 虛擬環境。

    -   `--prefix`指定 conda 虛擬環境所在的路徑。 有幾個需要根據此處指定的路徑進一步變更的需求。 在此範例中，我們會使用 py35new，因為叢集已有名為 py35 的現有虛擬環境。
    -   `python=`指定虛擬環境的 Python 版本。 在此範例中，我們使用版本3.5，這是內建叢集的相同版本。 您也可以使用其他 Python 版本來建立虛擬環境。
    -   `anaconda`將 package_spec 指定為 anaconda，以在虛擬環境中安裝 Anaconda 套件。
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. 視需要在建立的虛擬環境中安裝外部 Python 套件。 針對具有下列腳本的所有節點，在您的叢集上執行腳本動作，以安裝外部 Python 套件。 您需要在這裡擁有 sudo 許可權，才能將檔案寫入虛擬環境資料夾。

    您可以搜尋[套件索引](https://pypi.python.org/pypi)，以取得可用的套件完整清單。 您也可以從其他來源取得可用套件清單。 例如，您可以安裝經由 [conda-forge](https://conda-forge.org/feedstocks/) 提供的套件。

    如果您想要安裝具有最新版本的程式庫，請使用下列命令：

    - 使用 conda 通道：

        -   `seaborn`是您想要安裝的套件名稱。
        -   `-n py35new`指定只建立的虛擬環境名稱。 請務必根據您的虛擬環境建立，相應地變更名稱。

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - 或使用 PyPi 存放庫， `seaborn`並`py35new`相應地變更和：
        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install seaborn
        ```

    如果您想要安裝具有特定版本的程式庫，請使用下列命令：

    - 使用 conda 通道：

        -   `numpy=1.16.1`是您想要安裝的套件名稱和版本。
        -   `-n py35new`指定只建立的虛擬環境名稱。 請務必根據您的虛擬環境建立，相應地變更名稱。

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - 或使用 PyPi 存放庫， `numpy==1.16.1`並`py35new`相應地變更和：

        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install numpy==1.16.1
        ```

    如果您不知道虛擬環境名稱，您可以透過 SSH 連線到叢集的前端節點，並執行`/usr/bin/anaconda/bin/conda info -e`來顯示所有虛擬環境。

3. 變更 Spark 和 Livy 的 [進行]，並指向所建立的虛擬環境。

    1. 開啟 Ambari UI，移至 Spark2 頁面的 [進行中] 索引標籤。

        ![透過 Ambari 變更 Spark 和 Livy 設定](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. 展開 [Advanced livy2-env]，在底部加入下列語句。 如果您已使用不同的前置詞安裝虛擬環境，請相應地變更路徑。

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![透過 Ambari 變更 Livy 設定](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. 展開 [Advanced spark2-env]，並在底部取代現有的匯出 PYSPARK_PYTHON 語句。 如果您已使用不同的前置詞安裝虛擬環境，請相應地變更路徑。

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![透過 Ambari 變更 Spark 設定](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. 儲存變更並重新啟動受影響的服務。 這些變更需要重新開機 Spark2 服務。 Ambari UI 會提示您輸入必要的重新開機提醒，請按一下 [重新開機] 重新開機所有受影響的服務。

        ![透過 Ambari 變更 Spark 設定](./media/apache-spark-python-package-installation/ambari-restart-services.png)

4. 如果您想要在 Jupyter 上使用新建立的虛擬環境。 您需要變更 Jupyter 的 [的]，並重新啟動 Jupyter。 在所有具有下列語句的標頭節點上執行腳本動作，以將 Jupyter 指向新建立的虛擬環境。 請務必修改您為虛擬環境指定的前置詞路徑。 執行此腳本動作之後，請透過 Ambari UI 重新開機 Jupyter 服務，讓這種變更可供使用。

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    您可以執行下列程式碼，在 Jupyter Notebook 中重複確認 Python 環境：

    ![檢查 Jupyter Notebook 中的 Python 版本](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>已知問題

Anaconda 版本4.7.11、4.7.12 和4.8.0 有已知的錯誤。 如果您看到您的腳本動作在`"Collecting package metadata (repodata.json): ...working..."`發生時停止`"Python script has been killed due to timeout after waiting 3600 secs"`，並失敗。 您可以下載[此腳本](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh)，並將它當做所有節點上的腳本動作執行，以修正此問題。

若要檢查您的 Anaconda 版本，您可以透過 SSH 連線到叢集標`/usr/bin/anaconda/bin/conda --v`頭節點，然後執行。

## <a name="next-steps"></a>後續步驟

* [概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)
* [Apache Spark 和 BI：在 HDInsight 中搭配 BI 工具使用 Spark 執行互動式資料分析](apache-spark-use-bi-tools.md)
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](apache-spark-job-debugging.md)
