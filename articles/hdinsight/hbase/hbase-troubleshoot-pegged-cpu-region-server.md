---
title: 在 Apache HBase 叢集中限定 CPU-Azure HDInsight
description: 針對 Azure HDInsight 中 Apache HBase 叢集中的區域伺服器上限定的 CPU 進行疑難排解
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 16c994029e91d743f1c2a7e2eab51eb86fc378e8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75887303"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>案例：在 Azure HDInsight 中的 Apache HBase 叢集中的區域伺服器上限定 CPU

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

Apache HBase 區域伺服器進程會開始佔用接近200% 的 CPU，導致 HBase Master 進程上引發警示，而叢集無法以完整容量運作。

## <a name="cause"></a>原因

如果您執行 HBase cluster v4.0，可能是因為將 jdk 升級至版本 1.7.0 _151 所造成的潛在錯誤。 我們看到的徵兆是「區域伺服器進程」開始佔用接近200% 的 CPU （以確認這會執行 `top` 命令; 如果有接近 200% cpu 的進程，請取得其 pid，並藉由執行來確認它是區域伺服器進程 `ps -aux | grep` ）。

## <a name="resolution"></a>解決方案

1. 在叢集中的所有節點上安裝 jdk 1.8，如下所示：

    * 執行腳本動作 `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh` 。 請務必選取要在所有節點上執行的選項。

    * 或者，您可以登入每個個別節點，然後執行命令 `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk` 。

1. 移至 Ambari UI- `https://<clusterdnsname>.azurehdinsight.net` 。

1. 流覽至**HBase->的 [>advanced]->advanced** ] `hbase-env configs` ，並將變數變更 `JAVA_HOME` 為 `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64` 。 儲存設定變更。

1. [選用但建議][清除叢集上的所有資料表](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)。

1. 再次從 Ambari UI，重新開機所有需要重新開機的 HBase 服務。

1. 視叢集上的資料而定，可能需要幾分鐘到一小時的時間，叢集才會達到穩定狀態。 確認叢集達到穩定狀態的方式，是從 Ambari （重新整理）或前端節點執行 HBase shell，然後執行 status 命令檢查 HMaster UI （所有區域伺服器都應該是作用中）。

若要確認您的升級是否成功，請使用適當的 java 版本來檢查相關的 HBase 進程是否已啟動，例如，區域伺服器檢查的實例：

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
