---
title: 在 Azure HDInsight 上使用 Grafana
description: 瞭解如何在 Azure HDInsight 中使用 Apache Hadoop 叢集存取 Grafana 儀表板
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/27/2019
ms.openlocfilehash: cd2a09b6c3196302dcb74fd363481706021a4d4d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940881"
---
# <a name="access-grafana-in-azure-hdinsight"></a>在 Azure HDInsight 中存取 Grafana

[Grafana](https://grafana.com/) \(英文\) 是廣受使用的開放原始碼圖形和儀表板產生器。 Grafana 的功能非常豐富；不僅可讓使用者建立可自訂和可共用的儀表板，也提供樣板化/指令碼式儀表板、LDAP 整合、多種資料來源等功能。

目前，在 Azure HDInsight 中，Spark、HBase、Kafka 和 Interactive Query 叢集類型支援 Grafana。 啟用企業安全性套件的叢集不支援此功能。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="create-an-apache-hadoop-cluster"></a>建立 Apache Hadoop 叢集

請參閱[使用 Azure 入口網站建立 Apache Hadoop 叢集](../hdinsight-hadoop-create-linux-clusters-portal.md)。 針對叢集 **類型**，選取 **Spark**、 **Kafka**、 **HBase** 或 **Interactive Query**。

## <a name="access-the-grafana-dashboard"></a>存取 Grafana 儀表板

1. 從網頁瀏覽器中，流覽至 `https://CLUSTERNAME.azurehdinsight.net/grafana/` CLUSTERNAME 是您的叢集名稱。

1. 輸入 Hadoop 叢集使用者認證。

1. Grafana 儀表板隨即出現，且看起來如以下範例：

    ![HDInsight Grafana web 儀表板](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "HDInsight Grafana 儀表板")

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟刪除您所建立的叢集：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 在頂端的 [搜尋]  方塊中，輸入 **HDInsight**。

1. 在 [服務]  底下，選取 [HDInsight 叢集]  。

1. 在出現的 HDInsight 叢集清單中，選取您建立之叢集旁的 **...** 。

1. 選取 [刪除]  。 選取 [是]  。

## <a name="next-steps"></a>後續步驟

若要深入了解如何使用 HDInsight 分析資料，請參閱下列文章：

* [使用 Apache Hive 搭配 HDInsight](../hadoop/hdinsight-use-hive.md)。

* 搭配[HDInsight 使用 MapReduce](../hadoop/hdinsight-use-mapreduce.md)。

* [開始使用 Visual Studio Hadoop tools For HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)。
