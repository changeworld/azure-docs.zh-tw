---
title: 監視叢集效能 - Azure HDInsight
description: 如何在 Azure HDInsight 中監視 Apache Hadoop 叢集的健全狀況和效能。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/09/2020
ms.openlocfilehash: 75ac5a7fc352f877573d79a004d8da761c6f1cef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79082875"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>監視 Azure HDInsight 中的叢集效能

監視 HDInsight 叢集的健康情況和效能對於維護最佳效能與資源使用率而言是不可或缺的。 監視也可以協助您偵測並解決叢集設定錯誤和使用者程式碼問題。

下列各節說明如何對叢集、Apache Hadoop YARN 佇列上的負載進行監視和最佳化，以及偵測儲存體節流問題。

## <a name="monitor-cluster-load"></a>監視叢集負載

當叢集上的負載平均分散於所有節點時，Hadoop 叢集可以提供最佳效能。 這可讓處理工作在執行時，不會受限於個別節點上的 RAM、CPU 或磁碟資源。

若要取得叢集節點和其載入的高階查看，請登入[Ambari WEB UI](hdinsight-hadoop-manage-ambari.md)，然後選取 [**主機**] 索引標籤。您的主機會依照其完整功能變數名稱列出。 每個主機的操作狀態是依彩色的健康情況指示器來顯示：

| Color | 說明 |
| --- | --- |
| 紅色 | 主機上至少有一個主要元件已關閉。 暫留以查看列出受影響元件的工具提示。 |
| 橙色 | 主機上至少有一個次要元件已關閉。 暫留以查看列出受影響元件的工具提示。 |
| 黃色 | Ambari 伺服器未從主機收到超過3分鐘的信號。 |
| 綠色 | 一般執行狀態。 |

您也會看到資料行顯示每個主機的核心數及 RAM 數量，以及磁碟使用量和負載平均。

