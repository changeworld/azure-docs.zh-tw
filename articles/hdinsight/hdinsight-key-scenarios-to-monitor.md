---
title: 監視叢集效能 - Azure HDInsight
description: 如何監視 Azure HDInsight 中 Apache Hadoop 群集的運行狀況和性能。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/09/2020
ms.openlocfilehash: 75ac5a7fc352f877573d79a004d8da761c6f1cef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082875"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>在 Azure HDInsight 中監視群集性能

監視 HDInsight 叢集的健康情況和效能對於維護最佳效能與資源使用率而言是不可或缺的。 監視也可以協助您偵測並解決叢集設定錯誤和使用者程式碼問題。

下列各節說明如何對叢集、Apache Hadoop YARN 佇列上的負載進行監視和最佳化，以及偵測儲存體節流問題。

## <a name="monitor-cluster-load"></a>監視叢集負載

當叢集上的負載平均分散於所有節點時，Hadoop 叢集可以提供最佳效能。 這可讓處理工作在執行時，不會受限於個別節點上的 RAM、CPU 或磁碟資源。

要查看群集的節點及其載入，請登錄到[Ambari Web UI，](hdinsight-hadoop-manage-ambari.md)然後選擇 **"主機**"選項卡。您的主機由其完全限定的功能變數名稱列出。 每個主機的操作狀態是依彩色的健康情況指示器來顯示：

| Color | 描述 |
| --- | --- |
| 紅色 | 主機上至少有一個主要元件已關閉。 暫留以查看列出受影響元件的工具提示。 |
| 橙色 | 主機上的至少一個輔助元件已關閉。 暫留以查看列出受影響元件的工具提示。 |
| 黃色 | Ambari 伺服器超過 3 分鐘沒有收到來自主機的活動訊號。 |
| 綠色 | 一般執行狀態。 |

您也會看到資料行顯示每個主機的核心數及 RAM 數量，以及磁碟使用量和負載平均。

