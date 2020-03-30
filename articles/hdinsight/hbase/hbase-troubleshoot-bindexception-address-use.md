---
title: 綁定異常 - Azure HDInsight 中已在使用的位址
description: 綁定異常 - Azure HDInsight 中已在使用的位址
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 80f984643d6d8be88b381881c6fc1cb1cb5f1815
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887337"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>方案：綁定異常 - Azure HDInsight 中已在使用的位址

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

Apache HBase 區域伺服器上的重新開機操作無法完成。 在區域`region-server.log`伺服器`/var/log/hbase`啟動失敗的工作節點上的 in 目錄中，您可能會看到類似于以下的錯誤訊息：

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>原因

在繁重的工作負載活動期間重新開機 Apache HBase 區域伺服器。 以下是當使用者從 Apache Ambari UI 啟動 HBase 區域伺服器上的重新開機操作時的幕後情況：

1. Ambari 代理程式會傳送停止要求給區域伺服器。

1. Ambari 代理等待 30 秒，區域伺服器正常關閉

1. 如果您的應用程式繼續與區域伺服器連線，則伺服器不會立即關機。 超過 30 秒逾時才會關機。

1. 30 秒後，Ambari 代理程式會傳送強制終止 (`kill -9`) 命令給區域伺服器。

1. 由於此突然關閉，儘管區域伺服器進程已終止，但可能無法釋放與進程關聯的埠，從而導致`AddressBindException`。

## <a name="resolution"></a>解決方案

在啟動重新開機之前，減少 HBase 區域伺服器上的負載。 此外，先排清所有資料表也是不錯的做法。 如需如何排清資料表的參考，請參閱 [HDInsight HBase：如何藉由排清資料表來改善 Apache HBase 叢集重新啟動時間](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)。

或者，請嘗試使用以下命令手動重新開機輔助節點上的區域伺服器：

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源：答案、支援和專家。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
