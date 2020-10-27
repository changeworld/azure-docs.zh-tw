---
title: BindException-位址已在 Azure HDInsight 中使用
description: BindException-位址已在 Azure HDInsight 中使用
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 60f95292d517277f607c968629c55641e0eecce1
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540256"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>案例： BindException 位址已在 Azure HDInsight 中使用

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

無法完成 Apache HBase 區域伺服器上的重新開機作業。 從 `region-server.log` `/var/log/hbase` 區域伺服器啟動失敗的背景工作節點上的目錄中，您可能會看到類似下面的錯誤訊息：

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>原因

在繁重的工作負載活動期間重新開機 Apache HBase 區域伺服器。 以下是當使用者從 Apache Ambari UI 起始 HBase 區域伺服器上的重新開機作業時，會發生什麼事：

1. Ambari 代理程式會傳送停止要求給區域伺服器。

1. Ambari 代理程式會等候30秒，讓區域伺服器正常關機

1. 如果您的應用程式繼續與區域伺服器連線，則伺服器不會立即關機。 超過 30 秒逾時才會關機。

1. 30 秒後，Ambari 代理程式會傳送強制終止 (`kill -9`) 命令給區域伺服器。

1. 由於這種突然關機的緣故，雖然會終止區域伺服器進程，但是與進程相關聯的埠可能不會被釋放，最後會導致 `AddressBindException` 。

## <a name="resolution"></a>解決方案

在起始重新開機之前，請先減少 HBase 區域伺服器的負載。 此外，先排清所有資料表也是不錯的做法。 如需如何排清資料表的參考，請參閱 [HDInsight HBase：如何藉由排清資料表來改善 Apache HBase 叢集重新啟動時間](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)。

或者，請嘗試使用下列命令，手動重新開機背景工作節點上的區域伺服器：

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。