---
title: HDInsight 互動式查詢叢集 (LLAP) 調整大小指南
description: LLAP 調整大小指南
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: a9b86f09ade0d437436779ef3e4a17fcdede2cf0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663686"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Azure HDInsight 互動式查詢叢集 (Hive LLAP) 調整大小指南

本文件描述如何針對一般工作負載調整 HDInsight 互動式查詢叢集 (Hive LLAP 叢集) 的大小，藉此提升效能。 請注意，本文件中提供的建議為一般指南，特定的工作負載可能需要使用 特定的調整。

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**適用於 HDInsight 互動式查詢叢集 (LLAP) 的 Azure 預設 VM 類型**

| 節點類型      | 執行個體 | 大小     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 個 vCPU、56 GB RAM、400 GB SSD   |
| 背景工作   | **D14 v2**        | **16 個 vCPU、112 GB RAM、800 GB SSD**       |
| ZooKeeper   | A4 v2        | 4 個 vCPU、8 GB RAM、40 GB SSD       |

***注意：所有建議的設定值都是以 D14 v2 類型背景工作節點為基礎***  

### <a name="configuration"></a>**組態：**    
| 設定機碼      | 建議值  | 描述 |
| :---        |    :----:   | :---     |
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | 節點上所有 YARN 容器的指定記憶體總數 (以 MB 為單位) | 
| yarn.scheduler.maximum-allocation-mb | 102400 (MB) | 每個容器於 RM 所要求的最大配置 (以 MB 為單位)。 高於此值的記憶體要求將不會生效 |
| yarn.scheduler.maximum-allocation-vcores | 12 |每個容器於 Resource Manager 所要求的最大 CPU 核心數。 高於此值的要求將不會生效。 |
| yarn.nodemanager.resource.cpu-vcores | 12 | 每個 NodeManager 可為容器配置的 CPU 核心數。 |
| yarn.scheduler.capacity.root.llap.capacity | 80 (%) | LLAP 佇列的 YARN 容量配置  |
| tez.am.resource.memory.mb | 4096 (MB) | 要供 Tez AppMaster 使用的記憶體數量 (以 MB 為單位) |
| hive.server2.tez.sessions.per.default.queue | <背景工作節點數> |hive.server2.tez.default.queues 中每個具名佇列的工作階段數目。 此數目會對應於查詢協調器的數目 (Tez AM) |
| hive.tez.container.size | 4096 (MB) | 指定的 Tez 容器大小 (以 MB 為單位) |
| hive.llap.daemon.num.executors | 12 | 每個 LLAP 精靈的執行程式數目 | 
| hive.llap.io.threadpool.size | 12 | 執行程式的執行緒集區大小 |
| hive.llap.daemon.yarn.container.mb | 77824 (MB) | 個別 LLAP 精靈所使用的記憶體總數 (每個精靈所使用的記憶體、以 MB 為單位)
| hive.llap.io.memory.size | 235520 (MB) | 啟用 SSD 快取時，每個 LLAP 精靈的快取大小 (以 MB 為單位) |
| hive.auto.convert.join.noconditionaltask.size | 2048 (MB) | 執行對應聯結的記憶體大小 (以 MB 為單位) |

### <a name="llap-daemon-size-estimations"></a>**LLAP 精靈大小估算：**  

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1.決定節點上所有容器的總 YARN 記憶體配置**    
設定：***yarn.nodemanager.resource.memory-mb***  

此值表示每個節點上 YARN 容器可使用的記憶體最大總和 (以 MB 為單位)。 指定值應小於該節點上的實體記憶體總數。   
單一節點上所有 YARN 容器的記憶體總數 = [實體記憶體總數] – [OS + 其他服務所使用的記憶體]  
請將此值設定為約可用 RAM 大小的 90%。  
針對 D14 v2，建議值為  **102400 MB**。 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2.決定每個 YARN 容器要求的最大記憶體數量**  
設定：***yarn.scheduler.maximum-allocation-mb***

