---
title: 在 HDInsight Linux 叢集上使用 Hue 與 Hadoop - Azure - Azure
description: 了解如何在 HDInsight 叢集上安裝 Hue，並且使用通道將要求路由至 Hue。 使用 Hue 瀏覽儲存體並執行 Hive 或 Pig。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/31/2020
ms.openlocfilehash: ef30672e250e598688d1b81fd33fe0a995e78c7d
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86087719"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>在 HDInsight Hadoop 叢集上安裝和使用 Hue

了解如何在 HDInsight 叢集上安裝 Hue，並且使用通道將要求路由至 Hue。

## <a name="what-is-hue"></a>何謂 Hue？

Hue 是一組用來與 Apache Hadoop 叢集進行互動的 Web 應用程式。 您可以使用 Hue 以瀏覽與 Hadoop 叢集 (在 HDInsight 叢集的案例中為 WASB) 相關聯的儲存體、執行 Hive 工作和 Pig 指令碼等等。 HDInsight Hadoop 叢集上的 Hue 安裝可使用下列元件。

* Beeswax Hive 編輯器
* Apache Pig
* Metastore 管理員
* Apache Oozie
* FileBrowser (與 WASB 預設容器進行通訊)
* 工作瀏覽器

> [!WARNING]  
> 透過 HDInsight 叢集提供的元件會受到完整支援，且 Microsoft 支援服務將協助釐清與解決這些元件的相關問題。
>
> 自訂元件則獲得商務上合理的支援，協助您進一步疑難排解問題。 如此可能會進而解決問題，或要求您利用可用管道，以找出開放原始碼技術，從中了解該技術的深度專業知識。 例如，有許多社群網站可供使用，例如：[適用於 HDInsight 的 Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-hdinsight.html)，[https://stackoverflow.com](https://stackoverflow.com)。 此外，Apache 專案在 [https://apache.org](https://apache.org) 上也有專案網站，例如：[Hadoop](https://hadoop.apache.org/)。

## <a name="install-hue-using-script-actions"></a>使用指令碼動作安裝 Hue

請將下表中的資訊用於您的指令碼動作。 如需有關使用指令碼動作的特定資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

> [!NOTE]  
> 若要在 HDInsight 叢集上安裝 Hue，建議的 HeadNode 大小為至少 A4 (8 核心、14 GB 記憶體)。

|屬性 |值 |
|---|---|
|指令碼類型：|- 自訂|
|名稱|安裝 Hue|
|Bash 指令碼 URI|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|節點類型：|Head|

## <a name="use-hue-with-hdinsight-clusters"></a>搭配使用 Hue 與 HDInsight 叢集

在一般叢集上，您只能有一個具有 Hue 的使用者帳戶。 如需多使用者存取，請在叢集上啟用[企業安全性套件](./domain-joined/hdinsight-security-overview.md)。 SSH 通道一旦執行，就是在叢集上存取 Hue 的唯一方式。 透過 SSH 的通道允許直接至在其中執行 Hue 之叢集的前端節點的流量。 叢集完成佈建之後，請使用下列步驟，在 HDInsight 叢集上使用 Hue。

> [!NOTE]  
> 建議您使用 Firefox 網頁瀏覽器來遵循下列指示。

1. 請利用[使用 SSH 通道來存取 Apache Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie 及其他 Web UI](hdinsight-linux-ambari-ssh-tunnel.md) 中的資訊，建立從用戶端系統到 HDInsight 叢集的 SSH 通道，然後將網頁瀏覽器設定成使用 SSH 通道作為 Proxy。

1. 使用 [ssh 命令](./hdinsight-hadoop-linux-use-ssh-unix.md)來連線到您的叢集。 編輯以下命令並將 CLUSTERNAME 取代為您叢集的名稱，然後輸入命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 連線之後，請使用下列命令來取得主要前端節點的完整網域名稱︰

    ```bash
    hostname -f
    ```

    此命令會傳回類似以下的名稱：

    ```output
    myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    ```

    這是 Hue 網站所在之主要前端節點的主機名稱。

1. 使用瀏覽器開啟位於 `http://HOSTNAME:8888` 的 Hue 入口網站。 以您在先前步驟取得的名稱取代 HOSTNAME。

   > [!NOTE]  
   > 當您第一次登入時，系統會提示您建立帳戶來登入 Hue 入口網站。 您在此處指定的認證會限制為入口網站，並且與佈建叢集時您指定的系統管理員或 SSH 使用者認證不相關。

    ![HDInsight Hue 入口網站登入視窗](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "指定 Hue 入口網站的登入資訊")

### <a name="run-a-hive-query"></a>執行 HIVE 查詢

1. 從 Hue 入口網站中，選取 [查詢編輯器]，然後選取 [Hive] 開啟 Hive 編輯器。

    ![HDInsight Hue 入口網站使用 Hive 編輯器](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "使用 Hive")

2. 在 [協助] 索引標籤中，於 [資料庫] 底下，您應該會看到 **hivesampletable**。 這是 HDInsight 上的所有 Hadoop 叢集隨附的範例資料表。 在右窗格中輸入範例查詢，然後在下方窗格的 [結果]  索引標籤中查看輸出，如螢幕擷取畫面所示。

    ![HDInsight Hue 入口網站 Hive 查詢](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "執行 Hive 查詢")

    您也可以使用 [圖表]  索引標籤來查看結果的視覺表示。

### <a name="browse-the-cluster-storage"></a>瀏覽叢集儲存體

1. 從 Hue 入口網站中，選取功能表列右上角的 [檔案瀏覽器]。
2. 根據預設，檔案瀏覽器會在 **/user/myuser** 目錄中開啟。 選取路徑中使用者目錄前面的正斜線，以移至與叢集相關聯的 Azure 儲存體容器的根目錄。

    ![HDInsight Hue 入口網站檔案瀏覽器](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "使用檔案瀏覽器")

3. 以滑鼠右鍵按一下檔案或資料夾，以查看可用的作業。 使用右邊的 [上傳]  按鈕，將檔案上傳至目前的目錄。 使用 [新增]  按鈕建立新的檔案或目錄。

> [!NOTE]  
> Hue 檔案瀏覽器只會顯示與 HDInsight 叢集相關聯的預設容器的內容。 已與叢集相關聯的任何額外儲存體帳戶/容器將無法使用檔案瀏覽器存取。 不過，與叢集相關聯的其他容器一律可供 Hive 工作存取。 例如，如果您在 Hive 編輯器中輸入 `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` 命令，則您也可以看到其他容器的內容。 在這個命令中， **newcontainer** 不是與叢集相關聯的預設容器。

## <a name="important-considerations"></a>重要考量︰

1. 用來安裝 Hue 的指令碼只會將它安裝在叢集的主要前端節點上。

1. 在安裝期間，會重新啟動多個 Hadoop 服務 (HDFS、YARN、MR2、Oozie) 以更新與設定。 指令碼完成安裝 Hue 之後，可能需要一些時間讓其他 Hadoop 服務啟動。 一開始可能會影響 Hue 的效能。 所有服務啟動之後，Hue 就可以完全正常運作。

1. Hue 並不了解 Apache Tez 作業，這是 Hive 目前的預設值。 如果您想要使用 MapReduce 做為 Hive 執行引擎，請更新指令碼以在您的指令碼中使用下列命令：

   `set hive.execution.engine=mr;`

1. 使用 Linux 叢集，您就可以擁有在主要前端節點上執行服務，在次要前端節點上執行 Resource Manager 的案例。 使用 Hue 以檢視叢集上「執行中」工作的詳細資料時，此類案例可能會導致錯誤 (如下所示)。 不過，您可以在工作完成時檢視工作詳細資料。

   ![Hue 入口網站錯誤範例訊息](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Hue 入口網站錯誤")

   這是由已知問題造成的。 因應措施是修改 Ambari，讓作用中的 Resource Manager 也在主要前端節點上執行。

1. 當 HDInsight 叢集使用 Azure 儲存體 (使用 `wasbs://`) 時，色調能了解 WebHDFS。 因此，搭配指令碼動作使用的自訂指令碼會安裝 WebWasb，這是針對與 WASB 通訊的 WebHDFS 相容服務。 所以，即使在 Hue 入口網站顯示有 HDFS (例如將滑鼠移至 [檔案瀏覽器] 時)，應將它解讀成 WASB。

## <a name="next-steps"></a>後續步驟

[在 HDInsight 叢集上安裝 R](hdinsight-hadoop-r-scripts-linux.md)。 在 HDInsight Hadoop 叢集上使用叢集自訂安裝 R。 R 是一個用於統計計算的開放原始碼語言和環境。 它提供數百個內建的統計函式及它自己的程式設計語言，此語言結合了函式型和物件導向程式設計的層面。 它也提供廣泛的圖形功能。
