---
title: 如何為您的 Azure HDInsight 叢集選取正確的 VM 大小
description: 瞭解如何為您的 HDInsight 叢集選取正確的 VM 大小。
keywords: vm 大小, 叢集大小, 叢集設定
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: a21e8d6c76c93b3084619c09f6a7664a25c1929c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "73682213"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>為您的 Azure HDInsight 叢集選取適當的 VM 大小

本文討論如何為 HDInsight 叢集中的各種節點選取正確的 VM 大小。 

一開始，請先瞭解虛擬機器的屬性（例如 CPU 處理、RAM 大小和網路延遲）會如何影響您的工作負載處理。 接下來，請考慮您的應用程式，以及它如何與已優化的不同 VM 系列進行比對。 請確定您想要使用的 VM 系列與您打算部署的叢集類型相容。 如需每個叢集類型的所有受支援和建議的 VM 大小清單，請參閱[Azure HDInsight 支援的節點](hdinsight-supported-node-configuration.md)設定。 最後，您可以使用基準測試程式來測試一些範例工作負載，並檢查該系列內的哪個 SKU 適合您。

如需有關規劃叢集其他層面（例如選取儲存體類型或叢集大小）的詳細資訊，請參閱[HDInsight 叢集的容量規劃](hdinsight-capacity-planning.md)。

## <a name="vm-properties-and-big-data-workloads"></a>VM 屬性和海量資料工作負載

VM 大小與類型是由 CPU 處理能力、RAM 大小和網路延遲所決定：

- CPU：VM 大小會規定核心的數目。 核心越多，每個節點可達到的平行計算程度就越大。 此外，某些 VM 類型具有更快的核心。

- RAM：VM 大小也會決定 VM 可用的 RAM 數量。 針對將資料儲存在記憶體以進行處理的工作負載，並非從磁碟讀取，請確保背景工作節點有足夠的記憶體來容納資料。

- 網路：對於大部分的叢集類型而言，由叢集處理的資料不在本機磁片上，而是在外部儲存體服務中，例如 Data Lake Storage 或 Azure 儲存體。 請考慮節點 VM 與儲存體服務之間的網路頻寬和輸送量。 可供 VM 使用的網路頻寬通常會隨著較大的大小而增加。 如需詳細資訊，請參閱 [VM 大小概觀](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)。

## <a name="understanding-vm-optimization"></a>瞭解 VM 優化

Azure 中的虛擬機器系列已優化，以符合不同的使用案例。 在下表中，您可以找到一些最受歡迎的使用案例和與它們相符的 VM 系列。

| 類型                     | 大小           |    描述       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [專案層級](../virtual-machines/linux/sizes-general.md)          | A、Av2  | CPU 效能和記憶體設定最適合進入層級的工作負載，例如開發和測試。 它們是經濟實惠的，可提供低成本的選項來開始使用 Azure。 |
| [一般用途](../virtual-machines/linux/sizes-general.md)          | D、DSv2、Dv2  | CPU 與記憶體的比例平均。 適用於測試和開發、小型至中型資料庫，以及低至中流量 Web 伺服器。 |
| [計算最佳化](../virtual-machines/linux/sizes-compute.md)        | F           | CPU 與記憶體的比例高。 適用於中流量 Web 伺服器、網路設備、批次處理，以及應用程式伺服器。        |
| [記憶體最佳化](../virtual-machines/linux/sizes-memory.md)         | Esv3、Ev3  | 記憶體與 CPU 的比例高。 適用於關聯式資料庫伺服器、中型至大型快取，以及記憶體內部分析。                 |

- 如需有關 HDInsight 支援區域中可用 VM 實例價格的詳細資訊，請參閱[Hdinsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="cost-saving-vm-types-for-light-workloads"></a>成本節省輕量工作負載的 VM 類型

如果您有很輕的處理需求， [F 系列](https://azure.microsoft.com/blog/f-series-vm-size/)可能是開始使用 HDInsight 的絕佳選擇。 F 系列的每小時訂價較低，在 Azure 產品組合中，就每一 vCPU 的「Azure 計算單位」(ACU) 而言，具有最佳的價格/性能比表現。

下表描述叢集類型和節點類型，您可以使用 Fsv2 系列 Vm 來建立它們。

| 叢集類型 | 版本 | 背景工作節點 | 前端節點 | Zookeeper 節點 |
|---|---|---|---|---|
| Spark | 全部 | F4 和更新版本 | 否 | 否 |
| Hadoop | 全部 | F4 和更新版本 | 否 | 否 |
| Kafka | 全部 | F4 和更新版本 | 否 | 否 |
| hbase | 全部 | F4 和更新版本 | 否 | 否 |
| LLAP | disabled | 否 | 否 | 否 |
| Storm | disabled | 否 | 否 | 否 |
| ML 服務 | 僅限 HDI 3。6 | F4 和更新版本 | 否 | 否 |

若要查看每個 F 系列 SKU 的規格，請參閱[f 系列 VM 大小](https://azure.microsoft.com/blog/f-series-vm-size/)。

## <a name="benchmarking"></a>效能評定

效能評定是在不同的 Vm 上執行模擬工作負載的程式，以測量它們針對生產環境工作負載的運作程度。 

如需 VM Sku 和叢集大小之基準測試的詳細資訊，請參閱[Azure HDInsight 中的叢集容量規劃](hdinsight-capacity-planning.md#choose-the-vm-size-and-type)。

## <a name="next-steps"></a>後續步驟

- [Azure HDInsight 支援的節點設定](hdinsight-supported-node-configuration.md)
- [Azure 中的 Linux 虛擬機器大小](../virtual-machines/linux/sizes.md)
