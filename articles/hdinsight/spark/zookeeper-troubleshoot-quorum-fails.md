---
title: Apache ZooKeeper server 無法在 Azure HDInsight 中形成仲裁
description: Apache ZooKeeper server 無法在 Azure HDInsight 中形成仲裁
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 05/20/2020
ms.openlocfilehash: dc93121d7565b95b9bd604160028659f3a741b0c
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860489"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper server 無法在 Azure HDInsight 中形成仲裁

本文說明在 Azure HDInsight 叢集中，Zookeeper 相關問題的疑難排解步驟和可能的解決辦法。

## <a name="symptoms"></a>徵狀

* 兩個資源管理員都進入待命模式
* NameNode 都處於待命模式
* Spark、Hive、Yarn 作業或 Hive 查詢因 Zookeeper 連線失敗而失敗
* 無法在安全 Spark 或安全互動式 Hive 叢集上啟動 LLAP 精靈

## <a name="sample-log"></a>範例記錄

您可能看到類似下列的錯誤訊息：

```output
2020-05-05 03:17:18.3916720|Lost contact with Zookeeper. Transitioning to standby in 10000 ms if connection is not reestablished.
Message
2020-05-05 03:17:07.7924490|Received RMFatalEvent of type STATE_STORE_FENCED, caused by org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth
...
2020-05-05 03:17:08.3890350|State store operation failed 
2020-05-05 03:17:08.3890350|Transitioning to standby state
```

## <a name="related-issues"></a>相關問題

* Yarn、NameNode、Livy 等高可用性服務關閉的可能原因很多。
* 請確認與 Zookeeper 連線相關的記錄
* 請確定問題重複發生 (請勿將這些解決辦法用於一次性案例)
* 作業可能會因為 Zookeeper 連線問題而暫時失敗

## <a name="common-causes-for-zookeeper-failure"></a>Zookeeper 失敗的常見原因

* Zookeeper 伺服器的 CPU 高使用率
  * 在 Ambari UI 中，如果您看到 Zookeeper 伺服器上的 CPU 使用率接近 100%，則在該期間內開啟的 Zookeeper 工作階段可能會過期和逾時
* Zookeeper 用戶端頻繁報告逾時
  * 在 Resource Manager、Namenode 及其他服務的記錄中，您會看到頻繁的用戶端連線逾時
  * 這可能會導致仲裁遺失、頻繁容錯移轉和其他問題

## <a name="check-for-zookeeper-status"></a>檢查 Zookeeper 狀態

* 從 /etc/hosts 檔案或從 Ambari UI 中尋找 Zookeeper 伺服器
* 執行下列命令
  * `echo stat | nc <ZOOKEEPER_HOST_IP> 2181` (或 2182)  
  * 連接埠 2181 是 Apache Zookeeper 執行個體
  * HDInsight Zookeeper 會使用連接埠 2182 (為非原生高可用性的服務提供高可用性)
  * 如果命令未顯示任何輸出，則表示 Zookeeper 伺服器未執行
  * 如果伺服器正在執行，結果會包含用戶端連線的統計資料和其他統計資料

```output
Zookeeper version: 3.4.6-8--1, built on 12/05/2019 12:55 GMT
Clients:
 /10.2.0.57:50988[1](queued=0,recved=715,sent=715)
 /10.2.0.57:46632[1](queued=0,recved=138340,sent=138347)
 /10.2.0.34:14688[1](queued=0,recved=264653,sent=353420)
 /10.2.0.52:49680[1](queued=0,recved=134812,sent=134814)
 /10.2.0.57:50614[1](queued=0,recved=19812,sent=19812)
 /10.2.0.56:35034[1](queued=0,recved=2586,sent=2586)
 /10.2.0.52:63982[1](queued=0,recved=72215,sent=72217)
 /10.2.0.57:53024[1](queued=0,recved=19805,sent=19805)
 /10.2.0.57:45126[1](queued=0,recved=19621,sent=19621)
 /10.2.0.56:41270[1](queued=0,recved=1348743,sent=1348788)
 /10.2.0.53:59097[1](queued=0,recved=72215,sent=72217)
 /10.2.0.56:41088[1](queued=0,recved=788,sent=802)
 /10.2.0.34:10246[1](queued=0,recved=19575,sent=19575)
 /10.2.0.56:40944[1](queued=0,recved=717,sent=717)
 /10.2.0.57:45466[1](queued=0,recved=19861,sent=19861)
 /10.2.0.57:59634[0](queued=0,recved=1,sent=0)
 /10.2.0.34:14704[1](queued=0,recved=264622,sent=353355)
 /10.2.0.57:42244[1](queued=0,recved=49245,sent=49248)

Latency min/avg/max: 0/3/14865
Received: 238606078
Sent: 239139381
Connections: 18
Outstanding: 0
Zxid: 0x1004f99be
Mode: follower
Node count: 133212
```

## <a name="cpu-load-peaks-up-every-hour"></a>CPU 負載尖峰每小時升高

* 登入 Zookeeper 伺服器，查看 /etc/crontab
* 如果此時有任何每小時執行的作業，請讓不同 Zookeeper 伺服器上的開始時間隨機分散。
  
## <a name="purging-old-snapshots"></a>清除舊快照集

* 設定 Zookeeper 為自動清除舊快照集
* 根據預設，會保留最後 30 個快照集
* 保留的快照集數目，是由設定金鑰 `autopurge.snapRetainCount` 所控制。 這個屬性可以在下列檔案中找到：
  * `/etc/zookeeper/conf/zoo.cfg`，用於 Hadoop Zookeeper
  * `/etc/hdinsight-zookeeper/conf/zoo.cfg`，用於 HDInsight Zookeeper
* 將 `autopurge.snapRetainCount` 的值設定為 3，然後重新開機 Zookeeper 伺服器
  * 可以更新 Hadoop Zookeeper config，而且可以透過 Ambari 重新啟動服務
  * 手動停止並重新啟動 HDInsight Zookeeper
    * `sudo lsof -i :2182` 會提供您要終止的流程識別碼
    * `sudo python /opt/startup_scripts/startup_hdinsight_zookeeper.py`
* 不要手動清除快照集 - 手動刪除快照集可能會導致資料遺失

## <a name="cancelledkeyexception-in-the-zookeeper-server-log-doesnt-require-snapshot-cleanup"></a>Zookeeper 伺服器記錄中的 CancelledKeyException 不需要清除快照集

* 這個例外狀況通常表示用戶端已不再使用，而且伺服器無法傳送訊息
* 此例外狀況也表示 Zookeeper 用戶端提前結束工作階段
* 尋找本文件中所述的其他徵兆

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

- 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。
- 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。
- 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。