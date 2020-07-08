---
title: 在 Azure HDInsight 上使用 Grafana
description: 瞭解如何在 Azure HDInsight 中使用 Apache Hadoop 叢集來存取 Grafana 儀表板
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/27/2019
ms.openlocfilehash: 7750544367044cab2a0243577c200025bb9ff693
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86083027"
---
# <a name="access-grafana-in-azure-hdinsight"></a>在 Azure HDInsight 中存取 Grafana

[Grafana](https://grafana.com/) \(英文\) 是廣受使用的開放原始碼圖形和儀表板產生器。 Grafana 的功能非常豐富；不僅可讓使用者建立可自訂和可共用的儀表板，也提供樣板化/指令碼式儀表板、LDAP 整合、多種資料來源等功能。

目前，在 Azure HDInsight 中，Spark、HBase、Kafka 和互動式查詢叢集類型都支援 Grafana。 對於已啟用企業安全性套件的叢集，則不支援此功能。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="create-an-apache-hadoop-cluster"></a>建立 Apache Hadoop 叢集

請參閱[使用 Azure 入口網站建立 Apache Hadoop](../hdinsight-hadoop-create-linux-clusters-portal.md)叢集。 針對叢集**類型**，選取 [ **Spark**]、[ **Kafka**]、[ **HBase**] 或 [**互動式查詢**]。

## <a name="access-the-grafana-dashboard"></a>存取 Grafana 儀表板

1. 從網頁瀏覽器流覽至， `https://CLUSTERNAME.azurehdinsight.net/grafana/` 其中 CLUSTERNAME 是您的叢集名稱。

1. 輸入 Hadoop 叢集使用者認證。

1. Grafana 儀表板隨即出現，且看起來如以下範例：

    ![HDInsight Grafana web 儀表板](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "HDInsight Grafana 儀表板")

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟刪除您所建立的叢集：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 在頂端的 [搜尋] 方塊中，輸入 **HDInsight**。

1. 在 [服務] 底下，選取 [HDInsight 叢集]。

1. 在出現的 HDInsight 叢集清單中，選取您所建立叢集旁的 [ **...** ]。

1. 選取 [刪除]  。 選取 [是]  。

## <a name="next-steps"></a>後續步驟

若要深入了解如何使用 HDInsight 分析資料，請參閱下列文章：

* 搭配[HDInsight 使用 Apache Hive](../hadoop/hdinsight-use-hive.md)。

* 搭配[HDInsight 使用 MapReduce](../hadoop/hdinsight-use-mapreduce.md)。

* [開始使用 Visual Studio Hadoop tools For HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)。
