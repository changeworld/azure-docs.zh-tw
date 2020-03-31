---
title: Apache 動物園守護者伺服器無法在 Azure HDInsight 中形成仲裁
description: Apache 動物園守護者伺服器無法在 Azure HDInsight 中形成仲裁
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 4e46efaf17ae9bad5df6f1f61f401d3e6de58a85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250227"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache 動物園守護者伺服器無法在 Azure HDInsight 中形成仲裁

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

Apache ZooKeeper 伺服器不正常，症狀可能包括：資源管理器/名稱節點都處於待機模式，簡單的 HDFS 操作不起作用，`zkFailoverController`已停止且無法啟動，Yarn/Spark/Livy 作業由於 Zookeeper 錯誤而失敗。 LLAP 守護進程也可能無法在安全火花或互動式蜂巢群集上啟動。 您可能會看到類似于以下錯誤的錯誤訊息：

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

在動物園管理員伺服器日誌上任何動物園管理員主機在 /var/log/zookeeper/zookeeper-zookeeper-server-out\*中，您可能還會看到以下錯誤：

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>原因

當快照檔量較大或快照檔損壞時，ZooKeeper 伺服器將無法形成仲裁，從而導致 ZooKeeper 相關服務不正常。 ZooKeeper 伺服器不會從其資料目錄中刪除舊的快照檔，而是使用者為維護 ZooKeeper 的運行狀況而執行的定期任務。 有關詳細資訊，請參閱[動物園管理員的優勢和限制](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations)。

## <a name="resolution"></a>解決方案

檢查 ZooKeeper`/hadoop/zookeeper/version-2`資料`/hadoop/hdinsight-zookeeper/version-2`目錄，瞭解快照檔案大小是否大。 如果存在大型快照，請執行以下步驟：

1. 在 和`/hadoop/hdinsight-zookeeper/version-2`中`/hadoop/zookeeper/version-2`備份快照。

1. 清理 和`/hadoop/hdinsight-zookeeper/version-2`中的`/hadoop/zookeeper/version-2`快照。

1. 從阿帕奇 Ambari UI 重新開機所有動物園管理員伺服器。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

- 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

- 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源：答案、支援和專家。

- 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
