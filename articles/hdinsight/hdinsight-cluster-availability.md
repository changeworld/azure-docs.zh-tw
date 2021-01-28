---
title: 如何在 Azure HDInsight 中使用 Apache Ambari 監視叢集可用性
description: 瞭解如何使用 Apache Ambari 來監視叢集健康情況和可用性。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/01/2020
ms.openlocfilehash: 34f752d4c3d50d5f680e317b2724b1e631537f32
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933202"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-in-azure-hdinsight"></a>如何在 Azure HDInsight 中使用 Apache Ambari 監視叢集可用性

HDInsight 叢集包含 Apache Ambari，可提供健全狀況資訊一覽和預先定義的警示。

本文說明如何使用 Ambari 來監視您的叢集，並逐步解說設定 Ambari 警示的一些範例、監視節點可用性速率，以及建立在五小時內未收到一或多個節點的信號時引發的 Azure 監視器警示。

## <a name="dashboard"></a>儀表板

Ambari 儀表板的存取方式，是在 Azure 入口網站中的 [HDInsight 總覽] 的 [叢集 **儀表板**] 區段中選取 [ **Ambari 首頁**] 連結，如下所示。 或者，您也可以藉由 `https://CLUSTERNAME.azurehdinsight.net` 在瀏覽器中流覽至 CLUSTERNAME 是您的叢集名稱來存取它。

![HDInsight 資源入口網站視圖](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

然後，系統會提示您輸入叢集登入使用者名稱和密碼。 輸入您在建立叢集時所選擇的認證。

然後，您會進入 Ambari 儀表板，其中包含的 widget 會顯示一些計量，讓您快速流覽 HDInsight 叢集的健康情況。 這些小工具會顯示計量，例如即時 [Datanode 數目 (背景工作節點) 和 JournalNodes (zookeeper 節點) 、Namenode (前端節點) 執行時間，以及特定叢集類型的計量（例如 Spark 和 Hadoop 叢集的 YARN ResourceManager 執行時間）。

![Apache Ambari 使用儀表板顯示](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

## <a name="hosts--view-individual-node-status"></a>主機–查看個別節點狀態

您也可以查看個別節點的狀態資訊。 選取 [ **主機** ] 索引標籤，以查看您叢集中所有節點的清單，並查看每個節點的基本資訊。 每個節點名稱左邊的綠色核取記號表示節點上的所有元件都已啟動。 如果某個元件在某個節點上關閉，您會看到紅色的警示三角形，而不是綠色的核取記號。

![HDInsight Apache Ambari 主機視圖](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

然後，您可以選取節點的 **名稱** ，以查看該特定節點更詳細的主機計量。 這個視圖會顯示每個個別元件的狀態/可用性。

![Apache Ambari 主控單一節點視圖](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

## <a name="ambari-alerts"></a>Ambari 警示

Ambari 還提供數個可設定的警示，可提供特定事件的通知。 觸發警示時，它們會顯示在 Ambari 的左上角，內含警示數目的紅色徽章。 選取此徽章會顯示目前警示的清單。

![Apache Ambari 目前的警示計數](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

若要查看警示定義清單及其狀態，請選取 [ **警示** ] 索引標籤，如下所示。

![Ambari 警示定義視圖](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari 提供許多與可用性相關的預先定義警示，包括：

| 警示名稱                        | 描述   |
|---|---|
| DataNode 健全狀況摘要           | 如果有狀況不良的 [Datanode，則會觸發此服務層級警示|
| NameNode 高可用性健全狀況 | 如果使用中的 NameNode 或待命 NameNode 未執行，就會觸發此服務層級警示。|
| 可用的 JournalNodes 百分比    | 如果叢集中的 JournalNodes 數目大於設定的 [重大臨界值]，就會觸發此警示。 它會匯總 JournalNode 流程檢查的結果。 |
| 可用的 [Datanode 百分比       | 如果叢集中的 [Datanode 數目大於設定的 [重大臨界值]，就會觸發此警示。 它會匯總 DataNode 流程檢查的結果。|


若要查看警示的詳細資料或修改準則，請選取警示的 **名稱** 。 以 **DataNode 健全摘要** 作為範例。 您可以看到警示的描述，以及會觸發「警告」或「重大」警示的特定準則，以及準則的檢查間隔。 若要編輯設定，請選取 [設定] 方塊右上角的 [ **編輯** ] 按鈕。

![Apache Ambari 警示設定](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

您可以在這裡編輯描述，更重要的是警告或重大警示的檢查間隔和臨界值。

![Ambari 警示設定編輯檢視](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

在此範例中，您可以讓2個狀況不良的 [Datanode 觸發重大警示，而1個狀況不良的 DataNode 只會觸發警告。 當您完成編輯時，請選取 [ **儲存** ]。

## <a name="email-notifications"></a>電子郵件通知

您也可以選擇性地設定 Ambari 警示的電子郵件通知。 若要這樣做，請在 [ **警示** ] 索引標籤上，按一下左上角的 [ **動作** ] 按鈕，然後 **管理通知。**

![Ambari 管理通知動作](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

管理警示通知的對話方塊隨即開啟。 選取 **+** 對話方塊底部的，並填寫必要欄位，以提供 Ambari 電子郵件伺服器詳細資料，以便傳送電子郵件。

> [!TIP]
> 設定 Ambari 電子郵件通知在管理許多 HDInsight 叢集時，可能是在一個地方接收警示的好方法。

## <a name="next-steps"></a>後續步驟

- [HDInsight 中 Apache Hadoop 叢集的可用性和可靠性](./hdinsight-business-continuity.md)
- [叢集可用性 - Azure 監視器記錄](./cluster-availability-monitor-logs.md)
- [使用 Azure 監視器記錄](hdinsight-hadoop-oms-log-analytics-tutorial.md)
- [Apache Ambari 電子郵件通知](apache-ambari-email.md)
