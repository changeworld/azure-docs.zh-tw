---
title: Apache HBase Master 在 Azure HDInsight 中無法啟動
description: Apache HBase Master (HMaster) 無法在 Azure HDInsight 中啟動
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: c30077d0d8f359e93745b53755f9dae998073d4d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936900"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Apache HBase Master (HMaster) 無法在 Azure HDInsight 中啟動

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="scenario-atomic-renaming-failure"></a>案例：不可部分完成的重新命名失敗

### <a name="issue"></a>問題

啟動程式期間發現未預期的檔案。

### <a name="cause"></a>原因

在啟動過程中，HMaster 會執行許多初始化步驟，包括將資料從臨時 ( .tmp) 資料夾移至資料檔案夾。 HMaster 也會查看預先寫入記錄檔 (WAL) 資料夾，以查看是否有任何未回應的區域伺服器。

HMaster 會在 WAL 資料夾上執行基本欄表命令。 如果任何時間 HMaster 在任一資料夾中看到未預期的檔案，則會擲回例外狀況，而且不會啟動。

### <a name="resolution"></a>解決方案

檢查呼叫堆疊，並嘗試判斷哪個資料夾可能造成問題 (例如，可能是 WAL 資料夾或 .tmp 資料夾) 。 接著，在 Cloud Explorer 中或使用 HDFS 命令嘗試找出問題檔案。 通常，這是一個檔案 `*-renamePending.json` 。  (檔案 `*-renamePending.json` 是用來在 WASB 驅動程式中執行不可部分完成重新命名作業的日誌檔案。 由於這項處理常式中的錯誤，這些檔案可能會在進程損毀之後離開，依此類推。 ) 在 Cloud Explorer 中或使用 HDFS 命令強制刪除此檔案。

有時可能也會有一個名如下的暫存檔案 `$$$.$$$` 。 您必須使用 HDFS `ls` 命令來查看此檔案；您無法在 Cloud Explorer 中查看此檔案。 若要刪除此檔案，請使用 HDFS 命令 `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`。

執行這些命令之後，HMaster 應立即啟動。

---

## <a name="scenario-no-server-address-listed"></a>案例：未列出任何伺服器位址

### <a name="issue"></a>問題

您可能會看到一則訊息，指出該 `hbase: meta` 資料表不在線上。 `hbck`執行可能會報告 `hbase: meta table replicaId 0 is not found on any region.` 在 HMaster 記錄中，您可能會看到下列訊息： `No server address listed in hbase: meta for region hbase: backup <region name>` 。  

### <a name="cause"></a>原因

重新開機 HBase 之後，HMaster 無法初始化。

### <a name="resolution"></a>解決方案

1. 在 HBase Shell 中，輸入下列命令 (視情況變更實際值)：

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. 刪除 `hbase: namespace` 專案。 此專案可能是掃描資料表時所報告的相同錯誤 `hbase: namespace` 。

1. 從 Ambari UI 重新啟動使用中的 HMaster，使 HBase 處於執行中狀態。

1. 在 HBase Shell 中，若要啟動所有離線資料表，請執行下列命令：

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>案例： IOException：逾時

### <a name="issue"></a>問題

HMaster 發生嚴重例外狀況的時間，類似于： `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned` 。

### <a name="cause"></a>原因

如果您有許多資料表和區域在重新啟動 HMaster 服務時尚未排清，則可能會遇到此問題。 HMaster 時是已知的瑕疵。 一般叢集啟動工作可能需要很長的時間。 如果尚未指派命名空間資料表，HMaster 會關閉。 當有大量的未清除資料存在，但有五分鐘的時間不足時，就會發生冗長的啟動工作。

### <a name="resolution"></a>解決方案

1. 從 Apache Ambari UI 中，移至 **HBase** 的 [連線]  >  ****。 在自訂檔案中 `hbase-site.xml` ，新增下列設定：

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. 重新啟動必要的服務 (HMaster，以及其他可能的 HBase 服務)。

---

## <a name="scenario-frequent-region-server-restarts"></a>案例：頻繁的區域伺服器重新開機

### <a name="issue"></a>問題

節點會定期重新開機。 從區域伺服器記錄中，您可能會看到類似下列的專案：

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>原因

長 `regionserver` JVM GC 暫停。 暫停會導致沒有 `regionserver` 回應，而且無法在 zk 會話超時40s 內傳送 HMaster。 HMaster 會認為 `regionserver` 是不正確，並會中止 `regionserver` 並重新啟動。

### <a name="resolution"></a>解決方案

變更 Zookeeper 會話超時，但不只 `hbase-site` 是設定， `zookeeper.session.timeout` 也 `zoo.cfg` `maxSessionTimeout` 需要變更 Zookeeper 設定。

1. 存取 Ambari UI，移至 **HBase-> 設定-> 設定**，在 [超時] 區段中，變更 [Zookeeper 會話超時] 的值。

1. 存取 Ambari UI，移至 **Zookeeper-> 設定-> 自訂**] `zoo.cfg` ，新增/變更下列設定。 請確定值與 HBase 相同 `zookeeper.session.timeout` 。

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. 重新開機必要的服務。

---

## <a name="scenario-log-splitting-failure"></a>案例：記錄分割失敗

### <a name="issue"></a>問題

HMasters 無法出現在 HBase 叢集上。

### <a name="cause"></a>原因

次要儲存體帳戶的 HDFS 和 HBase 設定設定錯誤。

### <a name="resolution"></a>解決方案

wasb://@.blob.core.windows.net/hbase在 Ambari 上設定 rootdir：並重新啟動服務。

---

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。