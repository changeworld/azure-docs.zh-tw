---
title: 如何為 Azure HDInsight 群集選擇正確的 VM 大小
description: 瞭解如何為 HDInsight 群集選擇正確的 VM 大小。
keywords: vm 大小、群集大小、群集配置
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: a21e8d6c76c93b3084619c09f6a7664a25c1929c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73682213"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>為 Azure HDInsight 群集選擇合適的 VM 大小

本文討論如何為 HDInsight 群集中的各個節點選擇正確的 VM 大小。 

首先瞭解虛擬機器的屬性（如 CPU 處理、RAM 大小和網路延遲）將如何影響工作負載的處理。 接下來，考慮應用程式及其如何與針對不同的 VM 系列進行優化。 確保要使用的 VM 系列與計畫部署的群集類型相容。 有關每種群集類型受支援的所有受支援和推薦的 VM 大小的清單，請參閱[Azure HDInsight 支援的節點配置](hdinsight-supported-node-configuration.md)。 最後，您可以使用基準測試過程來測試一些示例工作負載，並檢查該系列中哪個 SKU 適合您的。

有關規劃群集其他方面（如選擇存儲類型或群集大小）的詳細資訊，請參閱[HDInsight 群集的容量規劃](hdinsight-capacity-planning.md)。

## <a name="vm-properties-and-big-data-workloads"></a>VM 屬性和大資料工作負載

VM 大小與類型是由 CPU 處理能力、RAM 大小和網路延遲所決定：

- CPU：VM 大小會規定核心的數目。 核心越多，每個節點可達到的平行計算程度就越大。 此外，某些 VM 類型具有更快的核心。

- RAM：VM 大小也會決定 VM 可用的 RAM 數量。 針對將資料儲存在記憶體以進行處理的工作負載，並非從磁碟讀取，請確保背景工作節點有足夠的記憶體來容納資料。

- 網路：對於大多數群集類型，群集處理的資料不在本地磁片上，而是在外部存儲服務（如資料湖存儲或 Azure 存儲）中。 請考慮節點 VM 與儲存體服務之間的網路頻寬和輸送量。 可供 VM 使用的網路頻寬通常會隨著較大的大小而增加。 如需詳細資訊，請參閱 [VM 大小概觀](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)。

## <a name="understanding-vm-optimization"></a>瞭解 VM 優化

Azure 中的虛擬機器系列經過優化以適應不同的用例。 在下表中，您可以找到一些最流行的用例和與之匹配的 VM 系列。

| 類型                     | 大小           |    描述       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [入門級](../virtual-machines/linux/sizes-general.md)          | A， Av2  | CPU 性能和記憶體配置最適合入門級工作負載，如開發和測試。 它們經濟，並提供一個低成本的選項，以開始使用 Azure。 |
| [一般用途](../virtual-machines/linux/sizes-general.md)          | D， DSv2， Dv2  | CPU 與記憶體的比例平均。 適用於測試和開發、小型至中型資料庫，以及低至中流量 Web 伺服器。 |
| [計算最佳化](../virtual-machines/linux/sizes-compute.md)        | F           | CPU 與記憶體的比例高。 適用於中流量 Web 伺服器、網路設備、批次處理，以及應用程式伺服器。        |
| [記憶體優化](../virtual-machines/linux/sizes-memory.md)         | Esv3， Ev3  | 記憶體與 CPU 的比例高。 適用於關聯式資料庫伺服器、中型至大型快取，以及記憶體內部分析。                 |

- 有關跨 HDInsight 支援區域的可用 VM 實例定價的資訊，請參閱[HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="cost-saving-vm-types-for-light-workloads"></a>為輕型工作負載節省 VM 類型的成本

如果您有光處理要求[，F 系列](https://azure.microsoft.com/blog/f-series-vm-size/)可能是開始使用 HDInsight 的好選擇。 F 系列的每小時訂價較低，在 Azure 產品組合中，就每一 vCPU 的「Azure 計算單位」(ACU) 而言，具有最佳的價格/性能比表現。

下表描述了可以使用 Fsv2 系列 VM 創建的群集類型和節點類型。

| 叢集類型 | 版本 | 輔助節點 | 頭節點 | 動物園管理員節點 |
|---|---|---|---|---|
| Spark | 全部 | F4 及以上 | 否 | 否 |
| Hadoop | 全部 | F4 及以上 | 否 | 否 |
| Kafka | 全部 | F4 及以上 | 否 | 否 |
| hbase | 全部 | F4 及以上 | 否 | 否 |
| LLAP | disabled | 否 | 否 | 否 |
| Storm | disabled | 否 | 否 | 否 |
| ML 服務 | 僅限 HDI 3.6 | F4 及以上 | 否 | 否 |

要查看每個 F 系列 SKU 的規格，請參閱[F 系列 VM 大小](https://azure.microsoft.com/blog/f-series-vm-size/)。

## <a name="benchmarking"></a>效能評定

基準測試是在不同 VM 上運行類比工作負載的過程，以衡量它們對生產工作負載的性能。 

有關 VM SKU 和群集大小的基準測試的詳細資訊，請參閱[Azure HDInsight 中的群集容量規劃](hdinsight-capacity-planning.md#choose-the-vm-size-and-type)。

## <a name="next-steps"></a>後續步驟

- [Azure HDInsight 支援的節點配置](hdinsight-supported-node-configuration.md)
- [Azure 中的 Linux 虛擬機器大小](../virtual-machines/linux/sizes.md)
