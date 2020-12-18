---
title: HDInsight 互動式查詢叢集 (LLAP) 調整大小指南
description: LLAP 調整大小指南
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: 626b061cc237f7238d47863a3e1ed88961d2f742
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680590"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Azure HDInsight 互動式查詢叢集 (Hive LLAP) 調整大小指南

本文件描述如何針對一般工作負載調整 HDInsight 互動式查詢叢集 (Hive LLAP 叢集) 的大小，藉此提升效能。 請注意，本文件中提供的建議為一般指南，特定的工作負載可能需要使用特定的調整。

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**適用於 HDInsight 互動式查詢叢集 (LLAP) 的 Azure 預設 VM 類型**

| 節點類型      | 執行個體 | 大小     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 個 vCPU、56 GB RAM、400 GB SSD   |
| 背景工作   | **D14 v2**        | **16 個 vCPU、112 GB RAM、800 GB SSD**       |
| ZooKeeper   | A4 v2        | 4 個 vCPU、8 GB RAM、40 GB SSD       |

**_注意：所有建議的設定值都是以 D14 v2 類型背景工作節點 _ 為基礎_*  

### <a name="_configuration"></a>_ 設定 *：**    
| 設定機碼      | 建議值  | 描述 |
| :---        |    :----:   | :---     |
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | 節點上所有 YARN 容器的指定記憶體總數 (以 MB 為單位) | 
| yarn.scheduler.maximum-allocation-mb | 102400 (MB) | 每個容器於 RM 所要求的最大配置 (以 MB 為單位)。 高於此值的記憶體要求將不會生效 |
| yarn.scheduler.maximum-allocation-vcores | 12 |每個容器於 Resource Manager 所要求的最大 CPU 核心數。 高於此值的要求將不會生效。 |
| yarn.nodemanager.resource.cpu-vcores | 12 | 每個 NodeManager 可為容器配置的 CPU 核心數。 |
| yarn.scheduler.capacity.root.llap.capacity | 85 (% )  | LLAP 佇列的 YARN 容量配置  |
| tez.am.resource.memory.mb | 4096 (MB) | 要供 Tez AppMaster 使用的記憶體數量 (以 MB 為單位) |
| hive.server2.tez.sessions.per.default.queue | <背景工作節點數> |hive.server2.tez.default.queues 中每個具名佇列的工作階段數目。 此數目會對應於查詢協調器的數目 (Tez AM) |
| hive.tez.container.size | 4096 (MB) | 指定的 Tez 容器大小 (以 MB 為單位) |
| hive.llap.daemon.num.executors | 19 | 每個 LLAP 精靈的執行程式數目 | 
| hive.llap.io.threadpool.size | 19 | 執行程式的執行緒集區大小 |
| hive.llap.daemon.yarn.container.mb | 81920 (MB)  | 個別 LLAP 精靈所使用的記憶體總數 (每個精靈所使用的記憶體、以 MB 為單位)
| hive.llap.io.memory.size | 242688 (MB)  | 啟用 SSD 快取時，每個 LLAP 精靈的快取大小 (以 MB 為單位) |
| hive.auto.convert.join.noconditionaltask.size | 2048 (MB) | 執行對應聯結的記憶體大小 (以 MB 為單位) |

### <a name="llap-architecturecomponents"></a>**LLAP 架構/元件：**  

![' LLAP 架構/元件 '](./media/hive-llap-sizing-guide/LLAP_architecture_sizing_guide.png "LLAP 架構/元件")

### <a name="llap-daemon-size-estimations"></a>**LLAP 精靈大小估算：** 

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1.決定節點上所有容器的總 YARN 記憶體配置**    
設定： **_yarn. nodemanager。記憶體-mb_* _  

此值表示每個節點上 YARN 容器可使用的記憶體最大總和 (以 MB 為單位)。 指定值應小於該節點上的實體記憶體總數。   
節點上所有 YARN 容器的記憶體總計 = (實體記憶體總計– OS + 其他服務) 的記憶體  
請將此值設定為約可用 RAM 大小的 90%。  
針對 D14 v2，建議值為 _ * 102400 MB * *。 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2.決定每個 YARN 容器要求的最大記憶體數量**  
設定： **_yarn。排程器。最大配置-mb_* _

