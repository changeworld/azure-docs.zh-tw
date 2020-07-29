---
title: Azure HDInsight 中的互動式查詢叢集調整大小指南
description: Azure HDInsight 中的互動式查詢調整大小指南
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: a81eff1dcf48996c319933aa4dd46170043b943b
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663646"
---
# <a name="interactive-query-cluster-sizing-guide-in-azure-hdinsight"></a>Azure HDInsight 中的互動式查詢叢集調整大小指南

本文件描述如何針對一般工作負載調整 HDInsight 互動式查詢叢集 (LLAP) 的大小，藉此提升效能。 本文件中提供的建議為一般指南，特定的工作負載可能需要使用特定的調整。

## <a name="default-vm-types-for-interactive-query"></a>互動式查詢的預設 VM 類型

| 節點類型 | 執行個體 | 大小 |
|---|---|---|
| Head | D13 v2 | 8 個 VCPU、56-GB RAM、400 GB SSD |
| 背景工作 | D14 v2 | 16 個 VCPU、112-GB RAM、800 GB SSD |
| ZooKeeper | A4 v2 | 4 個 VCPU、8 GB RAM、40 GB SSD |

## <a name="recommended-configurations"></a>建議設定

建議的設定值都是以 D14 v2 類型背景工作節點為基礎。

| Key | 值 | 描述 |
|---|---|---|
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | 節點上所有 YARN 容器的指定記憶體總數 (以 MB 為單位)。 |
| yarn.scheduler.maximum-allocation-mb | 102400 (MB) | 每個容器於 RM 所要求的最大配置 (以 MB 為單位)。 高於此值的記憶體要求將不會生效。 |
| yarn.scheduler.maximum-allocation-vcores | 12 |每個容器於 Resource Manager 所要求的最大 CPU 核心數。 高於此值的要求將不會生效。 |
| yarn.scheduler.capacity.root.llap.capacity | 90% | LLAP 佇列的 YARN 容量配置。  |
| hive.server2.tez.sessions.per.default.queue | number_of_worker_nodes |hive.server2.tez.default.queues 中每個具名佇列的工作階段數目。 此數目會對應於查詢協調器的數目 (Tez AM)。 |
| tez.am.resource.memory.mb | 4096 (MB) | 要供 Tez AppMaster 使用的記憶體數量 (以 MB 為單位)。 |
| hive.tez.container.size | 4096 (MB) | 指定的 Tez 容器大小 (以 MB 為單位)。 |
| hive.llap.daemon.num.executors | 12 | 每個 LLAP 精靈的執行程式數目。 |
| hive.llap.io.threadpool.size | 12 | 執行程式的執行緒集區大小。 |
| hive.llap.daemon.yarn.container.mb | 86016 (MB) | 個別 LLAP 精靈所使用的記憶體總數 (每個精靈所使用的記憶體、以 MB 為單位)。|
| hive.llap.io.memory.size | 409600 (MB) | 啟用 SSD 快取時，每個 LLAP 精靈的快取大小 (以 MB 為單位)。 |
| hive.auto.convert.join.noconditionaltask.size | 2048 (MB) | 執行對應聯結的記憶體大小 (以 MB 為單位)。 |

## <a name="llap-daemon-size-estimations"></a>LLAP 精靈大小估算  

### <a name="yarnnodemanagerresourcememory-mb"></a>yarn.nodemanager.resource.memory-mb

此值表示每個節點上 YARN 容器使用的記憶體最大總和 (以 MB 為單位)。 會指定此節點上 YARN 可以使用的記憶體數量，因此這個值應該小於該節點的總記憶體。

設定此值 = [節點上的實體記憶體總計] – [作業系統 + 其他服務的的記憶體]。

建議請將此值設定為約可用 RAM 大小的 90%。 針對 D14 v2，建議值為 **102400 MB**。

### <a name="yarnschedulermaximum-allocation-mb"></a>yarn.scheduler.maximum-allocation-mb

此值表示 Resource Manager 中每個容器要求的最大配置 (以 MB 為單位)。 高於此指定值的記憶體要求將不會生效。 Resource Manager 只能以 `yarn.scheduler.minimum-allocation-mb` 的遞增形式提供記憶體給容器，而且不能超過 `yarn.scheduler.maximum-allocation-mb` 指定的大小。 此值不應該超過節點的指定總記憶體，這是由 `yarn.nodemanager.resource.memory-mb` 所指定。