此值表示 Resource Manager 中每個容器要求的最大配置 (以 MB 為單位)。 高於此指定值的記憶體要求將不會生效。 Resource Manager 可藉由 *yarn.scheduler.minimum-allocation-mb* 的增量，將記憶體提供給容器，但無法超過 *yarn.scheduler.maximum-allocation-mb* 所指定的大小。 此指定值不應大於 *yarn.nodemanager.resource.memory-mb* 指定節點上所有容器的指定記憶體總數。    
針對 D14 v2 背景工作節點，建議值為 **102400 MB**

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3.決定每個 YARN 容器要求的最大 vCore 數**  
設定：***yarn.scheduler.maximum-allocation-vcores***  

此值表示 Resource Manager 中每個容器要求的最大虛擬 CPU 核心數。 高於此值的 vCore 數要求將不會生效。 此為 YARN 排程器的全域屬性。 針對 LLAP 精靈容器，此值可設定為可用 vCore 總數的 75%。 剩餘 25% 應保留給在背景工作節點中執行的 NodeManager、DataNode 和其他服務。  
D14 v2 VM 上具有 16 個 vCore，而 LLAP 精靈容器可使用此 16 個 vCore 中的75%。  
針對 D14 v2，建議值為 **12**。  

#### <a name="4-number-of-concurrent-queries"></a>**4.並行查詢數目**  
設定：***hive.server2.tez.sessions.per.default.queue***

此設定值會決定可同時啟動的 Tez 工作階段數目。 這些 Tez 工作階段會針對 "hive.server2.tez.default.queues" 所指定的每個佇列啟動。 該值會對應到 Tez AM (查詢協調器) 的數目。 建議設為與背景工作節點相同的數目。 Tez AM 數目可高於 LLAP 精靈節點的數目。 Tez AM 主要負責協調查詢執行，並將查詢計劃片段指派給對應的 LLAP 精靈來執行。 請將此值設為 LLAP 精靈節點的倍數，藉此達到更高的輸送量。  

預設 HDInsight 叢集具有四個 LLAP 精靈，在四個背景工作節點上執行，因此建議值為 **4**。  

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5.Tez Container 與 Tez Application Master 的大小**    
設定：***tez.am.resource.memory.mb, hive.tez.container.size***  

*tez.am.resource.memory.mb* - 定義 Tez Application Master 的大小。  
建議值為 **4096 MB**。
   
*hive.tez.container.size* - 定義提供給 Tez 容器的記憶體數量。 此值必須介於 YARN 最小容器大小 (*yarn.scheduler.minimum-allocation-mb*) 與 YARN 最大容器大小 (*yarn.scheduler.maximum-allocation-mb*) 之間。 LLAP 精靈執行程式會使用此值來限制每個執行程式的記憶體使用方式。  
建議值為 **4096 MB**。  

#### <a name="6-llap-queue-capacity-allocation"></a>**6.LLAP 佇列容量配置**   
設定：***yarn.scheduler.capacity.root.llap.capacity***  

此值表示指定給 LLAP 佇列的容量百分比。 根據 YARN 佇列的設定方式而定，針對不同的工作負載，容量配置可能會有不同值。 如果工作負載是唯讀作業，請將其設定為容量的 90%。 不過，如果工作負載是受控資料表的更新/刪除/合併的作業組合，則建議為 LLAP 佇列提供容量的 80%。 剩餘的 20% 容量可供其他工作使用 (例如壓縮等)，以從預設佇列配置容器。 如此一來，預設佇列中的工作便不會佔用 YARN 資源。    
針對 D14 v2 背景工作節點，建議將 LLAP 佇列的值設為 **80**。   
(針對唯讀工作負載，則可適度地增加至 90。)  

#### <a name="7-llap-daemon-container-size"></a>**7.LLAP 精靈容器大小**    
設定：***hive.llap.daemon.yarn.container.mb***  
   
