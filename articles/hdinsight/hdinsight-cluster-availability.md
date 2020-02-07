---
title: 監視： Apache Ambari & Azure 監視器記錄-Azure HDInsight
description: 瞭解如何使用 Ambari 和 Azure 監視器記錄來監視叢集的健全狀況和可用性。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/06/2020
ms.openlocfilehash: 383366fa3e436c79bed28a7c47f1e9daa5f0d9de
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77060164"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-and-azure-monitor-logs"></a>如何使用 Apache Ambari 和 Azure 監視器記錄監視叢集可用性

HDInsight 叢集包括 Apache Ambari，這兩種方式都會提供快速的健全狀況資訊和預先定義的警示，以及 Azure 監視器的記錄整合，可提供查詢計量和記錄，以及可設定的警示。

本檔說明如何使用這些工具來監視您的叢集，並逐步解說一些範例，以設定 Ambari 警示、監視節點可用性速率，以及建立當未收到一或多個節點的心跳時所引發的 Azure 監視器警示五小時內。

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>儀表板

您可以在 Azure 入口網站的 HDInsight 總覽的 [叢集**儀表板**] 區段中選取 [ **Ambari 首頁**] 連結，以存取 Ambari 儀表板，如下所示。 或者，您也可以在瀏覽器中流覽至 `https://CLUSTERNAME.azurehdinsight.net` （其中 CLUSTERNAME 是您的叢集名稱）來存取它。

