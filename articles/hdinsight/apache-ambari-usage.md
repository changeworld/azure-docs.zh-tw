---
title: Azure HDInsight 中的 Apache Ambari 使用方式
description: 討論如何在 Azure HDInsight 中使用 Apache Ambari。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: fe7d6d4e70bc55a6a91d3c1a1b910db4b5469fe6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84197079"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Azure HDInsight 中的 Apache Ambari 使用方式

HDInsight 使用 Apache Ambari 進行叢集部署和管理。 Ambari 代理程式會在每個節點上執行（前端節點、背景工作節點、zookeeper 和 edgenode （如果有的話））。 Ambari 伺服器只會在前端節點（hn0 或 hn1）上執行。 一次只能執行一個 Ambari 伺服器實例。 這是由 HDInsight 容錯移轉控制器所控制。 當其中一個前端節點關閉進行重新開機或維護時，另一個前端節點將會變成作用中，而第二個前端節點上的 Ambari 伺服器將會啟動。

所有叢集設定都應該透過[AMBARI UI](./hdinsight-hadoop-manage-ambari.md)完成，當節點重新開機時，將會覆寫任何本機變更。

## <a name="failover-controller-services"></a>容錯移轉控制器服務

HDInsight 容錯移轉控制器也會負責更新前端節點主機的 IP 位址，指向目前作用中的前端節點。 所有的 Ambari 代理程式都設定為向前端節點主機報告其狀態和心跳。 容錯移轉控制器是在叢集的每個節點上執行的一組服務，如果它們不在執行中，前端節點容錯移轉可能無法正常運作，而且當您嘗試存取 Ambari 伺服器時，將會得到 HTTP 502。

若要檢查哪些前端節點作用中，其中一種方式是透過 ssh 連線到叢集中的其中一個節點，然後執行 `ping headnodehost` 並比較 IP 與這兩個前端節點的其中一個。

如果容錯移轉控制器服務未執行，前端節點容錯移轉可能無法正常運作，這可能會導致無法執行 Ambari 伺服器。 若要檢查容錯移轉控制器服務是否正在執行，請執行：

```bash
ps -ef | grep failover
```

## <a name="logs"></a>記錄

在使用中的前端節點上，您可以在下列位置檢查 Ambari 伺服器記錄：

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

在叢集中的任何節點上，您可以在下列位置檢查 Ambari 代理程式記錄：

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>服務啟動順序

這是在開機時啟動服務的順序：

1. Hdinsight 代理程式會啟動容錯移轉控制器服務。
1. 容錯移轉控制器服務會在 active 前端節點上的每個節點和 Ambari 伺服器上啟動 Ambari 代理程式。

## <a name="ambari-database"></a>Ambari 資料庫

HDInsight 會在 SQL Database 中建立資料庫，以做為 Ambari 伺服器的資料庫。 預設[服務層級為 S0](../azure-sql/database/elastic-pool-scale.md)。

在建立叢集時，如果背景工作角色節點計數大於16的叢集，S2 就是資料庫服務層級。

## <a name="takeaway-points"></a>重點點

除非您嘗試重新開機服務來解決問題，否則請勿手動啟動/停止 ambari 或 ambari 代理程式服務。 若要強制容錯移轉，您可以重新開機現用前端節點。

絕對不要手動修改任何叢集節點上的任何設定檔，讓 Ambari UI 為您執行工作。

## <a name="next-steps"></a>後續步驟

* [使用 Apache Ambari Web UI 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)
* [使用 Apache Ambari REST API 來管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari-rest-api.md)

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