LLAP 精靈會在每個背景工作節點上以 YARN 容器的形式執行。 LLAP 精靈容器的記憶體大小總數取決於下列因素：    
*  YARN 容器大小的設定 (yarn.scheduler.minimum-allocation-mb、yarn.scheduler.maximum-allocation-mb、yarn.nodemanager.resource.memory-mb)
*  節點上的 Tez AM 數
*  為節點上所有容器所設定的記憶體總數，以及 LLAP 佇列容量  

Tez Application Master (Tez AM) 所需的記憶體可以下列方式計算。  
針對 HDInsight 互動式叢集，根據預設，每個背景工作節點會有一個 Tez AM 作為查詢協調器。 您可根據所要執行並行查詢數來設定 Tez AM 的數目。
建議每個 Tez AM 都要有 4 GB 的記憶體。

每個節點的 Tez AM 記憶體 = [ceil(Tez AM 數目/LLAP 精靈節點數目)] x [Tez AM 容器大小]  
針對 D14 v2，預設設定具有四個 Tez AM 與四個 LLAP 精靈節點。  
則每個節點的 Tez AM 記憶體 = (ceil(4/4) x 4 GB) = 4 GB

在每個背景工作節點上，LLAP 佇列可用的記憶體總數可以下列方式計算：此值取決於節點上所有 YARN 容器可用的記憶體總數 (*yarn.nodemanager.resource.memory-mb*) 以及為 LLAP 佇列所設定的容量百分比 (*yarn.scheduler.capacity.root.llap.capacity*)。  
背景工作節點上 LLAP 佇列的記憶體總數 = 節點上所有 YARN 容器的可用記憶體總數 x LLAP 佇列的容量百分比。  
針對 D14 v2，此值為 [ 100 GB x 0.80 ] = 80 GB。

LLAP 精靈容器大小可以下列方式計算：

**LLAP 精靈容器大小 = [LLAP 佇列可用的記憶體總數] – [每個節點的 Tez AM 記憶體]**  
    
針對 D14 v2 背景工作節點，HDI 4.0 - 建議值為 (80 GB - 4 GB)) = **76 GB**   
(針對 HDI 3.6，建議值為 **74 GB**，因為需為 Slider AM 額外保留大約 2 GB。)  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8.決定每個 LLAP 精靈的執行程式數目**  
設定：***hive.llap.daemon.num.executors***、***hive.llap.io.threadpool.size***

***hive.llap.daemon.num.executors***：   
此設定會控制每個 LLAP 精靈中，可同時執行工作的執行程式數。 此值取決於 vCore 數、每個執行程式所提供的記憶體數量，以及可供 LLAP 精靈使用的記憶體總數。 通常，會盡可能地將此值設為與 vCore 數相近的值。
D14 v2 VM 上具有 16 個 vCore。 不過，您不應將所有的 vCore 都用於此處，因為 NodeManager、DataNode、計量監視器等其他服務也需要使用到一部分的 vCore。 

如果需要調整執行程式的數目，建議按照 *hive.tez.container.size* 中所規定、考慮為每個執行程式分配 4 GB 的記憶體，並確定所有執行程式所需的記憶體總數不超過 LLAP 精靈容器可用的記憶體總數。  
此值最多可設定為節點上可用 vCore 總數的 75%。  
針對 D14 v2，建議值為 (.75 X 16) = **12**

***hive.llap.io.threadpool.size***：   
此值會指定執行程式的執行緒集區大小。 由於執行程式依規定為固定，所以該值會與每個 LLAP 精靈的執行程式數相同。   
針對 D14 v2，建議值為 **12**。

#### <a name="9-determining-llap-daemon-cache-size"></a>**9.決定 LLAP 精靈快取大小**  
設定：***hive.llap.io.memory.size***

LLAP 精靈容器記憶體是由下列元件所組成：
*  空餘空間
*  執行程式所使用的堆積記憶體 (Xmx)
*  每個精靈的記憶體內部快取 (其堆積外的記憶體大小，當啟用 SSD 快取時則不適用)
*  記憶體內部快取中繼資料大小 (僅於啟用 SSD 快取時適用)

