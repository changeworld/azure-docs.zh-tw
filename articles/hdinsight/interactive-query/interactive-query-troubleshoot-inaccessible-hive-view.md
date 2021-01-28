---
title: Apache Hive 與 Apache Zookeeper 的連接-Azure HDInsight
description: Apache Hive View 因為 Azure HDInsight 中的 Apache Zookeeper 問題而無法存取
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 42fb9f5389cbc31e772dc9cf36b6a975c5e18d3c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939308"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>案例： Apache Hive 無法在 Azure HDInsight 中建立與 Apache Zookeeper 的連線

本文說明使用 Azure HDInsight 叢集中的 Interactive Query 元件時，所發生之問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

Hive 視圖無法存取，且中的記錄 `/var/log/hive` 顯示類似下列的錯誤：

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (zk0-cluster.cloud.wbmi.com:2181,zk1-cluster.cloud.wbmi.com:2181,zk2-cluster.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>原因

Hive 可能無法建立與 Zookeeper 的連線，這會讓 Hive 視圖無法啟動。

## <a name="resolution"></a>解決方案

1. 檢查 Zookeeper 服務是否狀況良好。

1. 檢查 Zookeeper 服務是否有 Hive Server2 的 ZNode 專案。 值將會遺失或不正確。

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server zk1-wbwdhs
    [zk: zk0-cluster(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. 若要重新建立連線，請重新開機 Zookeeper 節點，然後重新開機 HiveServer2。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]