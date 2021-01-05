---
title: HC 系列 VM 總覽-Azure 虛擬機器 |Microsoft Docs
description: 瞭解 Azure 中 HC 系列 VM 大小的預覽支援。
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: b9fe978da9accd28ea0e538f458325f10c9c5d8d
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831885"
---
# <a name="hc-series-virtual-machine-overview"></a>HC 系列虛擬機器總覽

將 Intel 可調整規模處理器上的 HPC 應用程式效能最大化，需要一種方法來處理這個新架構上的放置。 在這裡，我們將概述如何在適用于 HPC 應用程式的 Azure HC 系列 Vm 上執行它。 我們將使用 "pNUMA" 一詞來參考實體 NUMA 網域，並使用 "Vnuma 解決方案" 來參考虛擬 NUMA 網域。 同樣地，我們將使用 "pCore" 一詞來指稱實體 CPU 核心，並使用 "vCore" 來指稱虛擬化 CPU 核心。

實際上， [HC 系列](../../hc-series.md) 伺服器是 2 * 24 核心 Intel 的頂級白金 8168 cpu，總共有48個實體核心。 每個 CPU 都是單一 pNUMA 網域，並可整合存取六個 DRAM 通道。 Intel 最強的白金級 Cpu 有4倍以上的 L2 快取，而不是先前的世代 (256 KB/核心-> 1 MB/核心) ，同時也會減少 L3 快取與先前 Intel Cpu (2.5 MB/核心-> 1.375 MB/核心) 。

上述拓撲也會攜帶到 HC 系列的虛擬程式設定。 為了提供空間讓 Azure 虛擬機器在不幹擾 VM 的情況下運作，我們保留 pCores 0-1 和 24-25 (也就是每個通訊端上的前2個 pCores) 。 然後，將所有剩餘的核心指派給 VM pNUMA 網域。 因此，VM 會看到：

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` 每個 VM 的核心

VM 不知道 pCores 0-1 和24-25 未提供給它。 因此，它會公開每個 Vnuma 解決方案，就像原生有22個核心一樣。

Intel 以上的白金級、金級和銀級 Cpu 也引進了一個在 CPU 通訊端內部和外部進行通訊的內部網路2D 網格網路。 強烈建議將程式釘選以獲得最佳效能與一致性。 進程釘選可在 HC 系列 Vm 上運作，因為基礎晶片會依原樣公開給來賓 VM。

下圖顯示保留給 Azure 虛擬程式和 HC 系列 VM 的核心隔離。

![為 Azure 虛擬程式和 HC 系列 VM 保留的核心隔離](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>硬體規格

| 硬體規格          | HC 系列 VM                     |
|----------------------------------|----------------------------------|
| 核心                            | 44 (HT 已停用)                  |
| CPU                              | Intel 的頂級白金8168         |
| CPU 頻率 (非 AVX)           | 3.7 GHz (單一核心) ，2.7-3.4 GHz (所有核心)  |
| 記憶體                           | 8 GB/核心 (352 總計)             |
| 本機磁碟                       | 700 GB SSD                       |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5   |
| 網路                          | 50 Gb 乙太網路 (40 Gb 可用) Azure 第二代 SmartNIC    |

## <a name="software-specifications"></a>軟體規格

| 軟體規格     |HC 系列 VM           |
|-----------------------------|-----------------------|
| MPI 作業大小上限            | 13200核心 (具有 singlePlacementGroup = true 的單一虛擬機器擴展集中的 300 Vm)   |
| MPI 支援                 | HPC-X、Intel MPI、OpenMPI、MVAPICH2、MPICH、Platform MPI  |
| 其他架構       | 整合通訊 X、libfabric、PGAS |
| Azure 儲存體支援       | 標準和 Premium 磁片 (最多4個磁片)  |
| SRIOV RDMA 的 OS 支援   | CentOS/RHEL 7.6 +、SLES 12 SP4 +、WinServer 2016 +  |
| 協調器支援        | CycleCloud，Batch  |

## <a name="next-steps"></a>後續步驟

- 深入瞭解 Intel 以上的 [SP 架構](https://software.intel.com/content/www/us/en/develop/articles/intel-xeon-processor-scalable-family-technical-overview.html)。
- 請參閱 [Azure 運算技術社群部落格](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)的最新公告和一些 HPC 範例和結果。
- 如需執行中 HPC 工作負載較高階的架構檢視，請參閱 [Azure 上的高效能運算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
