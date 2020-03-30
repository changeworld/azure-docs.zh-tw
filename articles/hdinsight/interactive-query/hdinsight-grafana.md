---
title: 在 Azure HDInsight 上使用 Grafana
description: 瞭解如何在 Azure HDInsight 中使用 Apache Hadoop 群集訪問格拉法納儀表板
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: cd515bfd1dc57e78a041ed96686e1ba692bf6d3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082858"
---
# <a name="access-grafana-in-azure-hdinsight"></a>在 Azure HDInsight 中存取 Grafana

[Grafana](https://grafana.com/) \(英文\) 是廣受使用的開放原始碼圖形和儀表板產生器。 Grafana 的功能非常豐富；不僅可讓使用者建立可自訂和可共用的儀表板，也提供樣板化/指令碼式儀表板、LDAP 整合、多種資料來源等功能。

目前，在 Azure HDInsight 中，Spark、HBase、Kafka 和互動式查詢群集類型支援 Grafana。 對於啟用了企業安全包的群集，不支援它。

如果沒有 Azure 訂閱，請先創建[一個免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。"

## <a name="create-an-apache-hadoop-cluster"></a>建立 Apache Hadoop 叢集

請參閱[使用 Azure 門戶創建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-portal.md)。 對於**群集類型**，選擇**Spark、****卡夫卡****、HBase**或**互動式查詢**。

## <a name="access-the-grafana-dashboard"></a>存取 Grafana 儀表板

1. 從 Web 瀏覽器導航到`https://CLUSTERNAME.azurehdinsight.net/grafana/`CLUSTERNAME 是群集名稱的位置。

1. 輸入 Hadoop 叢集使用者認證。

1. Grafana 儀表板隨即出現，且看起來如以下範例：

    ![HDInsight 格拉法納網路儀表板](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "HDInsight格拉法納儀表板")

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟刪除您所建立的叢集：

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. 在頂端的 [搜尋]**** 方塊中，輸入 **HDInsight**。

1. 在 [服務]**** 底下，選取 [HDInsight 叢集]****。

1. 在顯示的 HDInsight 群集清單中，選擇您創建的群集旁邊的 **...**

1. 選擇 **"刪除**"。 選取 [是]****。

## <a name="next-steps"></a>後續步驟

若要深入了解如何使用 HDInsight 分析資料，請參閱下列文章：

* [使用阿帕奇蜂巢與HDInsight。](../hadoop/hdinsight-use-hive.md)

* [使用地圖減少與HDInsight。](../hadoop/hdinsight-use-mapreduce.md)

* [開始使用視覺工作室Hadoop工具的HDInsight。](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