![HDInsight 資源入口網站視圖](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

接著，系統會提示您輸入叢集登入使用者名稱和密碼。 輸入您在建立叢集時所選擇的認證。

您接著會進入 Ambari 儀表板，其中包含的 widget 會顯示一些計量，讓您快速流覽 HDInsight 叢集的健康情況。 這些小工具會顯示計量，例如即時 Datanode （背景工作節點）和 JournalNodes （zookeeper 節點）、Namenode （前端節點）執行時間，以及特定叢集類型特有的計量，例如 Spark 和 Hadoop 叢集的 YARN ResourceManager 執行時間。

![Apache Ambari 使用儀表板顯示](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>主機–查看個別節點狀態

您也可以查看個別節點的狀態資訊。 選取 [**主機**] 索引標籤，以查看叢集中所有節點的清單，並查看每個節點的基本資訊。 每個節點名稱左邊的綠色核取記號表示節點上的所有元件都已啟動。 如果節點上的元件已關閉，您將會看到紅色的警示三角形，而不是綠色的檢查。

![HDInsight Apache Ambari 主機視圖](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

然後，您可以選取節點的**名稱**，以查看該特定節點的更詳細主機計量。 此視圖會顯示每個個別元件的狀態/可用性。

![Apache Ambari 主控單一節點的視圖](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari 警示

Ambari 也提供數個可設定的警示，以提供特定事件的通知。 觸發警示時，它們會顯示在 Ambari 的左上角，其中包含警示數目的紅色徽章。 選取此徽章會顯示目前警示的清單。

![Apache Ambari 目前的警示計數](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

若要查看警示定義及其狀態的清單，請選取 [**警示**] 索引標籤，如下所示。

![Ambari 警示定義視圖](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari 提供許多與可用性相關的預先定義警示，包括：

| 警示名稱                        | 描述   |
|---|---|
| DataNode 健全狀況摘要           | 如果有狀況不良的 Datanode，就會觸發此服務層級警示|
| NameNode 高可用性健全狀況 | 如果作用中的 NameNode 或待命 NameNode 不在執行中，就會觸發此服務層級警示。|
| 可用的 JournalNodes 百分比    | 如果叢集中的關閉 JournalNodes 數目大於設定的重大閾值，就會觸發此警示。 它會匯總 JournalNode 流程檢查的結果。 |
| 可用的 Datanode 百分比       | 如果叢集中的關閉 Datanode 數目大於設定的重大閾值，就會觸發此警示。 它會匯總 DataNode 流程檢查的結果。|

如需可協助監視叢集可用性的 Ambari 警示完整清單，請參閱[這裡](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)。

若要查看警示或修改準則的詳細資料，請選取警示的**名稱**。 請以**DataNode 健康情況摘要**作為範例。 您可以查看警示的描述，以及將觸發「警告」或「重大」警示的特定準則，以及準則的檢查間隔。 若要編輯設定，請選取 [設定] 方塊右上角的 [**編輯**] 按鈕。

![Apache Ambari 警示設定](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

在這裡，您可以編輯 [描述]，更重要的是 [警告] 或 [重大] 警示的 [檢查間隔] 和 [閾值]。

![Ambari 警示設定編輯檢視](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

在此範例中，您可以讓2個狀況不良的 Datanode 觸發重大警示，而1個狀況不良的 DataNode 只會觸發警告。 完成編輯時，請選取 [**儲存**]。

### <a name="email-notifications"></a>電子郵件通知

您也可以選擇性地設定 Ambari 警示的電子郵件通知。 若要這麼做，請在 [**警示**] 索引標籤上，按一下左上方的 [**動作**] 按鈕，然後管理 [**通知]。**

![Ambari 管理通知動作](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

隨即會開啟用於管理警示通知的對話方塊。 選取對話方塊底部的 [ **+** ]，並填寫必要的欄位，以提供 Ambari 電子郵件伺服器詳細資料，讓您從中傳送電子郵件。

> [!TIP]
> 設定 Ambari 電子郵件通知，是在管理許多 HDInsight 叢集時，可以在一個位置接收警示的好方法。

## <a name="azure-monitor-logs-integration"></a>Azure 監視器記錄整合

Azure 監視器記錄可讓多個資源（例如 HDInsight 叢集）所產生的資料在一個位置收集和匯總，以達成一致的監視體驗。

必要條件是，您需要有 Log Analytics 工作區來儲存收集的資料。 如果您尚未建立，您可以依照下列指示進行：[建立 Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>啟用 HDInsight Azure 監視器記錄整合

從入口網站的 [HDInsight 叢集資源] 頁面中，選取 [ **Azure 監視器**]。 然後，選取 [**啟用**]，然後從下拉式選單中選取您的 Log Analytics 工作區。

![HDInsight 操作管理套件](media/hdinsight-cluster-availability/azure-portal-monitoring.png)

### <a name="query-metrics-and-logs-tables"></a>查詢計量和記錄資料表

一旦啟用 Azure 監視器記錄整合（這可能需要幾分鐘的時間），請流覽至您的**Log Analytics 工作區**資源，然後選取 [**記錄**]。

![Log Analytics 工作區記錄](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

記錄會列出一些範例查詢，例如：

| 查詢名稱                      | 描述                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| 目前的電腦可用性    | 圖表傳送記錄的電腦數目（每小時）                     |
| 列出心跳                 | 列出過去一小時內的所有電腦的心跳                           |
| 每部電腦的最後一個信號 | 顯示每部電腦傳送的最後一個信號                             |
| 無法使用的電腦           | 列出過去5小時內未傳送信號的所有已知電腦 |
| 可用性速率               | 計算每部連線電腦的可用性費率                |

例如，在該查詢上選取 [**執行**]，以執行 [**可用性速率**] 範例查詢，如上面的螢幕擷取畫面所示。 這會顯示叢集中每個節點的可用性速率（以百分比表示）。 如果您已啟用多個 HDInsight 叢集將計量傳送至相同的 Log Analytics 工作區，您會看到顯示在這些叢集中所有節點的可用性費率。

![Log Analytics 工作區記錄「可用性速率」範例查詢](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE]  
> 可用性速率是以24小時的期間來測量，因此您的叢集至少必須執行24小時，您才會看到正確的可用性速率。

您可以按一下右上角的 [**釘**選]，將此資料表釘選到共用的儀表板。 如果您沒有任何可寫入的共用儀表板，您可以在這裡瞭解如何建立它：[在 Azure 入口網站中建立和共用儀表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard)。

### <a name="azure-monitor-alerts"></a>Azure 監視器警示

您也可以設定 Azure 監視器警示，當計量的值或查詢的結果符合特定條件時，就會觸發此警示。 例如，讓我們建立警示，以在一或多個節點未在5小時（也就是假設為無法使用）傳送信號時，傳送電子郵件。

在 [**記錄**] 中，選取 [在該查詢上**執行**] 來執行 [**無法使用的電腦**] 範例查詢，如下所示。

![Log Analytics 工作區記錄「無法使用的電腦」範例](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

如果所有節點都可以使用，此查詢應該不會立即傳回零個結果。 按一下 [**新增警示規則**]，開始設定此查詢的警示。

![Log Analytics 工作區新增警示規則](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

警示有三個元件：用來建立規則的*資源*（在此案例中為 Log Analytics 工作區）、觸發警示的*條件*，以及決定觸發警示時將發生什麼情況的*動作群組*。
按一下 [**條件] 標題**（如下所示），以完成設定信號邏輯。

![入口網站警示建立規則條件](media/hdinsight-cluster-availability/portal-condition-title.png)

這會開啟 [**設定信號邏輯**]。

設定 [**警示邏輯**] 區段，如下所示：

*依據：結果數目，條件：大於，臨界值：0。*

由於此查詢只會傳回無法使用的節點做為結果，因此，如果結果數目大於0，就應該引發警示。

在 [**評估依據**] 區段中，根據您要檢查無法使用的節點的頻率來設定**期間**和**頻率**。

基於此警示的目的，您會想要確認**Period = Frequency。** 您可以在[這裡](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)找到有關期間、頻率和其他警示參數的詳細資訊。

當您完成設定信號邏輯時，請選取 [**完成**]。

![警示規則設定信號邏輯](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

如果您還沒有現有的動作群組，請按一下 [**動作群組**] 區段底下的 [**建立新**的]。

![警示規則會建立新的動作群組](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

這會開啟 [**新增動作群組**]。 選擇 [**動作組名**]、[**簡短名稱**]、[**訂**用帳戶] 和 [**資源群組]。** 在 [**動作**] 區段下，選擇**動作名稱**，然後選取 [**電子郵件/SMS/推播/語音**] 作為**動作類型。**

> [!NOTE]
> 除了電子郵件/SMS/推播/語音以外，還有其他幾個動作會觸發警示，例如 Azure 函數、LogicApp、Webhook、ITSM 和自動化 Runbook。 [瞭解更多資訊。](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

這會開啟 [**電子郵件/SMS/推播/語音**]。 選擇收件者的**名稱**，**勾選**[**電子郵件**] 方塊，然後輸入您要傳送警示的目標電子郵件地址。 在 [**電子郵件/SMS/推播/語音**] 中選取 **[確定]** ，然後在 [**新增動作群組**] 中完成動作群組的設定。

![警示規則會建立新增動作群組](media/hdinsight-cluster-availability/portal-add-action-group.png)

在這些 blade 關閉之後，您應該會看到您的動作群組列在 [**動作群組**] 區段底下。 最後，輸入**警示規則名稱**和**描述**，並選擇**嚴重性**，以完成 [**警示詳細資料**] 區段。 按一下 [**建立警示規則**] 完成。

![入口網站建立警示規則完成](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> 指定**嚴重性**的能力是一種功能強大的工具，可在建立多個警示時使用。 例如，您可以建立一個警示來引發警告（嚴重性1），如果單一前端節點中斷，另一個警示則會在這兩個前端節點關閉的罕見事件中引發重大（嚴重性0）。

當符合此警示的條件時，將會引發警示，而您會收到包含警示詳細資料的電子郵件，如下所示：

![Azure 監視器警示電子郵件範例](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

您也可以前往**Log Analytics 工作區**中的 [**警示**]，以查看所有已引發的警示（依嚴重性分組）。

![Log Analytics 工作區警示](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

在嚴重性群組上選取（如上方反白顯示的**嚴重性 1** ）將會顯示該嚴重性的所有警示記錄，如下所示：

![Log Analytics 工作區嚴重性一個警示](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>後續步驟

- [HDInsight 中 Apache Hadoop 叢集的可用性和可靠性](hdinsight-high-availability-linux.md)
