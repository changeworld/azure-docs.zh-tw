---
title: BindException-位址已在 Azure HDInsight 中使用
description: BindException-位址已在 Azure HDInsight 中使用
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 80f984643d6d8be88b381881c6fc1cb1cb5f1815
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75887337"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>案例： BindException-位址已在 Azure HDInsight 中使用

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

Apache HBase 區域伺服器上的重新開機作業無法完成。 從區域`region-server.log`伺服器`/var/log/hbase`啟動失敗的背景工作節點上的目錄中，您可能會看到類似如下的錯誤訊息：

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>原因

在繁重的工作負載活動期間重新開機 Apache HBase 區域伺服器。 以下是當使用者從 Apache Ambari UI 起始 HBase 區域伺服器上的重新開機作業時，會在幕後發生的情況：

1. Ambari 代理程式會傳送停止要求給區域伺服器。

1. Ambari 代理程式會等待30秒，讓區域伺服器正常關閉

1. 如果您的應用程式繼續與區域伺服器連線，則伺服器不會立即關機。 超過 30 秒逾時才會關機。

1. 30 秒後，Ambari 代理程式會傳送強制終止 (`kill -9`) 命令給區域伺服器。

1. 由於這種突然關機，雖然區域伺服器進程已終止，但與進程相關聯的埠可能無法釋出，最後會導致`AddressBindException`。

## <a name="resolution"></a>解決方法

請先減少 HBase 區域伺服器的負載，再起始重新開機。 此外，先排清所有資料表也是不錯的做法。 如需如何排清資料表的參考，請參閱 [HDInsight HBase：如何藉由排清資料表來改善 Apache HBase 叢集重新啟動時間](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)。

或者，請嘗試使用下列命令，手動重新開機背景工作節點上的區域伺服器：

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，以改善客戶體驗。 將 Azure 社區連接到正確的資源：解答、支援和專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