![Apache Ambari 主機索引標籤總覽](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

選取任何主機名稱，以詳細查看該主機和其計量上執行的元件。 計量會顯示為可選取的 CPU 使用量時間軸、負載、磁碟使用量、記憶體使用量、網路使用量和程序的數目。

![Apache Ambari 主機詳細資料總覽](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

如需有關設定警示和檢視計量的詳細資料，請參閱[使用 Apache Ambari Web UI 來管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)。

## <a name="yarn-queue-configuration"></a>YARN 佇列組態

Hadoop 有各種服務在其分散式平台之間執行。 YARN (Yet Another Resource Negotiator) 可協調這些服務並配置叢集資源，確保將任何負載平均分散於叢集。

YARN 會將 JobTracker、資源管理及作業排程/監視的兩個責任分割為兩個精靈：全域 Resource Manager 和每個應用程式 ApplicationMaster (AM)。

Resource Manager 是純排程器**，且會單獨仲裁所有競爭應用程式之間的可用資源。 Resource Manager 可確保所有資源一律在使用中、最佳化各種常數，例如 SLA、容量保證等等。 ApplicationMaster 會交涉 Resource Manager 的資源，並使用 NodeManager(s) 來執行及監視容器和其資源耗用量。

當多個租使用者共用大型叢集時，就會對叢集的資源進行競爭。 CapacityScheduler 是隨插即用的排程器，可藉由將要求排入佇列來協助資源共用。 CapacityScheduler 也支援*階層式佇列*，以確保組織的子佇列之間會共用資源，而其他應用程式的佇列則可使用免費資源。

YARN 可讓我們將資源配置給這些佇列，並顯示是否已指派所有可用的資源。 若要檢視您佇列的相關資訊，請登入 Ambari Web UI，然後從頂端功能表中選取 [YARN 佇列管理員]****。

![Apache Ambari YARN 佇列管理員](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

[YARN 佇列管理員] 頁面左側會顯示您的佇列清單，以及指派給每個佇列的容量百分比。

![YARN 佇列管理員詳細資料頁面](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

若要更詳細查看您的佇列，請從 Ambari 儀表板中的左側清單選取 [YARN]**** 服務。 然後在 [快速連結]**** 下拉式功能表中，選取作用中節點下的 [Resource Manager UI]****。

![Resource Manager UI 功能表連結](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

在 Resource Manager UI 中，從左側功能表選取 [排程器]****。 您會在 [應用程式佇列]** 下方看到您的佇列清單。 您可以在這裡查看每個佇列使用的容量，作業在它們之間散發的情況，以及是否有任何作業為有限資源。

![Apache HAdoop Resource Manager UI 功能表](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## <a name="storage-throttling"></a>儲存體節流

叢集的效能瓶頸可能會發生在儲存層級中。 這種類型的瓶頸最常見的原因是*封鎖*輸入/輸出（IO）作業，這會在執行中的工作傳送比儲存體服務可處理的 IO 更多時發生。 此封鎖會建立 IO 要求的佇列，等候目前 IO 處理完成後才會予以處理。 區塊的原因是*儲存體節流*，這不是實體限制，而是由服務等級協定（SLA）的儲存體服務所加諸的限制。 這項限制可確保沒有任何單一用戶端或租用戶可以獨佔服務。 SLA 會限制 Azure 儲存體的每秒 Io 數（IOPS）-如需詳細資訊，請參閱[標準儲存體帳戶的擴充性和效能目標](../storage/common/scalability-targets-standard-account.md)。

如果您使用 Azure 儲存體，如需有關監視儲存體相關問題的資訊（包括節流），請參閱[監視、診斷和疑難排解 Microsoft Azure 儲存體](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting)。

如果您叢集的備份存放區是 Azure Data Lake Storage （ADLS），您的節流很有可能是因為頻寬限制。 在此情況下，透過觀察工作記錄中的節流錯誤即可識別節流。 如需 ADLS，請參閱這些文章中的節流一節以了解適當服務：

* [HDInsight 和 Azure Data Lake Storage 上的 Apache Hive 效能微調方針](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [HDInsight 和 Azure Data Lake Storage 上的 MapReduce 效能微調方針](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [HDInsight 和 Azure Data Lake Storage 上的 Apache Storm 效能微調方針](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="troubleshoot-sluggish-node-performance"></a>針對緩慢的節點效能進行疑難排解

在某些情況下，sluggishness 可能會因為叢集上的磁碟空間不足而發生。 請使用下列步驟進行調查：

1. 使用[ssh 命令](./hdinsight-hadoop-linux-use-ssh-unix.md)連接到每個節點。

1. 執行下列其中一個命令來檢查磁片使用量：

    ```bash
    df -h
    du -h --max-depth=1 / | sort -h
    ```

1. 檢查輸出，並檢查`mnt`資料夾或其他資料夾中是否有任何大型檔案。 `usercache`通常，和`appcache` （mnt/resource/hadoop/yarn/local/usercache/hive/appcache/）資料夾包含大型檔案。

1. 如果有大型檔案，可能是目前的工作造成檔案成長，或先前的工作已失敗。 若要檢查此行為是否由目前的作業所造成，請執行下列命令：

    ```bash
    sudo du -h --max-depth=1 /mnt/resource/hadoop/yarn/local/usercache/hive/appcache/
    ```

1. 如果此命令指出特定的作業，您可以選擇使用類似下列的命令來終止作業：

    ```bash
    yarn application -kill -applicationId <application_id>
    ```

    以`application_id`應用程式識別碼取代。 如果沒有指定特定的工作，請移至下一個步驟。

1. 在上述命令完成之後，或如果沒有指定特定的工作，請執行類似下列的命令來刪除您所識別的大型檔案：

    ```bash
    rm -rf filecache usercache
    ```

如需有關磁碟空間問題的詳細資訊，請參閱[磁碟空間不足](./hadoop/hdinsight-troubleshoot-out-disk-space.md)。

> [!NOTE]  
> 如果您有想要保留的大型檔案，但卻造成磁碟空間不足的問題，您必須相應增加您的 HDInsight 叢集，並重新啟動您的服務。 完成此程式並等候幾分鐘後，您會注意到儲存體已釋出，而且會還原節點的一般效能。

## <a name="next-steps"></a>後續步驟

請造訪下列連結以取得關於疑難排解和監視您叢集的詳細資訊：

* [分析 HDInsight 記錄](hdinsight-debug-jobs.md)
* [利用 Apache Hadoop YARN 記錄為應用程式偵錯](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [在以 Linux 為基礎的 HDInsight 上啟用 Apache Hadoop 服務的堆積傾印](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