針對 D14 v2 背景工作角色節點，建議值為 **102400 MB**

### <a name="yarnschedulermaximum-allocation-vcores"></a>yarn.scheduler.maximum-allocation-vcores

此設定表示 Resource Manager 中每個容器要求的最大虛擬 CPU 核心數。 要求比這個設定更高的值將不會生效。 此設定為 YARN 排程器的全域屬性。 針對 LLAP 精靈容器，此值可設定為可用虛擬核心 (VCORE) 總數的 75%。 剩餘 25% 應保留給在背景工作節點中執行的 NodeManager、DataNode 和其他服務。  

針對 D14 v2 背景工作角色節點，有 16 個 VCORE，可以指定 16 個 VCORE 的 75%。 因此，LLAP 精靈容器的建議值是 **12**。

### <a name="hiveserver2tezsessionsperdefaultqueue"></a>hive.server2.tez.sessions.per.default.queue

此設定值會決定針對 `hive.server2.tez.default.queues` 所指定的每個佇列，應該同時啟動的 Tez 工作階段數目。 該值會對應到 Tez AM (查詢協調器) 的數目。 建議設為與背景工作角色節點相同的數目，讓每個節點具有一個 Tez AM。 Tez AM 數目可高於 LLAP 精靈節點的數目。 Tez AM 主要負責協調查詢執行，並將查詢計劃片段指派給對應的 LLAP 精靈來執行。 建議將此值設為 LLAP 精靈節點的倍數，藉此達到更高的輸送量。  

預設 HDInsight 叢集具有四個 LLAP 精靈，在四個背景工作角色節點上執行，因此建議值為 **4**。  

### <a name="tezamresourcememorymb-hivetezcontainersize"></a>tez.am.resource.memory.mb, hive.tez.container.size

`tez.am.resource.memory.mb` 會定義 Tez 應用程式主要大小。  
建議值為 **4096 MB**。

`hive.tez.container.size` 會定義提供給 Tez 容器的記憶體數量。 此值必須設定為 YARN 最小容器大小 (`yarn.scheduler.minimum-allocation-mb`) 與 YARN 最大容器大小 (`yarn.scheduler.maximum-allocation-mb`) 之間的值。  
建議您將其設定為 **4096 MB**。  

考量到每個容器一個處理器，一般規則是讓其小於每個處理器的記憶體數量。 在針對 LLAP 精靈提供記憶體之前，針對節點上 Tez AM 的數目 `Rreserve` 記憶體。 舉例來說，如果您在每個節點上使用兩個 Tez AM (各 4 GB)，針對 LLAP 精靈提供 90 GB 當中的 82 GB，為兩個 Tez AM 保留 8 GB。

### <a name="yarnschedulercapacityrootllapcapacity"></a>yarn.scheduler.capacity.root.llap.capacity

此值表示指定給 LLAP 佇列的容量百分比。 HDInsights 互動式查詢叢集針對 LLAP 佇列提供總容量的 90%，而其餘 10% 則是設定為其他容器配置的預設佇列。  
針對 D14 v2 背景工作角色節點，針對 LLAP 佇列的建議值設為 **90**。

### <a name="hivellapdaemonyarncontainermb"></a>hive.llap.daemon.yarn.container.mb

LLAP 精靈的記憶體大小總數取決於下列元件：

* YARN 容器大小的設定 (`yarn.scheduler.maximum-allocation-mb`、`yarn.scheduler.maximum-allocation-mb`、`yarn.nodemanager.resource.memory-mb`)

* 執行程式所使用的堆積記憶體 (Xmx)

    取出空餘空間大小之後，可以使用的 RAM 數量。  
    針對 D14 v2，HDI 4.0 - 此值為 (86 GB - 6 GB) = 80 GB  
    針對 D14 v2，HDI 3.6 - 此值為 (84 GB - 6 GB) = 78 GB

* 每個精靈的堆積外記憶體內部快取 (hive.llap.io.memory.size)

* 空餘空間

    其為用於 Java VM 額外負荷的部分堆積外記憶體 (Metaspace、執行緒堆疊、GC 資料結構等)。 所觀察到的這部分是大約堆積大小 (Xmx) 的 6%。 爲以防萬一，您可將其計算為 LLAP 精靈記憶體大小總數的 6%。 因為在啟用 SSD 快取時有可能發生，所以會允許 LLAP 精靈使用所有可用的記憶體內部空間，僅供堆積使用。  
    針對 D14 v2，建議值為 ceil(86 GB x 0.06) ~= **6 GB**。  

