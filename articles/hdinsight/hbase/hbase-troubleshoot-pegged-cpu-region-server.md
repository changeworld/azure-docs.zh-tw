---
title: 阿帕奇 HBase 群集中的固定 CPU - Azure HDInsight
description: 在 Azure HDInsight 中的 Apache HBase 群集中的區域伺服器上對固定 CPU 進行故障排除
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 16c994029e91d743f1c2a7e2eab51eb86fc378e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887303"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>方案：在 Azure HDInsight 中 Apache HBase 群集中的區域伺服器上標記 CPU

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

Apache HBase 區域伺服器進程開始佔用接近 200% 的 CPU，導致 HBase 主進程和群集上的警報觸發，無法滿負荷運行。

## <a name="cause"></a>原因

如果您正在運行 HBase 群集 v3.4，則由於 jdk 升級到版本 1.7.0_151 而導致的潛在錯誤可能已遇到。 我們看到的症狀是區域伺服器進程開始佔用接近 200% CPU（以驗證此運行`top`命令;如果有一個進程佔用接近 200% CPU 獲取其 pid，並通過運行`ps -aux | grep`確認它是區域伺服器進程）。

## <a name="resolution"></a>解決方案

1. 在群集的所有節點上安裝 jdk 1.8，如下所示：

    * 運行腳本操作`https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`。 請務必選擇在所有節點上運行的選項。

    * 或者，您可以登錄到每個單獨的節點並運行命令`sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk`。

1. 轉到安巴里 UI `https://<clusterdnsname>.azurehdinsight.net`- . .

1. 導航到**HBase->配置->高級>高級，**`hbase-env configs`並將變數`JAVA_HOME`更改為`export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`。 保存配置更改。

1. [可選但建議][刷新群集上的所有表](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)。

1. 再次從 Ambari UI 重新開機所有需要重新開機的 HBase 服務。

1. 根據群集上的資料，群集可能需要幾分鐘到一個小時才能達到穩定狀態。 確認群集達到穩定狀態的方式是檢查 Ambari（刷新）或頭節點運行 HBase shell 的 HMaster UI（所有區域伺服器都應處於活動狀態），然後運行狀態命令。

要驗證升級是否成功，請檢查是否使用適當的 java 版本啟動相關的 HBase 進程 - 例如，區域伺服器檢查如下：

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
