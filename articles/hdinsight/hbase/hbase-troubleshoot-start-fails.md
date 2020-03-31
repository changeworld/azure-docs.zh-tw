---
title: Apache HBase 主控形狀無法在 Azure HDInsight 中啟動
description: Apache HBase 主控形狀 （HMaster） 無法在 Azure HDInsight 中啟動
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 290b541d9b5e86616373d2e426241fca07e780ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887201"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Apache HBase 主控形狀 （HMaster） 無法在 Azure HDInsight 中啟動

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="scenario-atomic-renaming-failure"></a>方案：原子重命名失敗

### <a name="issue"></a>問題

啟動過程中標識的意外檔。

### <a name="cause"></a>原因

在啟動過程中，HMaster 執行許多初始化步驟，包括將資料從頭開始 （.tmp） 資料夾移動到資料檔案夾。 HMaster 還會查看預寫記錄檔 （WAL） 資料夾，以查看是否有任何無回應的區域伺服器。

HMaster 在 WAL 資料夾上執行基本欄表命令。 如果任何時間 HMaster 在任一資料夾中看到未預期的檔案，則會擲回例外狀況，而且不會啟動。

### <a name="resolution"></a>解決方案

檢查呼叫堆疊並嘗試確定哪個資料夾可能導致問題（例如，可能是 WAL 資料夾或 .tmp 資料夾）。 接著，在 Cloud Explorer 中或使用 HDFS 命令嘗試找出問題檔案。 通常，這是一個`*-renamePending.json`檔。 （該檔`*-renamePending.json`是用於在 WASB 驅動程式中實現原子重命名操作的日誌檔。 由於此實現中的錯誤，這些檔可以在進程崩潰後保留，等等。強制刪除此檔，無論是在雲資源管理器或使用 HDFS 命令。

有時，在此位置可能還有名為類似內容`$$$.$$$`的暫存檔案。 您必須使用 HDFS `ls` 命令來查看此檔案；您無法在 Cloud Explorer 中查看此檔案。 若要刪除此檔案，請使用 HDFS 命令 `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`。

執行這些命令之後，HMaster 應立即啟動。

---

## <a name="scenario-no-server-address-listed"></a>方案：未列出伺服器位址

### <a name="issue"></a>問題

您可能會看到一條消息，指示`hbase: meta`表未連線。 正在`hbck`運行可能會報告`hbase: meta table replicaId 0 is not found on any region.`在 HMaster 日誌中，您可能會看到消息： `No server address listed in hbase: meta for region hbase: backup <region name>`。  

### <a name="cause"></a>原因

重新開機 HBase 後，HMaster 無法初始化。

### <a name="resolution"></a>解決方案

1. 在 HBase Shell 中，輸入下列命令 (視情況變更實際值)：

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. 刪除該`hbase: namespace`條目。 此條目可能與掃描`hbase: namespace`表時報告的錯誤相同。

1. 從 Ambari UI 重新啟動使用中的 HMaster，使 HBase 處於執行中狀態。

1. 在 HBase Shell 中，若要啟動所有離線資料表，請執行下列命令：

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>方案：java.io.IO 例外：超時

### <a name="issue"></a>問題

HMaster 超時，但有類似于以下致命異常`java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`。

### <a name="cause"></a>原因

如果您有許多資料表和區域在重新啟動 HMaster 服務時尚未排清，則可能會遇到此問題。 超時是 HMaster 的已知缺陷。 一般叢集啟動工作可能需要很長的時間。 如果命名空間表尚未分配，HMaster 將關閉。 如果存在大量未刷新的資料，並且超時 5 分鐘是不夠的，則會發生冗長的啟動任務。

### <a name="resolution"></a>解決方案

1. 從阿帕奇安巴里UI，去**HBase** > **配置**。 在自訂`hbase-site.xml`檔中，添加以下設置：

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. 重新啟動必要的服務 (HMaster，以及其他可能的 HBase 服務)。

---

## <a name="scenario-frequent-region-server-restarts"></a>方案：頻繁的區域伺服器重新開機

### <a name="issue"></a>問題

節點定期重新開機。 從區域伺服器日誌中，您可能會看到類似于的條目：

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>原因

長`regionserver`JVM GC 暫停。 暫停將導致`regionserver`無回應，無法在 zk 會話超時 40s 內將心跳發送到 HMaster。 HMaster 會`regionserver`相信已死，並將中止`regionserver`並重新啟動。

### <a name="resolution"></a>解決方案

更改動物園管理員會話超時，`hbase-site`不僅設置`zookeeper.session.timeout`，而且需要更改動物園管理員`zoo.cfg`設置。 `maxSessionTimeout`

1. 訪問 Ambari UI，轉到**HBase -> 配置 -> 設置**，在超時部分中，更改動物園管理員會話超時的值。

1. 訪問 Ambari UI，轉到**動物園管理員 -> 配置 - >自訂**`zoo.cfg`，添加/更改以下設置。 確保該值與 HBase`zookeeper.session.timeout`相同。

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. 重新開機所需的服務。

---

## <a name="scenario-log-splitting-failure"></a>方案：日誌拆分失敗

### <a name="issue"></a>問題

HMasters 未能在 HBase 群集上出現。

### <a name="cause"></a>原因

輔助存儲帳戶配置錯誤的 HDFS 和 HBase 設置。

### <a name="resolution"></a>解決方案

設置 hbase.rootdir：wasb://@.blob.core.windows.net/hbase並重新啟動 Ambari 上的服務。

---

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源：答案、支援和專家。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
