---
title: 阿帕奇蜂巢連接到阿帕奇動物園管理員 - Azure HDInsight
description: 由於 Azure HDInsight 中的 Apache 動物園管理員問題，Apache Hive 視圖無法訪問
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 6e000d31ffbacd7cb716bd59dde4f935638b0810
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895201"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>場景：Apache Hive 未能在 Azure HDInsight 中建立與 Apache 動物園管理員的連接

本文介紹了在 Azure HDInsight 群集中使用互動式查詢元件時，故障排除步驟和可能解決的問題。

## <a name="issue"></a>問題

Hive 視圖不可訪問，中的`/var/log/hive`日誌顯示類似于以下內容的錯誤：

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (zk0-cluster.cloud.wbmi.com:2181,zk1-cluster.cloud.wbmi.com:2181,zk2-cluster.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>原因

Hive 可能無法與動物園管理員建立連接，從而阻止蜂巢視圖的啟動。

## <a name="resolution"></a>解決方案

1. 檢查動物園管理員服務是否正常。

1. 檢查 Zookeeper 服務是否具有 Hive Server2 的 ZNode 條目。 該值將丟失或不正確。

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server zk1-wbwdhs
    [zk: zk0-cluster(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. 要重新建立連接，請重新開機 Zookeeper 節點，然後重新開機 HiveServer2。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
