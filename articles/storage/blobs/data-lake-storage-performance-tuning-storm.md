---
title: 調優性能：風暴、HDInsight& Azure 資料存儲第 2 代 |微軟文檔
description: Azure Data Lake Storage Gen2 Storm 效能微調指導方針
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 125c583512f6bae34c2dd3c3dd76a1b96a181ac1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327911"
---
# <a name="tune-performance-storm-hdinsight--azure-data-lake-storage-gen2"></a>調整性能：風暴、HDInsight & Azure 資料存儲第 2 代

了解在微調 Azure Storm 拓撲的效能時應考量的因素。 例如，務必要了解由 Spout 和 Bolt 所完成之工作 (不論是 I/O 密集或記憶體密集工作) 的特性。 本文探討各種效能微調指導方針，包括疑難排解方面的常見問題。

## <a name="prerequisites"></a>Prerequisites

* **Azure 訂閱**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Storage Gen2 帳戶**。 有關如何創建一個的說明，請參閱[快速入門：為分析創建存儲帳戶](data-lake-storage-quickstart-create-account.md)。
* 可存取 Data Lake Storage Gen2 帳戶的 **Azure HDInsight 叢集**。 請參閱[搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)。 請確實為叢集啟用遠端桌面。
* **在 Data Lake Storage Gen2 上執行 Storm 叢集**。 有關詳細資訊，請參閱[HDInsight 上的風暴](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview)。
* **Data Lake Storage Gen2 的效能微調方針**。  如需一般的效能概念，請參閱 [Data Lake Storage Gen2 效能微調指導方針](data-lake-storage-performance-tuning-guidance.md)。   

## <a name="tune-the-parallelism-of-the-topology"></a>調整拓撲的平行處理原則

提升 Data Lake Storage Gen2 的往返 I/O 並行能力，或許能改善效能。 Storm 拓撲有一組可決定平行處理原則的組態︰
* 背景工作處理序數目 (背景工作會平均分散給 VM)。
* Spout 執行程式執行個體數目。
* Bolt 執行程式執行個體數目。
* Spout 工作數目。
* Bolt 工作數目。

例如，在具有 4 個 VM 和 4 個背景工作處理序、32 個 Spout 執行程式和 32 個 Spout 工作，以及 256 個 Bolt 執行程式和 512 個 Bolt 工作的叢集上，請考慮下列情況︰

每個監督員 (背景工作節點) 具有單一背景工作 Java 虛擬機器 (JVM) 處理序。 此 JVM 處理序管理 4 個 Spout 執行緒和 64 個 Bolt 執行緒。 在每個執行緒內，工作會循序執行。 在上述組態中，每個 Spout 執行緒有 1 個工作，而每個 Bolt 執行緒有 2 個工作。

在 Storm 中，以下是所涉及的各種元件，以及它們會如何影響您所擁有的平行處理原則層級︰
* 前端節點 (在 Storm 中稱為 Nimbus) 可用來提交和管理作業。 這些節點不會影響平行處理原則的程度。
* 監督員節點。 在 HDInsight 中，這會對應至背景工作節點 Azure VM。
* 背景工作的工作是在 VM 中執行的 Storm 處理序。 每個背景工作的工作會對應至 JVM 執行個體。 Storm 會盡量地將您指定的背景工作處理序數目平均分散給背景工作節點。
* Spout 和 Bolt 執行程式執行個體。 每個執行程式執行個體會對應至在背景工作 (JVM) 內執行的執行緒。
* Storm 工作。 這些工作是每個執行緒所執行的邏輯工作。 這不會變更平行處理原則的層級，因此您應該評估每個執行程式是否需要多個工作。

### <a name="get-the-best-performance-from-data-lake-storage-gen2"></a>從 Data Lake Storage Gen2 獲得最佳效能

