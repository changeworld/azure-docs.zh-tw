---
title: 查詢 Azure 監視器記錄以監視 Azure HDInsight 叢集
description: 瞭解如何對 Azure 監視器記錄執行查詢，以監視在 HDInsight 叢集中執行的作業。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: f9213f36ec33939c3df3b56d21822aa3b6a17c03
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945615"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>查詢 Azure 監視器記錄來監視 HDInsight 叢集

瞭解如何使用 Azure 監視器記錄來監視 Azure HDInsight 叢集的一些基本案例：

* [分析 HDInsight 叢集計量](#analyze-hdinsight-cluster-metrics)
* [建立事件警示](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>先決條件

您必須已將 HDInsight 叢集設定為使用 Azure 監視器記錄，並將 HDInsight 叢集特定的 Azure 監視器記錄監視解決方案新增至工作區。 如需相關指示，請參閱 [搭配使用 Azure 監視器記錄與 HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md)叢集。

## <a name="analyze-hdinsight-cluster-metrics"></a>分析 HDInsight 叢集計量

了解如何為您的 HDInsight 叢集尋找特定的計量。

1. 從 Azure 入口網站開啟與您的 HDInsight 叢集相關聯的 Log Analytics 工作區。
1. 在 [一般] 底下，選取 [記錄檔]。
1. 在 [搜尋] 方塊中輸入下列查詢，以搜尋所有設定為使用 Azure 監視器記錄的 HDInsight 叢集之所有可用計量的度量，然後選取 [ **執行**]。 檢閱結果。

    ```kusto
    search *
    ```

    ![Apache Ambari analytics 搜尋所有計量](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "搜尋所有計量")

1. 從左側功能表中，選取 [ **篩選** ] 索引標籤。

1. 在 [ **類型**] 下，選取 [ **信號**]。 然後選取 [套用 **& 執行**]。

    ![log analytics 搜尋特定計量](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "搜尋特定計量")

1. 請注意，文字方塊中的查詢會變更為：

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. 您可以使用左側功能表中的可用選項來更深入瞭解。 例如：

    - 若要查看特定節點的記錄：

        ![搜尋特定錯誤 output1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "搜尋特定錯誤 output1")

    - 若要在特定時間查看記錄：

        ![搜尋特定錯誤 output2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "搜尋特定錯誤 output2")

1. 選取 [套用] **& 執行** 並檢查結果。 另請注意，查詢已更新為：

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>其他範例查詢

以10分鐘間隔（依叢集名稱分類）所使用之平均資源的範例查詢：

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

除了根據平均使用資源精簡輸出外，還可以使用下列查詢，以每 10 分鐘為範圍，根據資源使用的尖峰時間 (以及第 90 個或第 95 個百分位數) 來精簡結果：

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>建立追蹤事件的警示

建立警示的第一個步驟是取得要觸發警示的查詢。 您可以使用任何您想要建立警示的查詢。

1. 從 Azure 入口網站開啟與您的 HDInsight 叢集相關聯的 Log Analytics 工作區。
1. 在 [一般] 底下，選取 [記錄檔]。
1. 執行下列您想要建立警示的查詢，然後選取 [ **執行**]。

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    查詢會提供在 HDInsight 叢集上執行失敗的應用程式清單。

1. 選取頁面頂端的 [ **新增警示規則** ]。

    ![輸入查詢以建立 alert1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "輸入查詢以建立 alert1")

1. 在 [建立規則] 視窗中，輸入查詢和其他詳細資料以建立警示，然後選取 [建立警示規則]。

    ![輸入查詢以建立警示2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "輸入查詢以建立警示2")

### <a name="edit-or-delete-an-existing-alert"></a>編輯或刪除現有的警示

1. 從 Azure 入口網站開啟 Log Analytics 工作區。

1. 從左側功能表中，選取 [ **監視**] 底下的 [ **警示**]。

1. 在頂端，選取 [ **管理警示規則**]。

1. 選取您要編輯或刪除的警示。

1. 您可以使用下列選項：**儲存**、**捨棄**、**停用** 和 **刪除**。

    ![HDInsight Azure 監視器記錄警示刪除編輯](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

如需詳細資訊，請參閱[使用 Azure 監視器建立、檢視及管理計量警示](../azure-monitor/platform/alerts-metric.md)。

## <a name="see-also"></a>另請參閱

* [開始使用 Azure 監視器中的查詢](../azure-monitor/log-query/get-started-queries.md)
* [在 Azure 監視器中使用 View Designer 建立自訂視圖](../azure-monitor/platform/view-designer.md)
