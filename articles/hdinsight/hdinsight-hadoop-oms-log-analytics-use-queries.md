---
title: 查詢 Azure 監視器日誌以監視 Azure HDInsight 群集
description: 瞭解如何在 Azure 監視器日誌上執行查詢，以監視在 HDInsight 群集中運行的作業。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 65e85548420116bdfcab87fe9f81a20e66226beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74803774"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>查詢 Azure 監視器日誌以監視 HDInsight 群集

瞭解如何使用 Azure 監視器日誌監視 Azure HDInsight 群集的一些基本方案：

* [分析 HDInsight 叢集計量](#analyze-hdinsight-cluster-metrics)
* [建立事件警示](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisites

您必須將 HDInsight 群集配置為使用 Azure 監視器日誌，並將特定于 HDInsight 群集的 Azure 監視器日誌監視解決方案添加到工作區。 有關說明，請參閱[使用具有 HDInsight 群集的 Azure 監視器日誌](hdinsight-hadoop-oms-log-analytics-tutorial.md)。

## <a name="analyze-hdinsight-cluster-metrics"></a>分析 HDInsight 叢集計量

了解如何為您的 HDInsight 叢集尋找特定的計量。

1. 從 Azure 入口網站開啟與您的 HDInsight 叢集相關聯的 Log Analytics 工作區。
1. 在 **"常規"** 下，選擇 **"日誌**"。
1. 在搜索框中鍵入以下查詢，以搜索配置為使用 Azure 監視器日誌的所有 HDInsight 群集的所有可用指標的所有可用指標，然後選擇"**運行**"。 檢閱結果。

    ```kusto
    search *
    ```

    ![阿帕奇·安巴里分析搜索所有指標](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "搜索所有指標")

1. 從左側功能表中，選擇 **"篩選"** 選項卡。

1. 在 **"類型"** 下，選擇 **"活動訊號**"。 然後選擇 **"應用&運行**。

    ![日誌分析搜索特定指標](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "搜索特定指標")

1. 請注意，文字方塊中的查詢更改為：

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. 您可以使用左側功能表中提供的選項進行更深入的挖掘。 例如：

    - 要查看來自特定節點的日誌：

        ![搜索特定錯誤輸出1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "搜索特定錯誤輸出1")

    - 要在特定時間查看日誌：

        ![搜索特定錯誤輸出2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "搜索特定錯誤輸出2")

1. 選擇 **"應用&運行**並查看結果。 另請注意，查詢已更新為：

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>其他依例查詢

基於 10 分鐘間隔內使用的資源平均值的依例查詢，按群集名稱分類：

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
1. 在 **"常規"** 下，選擇 **"日誌**"。
1. 運行要在其上創建警報的以下查詢，然後選擇 **"運行**"。

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    查詢會提供在 HDInsight 叢集上執行失敗的應用程式清單。

1. 選擇頁面頂部**的新警報規則**。

    ![輸入查詢以創建警報1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "輸入查詢以創建警報1")

1. 在 [建立規則]**** 視窗中，輸入查詢和其他詳細資料以建立警示，然後選取 [建立警示規則]****。

    ![輸入查詢以創建警報2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "輸入查詢以創建警報2")

### <a name="edit-or-delete-an-existing-alert"></a>編輯或刪除現有警報

1. 從 Azure 入口網站開啟 Log Analytics 工作區。

1. 從左側功能表中，在 **"監視"** 下，選擇 **"警報**"。

1. 向頂部，選擇 **"管理警報規則**"。

1. 選取您要編輯或刪除的警示。

1. 您可以使用下列選項：**儲存**、**捨棄**、**停用**和**刪除**。

    ![HDInsight Azure 監視器日誌警報刪除編輯](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

如需詳細資訊，請參閱[使用 Azure 監視器建立、檢視及管理計量警示](../azure-monitor/platform/alerts-metric.md)。

## <a name="see-also"></a>另請參閱

* [開始使用 Azure 監視器中的查詢](../azure-monitor/log-query/get-started-queries.md)
* [使用 Azure 監視器中的視圖設計器創建自訂視圖](../azure-monitor/platform/view-designer.md)