![阿帕奇·安巴里主機選項卡概述](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

選取任何主機名稱，以詳細查看該主機和其計量上執行的元件。 計量會顯示為可選取的 CPU 使用量時間軸、負載、磁碟使用量、記憶體使用量、網路使用量和程序的數目。

![阿帕奇·安巴里主機詳細資訊概述](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

如需有關設定警示和檢視計量的詳細資料，請參閱[使用 Apache Ambari Web UI 來管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)。

## <a name="yarn-queue-configuration"></a>YARN 佇列組態

Hadoop 有各種服務在其分散式平台之間執行。 YARN (Yet Another Resource Negotiator) 可協調這些服務並配置叢集資源，確保將任何負載平均分散於叢集。

YARN 會將 JobTracker、資源管理及作業排程/監視的兩個責任分割為兩個精靈：全域 Resource Manager 和每個應用程式 ApplicationMaster (AM)。

Resource Manager 是純排程器**，且會單獨仲裁所有競爭應用程式之間的可用資源。 Resource Manager 可確保所有資源一律在使用中、最佳化各種常數，例如 SLA、容量保證等等。 ApplicationMaster 會交涉 Resource Manager 的資源，並使用 NodeManager(s) 來執行及監視容器和其資源耗用量。

當多個租戶共用一個大型群集時，群集的資源將存在競爭。 CapacityScheduler 是隨插即用的排程器，可藉由將要求排入佇列來協助資源共用。 容量計劃程式還支援*分層佇列*，以確保在允許其他應用程式的佇列使用可用資源之前，資源在組織的子佇列之間共用。

YARN 可讓我們將資源配置給這些佇列，並顯示是否已指派所有可用的資源。 若要檢視您佇列的相關資訊，請登入 Ambari Web UI，然後從頂端功能表中選取 [YARN 佇列管理員]****。

![阿帕奇·安巴里·亞恩佇列管理器](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

[YARN 佇列管理員] 頁面左側會顯示您的佇列清單，以及指派給每個佇列的容量百分比。

![YARN 佇列管理員詳細資料頁面](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

若要更詳細查看您的佇列，請從 Ambari 儀表板中的左側清單選取 [YARN]**** 服務。 然後在 [快速連結]**** 下拉式功能表中，選取作用中節點下的 [Resource Manager UI]****。

![資源管理器 UI 功能表連結](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

在 Resource Manager UI 中，從左側功能表選取 [排程器]****。 您會在 [應用程式佇列]** 下方看到您的佇列清單。 您可以在這裡查看每個佇列使用的容量，作業在它們之間散發的情況，以及是否有任何作業為有限資源。

![阿帕奇哈杜普資源管理器 UI 功能表](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## <a name="storage-throttling"></a>儲存體節流

叢集的效能瓶頸可能會發生在儲存層級中。 這種類型的瓶頸通常是因為*阻塞*輸入/輸出 （IO） 操作，當正在運行的任務發送的 IO 超過存儲服務可以處理的 IO 時，就會發生這種情況。 此封鎖會建立 IO 要求的佇列，等候目前 IO 處理完成後才會予以處理。 這些塊是由於*存儲限制*（這不是物理限制），而是服務等級協定 （SLA） 對存儲服務施加的限制。 這項限制可確保沒有任何單一用戶端或租用戶可以獨佔服務。 SLA 限制 Azure 存儲每秒 IO （IOPS） 的數量 - 有關詳細資訊，請參閱[標準存儲帳戶的可伸縮性和性能目標](../storage/common/scalability-targets-standard-account.md)。

如果使用 Azure 存儲，請參閱監視與存儲相關的問題（包括限制）的資訊，請參閱[監視、診斷和排除 Microsoft Azure 存儲](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting)。

如果群集的備份存儲是 Azure 資料湖存儲 （ADLS），則限制很可能是由於頻寬限制。 在此情況下，透過觀察工作記錄中的節流錯誤即可識別節流。 如需 ADLS，請參閱這些文章中的節流一節以了解適當服務：

* [HDInsight 和 Azure Data Lake Storage 上的 Apache Hive 效能微調方針](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [HDInsight 和 Azure Data Lake Storage 上的 MapReduce 效能微調方針](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [HDInsight 和 Azure Data Lake Storage 上的 Apache Storm 效能微調方針](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="troubleshoot-sluggish-node-performance"></a>解決節點性能遲鈍的問題

在某些情況下，由於群集上的磁碟空間較低，可能會出現遲滯。 通過以下步驟進行調查：

1. 使用[ssh 命令](./hdinsight-hadoop-linux-use-ssh-unix.md)連接到每個節點。

1. 通過運行以下命令之一檢查磁片使用方式：

    ```bash
    df -h
    du -h --max-depth=1 / | sort -h
    ```

1. 查看輸出，並檢查`mnt`資料夾中是否存在任何大型檔。 通常，`usercache`和`appcache`（mnt/資源/哈圖普/紗線/本地/使用者緩存/hive/appcache/） 資料夾包含大型檔。

1. 如果有大型檔，則當前作業導致檔增長，或者以前的作業失敗可能導致此問題。 要檢查此行為是否由當前作業引起，請運行以下命令：

    ```bash
    sudo du -h --max-depth=1 /mnt/resource/hadoop/yarn/local/usercache/hive/appcache/
    ```

1. 如果此命令指示特定作業，則可以選擇使用類似于以下內容的命令終止作業：

    ```bash
    yarn application -kill -applicationId <application_id>
    ```

    替換為`application_id`應用程式 ID。 如果未指示特定作業，則轉到下一步。

1. 完成上述命令後，或者如果未指示特定作業，則通過運行類似于以下內容的命令刪除標識的大檔：

    ```bash
    rm -rf filecache usercache
    ```

有關磁碟空間問題的詳細資訊，請參閱[磁碟空間不足](./hadoop/hdinsight-troubleshoot-out-disk-space.md)。

> [!NOTE]  
> 如果您有要保留的大型檔，但導致磁碟空間不足問題，您必須擴展 HDInsight 群集並重新啟動服務。 完成此過程並等待幾分鐘後，您會注意到存儲已釋放，並且節點的通常性能已恢復。

## <a name="next-steps"></a>後續步驟

請造訪下列連結以取得關於疑難排解和監視您叢集的詳細資訊：

* [分析 HDInsight 記錄](hdinsight-debug-jobs.md)
* [利用 Apache Hadoop YARN 記錄為應用程式偵錯](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [在以 Linux 為基礎的 HDInsight 上啟用 Apache Hadoop 服務的堆積傾印](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
