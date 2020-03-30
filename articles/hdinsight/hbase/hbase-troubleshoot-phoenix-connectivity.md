---
title: Azure HDInsight 中的阿帕奇鳳凰連接問題
description: Azure HDInsight 中的 Apache HBase 和 Apache 鳳凰之間的連接問題
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: b886f51bcb2bb7308c49c76563dcb70148bbc583
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887286"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>方案：Azure HDInsight 中的阿帕奇鳳凰連接問題

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

無法與阿帕奇鳳凰連接到阿帕奇 HBase。 原因可能有所不同。

## <a name="cause-incorrect-ip"></a>原因：IP 不正確

活動 Zookeeper 節點的 IP 不正確。

### <a name="resolution"></a>解決方案

活動動物園管理員節點的IP可以通過以下連結識別從Ambari UI到**HBase** > **快速連結** > **ZK（活動）** > **動物園管理員資訊**。 根據需要更正 IP。

---

## <a name="cause-systemcatalog-table-offline"></a>原因：系統。目錄表離線

運行命令（如`!tables`）時，您會收到類似于的錯誤訊息：

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

運行命令（如`count 'SYSTEM.CATALOG'`）時，您會收到類似于的錯誤訊息：

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>解決方案

從 Apache Ambari UI 中，完成以下步驟以重新開機所有 ZooKeeper 節點上的 HMaster 服務：

1. 從 HBase 的**摘要**部分轉到**HBase** > **活動 HBase 主控**形狀 。

1. 從 **"元件"** 部分重新開機 HBase 主服務。

1. 針對所有其餘的 **Standby HBase Master** 服務，重複這些步驟。

HBase 主服務最多可能需要五分鐘才能穩定並完成恢復。 表`SYSTEM.CATALOG`恢復正常後，與 Apache 鳳凰的連接問題應自動解決。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源：答案、支援和專家。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
