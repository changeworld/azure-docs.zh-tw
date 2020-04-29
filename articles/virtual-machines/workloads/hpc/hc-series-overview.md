---
title: HC 系列 VM 總覽-Azure 虛擬機器 |Microsoft Docs
description: 瞭解 Azure 中 HC 系列 VM 大小的預覽支援。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: a4cd74c9c85ee7413cde9f0fb4cf3ffb54c9b3d0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76906738"
---
# <a name="hc-series-virtual-machine-overview"></a>HC 系列虛擬機器總覽

在 Intel 可延展的處理器上最大化 HPC 應用程式效能，需要審慎的方法來處理此新架構上的放置。 在這裡，我們將概述其在 HPC 應用程式的 Azure HC 系列 Vm 上的實作為。 我們會使用 "pNUMA" 一詞來參考實體 NUMA 網域，並使用 "Vnuma 解決方案" 來參考虛擬化的 NUMA 網域。 同樣地，我們將使用「pCore」一詞來指稱實體 CPU 核心，而「vCore」則是指虛擬化的 CPU 核心。

實際上，HC 伺服器是 2 * 24 核心 Intel 強白金 8168 Cpu，總共有48個實體核心。 每個 CPU 都是單一的 pNUMA 網域，並可統一存取六個 DRAM 通道。 Intel 強式白金 Cpu 的功能比先前的世代（256 KB/核心-> 1 MB/核心）多出4倍的 L2 快取，同時也減少了 L3 快取與先前的 Intel Cpu （2.5 MB/核心-> 1.375 MB/核心）。

上述拓撲也會攜帶至 HC 系列的虛擬機器設定。 為了讓 Azure 虛擬機器的空間在不幹擾 VM 的情況下運作，我們會保留 pCores 0-1 和24-25 （也就是每個插槽上的前2個 pCores）。 接著，我們會將所有剩餘核心的 pNUMA 網域指派給 VM。 因此，VM 會看到：

`(2 vNUMA domains) * (22 cores/vNUMA) = 44`每個 VM 的核心數

VM 沒有未提供 pCores 0-1 和24-25 的知識。 因此，它會公開每個 Vnuma 解決方案，就好像它原本就有22個核心一樣。

Intel 更強的白金、金級和銀級 Cpu 也引進了一種可在 CPU 通訊端內部和外部通訊的片上2D 網格網路。 我們強烈建議處理常式釘選以達到最佳效能和一致性。 進程釘選會在 HC 系列 Vm 上使用，因為基礎晶片會以本身的形式公開給來賓 VM。 若要深入瞭解，請參閱[Intel 強 SP 架構](https://bit.ly/2RCYkiE)。

下圖顯示保留給 Azure 虛擬機器和 HC 系列 VM 的核心隔離。

![保留給 Azure 虛擬機器和 HC 系列 VM 的核心隔離](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>硬體規格

| 硬體規格          | HC 系列 VM                     |
|----------------------------------|----------------------------------|
| 核心                            | 44（已停用 HT）                 |
| CPU                              | Intel 8168 級白金 *        |
| CPU 頻率（非 AVX）          | 3.7 GHz （單一核心）、2.7-3.4 GHz （所有核心） |
| 記憶體                           | 8 GB/核心（352總計）            |
| 本機磁碟                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 * * |
| 網路                          | 50 Gb 乙太網路（40 Gb 可用） Azure second Gen SmartNIC * * * |

## <a name="software-specifications"></a>軟體規格

| 軟體規格     | HC 系列 VM          |
|-----------------------------|-----------------------|
| MPI 作業大小上限            | 13200核心（單一 VMSS 中的 300 Vm，singlePlacementGroup = true） |
| MPI 支援                 | MVAPICH2、OpenMPI、MPICH、Platform MPI、Intel MPI  |
| 其他架構       | 整合通訊 X、libfabric、PGAS |
| Azure 儲存體支援       | Std + Premium （最多4個磁片） |
| SRIOV RDMA 的 OS 支援   | CentOS/RHEL 7.6 +、SLES 12 SP4 +、WinServer 2016 + |
| Azure CycleCloud 支援    | 是                         |
| Azure Batch 支援         | 是                         |

## <a name="next-steps"></a>後續步驟

* 深入瞭解 Azure 中適用于[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)和[WINDOWS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc)的 HPC VM 大小。

* 深入瞭解 Azure 中的[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) 。
