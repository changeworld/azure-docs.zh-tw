---
title: 從 Visual Studio R 工具提交作業 - Azure HDInsight
description: 從您的本機 Visual Studio 機器將 R 作業提交至 HDInsight 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 08426c74b26c18b15466578d9921520da1e9c923
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536227"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>從 Visual Studio R 工具提交作業

[Visual Studio R 工具](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS) 是免費、開放來源的擴充功能，適用於 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 和 [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129) 或更新版本的 Community (免費)、Professional 及 Enterprise 版本。 [Visual Studio 2019](/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?preserve-view=true&view=vs-2019)無法使用 RTVS。

RTVS 會增強您的 R 工作流程，方法是提供例如 [R 互動視窗](/visualstudio/rtvs/interactive-repl) (REPL)、IntelliSense (程式碼完成)、[繪圖視覺效果](/visualstudio/rtvs/visualizing-data) 等工具，透過例如 ggplot2 和 ggviz、[R 程式碼偵錯](/visualstudio/rtvs/debugging) 等程式庫。

## <a name="set-up-your-environment"></a>設定您的環境

1. 安裝 [Visual Studio R 工具](/visualstudio/rtvs/installing-r-tools-for-visual-studio)。

    ![在 Visual Studio 2017 中安裝 RTVS](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. 選取 *資料科學和分析應用程式* 工作負載，然後選取 **R 語言支援** 、 **R 開發的執行階段支援** 和 **Microsoft R 用戶端** 選項。

3. 您需要有公開和私密金鑰以進行 SSH 驗證。
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. 在您的電腦上安裝 [Machine Learning Server](/previous-versions/machine-learning-server/install/r-server-install-windows)。 ML 伺服器提供 [`RevoScaleR`](/machine-learning-server/r-reference/revoscaler/revoscaler) 和 `RxSpark` 功能。

5. 安裝 [PuTTY](https://www.putty.org/) 以提供計算內容，從您的本機用戶端將 `RevoScaleR` 函式執行至 HDInsight 叢集。

6. 您可以選擇將資料科學設定套用至 Visual Studio 環境，它為您的 R 工具工作區提供新的版面配置。
   1. 若要儲存目前的 Visual Studio 設定，使用 [工具] > [匯入和匯出設定]  命令，然後選取 [匯出選取的環境設定]  並且指定檔案名稱。 若要還原這些設定，請使用相同的命令並選取 [匯入選取的環境設定]  。

   2. 移至 [R 工具]  功能表項目，然後選取 [資料科學設定...]  .

       ![Visual Studio 資料科學設定](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > 使用步驟 1 中的方法，您也可以儲存和還原您的個人化資料科學家版面配置，而不用重複 [資料科學設定]  命令。

## <a name="execute-local-r-methods"></a>執行本機 R 方法

1. 建立 HDInsight ML 服務叢集。
2. 安裝 [RTVS 擴充功能](/visualstudio/rtvs/installation)。
3. 下載[範例 zip 檔案](https://github.com/Microsoft/RTVS-docs/archive/master.zip)。
4. 在 Visual Studio 中開啟 `examples/Examples.sln` 以啟動解決方案。
5. 開啟 `A first look at R` 解決方案資料夾中的 `1-Getting Started with R.R` 檔案。
6. 從檔案頂端開始，按下 Ctrl+Enter 一次傳送一行到 R 互動視窗。 有些行會安裝套件，所以可能需要一些時間。
    * 或者，您可以選取 R 檔案中的所有行 (Ctrl+A)，然後全部執行 (Ctrl+Enter)，或者選取工具列上的 [執行互動] 圖示。

        ![Visual Studio 執行互動式](./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png)

7. 執行指令碼中的所有行之後，您應該會看到類似以下的輸出：

    ![Visual Studio workspace R 工具](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>將作業提交至 HDInsight ML 服務叢集

您可以透過配備 PuTTY 的 Windows 電腦使用 Microsoft Machine Learning Server/Microsoft R Client，建立計算內容，此內容會從本機用戶端將分散式 `RevoScaleR` 函式執行至 HDInsight 叢集。 使用 `RxSpark` 來建立計算內容，指定您的使用者名稱、Apache Hadoop 叢集的邊緣節點、SSH 參數等等。

1. HDInsight 上的 ML 服務邊緣節點位址是 `CLUSTERNAME-ed-ssh.azurehdinsight.net` `CLUSTERNAME` 您的 ml 服務叢集的名稱。

1. 將下列程式碼貼到 Visual Studio 中的 R 互動視窗，修改設定變數的值以符合您的環境。

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
          sshHostname = mySshHostname,
          sshSwitches = mySshSwitches,
          sshProfileScript = mySshProfileScript,
          consoleOutput = TRUE,
          hdfsShareDir = myHdfsShareDir,
          shareDir = myShareDir,
          sshClientDir = mySshClientDir
    )

    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```

   ![apache spark 設定內容](./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png)

1. 在 R 互動視窗中執行下列命令：

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    您應該會看到如下所示的輸出：

    ![成功的 rx 命令執行 ](./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png) a
1. 確認 `rxHadoopCopy` 成功將 `people.json` 檔案從範例資料資料夾複製到新建立的 `/user/RevoShare/newUser` 資料夾：

    1. 從 Azure 中的 HDInsight ML 服務叢集窗格，選取左側功能表的 [儲存體帳戶]  。

        ![Azure HDInsight 儲存體帳戶](./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png)

    2. 選取叢集的預設儲存體帳戶，記下容器/目錄名稱。

    3. 從儲存體帳戶窗格的左側功能表選取 [容器]  。

        ![Azure HDInsight 儲存體容器](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. 選取叢集的容器名稱，瀏覽至  )，然後依序選取 [RevoShare]  、[newUser]  。 `people.json` 檔案應該會顯示在 `newUser` 資料夾中。

        ![HDInsight 複製的檔案資料夾位置](./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png)

1. 對目前的 Apache Spark 內容使用完畢之後，您必須將它停止。 您無法同時執行多個內容。

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>下一步

* [在 HDInsight 上計算 ML 服務的內容選項](r-server-compute-contexts.md)
* [結合 ScaleR 和 SparkR](../hdinsight-hadoop-r-scaler-sparkr.md)提供航班延誤預測的範例。