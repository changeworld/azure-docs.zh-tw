---
title: 部署和管理 HDInsight 上的 Apache Storm 拓撲
description: 瞭解如何使用以 Linux 為基礎的 HDInsight 上的「風暴」儀表板來部署、監視及管理 Apache Storm 拓撲。 使用適用於 Visual Studio 的 Hadoop 工具。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 9b190b5847c7412344e2bb09fd4000985816219b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995585"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>部署和管理 HDInsight 上的 Apache Storm 拓撲

在本文件中，您可以了解管理和監視在 Storm on HDInsight 叢集上所執行 [Apache Storm](https://storm.apache.org/) 拓撲的基本概念。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Storm 叢集。 請參閱[使用 Azure 入口網站建立 Apache Hadoop 叢集](../hdinsight-hadoop-create-linux-clusters-portal.md)，然後選取 [Storm] 作為 [叢集類型]。

*  (選用) 熟悉安全殼層 (SSH) 和安全複製 (SCP) 。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

*  (選用) Visual Studio、Azure SDK 2.5.1 或更新版本，以及適用于 Visual Studio 的 Data Lake 工具。 如需詳細資訊，請參閱 [Apache Hadoop & Visual Studio Data Lake 工具](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)。

## <a name="submit-a-topology-using-visual-studio"></a>使用 Visual Studio 提交拓撲

您可以使用適用于 Visual Studio 的 Data Lake 工具，將 c # 或混合式拓撲提交至您的風暴叢集。 下列步驟使用範例應用程式。 如需使用 Data Lake 工具建立拓撲的相關資訊，請參閱 [使用 Visual Studio 和 c # Apache Storm 拓撲](apache-storm-develop-csharp-visual-studio-topology.md)。

1. 如果您尚未安裝最新版的 Data Lake tools for Visual Studio，請參閱 [使用 Data Lake tools 進行 Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)。

    > [!NOTE]  
    > Azure Data Lake 和串流分析工具先前稱為 HDInsight Tools for Visual Studio。
    >
    > 適用于 Visual Studio 的 Azure Data Lake 和串流分析工具組含在適用于 Visual Studio 2019 的 **Azure 開發** 工作負載中。

1. 啟動 Visual Studio。

1. 在 [開始] 視窗中，選取 [建立新專案]。

1. 在 [ **建立新專案** ] 視窗中，選取 [搜尋] 方塊，然後輸入 `Storm` 。 然後從結果清單中選擇 [ **風暴 Sample Sample** ]，然後選取 **[下一步]**。

1. 在 [ **設定您的新專案** ] 視窗中，輸入 **專案名稱**，然後移至或建立儲存新專案的 **位置** 。 然後選取 [建立]。

    ![設定新的專案視窗，Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

1. 在 **伺服器總管** 中，以滑鼠右鍵按一下 [ **Azure** ]，然後選取 **[連線到 Microsoft Azure 訂** 用帳戶]，並完成登入程式。

1. 在 **方案總管** 中，以滑鼠右鍵按一下專案，然後選擇 [ **在 HDInsight 上提交到風暴**]。

    > [!NOTE]  
    > 如果出現提示，請輸入您 Azure 訂閱的登入認證。 如果您有多個訂用帳戶，請登入包含 Storm on HDInsight 叢集的訂用帳戶。

1. 在 [ **提交拓撲** ] 對話方塊的 [ **風暴** 叢集] 下拉式清單中，選擇您在 HDInsight 叢集上的風暴，然後選取 [ **提交**]。 您可以藉由查看 **輸出** 窗格來監視提交是否成功。

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>使用 SSH 和風暴命令來提交拓撲

1. 使用 [ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)來連線到您的叢集。 編輯以下命令並將 CLUSTERNAME 取代為您叢集的名稱，然後輸入命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 從 ssh 會話中，使用下列命令來啟動 **WordCount** 範例拓撲：

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    這個命令會在叢集上啟動範例 WordCount 拓撲。 這個拓撲會隨機產生句子，並計算句子中每個字詞的出現次數。

    > [!NOTE]  
    > 將拓撲提交至叢集時，您必須先複製包含叢集的 .jar 檔案，然後再使用 `storm` 命令。 若要將檔案複製到叢集，您可以使用 `scp` 命令。 例如，輸入 `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`。
    >
    > 您的叢集中已包含 *WordCount* 範例和其他的風暴入門範例 `/usr/hdp/current/storm-client/contrib/storm-starter/` 。

## <a name="submit-a-topology-programmatically"></a>以程式設計方式提交拓撲

您可以透過程式設計的方式，使用 Nimbus 服務部署拓撲。 [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) 提供範例 JAVA 應用程式，示範如何透過 Nimbus 服務部署和啟動拓撲。

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>監視和管理 Visual Studio 中的拓撲

當您使用 Visual Studio 提交拓撲時，會出現 [ **風暴拓撲視圖** ] 視窗。 從清單中選取拓撲，以檢視執行中拓撲的詳細資訊。

![監視拓撲、風暴拓撲視圖視窗、Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> 您也可以從 [伺服器總管] 檢視 [Storm 拓撲]。 展開 [ **Azure**  >  **HDInsight**]，以滑鼠右鍵按一下 HDInsight 叢集上的風暴，然後選取 [ **View 風暴拓撲**]。

選取 Spout 或 Bolt 的圖形以檢視這些元件的資訊。 針對選取的專案，會顯示具有元件資訊的工具提示。

### <a name="deactivate-and-reactivate-a-topology"></a>停用並重新啟用拓撲

停用拓撲會暫停它，直到拓撲終止或重新開機為止。 若要執行這些作業，請在 [**風暴拓撲] 視圖** 視窗頂端的 [**動作**] 區域中，使用 [**停用**] 和 [**重新** 啟動] 按鈕。

### <a name="rebalance-a-topology"></a>重新平衡拓撲

重新平衡拓撲可以讓系統修訂拓撲的平行處理原則。 例如，如果您已調整叢集的大小以新增更多附注，重新平衡可讓拓撲查看新的節點。

若要重新平衡拓撲，請使用 [**風暴拓撲] 視圖** 視窗之 [**動作**] 區域中的 [重新 **平衡**] 按鈕。

> [!WARNING]  
> 重新平衡拓撲會停用拓撲、將背景工作平均分散到整個叢集中，然後將拓撲傳回在重新平衡發生之前的狀態。 如果拓撲是作用中，則會再次變成作用中狀態。 如果拓撲已停用，則會保持停用狀態。

### <a name="kill-a-running-topology"></a>刪除執行中拓撲

風暴拓撲會繼續執行，直到停止或刪除叢集為止。 若要停止拓撲，請使用 [**動作**] 區域中的 [**終止**] 按鈕。

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>使用 SSH 和風暴命令監視和管理拓撲

`storm` 公用程式可讓您從命令列使用執行中拓撲。 使用 `storm -h` 以取得完整的命令清單。

### <a name="list-topologies"></a>列出拓撲

使用下列命令來列出所有執行中拓撲：

```shell
storm list
```

此命令會傳回類似以下文字的資訊：

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>停用並重新啟用拓撲

停用拓撲會暫停它，直到拓撲終止或重新開機為止。 使用下列命令來停用或重新開機：

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>刪除執行中拓撲

Storm 拓撲一旦啟動之後，就會繼續執行直到停止。 若要停止拓撲，請使用下列命令：

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>重新平衡拓撲

重新平衡拓撲可以讓系統修訂拓撲的平行處理原則。 例如，如果您已調整叢集的大小以新增更多附注，重新平衡可讓拓撲查看新的節點。

> [!WARNING]  
> 重新平衡拓撲會停用拓撲、將背景工作平均分散到整個叢集中，然後將拓撲傳回在重新平衡發生之前的狀態。 如果拓撲是作用中，則會再次變成作用中狀態。 如果已停用，它就會保持停用。

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>使用風暴 UI 監視和管理拓撲

風暴 UI 提供的 web 介面可用來處理執行中的拓撲，而且會包含在您的 HDInsight 叢集中。 若要檢視 Storm UI，請使用網頁瀏覽器開啟 `https://CLUSTERNAME.azurehdinsight.net/stormui`，其中 *CLUSTERNAME* 是叢集的名稱。

> [!NOTE]  
> 如果系統要求您提供使用者名稱和密碼，請輸入您在建立叢集時所使用的叢集系統管理員使用者名稱和密碼。

### <a name="storm-ui-main-page"></a>風暴 UI 主頁面

Storm UI 的主頁面會提供下列資訊：

| 區段 | 描述 |
| --- | --- |
| 叢集摘要| 有關 Storm 叢集的基本資訊。 |
| Nimbus 摘要 | 基本 Nimbus 資訊的清單。 |
| 拓撲摘要 | 執行中拓撲的清單。 若要查看特定拓撲的詳細資訊，請在 [ **名稱** ] 欄中選取其連結。 |
| 監督員摘要 | Storm 監督員的相關資訊。 若要查看與特定監督員相關聯的背景工作資源，請在 [ **主機** 或 **識別碼** ] 資料行中選取其連結。 |
| Nimbus 設定 | 適用於叢集的 Nimbus 設定。 |

風暴 UI 主頁面看起來類似這個網頁：

![主頁面、風暴 UI、Apache Storm 拓撲、Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>拓撲摘要

選取 [拓撲摘要]  區段中的連結會顯示拓撲的下列資訊：

| 區段 | 描述 |
| --- | --- |
| 拓撲摘要 | 有關拓撲的基本資訊。 |
| 拓撲動作| 您可以針對拓撲執行的管理動作。 這些可用的動作會在本節稍後說明。 |
| 拓撲統計資料 | 有關拓撲的統計資料。 若要設定此區段中某個專案的時間範圍，請在 [ **視窗]** 資料行中選取其連結。 |
| Spout *(時間範圍)* | 拓撲所使用的 Spout。 若要查看特定 spout 的詳細資訊，請在 [ **識別碼** ] 欄中選取其連結。 |
| *(時間範圍)* 的螺栓 | 拓撲所使用的 Bolt。 若要查看特定螺栓的詳細資訊，請在 [ **識別碼** ] 欄中選取其連結。 |
| 背景工作資源 | 背景工作資源的清單。 若要查看特定背景工作資源的詳細資訊，請在 [ **主機** ] 資料行中選取其連結。 |
| 拓撲視覺效果 | 顯示拓撲視覺效果的 **顯示視覺效果** 按鈕。 |
| 拓撲設定 | 所選取拓撲的設定。 |

[風暴拓撲摘要] 頁面看起來類似此網頁：

![拓撲摘要頁面、風暴 UI、Apache Storm、Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

在 [ **拓撲動作** ] 區段中，您可以選取下列按鈕來執行動作：

| 按鈕 | 描述 |
| --- | --- |
| 啟動 | 繼續處理已停用的拓撲。 |
| 停用 | 暫停執行中拓撲。 |
| 重新平衡 | 調整拓撲的平行處理原則。 變更叢集中的節點數目之後，您應該重新平衡正在執行的拓撲。 這項作業可讓拓撲調整平行處理原則，以彌補叢集中的其他或減少節點數目。<br/><br/>如需詳細資訊，請參閱 <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Understanding the parallelism of an Apache Storm topology</a> (了解 Apache Storm 拓撲的平行處理原則)。
| 終止 | 在指定的逾時之後終止 Storm 拓撲。 |
| 偵錯 | 開始執行中拓撲的偵錯工具會話。 |
| 停止 Debug | 結束執行中拓撲的偵錯工具會話。 |
| 變更記錄層級 | 修改調試記錄層級。 |

##### <a name="spout-and-bolt-summary"></a>Spout 和螺栓摘要

從 [Spout] 或 [Bolt] 區段中選取 Spout 會顯示所選取項目的下列資訊：

| 區段 | 描述 |
| --- | --- |
| 元件摘要 | 有關 Spout 或 Bolt 的基本資訊。 |
| 元件動作 | **調試** 和 **停止調試** 按鈕。 |
| Spout 統計資料或螺栓統計資料 | 有關 Spout 或 Bolt 的統計資料。 若要設定此區段中某個專案的時間範圍，請在 [ **視窗]** 資料行中選取其連結。 |
|  (僅限螺栓) <br/>輸入統計資料 *(時間範圍)* | Bolt 所取用之輸入串流的相關資訊。 |
| 輸出統計資料 *(時間範圍)* | Spout 或 Bolt 所發出資料流的資訊。 |
| 分析和偵錯工具 | 此頁面上的元件進行程式碼剖析和偵錯工具的控制項。 您可以將 [ **狀態]/[超時] (分鐘)** 值，也可以選取 **JStack**、 **重新開機背景工作** 和 **堆積** 的按鈕。 |
| 執行程式 *(時間範圍)* | Spout 或 Bolt 執行個體的相關資訊。 若要查看為此實例產生的診斷資訊記錄，請選取特定執行程式的 **埠** 專案。 您也可以在 [ **主機** ] 資料行中選取連結，以查看與特定執行程式相關聯的背景工作資源。 |
| Errors | Spout 或 Bolt 的任何錯誤資訊。 |

[風暴螺栓摘要] 頁面看起來類似這個網頁：

![螺栓摘要頁面、風暴 UI、Apache Storm、Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>使用 REST API 監視和管理拓撲

風暴 UI 是以 REST API 為基礎，因此您可以使用 REST API 來進行類似的管理和監視工作。 您可以使用 REST API 建立自訂工具來管理和監視 Storm 拓撲。

如需詳細資訊，請參閱 [Apache Storm UI REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html)。 下列資訊專用於搭配使用 REST API 與 Apache Storm on HDInsight。

> [!IMPORTANT]  
> 「風暴 REST API」無法透過網際網路公開使用。 您必須使用 HDInsight 叢集前端節點的 SSH 通道來存取它。 如需建立及使用 SSH 通道的相關資訊，請參閱 [使用 ssh 通道存取 Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md)。

### <a name="base-uri"></a>基底 URI

以 Linux 為基礎的 HDInsight 叢集上 REST API 的基底 URI 可在 URL 位址取得 `https://HEADNODEFQDN:8744/api/v1/` ，而您可以在其中將 *HEADNODEFQDN* 取代為前端節點。 前端節點的功能變數名稱是在叢集建立期間產生的，而且不是靜態的。

您可以透過數種方式，找到叢集前端節點的完整功能變數名稱 (FQDN) ：

| FQDN 探索方法 | 描述 |
| --- | --- |
| SSH 會話 | 使用命令 `headnode -f` (從 SSH 工作階段到叢集)。 |
| Ambari Web | 在 Ambari 叢集網頁 () 上 `https://CLUSTERNAME.azurehdinsight.net` ，從頁面頂端選取 [ **服務** ]，然後選取 [ **風暴**]。 從 [摘要] 索引標籤，選取 [Storm UI 伺服器]。 託管 Storm UI 和 REST API 的節點 FQDN 位於頁面頂端。 |
| Ambari REST API | 使用命令 `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` 來擷取 Storm UI 和 REST API 執行所在節點的相關資訊。 將 *CLUSTERNAME* 的兩個實例取代為叢集名稱。 當系統提示您時，請輸入使用者 (系統管理員) 帳戶的密碼。 在回應中，JSON 輸出的 "host_name" 專案包含節點的 FQDN。 |

### <a name="authentication"></a>驗證

REST API 的要求必須使用 *基本驗證*，因此您必須使用 HDInsight 叢集的系統管理員名稱和密碼。

> [!NOTE]  
> 因為使用純文字傳送基本驗證，所以您應該 *一律* 使用 HTTPS 來保護與叢集通訊的安全。

### <a name="return-values"></a>傳回值

從 REST API 傳回的資訊可能只可在叢集中使用。 例如，針對 [Apache ZooKeeper](https://zookeeper.apache.org/) 伺服器所傳回的完整功能變數名稱 (FQDN) 無法從網際網路存取。

## <a name="next-steps"></a>後續步驟

了解如何[使用 Apache Maven 開發 Java 型拓撲](apache-storm-develop-java-topology.md)。

如需更多範例拓撲的清單，請參閱 [Azure HDInsight 中的 Apache Storm 拓撲範例](apache-storm-example-topology.md)。
