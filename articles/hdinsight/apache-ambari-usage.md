---
title: Azure HDInsight 中的阿帕奇安巴里使用方式
description: 討論如何使用 Apache Ambari 在 Azure HDInsight 中。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 466c170985715be52a90d579c19ca23aefefe2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77067392"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Azure HDInsight 中的阿帕奇安巴里使用方式

HDInsight 使用 Apache Ambari 進行群集部署和管理。 Ambari 代理在每個節點上運行（頭節點、輔助節點、動物園管理員和邊緣節點（如果存在）。 Ambari 伺服器僅在頭節點上運行（hn0 或 hn1）。 一次只能運行一個 Ambari 伺服器實例。 這是由 HDInsight 容錯移轉控制器控制的。 當其中一個頭節點關閉以進行重新開機或維護時，另一個頭節點將變為活動狀態，第二個頭節點上的 Ambari 伺服器將啟動。

所有群集配置都應通過[Ambari UI](./hdinsight-hadoop-manage-ambari.md)完成，任何本地更改都將在重新開機節點時被覆蓋。

## <a name="failover-controller-services"></a>容錯移轉控制器服務

HDInsight 容錯移轉控制器還負責更新頭節點主機的 IP 位址，該位址指向當前活動頭節點。 所有 Ambari 代理都配置為向頭節點主機報告其狀態和活動訊號。 容錯移轉控制器是在群集中的每個節點上運行的服務集，如果它們未運行，則頭節點容錯移轉可能無法正常工作，在嘗試訪問 Ambari 伺服器時，您將獲得 HTTP 502。

要檢查哪個頭節點處於活動狀態，一種方法是 ssh 到群集中的一個節點，然後運行`ping headnodehost`IP 並將其與兩個頭節點的 IP 進行比較。

如果容錯移轉控制器服務未運行，頭節點容錯移轉可能無法正確發生，最終可能無法運行 Ambari 伺服器。 要檢查容錯移轉控制器服務是否正在運行，請執行：

```bash
ps -ef | grep failover
```

## <a name="logs"></a>記錄

在活動頭節點上，您可以在以下處檢查 Ambari 伺服器日誌：

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

在群集中的任何節點上，都可以在以下處檢查 Ambari 代理日誌：

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>服務啟動序列

這是啟動時開始的服務序列：

1. Hdinsight 代理啟動容錯移轉控制器服務。
1. 容錯移轉控制器服務在每個節點上啟動 Ambari 代理，在活動頭節點上啟動 Ambari 伺服器。

## <a name="ambari-database"></a>安巴里資料庫

HDInsight 在引擎蓋下創建 SQL Azure 資料庫，作為 Ambari 伺服器的資料庫。 預設[服務層為 S0](../sql-database/sql-database-elastic-pool-scale.md)。

對於創建群集時，輔助節點計數大於 16 的任何群集，S2 是資料庫服務層。

## <a name="takeaway-points"></a>外賣點

切勿手動啟動/停止 ambari 伺服器或 ambari 代理服務，除非您嘗試重新開機服務以解決問題。 要強制容錯移轉，可以重新開機活動頭節點。

切勿手動修改任何叢集節點上的任何設定檔，讓 Ambari UI 為您完成工作。

## <a name="next-steps"></a>後續步驟

* [使用 Apache Ambari Web UI 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)
* [使用 Apache Ambari REST API 來管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari-rest-api.md)

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源：答案、支援和專家。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
