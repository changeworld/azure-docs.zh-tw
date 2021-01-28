---
title: NoClassDefFoundError-在 Azure HDInsight 中使用 Apache Kafka 資料 Apache Spark
description: 從 Apache Kafka 叢集讀取資料的 Apache Spark 串流作業失敗，且 Azure HDInsight 中有 NoClassDefFoundError
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 4d00cbcb0151da39feb0cb015660291af544d7f4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946377"
---
# <a name="apache-spark-streaming-job-that-reads-apache-kafka-data-fails-with-noclassdeffounderror-in-hdinsight"></a>使用 HDInsight 中的 NoClassDefFoundError 讀取 Apache Kafka 資料的 Apache Spark 串流作業失敗

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時，疑難排解步驟和可能的解決方案。

## <a name="issue"></a>問題

Apache Spark 叢集會執行從 Apache Kafka 叢集讀取資料的 Spark 串流作業。 如果開啟 Kafka 資料流程壓縮，Spark 串流作業就會失敗。 在此情況下，Spark 串流 Yarn 應用程式 application_1525986016285_0193 失敗，因為發生下列錯誤：

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>原因

這個錯誤可能是因為指定的 jar 檔案版本與您正在執行的 `spark-streaming-kafka` Kafka 叢集版本不同。

例如，如果您執行的是 Kafka 叢集版本0.10.1，則下列命令會產生錯誤：

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>解決方案

將 Spark 提交命令與選項搭配使用 `–packages` ，並確定 Spark 資料流程 kafka jar 檔案的版本與您正在執行之 kafka 叢集的版本相同。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]