此值表示 Resource Manager 中每個容器要求的最大配置 (以 MB 為單位)。 高於此指定值的記憶體要求將不會生效。 Resource Manager 可以提供記憶體給容器，以 _yarn 的遞增量。排程器。最小配置-mb * 且不能超過 yarn 所指定的大小。 *最大配置-mb*。 此指定值不應大於 *yarn.nodemanager.resource.memory-mb* 指定節點上所有容器的指定記憶體總數。    
針對 D14 v2 背景工作節點，建議值為 **102400 MB**

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3.決定每個 YARN 容器要求的最大 vCore 數**  
設定： **_yarn。排程器。最大配置-虛擬核心_* _  

此值表示 Resource Manager 中每個容器要求的最大虛擬 CPU 核心數。 高於此值的 vCore 數要求將不會生效。 此為 YARN 排程器的全域屬性。 針對 LLAP 精靈容器，此值可設定為可用 vCore 總數的 75%。 剩餘 25% 應保留給在背景工作節點中執行的 NodeManager、DataNode 和其他服務。  
D14 v2 VM 上具有 16 個 vCore，而 LLAP 精靈容器可使用此 16 個 vCore 中的75%。  
針對 D14 v2，建議值為 _ * 12 * *。  

#### <a name="4-number-of-concurrent-queries"></a>**4.並行查詢數目**  
設定： **_tez。每個. 預設. 佇列_* _

此設定值會決定可同時啟動的 Tez 工作階段數目。 這些 Tez 工作階段會針對 "hive.server2.tez.default.queues" 所指定的每個佇列啟動。 該值會對應到 Tez AM (查詢協調器) 的數目。 建議設為與背景工作節點相同的數目。 Tez AM 數目可高於 LLAP 精靈節點的數目。 Tez AM 主要負責協調查詢執行，並將查詢計劃片段指派給對應的 LLAP 精靈來執行。 請將此值設為 LLAP 精靈節點的倍數，藉此達到更高的輸送量。  

預設 HDInsight 叢集有四個在四個背景工作節點上執行的 LLAP 守護程式，因此建議值為 _ * 4 * *。  

**Hive config variable 的 Ambari UI 滑杆 `hive.server2.tez.sessions.per.default.queue` ：**

![「LLAP 並行查詢數目上限」](./media/hive-llap-sizing-guide/LLAP_sizing_guide_max_concurrent_queries.png "LLAP 並行查詢數目上限")

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5.Tez Container 與 Tez Application Master 的大小**    
設定： **_tez.._* m a m. tez. size _  

_tez 的.. mb *-定義 Tez 應用程式主要大小。  
建議值為 **4096 MB**。
   
*hive.tez.container.size* - 定義提供給 Tez 容器的記憶體數量。 此值必須介於 YARN 最小容器大小 (*yarn.scheduler.minimum-allocation-mb*) 與 YARN 最大容器大小 (*yarn.scheduler.maximum-allocation-mb*) 之間。 LLAP 精靈執行程式會使用此值來限制每個執行程式的記憶體使用方式。  
建議值為 **4096 MB**。  

#### <a name="6-llap-queue-capacity-allocation"></a>**6.LLAP 佇列容量配置**   
Configuration： **_yarn. llap. 容量_* _  

此值表示指定給 LLAP 佇列的容量百分比。 根據 YARN 佇列的設定方式而定，針對不同的工作負載，容量配置可能會有不同值。 如果工作負載是唯讀作業，請將其設定為容量的 90%。 但是，如果您的工作負載與使用受控資料表的更新/刪除/合併作業混合，建議您為 llap 佇列提供85% 的容量。 剩餘的15% 容量可供其他工作使用，例如壓縮等，以從預設佇列配置容器。 如此一來，預設佇列中的工作便不會佔用 YARN 資源。    

針對 D14v2 背景工作節點，llap 佇列的建議值為 _ * 85 * *。     
(針對唯讀工作負載，則可適度地增加至 90。)  