在使用 Data Lake Storage Gen2 時，如果您執行下列作業，就能獲得最佳效能︰
* 將小型附加項目聯合成為較大的大小。
* 盡可能提出最多的並行要求。 因為每個 Bolt 執行緒都會進行區塊讀取，您會想要讓每個核心擁有 8-12 個左右的執行緒。 這會讓 NIC 和 CPU 受到充分利用。 較大的 VM 可允許更多的並行要求。  

### <a name="example-topology"></a>範例拓撲

假設您擁有 8 個背景工作節點的叢集，叢集中具有 D13v2 Azure VM。 此 VM 有 8 個核心，因此在 8 個背景工作節點之中，總共會有 64 個核心。

假設我們讓每個核心執行 8 個 Bolt 執行緒。 若給定 64 個核心，這表示我們總共需要 512 個 Bolt 執行程式執行個體 (亦即執行緒)。 在此情況下，假設我們一開始讓每個 VM 擁有一個 JVM，並且大多在 JVM 內使用執行緒並行存取以達到並行效果。 這表示我們需要 8 個背景工作的工作 (每個 Azure VM 一個工作) 和 512 個 Bolt 執行程式。 若給定此組態，Storm 會嘗試將背景工作平均分散給背景工作節點 (也稱為監督員節點)，讓每個背景工作節點有 1 個 JVM。 現在在監督員內，Storm 會嘗試在監督員之間平均分散執行程式，讓每個監督員 (亦即 JVM) 各有 8 個執行緒。

## <a name="tune-additional-parameters"></a>微調其他參數
在擁有基本拓撲之後，您可以考慮是否要調整任何參數︰
* **每個背景工作節點的 JVM 數目。** 如果您在記憶體中裝載了一個大型資料結構 (例如，查閱資料表)，每個 JVM 都需要個別的複本。 或者，如果您的 JVM 較少，您可以使用跨多個執行緒的資料結構。 針對 Bolt 的 I/O，JVM 數目所造成的差異，不會比跨這些 JVM 所新增的執行緒數目還多。 為了簡單起見，最好讓每個背景工作有一個 JVM。 但根據 Bolt 所執行的作業或您所需的應用程式處理而定，您可能需要變更此數量。
* **Spout 執行程式的數目。** 因為上述範例使用 Bolt 來寫入至 Data Lake Storage Gen2，所以 Spout 的數目不會與 Bolt 的效能直接相關。 不過，根據 Spout 中發生的處理或 I/O 數量，最好是微調 Spout 以獲得最佳效能。 確定您有足夠的 Spout 能讓 Bolt 保持忙碌。 Spout 的輸出速率應該符合 Bolt 的輸送量。 實際組態取決於 Spout。
* **工作數目。** 每個 Bolt 都會以單一執行緒的形式來執行。 每個 Bolt 的其他工作不會提供任何額外的並行能力。 如果認可 Tuple 的處理序會佔用 Bolt 大部分的執行時間，它們的優點才會浮現。 您最好先將許多 Tuple 群組成較大的附加項目，再從 Bolt 傳送認可。 因此，在大部分情況下，多個工作不會提供額外的好處。
* **本機或隨機群組。** 本設定啟用時，會將 Tuple 傳送至相同背景工作處理序內的 Bolt。 這可降低處理序間的通訊和網路呼叫。 這是大部分拓撲的建議作法。

這個基本案例是不錯的起點。 使用您自己的資料進行測試來調整前述參數，以達到最佳效能。

## <a name="tune-the-spout"></a>微調 Spout

您可以修改下列設定來微調 Spout。

- **Tuple 逾時︰topology.message.timeout.secs**。 此設定會決定訊息完成和接收認可所需的時間量，在此時間後，便會將訊息視為失敗。

- **每個背景工作處理序的記憶體上限：worker.childopts**。 此設定可讓您指定 Java 背景工作的其他命令列參數。 這裡最常使用的設定是 XmX，它會決定配置給 JVM 堆積的記憶體上限。

