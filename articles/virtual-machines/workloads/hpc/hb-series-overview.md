---
title: HB 系列 VM 總覽-Azure 虛擬機器 |Microsoft Docs
description: 瞭解 Azure 中 HB 系列 VM 大小的預覽支援。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: fed5606da84d8311785752cc8319b7a3c642c1f5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86508027"
---
# <a name="hb-series-virtual-machines-overview"></a>HB 系列虛擬機器總覽

將 AMD EPYC 上的高效能計算（HPC）應用程式效能最大化，需要審慎的記憶體位置和進程位置。 下面我們將概述 AMD EPYC 架構，並在 Azure 上對 HPC 應用程式執行它。 我們會使用 "pNUMA" 一詞來參考實體 NUMA 網域，並使用 "Vnuma 解決方案" 來參考虛擬化的 NUMA 網域。

實際上，HB 系列是 2 * 32 核心 EPYC 7551 Cpu，總共64個實體核心。 這些64核心分成16個 pNUMA 網域（每一通訊端8個），每個都是四個核心，又稱為「CPU 複雜」（或「CCX」）。 每個 CCX 都有自己的 L3 快取，也就是 OS 會看到 pNUMA/Vnuma 解決方案界限的方式。 一對連續的 CCXs 可共用對兩個實體 DRAM 通道的存取（HB 系列伺服器中的 32 GB DRAM）。

為了讓 Azure 虛擬機器的空間在不幹擾 VM 的情況下運作，我們會保留實體 pNUMA 網域0（第一個 CCX）。 接著，我們會指派 VM 的 pNUMA 網域1-15 （剩餘的 CCX 單位）。 VM 將會看到：

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`每個 VM 的核心數

VM 本身並不知道 pNUMA 0 並未提供給它。 VM 瞭解 pNUMA 1-15 as Vnuma 解決方案0-14，其中 7 Vnuma 解決方案在 vSocket 0 和 8 Vnuma 解決方案 on vSocket 1。 雖然這是不對稱的，但您的 OS 應該會正常開機和運作。 稍後在本指南中，我們會指示在此非對稱式 NUMA 版面配置上執行 MPI 應用程式的最佳作法。

進程釘選適用于 HB 系列 Vm，因為我們會將基礎晶片公開給來賓 VM。 我們強烈建議處理常式釘選以達到最佳效能和一致性。

如需有關[AMD EPYC 架構](https://bit.ly/2Epv3kC)和[多晶片架構](https://bit.ly/2GpQIMb)的詳細資訊，請參閱 LinkedIn。 如需詳細資訊，請參閱[適用于 AMD EPYC 處理器的 HPC 微調指南](https://bit.ly/2T3AWZ9)。

下圖顯示保留給 Azure 虛擬機器和 HB 系列 VM 的核心隔離。

![保留給 Azure 虛擬機器和 HB 系列 VM 的核心隔離](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>硬體規格

| 硬體規格                | HB 系列 VM                     |
|----------------------------------|----------------------------------|
| 核心                            | 60（SMT 已停用）                |
| CPU                              | AMD EPYC 7551 *                   |
| CPU 頻率（非 AVX）          | ~ 2.55 GHz （單一 + 所有核心）   |
| 記憶體                           | 4 GB/核心（240總計）            |
| 本機磁碟                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 * * |
| 網路                          | 50 Gb 乙太網路（40 Gb 可用） Azure second Gen SmartNIC * * * |

## <a name="software-specifications"></a>軟體規格

| SW 規格           |HB 系列 VM           |
|-----------------------------|-----------------------|
| MPI 作業大小上限            | 6000核心（100虛擬機器擴展集）12000核心（200虛擬機器擴展集）  |
| MPI 支援                 | MVAPICH2、OpenMPI、MPICH、Platform MPI、Intel MPI  |
| 其他架構       | 整合通訊 X、libfabric、PGAS |
| Azure 儲存體支援       | Std + Premium （最多4個磁片） |
| SRIOV RDMA 的 OS 支援   | CentOS/RHEL 7.6 +、SLES 12 SP4 +、WinServer 2016 +  |
| Azure CycleCloud 支援    | 是                         |
| Azure Batch 支援         | 是                         |

## <a name="next-steps"></a>後續步驟

* 深入瞭解 Azure 中適用于[Linux](../../sizes-hpc.md)和[WINDOWS](../../sizes-hpc.md)的 HPC VM 大小。

* 深入瞭解 Azure 中的[HPC](/azure/architecture/topics/high-performance-computing/) 。