每個精靈的記憶體 = [記憶體內部快取大小] + [堆積大小] + [空餘空間]。

其計算方式如下：

每個節點的 Tez AM 記憶體 = [(Tez AM 數目/LLAP 精靈節點數目) * Tez AM 大小]。
LLAP 精靈容器大小 = [ 90% 的 YARN 最大容器記憶體] – [每個節點的 Tez AM 記憶體]。

針對 D14 v2 背景工作角色節點，HDI 4.0 - 建議值為 (90 - (1/1 * 4 GB)) = **86 GB**。
(針對 HDI 3.6，建議值為 **84 GB**，因為需為 Slider AM 保留大約 2 GB。)  

### <a name="hivellapiomemorysize"></a>hive.llap.io.memory.size

此設定是可作為 LLAP 精靈快取的記憶體數量。 LLAP 精靈可以使用 SSD 作為快取。 設定 `hive.llap.io.allocator.mmap` = true 將會啟用 SSD 快取。 D14 v2 隨附大約 800 GB 的 SSD，且 SSD 快取預設針對互動式查詢叢集 (LLAP) 啟用。 其設定為針對堆積外快取使用 50% 的 SSD 空間。

針對 D14 v2，建議值為 **409600 MB**。  

針對未啟用 SSD 快取的其他 VM，可以從針對 LLAP 快取提供部分可用 RAM 當中受益，以達到更佳的效能。 調整 LLAP 精靈的總記憶體大小，如下所示：  

LLAP 精靈總記憶體 = [LLAP 快取大小] + [堆積大小] + [空餘空間]。

建議您調整最適合您工作負載的快取大小和堆積大小。  

### <a name="hivellapdaemonnumexecutors"></a>hive.llap.daemon.num.executors

此設定會控制每個 LLAP 精靈中，可同時執行工作的執行程式數。 此值是可用 VCORE 數目的餘額、每個執行程式所提供的記憶體數量，以及可供 LLAP 精靈使用的總記憶體。 通常，會盡可能地將此值設為與核心數相近的值。

對於 D14 v2，有 16 個可用的 VCORE，不過並非所有 VCORE 都可以指定。 背景工作角色節點也會執行其他服務，例如 NodeManager、DataNode 和計量監視器，這需要一部分的可用 VCORE。 此值最多可設定為節點上可用 VCORE 總數的 75%。

針對 D14 v2，建議值為 (.75 X 16) = **12**

建議您為每個執行程式保留大約 6 GB 的堆積空間。 根據可用 LLAP 精靈大小，以及每個節點的可用 VCORE 數目，調整您的執行程式數目。  

### <a name="hivellapiothreadpoolsize"></a>hive.llap.io.threadpool.size

此值會指定執行程式的執行緒集區大小。 由於執行程式依規定為固定，所以該值會與每個 LLAP 精靈的執行程式數相同。

針對 D14 v2，建議您將此值設定為 **12**。

此設定不能超過 `yarn.nodemanager.resource.cpu-vcores` 值。

### <a name="hiveautoconvertjoinnoconditionaltasksize"></a>hive.auto.convert.join.noconditionaltask.size

請確定您已啟用 `hive.auto.convert.join.noconditionaltask`，此參數才會生效。 此設定可讓使用者依記憶體指定資料表大小，以便進行對應聯結。 如果 n 向聯結的 n-1 個 `tables/partitions` 大小總和小於設定的值，則會選擇對應聯結。 LLAP 執行程式記憶體大小應用於為自動轉換為對應聯結計算閾值。

針對 D14 v2，建議您將此值設定為 **2048 MB**。

建議您將此值調整為適合您工作負載的值，因為將此值設得太低可能會無法使用自動轉換功能。 將其設定得太高可能會導致 GC 暫停，這可能會對查詢效能造成負面影響。

## <a name="next-steps"></a>後續步驟

* [閘道指導方針](gateway-best-practices.md)
* [解釋 Apache Tez 記憶體微調 - 逐步解說](https://community.cloudera.com/t5/Community-Articles/Demystify-Apache-Tez-Memory-Tuning-Step-by-Step/ta-p/245279)
* [調整 LLAP 的對應聯結記憶體大小](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)
* [LLAP - 單一頁面架構概觀](https://community.cloudera.com/t5/Community-Articles/LLAP-a-one-page-architecture-overview/ta-p/247439)
* [Hive LLAP 深入探討](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)
