---
title: Apache Kafka 的 Azure 監視器記錄-Azure HDInsight
description: 瞭解如何在 Azure HDInsight 上使用 Azure 監視器記錄來分析 Apache Kafka 叢集的記錄。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: d577e96c3ae95103a412b96eba3e1293142f1acd
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932770"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>在 HDInsight 上分析 Apache Kafka 的記錄

瞭解如何使用 Azure 監視器記錄來分析 Apache Kafka on HDInsight 所產生的記錄。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>記錄位置

叢集中的 Apache Kafka 記錄檔位於 `/var/log/kafka` 。 不論是否使用受控磁片，Kafka 記錄都不會儲存或保存在叢集生命週期內。 下表顯示可用的記錄。

|記錄檔 |描述 |
|---|---|
|kafka|Kafka 進程的 stdout 和 stderr。 您會在此檔案中找到 Kafka 的啟動和關閉記錄。|
|伺服器 .log|主要 Kafka 伺服器記錄檔。 所有 Kafka broker 記錄都會在這裡結束。|
|controller .log|如果 broker 作為控制器，則為控制器記錄檔。|
|statechange .log|所有對訊息代理程式的狀態變更事件都會記錄在此檔案中。|
|kafka-gc .log|Kafka 垃圾收集統計資料。|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>啟用 Apache Kafka Azure 監視器記錄

針對所有 HDInsight 叢集啟用 HDInsight Azure 監視器記錄的步驟都相同。 若要了解如何建立和設定所需的服務，請使用下列連結：

1. 建立 Log Analytics 工作區。 如需詳細資訊，請參閱 [Azure 監視器檔中的記錄](../../azure-monitor/platform/data-platform-logs.md) 檔。

2. 在 HDInsight 叢集上建立 Kafka。 如需詳細資訊，請參閱[開始使用 HDInsight 上的 Apache Kafka](apache-kafka-get-started.md) 文件。

3. 設定 Kafka 叢集以使用 Azure 監視器記錄。 如需詳細資訊，請參閱 [使用 Azure 監視器記錄來監視 HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) 檔。

> [!IMPORTANT]  
> 可能需要大約20分鐘的時間，資料才可用於 Azure 監視器記錄。

## <a name="query-logs"></a>查詢記錄

1. 從 [Azure 入口網站](https://portal.azure.com)中，選取您的 Log Analytics 工作區。

2. 從左側功能表的 **[一般**] 底下，選取 [ **記錄**]。 您可以在這裡搜尋從 Kafka 收集而來的資料。 在查詢視窗中輸入查詢，然後選取 [ **執行**]。 以下是一些範例搜尋：

* 磁片使用量：

    ```kusto
    Perf
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* CPU 使用量：

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* 每秒傳入訊息數： (取代 `your_kafka_cluster_name` 為您的叢集名稱。 ) 

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* 每秒傳入位元組： (取代 `wn0-kafka` 為背景工作節點主機名稱。 ) 

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* 每秒傳出位元組數： (取代 `your_kafka_cluster_name` 為您的叢集名稱。 ) 

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

    ![Apache kafka log analytics cpu 使用量](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>後續步驟

如需 Azure 監視器的詳細資訊，請參閱 [Azure 監視器總覽](../../azure-monitor/overview.md)和 [查詢 Azure 監視器記錄來監視 HDInsight](../hdinsight-hadoop-oms-log-analytics-use-queries.md)叢集。

如需使用 Apache Kafka 的詳細資訊，請參閱下列文件：

* [在 HDInsight 叢集之間製作 Apache Kafka 的鏡像](apache-kafka-mirroring.md)
* [增加 HDInsight 上的 Apache Kafka 規模](apache-kafka-scalability.md)
* [搭配 Apache Kafka 使用 Apache Spark 串流 (DStream) ](../hdinsight-apache-spark-with-kafka.md)
* [將 Apache Spark 結構化串流用於 Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)