#### <a name="7-llap-daemon-container-size"></a>**7.LLAP 精靈容器大小**    
設定： **_hive. llap. yarn. mb_* _  
   
LLAP 精靈會在每個背景工作節點上以 YARN 容器的形式執行。 LLAP 精靈容器的記憶體大小總數取決於下列因素：    
_ YARN 容器大小的設定 (YARN。排程器。最小配置-mb，YARN。最大配置-mb，YARN. nodemanager. u-mb) 
*  節點上的 Tez AM 數
*  為節點上所有容器所設定的記憶體總數，以及 LLAP 佇列容量  

Tez Application Master (Tez AM) 所需的記憶體可以下列方式計算。  
Tez AM 可作為查詢協調器，而 Tez AMs 的數目則應根據要提供的多個並行查詢來設定。 理論上，我們可以考慮每個背景工作節點一個 Tez AM。 不過，您可能會在背景工作節點上看到一個以上的 Tez。 針對計算目的，我們假設在所有 LLAP daemon 節點/背景工作節點上，Tez AMs 的統一散發。
建議每個 Tez AM 都要有 4 GB 的記憶體。  

Hive config * Tez 所指定的 Tez Ams = 值數目 =**。依預設，queue** _。  
LLAP daemon 節點數目 = 由 Ambari UI 中的 env 變數 _*_num_llap_nodes_for_llap_daemons_*_ 指定。  
Tez AM container size = Tez config _*_Tez_*_ 所指定的值。  

