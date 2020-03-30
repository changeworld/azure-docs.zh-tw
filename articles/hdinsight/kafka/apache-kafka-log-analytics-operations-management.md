---
title: 阿帕奇卡夫卡的 Azure 監視器日誌 - Azure HDInsight
description: 瞭解如何使用 Azure 監視器日誌分析 Azure HDInsight 上的 Apache Kafka 群集的日誌。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 3f8ff3cbc24f6e3a7e0eccf1b18e01941c9584b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471175"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>在 HDInsight 上分析 Apache Kafka 的記錄

瞭解如何使用 Azure 監視器日誌來分析 Apache Kafka 在 HDInsight 上生成的日誌。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>日誌位置

群集中的 Apache Kafka 日誌位於`/var/log/kafka`。 無論是否使用託管磁片，Kafka 日誌不會跨群集生命週期保存或持久化。 下表顯示了可用的日誌。

|Log |描述 |
|---|---|
|卡夫卡·奧特|卡夫卡過程的粗壯和穩高。 您將在此檔中找到 Kafka 啟動和關閉日誌。|
|伺服器.日誌|主卡夫卡伺服器日誌。 所有卡夫卡經紀人日誌都在這裡結束。|
|控制器.日誌|如果代理充當控制器，則控制器將記錄。|
|狀態更改.log|所有狀態變更事件到代理都記錄在此檔中。|
|卡夫卡-gc.log|卡夫卡垃圾收集統計。|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>為阿帕奇卡夫卡啟用 Azure 監視器日誌

為所有 HDInsight 群集啟用 Azure 監視器日誌的步驟相同。 若要了解如何建立和設定所需的服務，請使用下列連結：

1. 建立 Log Analytics 工作區。 有關詳細資訊，請參閱 Azure[監視器文檔中的日誌](../../azure-monitor/platform/data-platform-logs.md)。

2. 在 HDInsight 叢集上建立 Kafka。 如需詳細資訊，請參閱[開始使用 HDInsight 上的 Apache Kafka](apache-kafka-get-started.md) 文件。

3. 將 Kafka 群集配置為使用 Azure 監視器日誌。 有關詳細資訊，請參閱[使用 Azure 監視器日誌來監視 HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md)文檔。

> [!IMPORTANT]  
> 可能需要大約 20 分鐘才能為 Azure 監視器日誌提供資料。

## <a name="query-logs"></a>查詢記錄

1. 從[Azure 門戶](https://portal.azure.com)中選擇日誌分析工作區。

2. 從左側功能表中，在 **"常規"** 下，選擇 **"日誌**"。 您可以在這裡搜尋從 Kafka 收集而來的資料。 在查詢視窗中輸入查詢，然後選擇 **"運行**"。 以下是一些範例搜尋：

* 磁碟使用量：

    ```kusto
    Perf
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* CPU 使用率：

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* 每秒傳入消息：（替換為`your_kafka_cluster_name`群集名稱。

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* 每秒傳入位元組：（替換為`wn0-kafka`輔助節點主機名稱。

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* 每秒傳出位元組：（替換為`your_kafka_cluster_name`群集名稱。

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    您也可以輸入 `*` 來搜尋所有記錄的類型。 目前我們提供以下記錄的查詢：

    | 記錄類型 | 描述 |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Kafka broker controller.log |
    | metrics\_kafka\_CL | Kafka JMX metrics |

    ![阿帕奇卡夫卡日誌分析cpu使用](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>後續步驟

有關 Azure 監視器的詳細資訊，請參閱[Azure 監視器概述](../../log-analytics/log-analytics-get-started.md)和[查詢 Azure 監視器日誌以監視 HDInsight 群集](../hdinsight-hadoop-oms-log-analytics-use-queries.md)。

如需使用 Apache Kafka 的詳細資訊，請參閱下列文件：

* [在 HDInsight 叢集之間製作 Apache Kafka 的鏡像](apache-kafka-mirroring.md)
* [擴大HDInsight上的阿帕奇卡夫卡的規模](apache-kafka-scalability.md)
* [搭配 Apache Kafka 使用 Apache Spark 串流 (DStream) ](../hdinsight-apache-spark-with-kafka.md)
* [將 Apache Spark 結構化串流用於 Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
