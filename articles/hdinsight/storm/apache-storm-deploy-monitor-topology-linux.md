---
title: 部署和管理 HDInsight 上的 Apache Storm 拓撲
description: 瞭解如何使用基於 Linux 的 HDInsight 上的風暴儀表板部署、監視和管理 Apache 風暴拓撲。 使用適用於 Visual Studio 的 Hadoop 工具。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: e890289230b3215bd102d8c5a78dca4f1b7b90f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271898"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>部署和管理 HDInsight 上的 Apache Storm 拓撲

在本文件中，您可以了解管理和監視在 Storm on HDInsight 叢集上所執行 [Apache Storm](https://storm.apache.org/) 拓撲的基本概念。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Storm 叢集。 請參閱[使用 Azure 入口網站建立 Apache Hadoop 叢集](../hdinsight-hadoop-create-linux-clusters-portal.md)，然後選取 [Storm]**** 作為 [叢集類型]****。

* （可選）熟悉安全外殼 （SSH） 和安全副本 （SCP）。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

* （可選）視覺化工作室、Azure SDK 2.5.1 或更新，以及視覺化工作室的資料湖工具。 有關詳細資訊，請參閱[Apache Hadoop &視覺化工作室資料湖工具](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)。

## <a name="submit-a-topology-using-visual-studio"></a>使用視覺化工作室提交拓撲

您可以使用視覺化工作室的資料湖工具將 C# 或混合拓撲提交到風暴群集。 下列步驟使用範例應用程式。 有關使用資料湖工具創建拓撲的資訊，請參閱[使用視覺化工作室和 C# 的 Apache 風暴拓撲](apache-storm-develop-csharp-visual-studio-topology.md)。

1. 如果您尚未安裝最新版本的資料湖工具用於視覺化工作室，請參閱[使用資料湖工具視覺化工作室](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)。

    > [!NOTE]  
    > Azure 資料湖和流分析工具以前稱為視覺化工作室的 HDInsight 工具。
    >
    > Visual Studio 的 Azure 資料湖和流分析工具組含在 Visual Studio 2019 的**Azure 開發**工作負荷中。

1. 啟動 Visual Studio。

1. 在 [開始]**** 視窗中，選取 [建立新專案]****。

1. 在"**創建新專案**"視窗中，選擇搜索框並輸入`Storm`。 然後從結果清單中選擇 **"風暴樣本**"，然後選擇 **"下一步**"。

1. 在 **"配置新專案**"視窗中，輸入**專案名稱**，然後轉到 或創建**位置**以將新專案保存到 中。 然後選擇 **"創建**"。

    ![配置新專案視窗視覺化工作室](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

1. 從**伺服器資源管理器**中，按右鍵**Azure**並選擇 **"連接到 Microsoft Azure 訂閱..."** 並完成登錄過程。

1. 從**解決方案資源管理器**中，按右鍵專案，然後選擇**在 HDInsight 上提交到風暴**。

    > [!NOTE]  
    > 如果出現提示，請輸入您 Azure 訂閱的登入認證。 如果您有多個訂用帳戶，請登入包含 Storm on HDInsight 叢集的訂用帳戶。

1. 在 **"提交拓撲"** 對話方塊中，在 **"風暴群集**"下拉清單下，在 HDInsight 群集上選擇"風暴"，然後選擇"**提交**"。 您可以通過查看 **"輸出"** 窗格來監視提交是否成功。

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>使用 SSH 和 Storm 命令提交拓撲

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)連接到群集。 通過將 CLUSTERNAME 替換為群集的名稱來編輯下面的命令，然後輸入以下命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 在 ssh 會話中，使用以下命令啟動**WordCount**示例拓撲：

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    這個命令會在叢集上啟動範例 WordCount 拓撲。 這個拓撲會隨機產生句子，並計算句子中每個字詞的出現次數。

    > [!NOTE]  
    > 向群集提交拓撲時，必須先複製包含群集的 .jar 檔，然後才能使用 該`storm`命令。 若要將檔案複製到叢集，您可以使用 `scp` 命令。 例如，輸入 `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`。
    >
    > *WordCount*示例和其他風暴啟動示例已包含在 群集中`/usr/hdp/current/storm-client/contrib/storm-starter/`。

## <a name="submit-a-topology-programmatically"></a>以程式設計方式提交拓撲

您可以透過程式設計的方式，使用 Nimbus 服務部署拓撲。 [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)提供了一個示例 JAVA 應用程式，演示如何通過 Nimbus 服務部署和啟動拓撲。

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>監視和管理視覺化工作室中的拓撲

使用 Visual Studio 提交拓撲時，將顯示 **"風暴拓撲視圖"** 視窗。 從清單中選取拓撲，以檢視執行中拓撲的詳細資訊。

![監視器拓撲、風暴拓撲視圖視窗、視覺化工作室](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> 您也可以從 [伺服器總管]**** 檢視 [Storm 拓撲]****。 展開**Azure** > **HDInsight，** 按右鍵 HDInsight 群集上的風暴，然後選擇 **"查看風暴拓撲**"。

選取 Spout 或 Bolt 的圖形以檢視這些元件的資訊。 為所選項目，將顯示一個帶有元件資訊的工具提示。

### <a name="deactivate-and-reactivate-a-topology"></a>停用並重新啟動拓撲

停用拓撲會暫停它，直到拓撲被終止或重新啟動。 要執行這些操作，請使用 **"風暴拓撲視圖"** 視窗頂部的 **"操作"** 區域中的 **"停用**"和 **"重新啟動"** 按鈕。

### <a name="rebalance-a-topology"></a>重新平衡拓撲

重新平衡拓撲可以讓系統修訂拓撲的平行處理原則。 例如，如果已調整群集大小以添加更多注釋，則重新平衡允許拓撲看到新節點。

要重新平衡拓撲，請使用 **"風暴拓撲視圖"** 視窗中**的"操作**"區域中的 **"重新平衡**"按鈕。

> [!WARNING]  
> 重新平衡拓撲將停用拓撲，在群集中均勻地重新分配輔助工作，然後將拓撲返回到重新平衡發生之前的狀態。 如果拓撲處於活動狀態，則它再次變為活動狀態。 如果拓撲已停用，則該拓撲將保持停用狀態。

### <a name="kill-a-running-topology"></a>刪除執行中拓撲

風暴拓撲將繼續運行，直到它們停止或群集被刪除。 要停止拓撲，請使用 **"操作"** 區域中的 **"終止**"按鈕。

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>使用 SSH 和 Storm 命令監視和管理拓撲

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

### <a name="deactivate-and-reactivate-a-topology"></a>停用並重新啟動拓撲

停用拓撲會暫停它，直到拓撲被終止或重新啟動。 使用以下命令停用或重新啟動：

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

重新平衡拓撲可以讓系統修訂拓撲的平行處理原則。 例如，如果已調整群集大小以添加更多注釋，則重新平衡允許拓撲看到新節點。

> [!WARNING]  
> 重新平衡拓撲將停用拓撲，在群集中均勻地重新分配輔助工作，然後將拓撲返回到重新平衡發生之前的狀態。 如果拓撲處於活動狀態，則它再次變為活動狀態。 如果已停用，它就會保持停用。

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>使用 Storm UI 監視和管理拓撲

Storm UI 提供了一個用於處理正在運行的拓撲的 Web 介面，並且它包含在 HDInsight 群集中。 若要檢視 Storm UI，請使用網頁瀏覽器開啟 `https://CLUSTERNAME.azurehdinsight.net/stormui`，其中 *CLUSTERNAME* 是叢集的名稱。

> [!NOTE]  
> 如果系統要求您提供使用者名和密碼，請輸入創建群集時使用的群集管理員使用者名和密碼。

### <a name="storm-ui-main-page"></a>風暴 UI 主頁

Storm UI 的主頁面會提供下列資訊：

| 區段 | 描述 |
| --- | --- |
| 叢集摘要| 有關 Storm 叢集的基本資訊。 |
| 尼姆布斯摘要 | 基本尼姆布斯資訊的清單。 |
| 拓撲摘要 | 執行中拓撲的清單。 要查看有關特定拓撲的詳細資訊，請在 **"名稱"** 列中選擇其連結。 |
| 主管摘要 | Storm 監督員的相關資訊。 要查看與特定主管關聯的工作資源，請在 **"主機**"或 **"Id"** 列中選擇其連結。 |
| 尼姆布斯配置 | 適用於叢集的 Nimbus 設定。 |

Storm UI 主頁看起來與此網頁類似：

![主頁， 風暴 UI， 阿帕奇風暴拓撲， Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>拓撲摘要

選取 [拓撲摘要] **** 區段中的連結會顯示拓撲的下列資訊：

| 區段 | 描述 |
| --- | --- |
| 拓撲摘要 | 有關拓撲的基本資訊。 |
| 拓撲操作| 可以對拓撲執行的管理操作。 本節稍後將介紹可用的操作。 |
| 拓撲統計資料 | 有關拓撲的統計資料。 要設置此部分中條目的時間範圍，請在 **"視窗**"列中選擇其連結。 |
| 噴口 *（時間範圍）* | 拓撲所使用的 Spout。 要查看有關特定出嘴的詳細資訊，請在 **"Id"** 列中選擇其連結。 |
| 螺栓 *（時間框架）* | 拓撲所使用的 Bolt。 要查看有關特定螺栓的詳細資訊，請在 **"Id"** 列中選擇其連結。 |
| 工作人員資源 | 輔助角色資源的清單。 要查看有關特定工作資源的詳細資訊，請在 **"主機"** 列中選擇其連結。 |
| 拓撲視覺化 | 顯示拓撲視覺化效果的"**顯示視覺化"** 按鈕。 |
| 拓撲設定 | 所選取拓撲的設定。 |

Storm 拓撲摘要頁與此網頁類似：

![拓撲摘要頁，風暴 UI，阿帕奇風暴，Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

在**拓撲操作**部分中，您可以選擇以下按鈕來執行操作：

| 按鈕 | 描述 |
| --- | --- |
| 啟動 | 繼續處理已停用的拓撲。 |
| 停用 | 暫停執行中拓撲。 |
| 重新平衡 | 調整拓撲的平行處理原則。 更改群集中的節點數後，應重新平衡正在運行的拓撲。 此操作允許拓撲調整並行性，以補償群集中節點數量增加或減少。<br/><br/>如需詳細資訊，請參閱 <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Understanding the parallelism of an Apache Storm topology</a> (了解 Apache Storm 拓撲的平行處理原則)。
| 終止 | 在指定的逾時之後終止 Storm 拓撲。 |
| 偵錯 | 開始正在運行的拓撲的調試會話。 |
| 停止調試 | 結束正在運行的拓撲的調試會話。 |
| 更改日誌級別 | 修改調試日誌級別。 |

##### <a name="spout-and-bolt-summary"></a>噴口和螺栓摘要

從 [Spout]**** 或 [Bolt]**** 區段中選取 Spout 會顯示所選取項目的下列資訊：

| 區段 | 描述 |
| --- | --- |
| 元件摘要 | 有關 Spout 或 Bolt 的基本資訊。 |
| 元件操作 | **調試**和**停止調試**按鈕。 |
| 噴口統計資訊或螺栓統計資訊 | 有關 Spout 或 Bolt 的統計資料。 要設置此部分中條目的時間範圍，請在 **"視窗**"列中選擇其連結。 |
| （僅限螺栓）<br/>輸入統計資訊 *（時間範圍）* | Bolt 所取用之輸入串流的相關資訊。 |
| 輸出統計 *（時間框架）* | Spout 或 Bolt 所發出資料流的資訊。 |
| 分析和調試 | 用於分析和調試此頁面上的元件的控制項。 您可以設置**狀態/超時（分鐘）** 值，也可以為**JStack、****重新開機輔助角色**和**堆選擇**按鈕。 |
| 執行者 *（時間框架）* | Spout 或 Bolt 執行個體的相關資訊。 要查看為此實例生成的診斷資訊日誌，請選擇特定執行器的**埠**條目。 您還可以通過在 **"主機"** 列中選擇與特定執行器的連結來查看與特定執行器關聯的工作資源。 |
| Errors | Spout 或 Bolt 的任何錯誤資訊。 |

Storm bolt 摘要頁與此網頁類似：

![螺栓摘要頁面， 風暴 UI， 阿帕奇風暴， Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>使用 REST API 監視和管理拓撲

Storm UI 構建在 REST API 之上，因此您可以使用 REST API 執行類似的管理和監視任務。 您可以使用 REST API 建立自訂工具來管理和監視 Storm 拓撲。

如需詳細資訊，請參閱 [Apache Storm UI REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html)。 下列資訊專用於搭配使用 REST API 與 Apache Storm on HDInsight。

> [!IMPORTANT]  
> 風暴 REST API 不在互聯網上公開提供。 必須使用 SSH 隧道訪問 HDInsight 群集頭節點。 有關創建和使用 SSH 隧道的資訊，請參閱[使用 SSH 隧道訪問 Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md)。

### <a name="base-uri"></a>基底 URI

基於 Linux 的 HDInsight 群集上的 REST API 的基礎 URI`https://HEADNODEFQDN:8744/api/v1/`可在 URL 位址上提供，您可以在該位址中用頭節點替換*HEADNODEFQDN。* 頭節點的功能變數名稱在群集創建期間生成，不是靜態的。

您可以通過多種方式找到群集頭節點的完全限定功能變數名稱 （FQDN）：

| FQDN 探索方法 | 描述 |
| --- | --- |
| SSH 會話 | 使用命令 `headnode -f` (從 SSH 工作階段到叢集)。 |
| Ambari Web | 在 Ambari 群集網頁`https://CLUSTERNAME.azurehdinsight.net`（）上，從頁面頂部選擇 **"服務**"，然後選擇 **"風暴**"。 從 [摘要]**** 索引標籤，選取 [Storm UI 伺服器]****。 託管 Storm UI 和 REST API 的節點 FQDN 位於頁面頂端。 |
| Ambari REST API | 使用命令 `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` 來擷取 Storm UI 和 REST API 執行所在節點的相關資訊。 將*CLUSTERNAME*的兩個實例替換為群集名稱。 系統提示您時，輸入使用者（管理員）帳戶的密碼。 在回應中，JSON 輸出的"host_name"條目包含節點的 FQDN。 |

### <a name="authentication"></a>驗證

對 REST API 的請求必須使用*基本驗證*，因此您必須使用 HDInsight 群集的管理員名稱和密碼。

> [!NOTE]  
> 因為使用純文字傳送基本驗證，所以您應該 *一律* 使用 HTTPS 來保護與叢集通訊的安全。

### <a name="return-values"></a>傳回值

從 REST API 傳回的資訊可能只可在叢集中使用。 例如，無法從 Internet 訪問[為 Apache ZooKeeper](https://zookeeper.apache.org/)伺服器返回的完全限定功能變數名稱 （FQDN）。

## <a name="next-steps"></a>後續步驟

了解如何[使用 Apache Maven 開發 Java 型拓撲](apache-storm-develop-java-topology.md)。

有關更多示例拓撲的清單，請參閱[Azure HDInsight 中的阿帕奇風暴拓撲示例](apache-storm-example-topology.md)。