每個節點的 Tez AM 記憶體 = _ *(** Ceil **(** Tez AMs **/** 數目 LLAP daemon 節點數目 **)** **x** Tez AM 容器大小 **)**  
針對 D14 v2，預設設定具有四個 Tez AM 與四個 LLAP 精靈節點。  
則每個節點的 Tez AM 記憶體 = (ceil(4/4) x 4 GB) = 4 GB

每個背景工作節點的 LLAP 佇列可用記憶體總計可以計算如下：  
此值取決於節點上所有 YARN 容器可用的總記憶體量 (*YARN。記憶體-mb*) ，以及為 llap 佇列 (*YARN* 設定的容量百分比。 nodemanager 的容量) 。  
背景工作節點上 LLAP 佇列的記憶體總數 = 節點上所有 YARN 容器的可用記憶體總數 x LLAP 佇列的容量百分比。  
針對 D14 v2，此值為 (100 GB x 0.85) = 85 GB。

LLAP 精靈容器大小可以下列方式計算：

**LLAP daemon 容器大小 = (LLAP 佇列在 workernode) 上的記憶體總計– (Tez AM 每個節點的記憶體) - (服務主機容器大小)**  
在其中一個背景工作節點上產生的叢集上，LLAP 服務) 只有一個服務主要 (應用程式主機。 針對計算目的，我們會針對每個背景工作節點考慮一個服務主機。  
針對 D14 v2 背景工作節點，HDI 4.0-建議的值為 (85 GB-4 GB-1 GB) # A2 = **80 GB**   
針對 HDI 3.6 的 (，建議值為 **79 GB** ，因為您應該保留其他 ~ 2 gb 的滑杆。 )   

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8.決定每個 LLAP 精靈的執行程式數目**  
Configuration： **_hive.llap.daemon.num.executors_* _、 _*_hive. llap_*_

_*_hive.llap.daemon.num.executors_*_：   
此設定會控制每個 LLAP 精靈中，可同時執行工作的執行程式數。 此值取決於虛擬核心數目、每個執行程式所使用的記憶體數量，以及 LLAP daemon 容器可用的總記憶體量。    針對每個背景工作節點，執行程式的數目可能會與120% 的可用虛擬核心。 不過，如果它不符合記憶體需求（根據每個執行程式所需的記憶體和 LLAP daemon 容器大小），則應該加以調整。

每個執行程式都相當於 Tez 容器，而且可能會耗用4GB 的 (Tez 容器大小) 的記憶體。 LLAP daemon 中的所有執行程式都會共用相同的堆積記憶體。 假設並非所有的執行程式都同時執行記憶體密集作業，您可以考慮每個執行程式的75% 的 Tez 容器大小 (4 GB) 。 如此一來，您可以為每個執行程式提供較少的記憶體，以增加執行程式的數目 (例如 3 GB 的) 以提高平行處理原則。 不過，建議您調整目標工作負載的這項設定。

D14 v2 VM 上具有 16 個 vCore。
針對 D14 v2，執行程式數目的建議值為每個考慮每個執行之3GB 的背景工作節點上 (16 虛擬核心 x 120% ) ~ = _ *19**。

**_Llap_*：這個值會指定執行程式的執行緒集區大小。由於執行程式是依指定固定，因此它會與每個 LLAP daemon 的執行程式數目相同。針對 D14 v2，建議值為 _* 19**。

#### <a name="9-determining-llap-daemon-cache-size"></a>**9.決定 LLAP 精靈快取大小**  
設定： **_llap。大小_* _

LLAP daemon 容器記憶體包含下列元件：_ 房間
*  執行程式所使用的堆積記憶體 (Xmx)
*  每個精靈的記憶體內部快取 (其堆積外的記憶體大小，當啟用 SSD 快取時則不適用)
*  記憶體內部快取中繼資料大小 (僅於啟用 SSD 快取時適用)

**空餘空間大小**：此大小表示用於 Java VM 額外負荷的部分堆積外記憶體 (Metaspace、執行緒堆疊、GC 資料結構等)。 一般而言，此額外負荷約為堆積大小的 6% (Xmx)。 爲以防萬一，您可將此值計算為 LLAP 精靈記憶體大小總數的 6%。  
針對 D14 v2，建議的值為 ceil (80 GB x 0.06) ~ = **4 GB**。  

**堆積大小 (Xmx)** ：此大小為所有執行程式可用的堆積記憶體數量。
堆積大小總計 = 執行程式的數目 x 3 GB  
針對 D14 v2，此值為 19 x 3 GB = **57 GB**  

`Ambari environment variable for LLAP heap size:`

![「LLAP 堆積大小」](./media/hive-llap-sizing-guide/LLAP_sizing_guide_llap_heap_size.png "LLAP 堆積大小")

停用 SSD 快取時，記憶體內部快取是從 LLAP daemon 容器大小取出空間大小和堆積大小之後剩餘的記憶體數量。

啟用 SSD 快取時，快取大小計算會有所不同。
設定 *hive.llap.io.allocator.mmap* = true 將會啟用 SSD 快取。
啟用 SSD 快取時，會使用部分記憶體來儲存 SSD 快取的中繼資料。 中繼資料會儲存在記憶體中，且預期會是 SSD 快取大小的8%。   
SSD 快取記憶體內部中繼資料大小 = LLAP daemon 容器大小- (的標頭空間 + 堆積大小)   
針對 D14 v2，HDI 4.0 的 SSD 快取記憶體內部中繼資料大小 = 80 GB- (4 GB + 57 GB) = **19 gb**  
針對 D14 v2，HDI 3.6 的 SSD 快取記憶體內部中繼資料大小 = 79 GB- (4 GB + 57 GB) = **18 GB**

只要指定儲存 SSD 快取中繼資料的可用記憶體大小，即可計算出能夠支援的 SSD 快取大小。  
SSD 快取的記憶體內部中繼資料大小 = LLAP daemon 容器大小- (的標頭空間 + 堆積大小) = 19 GB     
SSD 快取的大小 = SSD 快取的記憶體內部中繼資料大小 (19 GB) /0.08 (8%)   

針對 D14 v2 和 HDI 4.0，建議的 SSD 快取大小 = 19 GB/0.08 ~ = **237 GB**  
針對 D14 v2 和 HDI 3.6，建議的 SSD 快取大小 = 18 GB/0.08 ~ = **225 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10.調整對應聯結記憶體**   
Configuration： **_hive. >hive.auto.convert.join.noconditionaltask。 size_* _

請確定您已 _hive. >hive.auto.convert.join.noconditionaltask * 啟用，此參數才會生效。
這項設定會決定 Hive 優化器選取的 MapJoin 臨界值，以考慮其他執行程式的過度訂閱，使記憶體中的雜湊資料表有更多空間，以允許更多的地圖聯結轉換。 針對每個執行程式考慮3GB，此大小可能會對3GB 進行超額作業，但是某些堆積記憶體也可用於排序緩衝區、隨機緩衝區等等。   
因此針對 D14 v2，每個執行程式都有 3 GB 的記憶體，建議將此值設定為 **2048 MB**。  

 (附注：此值可能需要適用于您工作負載的調整。 將此值設得太低可能會無法使用自動轉換功能。 將它設定得太高可能會導致記憶體不足的例外狀況或 GC 暫停，因此可能會導致效能不良。 )   

#### <a name="11-number-of-llap-daemons"></a>**11. LLAP 的守護程式數目**
Ambari 環境變數： **_num_llap_nodes，num_llap_nodes_for_llap_daemons_* _  

_ *num_llap_nodes**-指定 Hive llap service 所使用的節點數目，這包括執行 llap DAEMON、Llap service Master 和 Tez Application Master (Tez AM) 的節點。  

![「LLAP 服務的節點數目」](./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes.png "LLAP 服務的節點數目")  

**num_llap_nodes_for_llap_daemons** 指定的節點數目，僅供 llap 的守護程式使用。 LLAP daemon 容器大小設定為 [最大符合] 節點，因此會在每個節點上產生一個 LLAP daemon。

![「LLAP 守護程式的節點數目」](./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes_for_llap_daemons.png "LLAP 守護程式的節點數目")

建議您將這兩個值保留為 Interactive Query 叢集中的背景工作節點數目。

### <a name="considerations-for-workload-management"></a>**工作負載管理的考慮**  
如果您想要啟用 LLAP 的工作負載管理，請確定您保留足夠的容量來讓工作負載管理如預期般運作。 工作負載管理需要設定自訂 YARN 佇列，也就是佇列以外的佇列 `llap` 。 根據您的工作負載需求，請務必將 llap 佇列與工作負載管理佇列中的總叢集資源容量分開。
工作負載管理會在啟用資源方案時，將 Tez 應用程式主機 (Tez AMs) 。
請注意：

* 藉由啟用資源方案所衍生的 Tez AMs 會取用工作負載管理佇列中的資源，如所指定 `hive.server2.tez.interactive.queue` 。  
* Tez AMs 的數目取決於 `QUERY_PARALLELISM` 資源計劃中所指定的值。  
* 工作負載管理在使用中之後，將不會使用 llap 佇列中的 Tez AMs。 只會使用工作負載管理佇列中的 Tez AMs 進行查詢協調。 `llap`停用工作負載管理時，會使用佇列中的 Tez AMs。
 
例如：叢集容量總計 = 100 GB 記憶體、除以 LLAP、工作負載管理和預設佇列，如下所示：
 - llap 佇列容量 = 70 GB
 - 工作負載管理佇列容量 = 20 GB
 - 預設佇列容量 = 10 GB

使用工作負載管理佇列容量中的 20 GB 時，資源方案可將 `QUERY_PARALLELISM` 值指定為5，這表示工作負載管理可以啟動五個 Tez AMs，每個都有 4 gb 的容器大小。 如果高於 `QUERY_PARALLELISM` 容量，您可能會看到部分 Tez 的 AMs 無反應處於 `ACCEPTED` 狀態。 Hiveserver2 Interactive 無法向未處於狀態的 Tez AMs 提交查詢片段 `RUNNING` 。


#### <a name="next-steps"></a>**後續步驟**
如果設定這些值無法解決問題，請瀏覽下列任一項目...

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。  

* ##### <a name="other-references"></a>**其他參考資料：**
  * [Configure other LLAP properties](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html) (設定其他 LLAP 屬性)  
  * [Configure the Hive server heap size](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html) (設定 Hive 伺服器堆積大小)  
  * [Map Join Memory Sizing for LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462) (調整 LLAP 的對應聯結記憶體大小)  
  * [Tez Execution Engine Properties](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html) (Tez 執行引擎屬性)  
  * [Hive LLAP deep dive](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893) (Hive LLAP 深入探討)