- **Spout 暫止上限：topology.max.spout.pending**。 此設定會決定任何時候每個 Spout 執行緒可以有的傳輸中 (尚未在拓撲中的所有節點上受到認可) Tuple 數目。

  良好的計算方式是估計每個 Tuple 的大小。 然後找出一個 Spout 執行緒有多少記憶體。 配置給執行緒的記憶體總數除以這個值，應該就能得出最大 Spout 暫止參數的上限。

預設 Data Lake Storage Gen2 Storm Bolt 有一個大小同步處理原則參數 (fileBufferSize) 可用來調整此參數。

在 I/O 密集的拓撲中，最好讓每個 Bolt 執行緒寫入到自己的檔案中，並設定檔案輪替原則 (fileRotationSize)。 當檔案達到特定大小時，就會自動排清資料流，並在其中寫入新檔案。 建議的輪替檔案大小為 1 GB。

## <a name="monitor-your-topology-in-storm"></a>監視 Storm 中的拓撲  
當您的拓撲在執行時，您可以在 Storm 使用者介面中進行監視。 以下是應該注意的主要參數︰

* **總處理序執行延遲。** 這是一個 Tuple 由 Spout 發出、由 Bolt 處理並受到認可所花費的平均時間。

* **總 Bolt 處理序延遲。** 這是 Tuple 收到認可之前在 Bolt 所花費的平均時間。

* **總 Bolt 執行延遲。** 這是 Bolt 在 execute 方法所花費的平均時間。

* **失敗次數。** 這是指 Tuple 在逾時之前能夠無法完全處理的次數。

* **能力。** 這是系統忙碌程度的量值。 如果這個數字為 1，Bolt 會以它最快的速度工作。 如果小於 1，將會增加平行處理原則。 如果大於 1，則會減少平行處理原則。

## <a name="troubleshoot-common-problems"></a>針對常見問題進行疑難排解
以下是一些常見的疑難排解案例。
* **許多元結正在超時。** 查看拓撲中的每個節點，以確定瓶頸的位置。 最常見的原因是 Bolt 無法跟上 Spout。 這導致 Tuple 在等待處理時阻塞了內部緩衝區。 請考慮增加逾時值，或減少 Spout 暫止上限。

* **總處理序執行延遲很高，但 Bolt 處理序延遲卻很低。** 在此情況下，認可 Tuple 的速度可能不夠快。 請確認認可者的數量足夠。 另一個可能的原因是，它們在佇列中等待很久之後，Bolt 才開始處理。 減少 Spout 暫止上限。

* **Bolt 執行延遲很高。** 這表示 Bolt 的 execute() 方法花費太久時間。 請將程式碼最佳化，或查看寫入大小和排清行為。

### <a name="data-lake-storage-gen2-throttling"></a>Data Lake Storage Gen2 節流
如果您達到 Data Lake Storage Gen2 所提供的頻寬限制，則可能會看到工作失敗。 請檢查工作記錄中的節流錯誤。 您可以藉由增加容器大小來減少平行處理原則。    

若要檢查您是否遭到節流，請在用戶端啟用偵錯記錄：

1. 在**Ambari** > **風暴** > **Config** > **高級風暴工作者-log4j**中，將**根級別="資訊"&lt;&gt;** 更改為**&lt;根級別="調試"。&gt; ** 重新啟動所有節點/服務，以便讓設定生效。
2. 監視背景工作節點上的 Storm 拓撲記錄 (在 /var/log/storm/worker-artifacts/&lt;TopologyName&gt;/&lt;port&gt;/worker.log 下)，注意是否有 Data Lake Storage Gen2 節流例外狀況。

## <a name="next-steps"></a>後續步驟
風暴的其他性能調優可以[在此博客](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/)中引用。

如需可執行的其他範例，請參閱 [GitHub 上的這一個](https://github.com/hdinsight/storm-performance-automation)。
