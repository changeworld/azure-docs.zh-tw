---
title: 在 Azure HDInsight 上使用 Jupyter 的 Python 包的腳本操作
description: 說明如何使用指令碼動作以設定讓 HDInsight Spark 叢集隨附之 Jupyter Notebook 使用外部 Python 套件的逐步指示。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 659af8b85cb3736d663e79676b04af8041aeabfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129634"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>使用指令碼動作在 Azure HDInsight 上安全地管理 Python 環境

> [!div class="op_single_selector"]
> * [使用資料格魔術](apache-spark-jupyter-notebook-use-external-packages.md)
> * [使用指令碼動作](apache-spark-python-package-installation.md)

HDInsight 在 Spark 群集中有兩個內置的 Python 安裝，即 Anaconda Python 2.7 和 Python 3.5。 在某些情況下，客戶需要自訂 Python 環境，例如安裝外部 Python 包或其他 Python 版本。 在本文中，我們將介紹在 HDInsight 上安全管理[Apache Spark](./apache-spark-overview.md)群集的 Python 環境的最佳做法。

## <a name="prerequisites"></a>Prerequisites

HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。 如果 HDInsight 上尚未具有 Spark 群集，則可以在群集創建期間運行腳本操作。 。請瀏覽有關[如何使用自訂指令碼動作](../hdinsight-hadoop-customize-cluster-linux.md)的文件。

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>支援在 HDInsight 叢集上使用開放原始碼軟體

