---
title: 監視：阿帕奇·安巴里& Azure 監視器日誌 - Azure HDInsight
description: 瞭解如何使用 Ambari 和 Azure 監視器日誌來監視群集運行狀況和可用性。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/06/2020
ms.openlocfilehash: 383366fa3e436c79bed28a7c47f1e9daa5f0d9de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060164"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-and-azure-monitor-logs"></a>如何使用 Apache Ambari 和 Azure 監視器日誌監視群集可用性

HDInsight 群集包括 Apache Ambari，它一目了然地提供運行狀況資訊和預定義警報，以及 Azure 監視器日誌集成，它提供可查詢的指標和日誌，以及可配置的警報。

本文檔演示如何使用這些工具監視群集，並演練一些示例，以配置 Ambari 警報、監視節點可用性速率以及創建 Azure 監視器警報，當一個或多個節點未收到活動訊號時觸發在五個小時。

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>儀表板

通過在 Azure 門戶中的 HDInsight 概述的**群集儀表板**部分中選擇**Ambari 主頁**連結，可以訪問 Ambari 儀表板，如下所示。 或者，可以通過`https://CLUSTERNAME.azurehdinsight.net`在瀏覽器中導航來訪問它，其中 CLUSTERNAME 是群集的名稱。

![HDInsight 資源門戶視圖](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

然後系統會提示您輸入群集登錄使用者名和密碼。 輸入您在建立叢集時所選擇的認證。

然後，您將被帶到 Ambari 儀表板，該儀表板包含顯示少量指標的小部件，以便快速概覽 HDInsight 群集的運行狀況。 這些小部件顯示指標，如即時資料節點（輔助節點）和 JournalNodes（zookeeper 節點）、NameNode（頭節點）的停機時間數，以及特定于某些群集類型的指標，如 Spark 和 Hadoop 群集的 YARN 資源管理器 Uptime。

![阿帕奇·安巴里使用儀表板顯示](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>主機 + 查看單個節點狀態

您還可以查看各個節點的狀態資訊。 選擇 **"主機"** 選項卡以查看群集中所有節點的清單，並查看有關每個節點的基本資訊。 每個節點名稱左側的綠色檢查指示所有元件都位於節點上。 如果元件位於節點上，您將看到一個紅色警報三角形，而不是綠色檢查。

![HDInsight 阿帕奇安巴里主機視圖](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

然後，您可以在節點**的名稱**上選擇以查看該特定節點的更詳細的主機指標。 此視圖顯示每個單個元件的狀態/可用性。

![阿帕奇·安巴里託管單節點視圖](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>安巴里警報

Ambari 還提供多個可配置的警報，這些警報可以提供某些事件的通知。 觸發警報時，警報顯示在 Ambari 的左上角，紅色徽章包含警報數。 選擇此徽章將顯示當前警報的清單。

![阿帕奇·安巴里電流警報計數](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

要查看警報定義及其狀態的清單，請選擇"**警報"** 選項卡，如下所示。

![Ambari 警報定義視圖](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari 提供許多與可用性相關的預定義警報，包括：

| 警示名稱                        | 描述   |
|---|---|
| 資料節點運行狀況摘要           | 如果存在不正常的資料節點，將觸發此服務等級警報|
| 名稱節點高可用性運行狀況 | 如果活動 NameNode 或備用 NameNode 未運行，則觸發此服務等級警報。|
| 可用的日誌節點百分比    | 如果群集中的日記節點數量大於配置的關鍵閾值，則觸發此警報。 它聚合日記本節點流程檢查的結果。 |
| 可用資料節點的百分比       | 如果群集中的資料節點的向下資料節點數大於配置的關鍵閾值，則觸發此警報。 它聚合 DataNode 過程檢查的結果。|

有助於監視群集可用性的 Ambari 警報的完整清單[可在此處](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)找到，

要查看警報的詳細資訊或修改條件，請選擇警報**的名稱**。 以**資料節點運行狀況摘要**為例。 您可以查看警報的說明以及將觸發"警告"或"嚴重"警報的特定條件以及條件的檢查間隔。 要編輯配置，請選擇"配置"框右上角的 **"編輯**"按鈕。

![阿帕奇安巴里警報配置](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

在這裡，您可以編輯說明，更重要的是，用於警告或嚴重警報的檢查間隔和閾值。

![Ambari 警報配置編輯檢視](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

在此示例中，可以使 2 個不正常的 DataNode 觸發嚴重警報，而 1 個不正常的 DataNode 僅觸發警告。 完成編輯後選擇 **"保存**"。

### <a name="email-notifications"></a>電子郵件通知

您還可以選擇為 Ambari 警報配置電子郵件通知。 為此，在 **"警報"** 選項卡上，按一下左上角的 **"操作"** 按鈕，然後**管理通知。**

![Ambari 管理通知操作](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

將打開用於管理警報通知的對話方塊。 **+** 選擇對話方塊底部的，並填寫所需的欄位，向 Ambari 提供發送電子郵件的電子郵件伺服器詳細資訊。

> [!TIP]
> 在管理許多 HDInsight 群集時，設置 Ambari 電子郵件通知是一種在一個位置接收警報的好方法。

## <a name="azure-monitor-logs-integration"></a>Azure 監視器日誌集成

Azure 監視器日誌允許將多個資源（如 HDInsight 群集）生成的資料收集和聚合到一個位置，以實現統一的監視體驗。

作為先決條件，您需要一個日誌分析工作區來存儲收集的資料。 如果您尚未創建，可以按照此處的說明：[創建日誌分析工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>啟用 HDInsight Azure 監視器日誌集成

從門戶中的 HDInsight 群集資源頁中，選擇**Azure 監視器**。 然後，選擇**啟用**並從下拉清單中選擇日誌分析工作區。

![HDInsight 運營管理套件](media/hdinsight-cluster-availability/azure-portal-monitoring.png)

### <a name="query-metrics-and-logs-tables"></a>查詢指標和日誌表

啟用 Azure 監視器日誌集成後（這可能需要幾分鐘時間），導航到**日誌分析工作區**資源並選擇 **"日誌**"。

![日誌分析工作區日誌](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

日誌列出了許多依例查詢，例如：

| 查詢名稱                      | 描述                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| 電腦可用性    | 圖表每小時發送日誌的電腦數                     |
| 清單活動訊號                 | 列出過去一小時的所有電腦活動訊號                           |
| 每台電腦的最後一個活動訊號 | 顯示每台電腦發送的最後一個活動訊號                             |
| 不可用的電腦           | 列出過去 5 小時內未發送活動訊號的所有已知電腦 |
| 供貨率               | 計算每台已連接電腦的可用性率                |

例如，通過選擇"在該查詢上運行"來**運行****可用性速率**依例查詢，如上圖所示。 這將以百分比的形式顯示群集中每個節點的可用性速率。 如果啟用了多個 HDInsight 群集，可以將指標發送到同一日誌分析工作區，您將看到顯示這些群集中所有節點的可用性速率。

![日誌分析工作區記錄"可用性率"依例查詢](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE]  
> 可用性速率在 24 小時內測量，因此群集需要運行至少 24 小時才能看到準確的可用性速率。

您可以通過按一下右上角的 **"Pin"** 來將此表固定到共用儀表板。 如果您沒有任何可寫共用儀表板，則可以在此處瞭解如何創建儀表板：[在 Azure 門戶中創建和共用儀表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard)。

### <a name="azure-monitor-alerts"></a>Azure 監視器警報

您還可以設置 Azure 監視器警報，當指標值或查詢結果滿足特定條件時，將觸發這些警報。 例如，讓我們創建一個警報，在一個或多個節點在 5 小時內未發送活動訊號時發送電子郵件（即假定為不可用）。

從**日誌**，通過選擇"**運行**"該查詢來運行 **"不可用的電腦**依例查詢"，如下所示。

![日誌分析工作區日誌"不可用的電腦"示例](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

如果所有節點都可用，則此查詢應返回當前零結果。 按一下 **"新建警報規則**"開始為此查詢配置警報。

![日誌分析工作區新警報規則](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

警報有三個元件：為其創建規則的*資源*（本例中的日誌分析工作區）、觸發警報*的條件*以及確定觸發警報時將發生的情況*的操作組*。
按一下**條件標題**（如下所示）以完成信號邏輯的配置。

![門戶警報創建規則條件](media/hdinsight-cluster-availability/portal-condition-title.png)

這將打開**配置信號邏輯**。

設置 **"警報邏輯**"部分，如下所示：

*基於：結果數，條件：大於，閾值：0。*

由於此查詢僅返回不可用的節點作為結果，因此如果結果數大於 0，警報應觸發。

在 **"基於"部分的評估**"中，根據要檢查不可用節點的頻率設置**期間**和**頻率**。

為此警報的目的，您希望確保 **"期間=頻率"。** 有關期間、頻率和其他警報參數的詳細資訊，請參閱[此處](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)。

完成配置信號邏輯後選擇 **"完成**"。

![警報規則配置信號邏輯](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

如果尚未現有操作組，請按一下"**操作組**"部分下的 **"新建**"。

![警報規則創建新的操作組](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

這將打開 **"添加操作組**"。 選擇**操作組名稱**、**短名稱**、**訂閱**和資源**組。** 在 **"操作"** 部分下，**選擇操作名稱**並選擇 **"電子郵件/短信/推送/語音**作為**操作類型"。**

> [!NOTE]
> 除了電子郵件/SMS/Push/語音之外，警報還可以觸發其他幾個操作，例如 Azure 函數、邏輯應用、Webhook、ITSM 和自動化 Runbook。 [瞭解更多資訊。](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

這將打開**電子郵件/短信/推送/語音**。 選擇收件者**的姓名**，**選中****"電子郵件**"框，然後鍵入您希望向其發送警報的電子郵件地址。 在 **"電子郵件/短信/推送/語音**"中選擇 **"確定"，** 然後在 **"添加操作組"** 中完成操作組的配置。

![警報規則創建添加操作組](media/hdinsight-cluster-availability/portal-add-action-group.png)

關閉這些邊欄選項卡後，您應該會看到操作**組在"操作組"** 部分下列出。 最後，通過鍵入**警報規則名稱**和**說明**並選擇**嚴重性**來完成 **"警報詳細資訊**"部分。 按一下 **"創建警報規則**"以完成。

![門戶創建警報規則完成](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> 指定**嚴重性**的能力是創建多個警報時可以使用的強大工具。 例如，如果單個頭節點關閉，則可以創建一個警報以引發警告 （Sev 1），如果兩個頭節點都發生故障，則創建另一個警報，該警報引發嚴重 （Sev 0）。

當滿足此警報的條件時，警報將觸發，您將收到一封電子郵件，其中包含警報詳細資訊，如下所示：

![Azure 監視器警報電子郵件示例](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

您還可以通過訪問**日誌分析工作區**中的**警報**來查看已觸發的所有警報（按嚴重性分組）。

![日誌分析工作區警報](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

選擇嚴重性分組（如上文突出顯示的**Sev 1）** 將顯示已觸發的所有嚴重性警報的記錄，如下所示：

![日誌分析工作區包含一個警報](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>後續步驟

- [HDInsight 中 Apache Hadoop 叢集的可用性和可靠性](hdinsight-high-availability-linux.md)
