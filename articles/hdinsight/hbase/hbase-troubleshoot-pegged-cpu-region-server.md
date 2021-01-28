---
title: 在 Apache HBase 叢集中限定的 CPU-Azure HDInsight
description: 針對 Azure HDInsight 中 Apache HBase 叢集中區域伺服器上的限定 CPU 進行疑難排解
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 7d0cf139f06bb296b486d2932d95b53fc1167a5d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937023"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>案例： Azure HDInsight 中的 Apache HBase 叢集中的區域伺服器上已限定 CPU

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

Apache HBase 區域伺服器進程的開始時間接近 200% CPU，導致 HBase Master 進程上引發警示，而叢集無法在完整容量運作。

## <a name="cause"></a>原因

如果您正在執行 HBase cluster 3.4 版，可能是因為將 jdk 升級為版本 1.7.0 _151 所造成的潛在錯誤。 我們看到的徵兆是，區域伺服器進程開始接近 200% CPU (若要確認這項作業，請執行 `top` 命令; 如果有接近 200% cpu 的進程，請取得其 pid，並藉由執行 ) 來確認其為區域伺服器進程 `ps -aux | grep` 。

## <a name="resolution"></a>解決方案

1. 在叢集中的所有節點上安裝 jdk 1.8，如下所示：

    * 執行腳本動作 `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh` 。 請務必選取要在所有節點上執行的選項。

    * 或者，您可以登入每個個別節點，然後執行命令 `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk` 。

1. 移至 Ambari UI- `https://<clusterdnsname>.azurehdinsight.net` 。

1. 流覽至 **HBase->的 [>advanced->advanced** ] `hbase-env configs` ，並將變數變更 `JAVA_HOME` 為 `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64` 。 儲存配置變更。

1. [選用，但建議使用] [清除叢集中的所有資料表](/archive/blogs/azuredatalake/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables)。

1. 再次從 Ambari UI 重新開機所有需要重新開機的 HBase 服務。

1. 視叢集上的資料而定，叢集可能需要幾分鐘的時間才能達到穩定狀態。 確認叢集達到穩定狀態的方式，就是檢查 HMaster UI (所有區域伺服器都應該是作用中) 從 Ambari (重新整理) 或從前端節點執行 HBase shell，然後執行狀態命令。

若要確認升級是否成功，請使用適當的 java 版本來檢查相關的 HBase 程式是否已啟動，例如區域伺服器檢查是否為：

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]