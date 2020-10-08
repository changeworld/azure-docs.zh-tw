---
title: 在 Azure HDInsight 上搭配 Jupyter 使用 Python 套件的指令碼動作
description: 說明如何使用指令碼動作以設定讓 HDInsight Spark 叢集隨附之 Jupyter Notebook 使用外部 Python 套件的逐步指示。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/29/2020
ms.openlocfilehash: a6ad1c068a41b4b865c148ebb7cdb509821609d4
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91823409"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>使用指令碼動作在 Azure HDInsight 上安全地管理 Python 環境

HDInsight 在 Spark 叢集中有兩個內建 Python 安裝，Anaconda Python 2.7 和 Python 3.5。 客戶可能需要自訂 Python 環境，例如安裝外部 Python 套件。 在這裡，我們將示範在 HDInsight 上安全地管理適用於 Apache Spark 叢集的 Python 環境最佳做法。

## <a name="prerequisites"></a>Prerequisites

HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。 如果您在 HDInsight 上還沒有 Spark 叢集，您可以在叢集建立期間執行指令碼動作。 瀏覽有關於[如何使用自訂指令碼動作](../hdinsight-hadoop-customize-cluster-linux.md)的文件。

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>支援在 HDInsight 叢集上使用開放原始碼軟體

Microsoft Azure HDInsight 服務會使用以 Apache Hadoop 為中心的開放原始碼技術環境。 Microsoft Azure 可為開放原始碼技術提供一般層級的支援。 如需詳細資訊，請參閱 [Azure 支援常見問題集網站](https://azure.microsoft.com/support/faq/)。 HDInsight 服務針對內建元件提供額外等級的支援。

HDInsight 服務中有兩種類型的開放原始碼元件可用：

|元件 |描述 |
|---|---|
|內建|這些元件會預先安裝在 HDInsight 叢集上，並且提供叢集的核心功能。 例如，Apache Hadoop YARN Resource Manager、Apache Hive 查詢語言 (HiveQL) 及 Mahout 程式庫都屬於這個類別。 叢集元件的完整清單可於 [HDInsight 所提供 Apache Hadoop 叢集版本的新功能](../hdinsight-component-versioning.md)中取得。|
|Custom|身為叢集的使用者，您可以安裝或在工作負載中使用社群中可用或您建立的任何元件。|

> [!IMPORTANT]
> 對隨 HDInsight 叢集提供的元件會有完整支援。 Microsoft 支援服務可協助隔離和解決這些元件的相關問題。
>
> 自訂元件則獲得商務上合理的支援，協助您進一步疑難排解問題。 Microsoft 支援服務可能可以解決問題，也可能要求您利用可用的開放原始碼技術管道，找到該技術的深度專業知識。 例如，有許多社群網站可供使用，例如：[適用於 HDInsight 的 Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-hdinsight.html)，`https://stackoverflow.com`。 此外，Apache 專案在 `https://apache.org` 上也有專案網站。

## <a name="understand-default-python-installation"></a>了解預設 Python 安裝

HDInsight Spark 叢集是使用 Anaconda 安裝所建立。 叢集中有兩個 Python 安裝，Anaconda Python 2.7 和 Python 3.5。 下表顯示 Spark、Livy 和 Jupyter 的預設 Python 設定。

|設定 |Python 2.7|Python 3.5|
|----|----|----|
|Path|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark 版本|預設值設定為 2.7|N/A|
|Livy 版本|預設值設定為 2.7|N/A|
|Jupyter|PySpark 核心|PySpark3 核心|

## <a name="safely-install-external-python-packages"></a>安全地安裝外部 Python 套件

HDInsight 叢集取決於內建的 Python 環境，也就是 Python 2.7 和 Python 3.5。 直接在這些預設的內建環境中安裝自訂套件可能會導致非預期的程式庫版本變更。 並且進一步中斷叢集。 若要安全地為您的 Spark 應用程式安裝自訂外部 Python 套件，請遵循下列步驟。

1. 使用 conda 建立 Python 虛擬環境。 虛擬環境會為您的專案提供隔離的空間，而不會中斷其他工作。 建立 Python 虛擬環境時，您可以指定您想要使用的 Python 版本。 即使您想要使用 Python 2.7 和 3.5，仍然需要建立虛擬環境。 這項需求是要確保叢集的預設環境不會中斷。 針對具有下列指令碼的所有節點，在您的叢集上執行指令碼動作，以建立 Python 虛擬環境。

    -   `--prefix` 會指定 conda 虛擬環境所在的路徑。 有幾個需要根據此處所指定路徑進一步變更的設定。 在此範例中，我們會使用 py35new，因為叢集已有名為 py35 的現有虛擬環境。
    -   `python=` 會指定虛擬環境的 Python 版本。 在此範例中，我們使用版本 3.5，這是與叢集內建相同的版本。 您也可以使用其他 Python 版本來建立虛擬環境。
    -   `anaconda` 會將 package_spec 指定為 anaconda，以在虛擬環境中安裝 Anaconda 套件。
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. 視需要在建立的虛擬環境中安裝外部 Python 套件。 針對具有下列指令碼的所有節點，在您的叢集上執行指令碼動作，以安裝外部 Python 套件。 您在這裡需要具有 sudo 權限，才能將檔案寫入虛擬環境資料夾。

    搜尋[套件索引](https://pypi.python.org/pypi)，以取得可用的套件完整清單。 您也可以從其他來源取得可用套件清單。 例如，您可以安裝經由 [conda-forge](https://conda-forge.org/feedstocks/) 提供的套件。

    如果您想要安裝具有最新版本的程式庫，請使用下列命令：

    - 使用 conda 通道：

        -   `seaborn` 是您想要安裝的套件名稱。
        -   `-n py35new` 會指定剛剛建立的虛擬環境名稱。 請務必根據您的虛擬環境建立，相應地變更名稱。

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - 或者使用 PyPi 存放庫，相應地變更 `seaborn` 和 `py35new`：
        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install seaborn
        ```

    如果您想要安裝具有特定版本的程式庫，請使用下列命令：

    - 使用 conda 通道：

        -   `numpy=1.16.1` 是您想要安裝的套件名稱和版本。
        -   `-n py35new` 會指定剛剛建立的虛擬環境名稱。 請務必根據您的虛擬環境建立，相應地變更名稱。

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - 或者使用 PyPi 存放庫，相應地變更 `numpy==1.16.1` 和 `py35new`：

        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install numpy==1.16.1
        ```

    如果您不知道虛擬環境名稱，您可以透過 SSH 連線到叢集的前端節點，然後執行 `/usr/bin/anaconda/bin/conda info -e` 以顯示所有虛擬環境。

3. 變更 Spark 和 Livy 設定，並指向所建立的虛擬環境。

    1. 開啟 Ambari UI，移至 Spark2 頁面的 [設定] 索引標籤。

        ![透過 Ambari 變更 Spark 和 Livy 設定](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. 展開進階 livy2-env，在底部新增下列陳述式。 如果您已使用不同的前置詞安裝虛擬環境，請相應地變更路徑。

        ```bash
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![透過 Ambari 變更 Livy 設定](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. 展開進階 spark2-env，並在底部取代現有的匯出 PYSPARK_PYTHON 陳述式。 如果您已使用不同的前置詞安裝虛擬環境，請相應地變更路徑。

        ```bash
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![透過 Ambari 變更 Spark 設定](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. 儲存變更，然後重新啟動受影響的服務。 這些變更需要重新啟動 Spark2 服務。 Ambari UI 會提示您輸入必要的重新啟動提醒，請按一下 [重新啟動] 以重新啟動所有受影響的服務。

        ![重新啟動服務](./media/apache-spark-python-package-installation/ambari-restart-services.png)

4. 如果您想要在 Jupyter 上使用新建立的虛擬環境。 變更 Jupyter 設定，然後重新啟動 Jupyter。 在具有下列陳述式的所有標頭節點上執行指令碼動作，以將 Jupyter 指向新建立的虛擬環境。 請務必將路徑修改您為虛擬環境指定的前置詞。 執行此指令碼動作之後，請透過 Ambari UI 重新啟動 Jupyter 服務，讓這個變更可供使用。

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    您可以藉由執行下列程式碼，在 Jupyter Notebook 中重複確認 Python 環境：

    ![檢查 Jupyter Notebook 中的 Python 版本](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>已知問題

針對 Anaconda 版本 `4.7.11`、`4.7.12` 和 `4.8.0`，有一個已知的錯誤。 如果您看到您的腳本動作停止回應 `"Collecting package metadata (repodata.json): ...working..."` ，以及失敗 `"Python script has been killed due to timeout after waiting 3600 secs"` 。 您可以下載[此指令碼](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh)，並在所有節點上以指令碼動作的形式執行，以修正問題。

若要檢查您的 Anaconda 版本，您可以透過 SSH 連線到叢集標題節點，然後執行 `/usr/bin/anaconda/bin/conda --v`。

## <a name="next-steps"></a>後續步驟

* [概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)
* [在 Apache Spark 中搭配 Jupyter Notebook 使用外部套件](apache-spark-jupyter-notebook-use-external-packages.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](apache-spark-job-debugging.md)