Microsoft Azure HDInsight 服務會使用以 Apache Hadoop 為中心的開放原始碼技術生態系統。 Microsoft Azure 可為開放原始碼技術提供一般層級的支援。 有關詳細資訊，請參閱[Azure 支援常見問題解答網站](https://azure.microsoft.com/support/faq/)。 HDInsight 服務針對內建元件提供額外等級的支援。

HDInsight 服務中有兩種類型的開放原始碼元件可用：

|元件 |描述 |
|---|---|
|內建|這些元件預先安裝在 HDInsight 群集上，並提供群集的核心功能。 例如，Apache Hadoop YARN 資源管理器、Apache Hive 查詢語言 （HiveQL） 和 Mahout 庫屬於此類別。 叢集元件的完整清單可於 [HDInsight 所提供 Apache Hadoop 叢集版本的新功能](../hdinsight-component-versioning.md)中取得。|
|Custom|作為群集的使用者，您可以在工作負載中安裝或使用社區中可用的或由您創建的任何元件。|

> [!IMPORTANT]
> 對隨 HDInsight 叢集提供的元件會有完整支援。 Microsoft 支援服務可協助隔離和解決這些元件的相關問題。
>
> 自訂元件則獲得商務上合理的支援，協助您進一步疑難排解問題。 Microsoft 支援服務可能可以解決問題，也可能要求您利用可用的開放原始碼技術管道，找到該技術的深度專業知識。 例如，有許多社區網站可以使用，例如[：HDInsight 的 MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)。 [https://stackoverflow.com](https://stackoverflow.com) 此外，Apache 專案也有專案[https://apache.org](https://apache.org)網站，例如： [Hadoop](https://hadoop.apache.org/)。

## <a name="understand-default-python-installation"></a>瞭解預設 Python 安裝

HDInsight Spark 群集是使用 Anaconda 安裝創建的。 群集中有兩個 Python 安裝，Anaconda Python 2.7 和 Python 3.5。 下表顯示了 Spark、Livy 和 Jupyter 的預設 Python 設置。

| |Python 2.7|Python 3.5|
|----|----|----|
|Path|/usr/bin/阿納康達/賓|/usr/bin/阿納康達/恩夫斯/py35/bin|
|Spark|預設設置為 2.7|N/A|
|Livy|預設設置為 2.7|N/A|
|Jupyter|PySpark 內核|PySpark3 內核|

## <a name="safely-install-external-python-packages"></a>安全安裝外部 Python 套裝軟體

HDInsight 群集取決於內置的 Python 環境，包括 Python 2.7 和 Python 3.5。 在這些預設的內置環境中直接安裝自訂包可能會導致意外的庫版本更改，並進一步中斷群集。 為了安全地為 Spark 應用程式安裝自訂外部 Python 包，請按照以下步驟操作。

1. 使用 conda 創建 Python 虛擬環境。 虛擬環境為您的專案提供一個孤立的空間，而不會破壞其他環境。 創建 Python 虛擬環境時，可以指定要使用的 python 版本。 請注意，即使您希望使用 Python 2.7 和 3.5，您仍需要創建虛擬環境。 這是為了確保群集的預設環境不會中斷。 在群集上為具有以下腳本的所有節點運行腳本操作，以創建 Python 虛擬環境。

    -   `--prefix`指定 conda 虛擬環境所在的路徑。 有幾個配置需要進一步根據此處指定的路徑進行更改。 在此示例中，我們使用 py35 new，因為群集已有一個稱為 py35 的現有虛擬環境。
    -   `python=`為虛擬環境指定 Python 版本。 在此示例中，我們使用版本 3.5，版本與內置於一個中的群集版本相同。 您還可以使用其他 Python 版本創建虛擬環境。
    -   `anaconda`指定package_spec作為在虛擬環境中安裝 Anaconda 包的 anaconda。
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. 如果需要，請在創建的虛擬環境中安裝外部 Python 包。 在群集上運行腳本操作，用於具有以下腳本的所有節點以安裝外部 Python 包。 您需要在此處具有 sudo 許可權，以便將檔寫入虛擬環境資料夾。

    您可以搜尋[套件索引](https://pypi.python.org/pypi)，以取得可用的套件完整清單。 您也可以從其他來源取得可用套件清單。 例如，您可以安裝經由 [conda-forge](https://conda-forge.org/feedstocks/) 提供的套件。

    如果要安裝具有最新版本的庫，請使用以下命令：

    - 使用 conda 通道：

        -   `seaborn`是要安裝的包名稱。
        -   `-n py35new`指定剛剛創建的虛擬環境名稱。 請確保根據虛擬環境創建相應地更改名稱。

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - 或使用 PyPi 回購，更改`seaborn`並`py35new`相應地：
        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install seaborn
        ```

    如果要安裝具有特定版本的庫，請使用以下命令：

    - 使用 conda 通道：

        -   `numpy=1.16.1`是要安裝的包名稱和版本。
        -   `-n py35new`指定剛剛創建的虛擬環境名稱。 請確保根據虛擬環境創建相應地更改名稱。

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - 或使用 PyPi 回購，更改`numpy==1.16.1`並`py35new`相應地：

        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install numpy==1.16.1
        ```

    如果您不知道虛擬環境名稱，則可以 SSH 到群集的頭節點並運行`/usr/bin/anaconda/bin/conda info -e`以顯示所有虛擬環境。

3. 更改 Spark 和 Livy 配置並指向創建的虛擬環境。

    1. 打開 Ambari UI，轉到 Spark2 頁面，"配置"選項卡。

        ![改變火花和利維配置通過安巴里](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. 展開高級 livy2-env，在底部添加下面的語句。 如果安裝了具有不同首碼的虛擬環境，請相應地更改路徑。

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![通過安巴里改變利維配置](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. 展開高級火花2-env，替換底部的現有匯出PYSPARK_PYTHON語句。 如果安裝了具有不同首碼的虛擬環境，請相應地更改路徑。

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![通過安巴里改變火花配置](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. 保存更改並重新啟動受影響的服務。 這些更改需要重新開機 Spark2 服務。 Ambari UI 將提示所需的重新開機提醒，按一下"重新開機"以重新開機所有受影響的服務。

        ![通過安巴里改變火花配置](./media/apache-spark-python-package-installation/ambari-restart-services.png)

4. 如果要在 Jupyter 上使用新創建的虛擬環境。 你需要改變朱皮特配置，重新開機朱皮特。 在所有頭節點上運行腳本操作，使用下面的語句將 Jupyter 指向新創建的虛擬環境。 請確保修改虛擬環境首碼的路徑。 運行此腳本操作後，通過 Ambari UI 重新開機 Jupyter 服務，以使此更改可用。

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    您可以通過運行以下代碼來雙重確認 Jupyter 筆記本中的 Python 環境：

    ![在聚居筆記本中檢查 Python 版本](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>已知問題

Anaconda 版本 4.7.11、4.7.12 和 4.8.0 存在已知錯誤。 如果您看到腳本操作掛起並`"Collecting package metadata (repodata.json): ...working..."`失敗。 `"Python script has been killed due to timeout after waiting 3600 secs"` 您可以下載[此腳本](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh)，並將其作為腳本操作在所有節點上運行以解決此問題。

要檢查 Anaconda 版本，可以將 SSH 連接到群集標頭節點並`/usr/bin/anaconda/bin/conda --v`運行 。

## <a name="next-steps"></a>後續步驟

* [概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)
* [Apache Spark 和 BI：在 HDInsight 中搭配 BI 工具使用 Spark 執行互動式資料分析](apache-spark-use-bi-tools.md)
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](apache-spark-job-debugging.md)
