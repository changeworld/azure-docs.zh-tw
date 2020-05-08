---
title: 在 Azure HDInsight 中使用 Apache Ambari 優化叢集
description: 使用 Apache Ambari web UI 來設定和優化 Azure HDInsight 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/04/2020
ms.openlocfilehash: a819c3a57efdc0ae87cf969fd7471818c51895f6
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793154"
---
# <a name="optimize-clusters-with-apache-ambari-in-azure-hdinsight"></a>在 Azure HDInsight 中使用 Apache Ambari 優化叢集

HDInsight 提供大規模資料處理應用程式的 Apache Hadoop 叢集。 管理、監視和優化這些複雜的多節點叢集可能是一項挑戰。 Apache Ambari 是用來管理和監視 HDInsight Linux 叢集的 web 介面。

如需使用 Ambari Web UI 的簡介，請參閱[使用 Apache Ambari Web UI 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)

使用您的叢集認證登入在 `https://CLUSTERNAME.azurehdidnsight.net` 的 Ambari。 初始畫面會顯示概觀儀表板。

![顯示的 Apache Ambari 使用者儀表板](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

Ambari web UI 是用來管理主機、服務、警示、設定和 views。 Ambari 無法用來建立 HDInsight 叢集或升級服務。 也無法管理堆疊和版本、解除委任或重新委任主機，或將服務新增至叢集。

## <a name="manage-your-clusters-configuration"></a>管理您的叢集設定

組態設定可協助微調特定服務。 若要修改服務的設定，請從 [**服務**] 提要欄位（位於左側）選取服務。 然後流覽至服務詳細資料頁面中的 [**內容] 索引**標籤。

![Apache Ambari Services 提要欄位](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

## <a name="modify-namenode-java-heap-size"></a>修改 NameNode Java 堆積大小

NameNode JAVA 堆積大小取決於許多因素，例如叢集上的負載。 此外，檔案數目和區塊數目。 1 GB 的預設大小適用於大部分的叢集，而某些工作負載可能需要更多或更少的記憶體。

修改 NameNode Java 堆積大小：

1. 選取[服務] 資訊看板中的 [HDFS]****，並瀏覽至 [設定]**** 索引標籤。

    ![Apache Ambari HDFS 設定](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. 找出 [NameNode Java 堆積大小]**** 設定。 您也可以使用 [篩選]**** 文字方塊來輸入並尋找特定的設定。 選取設定名稱旁邊的**畫筆**圖示。

    ![Apache Ambari NameNode JAVA 堆積大小](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. 在文字方塊中輸入新值，然後按 **Enter** 儲存變更。

    ![Ambari 編輯 NameNode JAVA 堆積 size1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. NameNode JAVA 堆積大小已從 2 GB 變更為 1 GB。

    ![已編輯 NameNode JAVA 堆積 size2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. 按一下設定畫面頂端的綠色 [儲存]**** 按鈕，儲存您的變更。

    ![[Apache Ambari 儲存設定]](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="next-steps"></a>後續步驟

* [使用 Apache Ambari Web UI 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [將 Apache HBase 優化](./optimize-hbase-ambari.md)
* [優化 Apache Hive](./optimize-hive-ambari.md)
* [將 Apache Pig 優化](./optimize-pig-ambari.md)