**空餘空間大小**：此大小表示用於 Java VM 額外負荷的部分堆積外記憶體 (Metaspace、執行緒堆疊、GC 資料結構等)。 一般而言，此額外負荷約為堆積大小的 6% (Xmx)。 爲以防萬一，您可將此值計算為 LLAP 精靈記憶體大小總數的 6%。  
針對 D14 v2，建議值為 ceil(76 GB x 0.06) ~= **5 GB**。  

**堆積大小 (Xmx)** ：此大小為所有執行程式可用的堆積記憶體數量。
堆積大小總數 = 執行程式數 x 4 GB  
針對 D14 v2，此值為 12 x 4 GB = **48 GB**  

如果停用 SSD 快取，則記憶體內部快取為 LLAP 精靈容器大小減去空餘空間大小與堆積大小後剩餘的記憶體數量。

啟用 SSD 快取時，快取大小計算會有所不同。
設定 *hive.llap.io.allocator.mmap* = true 將會啟用 SSD 快取。
啟用 SSD 快取時，會使用部分記憶體來儲存 SSD 快取的中繼資料。 中繼資料會儲存在記憶體中，且約為 SSD 快取大小的 10%。   
SSD 快取記憶體內部中繼資料大小 = [LLAP 精靈容器大小] - [空餘空間 + 堆積大小]  
針對 D14 v2，使用 HDI 4.0 的 SSD 快取記憶體內部中繼資料大小 = [ 76 GB ] - [ 5 GB + 48 GB ] = **23 GB**  
針對 D14 v2，使用 HDI 3.6 的 SSD 快取記憶體內部中繼資料大小 = [ 76 GB ] - [ 5 GB + 48 GB + 2 GB Slider ] = **21 GB**

只要指定儲存 SSD 快取中繼資料的可用記憶體大小，即可計算出能夠支援的 SSD 快取大小。  
SSD 快取的記憶體內部中繼資料大小 = SSD 快取大小的 10%       
SSD 快取的大小 = SSD 快取的記憶體內部中繼資料大小 x 10  

針對 D14 v2 及 HDI 4.0，建議的 SSD 快取大小 = 23 GB x 10 = **230 GB**  
針對 D14 v2 及 HDI 3.6，建議的 SSD 快取大小 = 21 GB x 10 = **210 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10.調整對應聯結記憶體**   
設定：***hive.auto.convert.join.noconditionaltask.size***

請確定已啟用 *hive.auto.convert.join.noconditionaltask*，此參數才會生效。
此設定可讓使用者依記憶體指定資料表大小，以便進行對應聯結。 如果 n 向聯結的 n-1 個資料表或分割區大小總和小於設定的值，則會選擇對應聯結。 LLAP 執行程式記憶體大小應用於為自動轉換為對應聯結計算閾值。
每個執行程式都假設具有 4 GB 的堆積大小，但並非全部都可用於對應聯結。 某些堆積記憶體也會被其他作業用於排序緩衝區、隨機緩衝區、雜湊表等。 因此，建議為對應聯結提供 4 GB 堆積記憶體的 50%。  
注意:此值可能需要針對工作負載進行調整。 將此值設得太低可能會無法使用自動轉換功能。 如果設定太高，可能會導致記憶體不足的例外狀況或 GC 暫停，因而造成效能不良。  
針對 D14 v2，使用每個具有 4 GB 記憶體的執行程式，建議將此值設定為 **2048 MB**。


#### <a name="next-steps"></a>**後續步驟**
如果設定這些值無法解決問題，請瀏覽下列任一項目...

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。  

* ##### <a name="other-references"></a>**其他參考資料：**
  * [Configure other LLAP properties](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html) (設定其他 LLAP 屬性)  
  * [Configure the Hive server heap size](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html) (設定 Hive 伺服器堆積大小)  
  * [Map Join Memory Sizing for LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462) (調整 LLAP 的對應聯結記憶體大小)  
  * [Tez Execution Engine Properties](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html) (Tez 執行引擎屬性)  
  * [Hive LLAP deep dive](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893) (Hive LLAP 深入探討)  
