---
title: HBv2 系列 VM 總覽-Azure 虛擬機器 |Microsoft Docs
description: 瞭解 Azure 中的 HBv2 系列 VM 大小。
services: virtual-machines
author: vermagit
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 48366f205ed8eb2d179bdc39c8da3d673f066a69
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92332614"
---
# <a name="hbv2-series-virtual-machine-overview"></a>HBv2 系列虛擬機器總覽 

 
在 AMD EPYC 上將高效能運算 (HPC) 應用程式效能最大化，需要一種精心的記憶體位置和進程放置。 以下將概述 AMD EPYC 架構，以及我們在 Azure 上針對 HPC 應用程式所執行的架構。 我們將使用 **pNUMA** 一詞來參考實體 numa 網域，並使用 **vnuma 解決方案** 來參考虛擬 numa 網域。 

實際上， [HBv2 系列](../../hbv2-series.md) 伺服器是 2 * 64 核心 EPYC 7742 cpu，總共有128個實體核心。 這些128核心分為 32 pNUMA 網域 (16 個通訊端) ，其中每個都是4個核心，而 AMD 稱為 **核心複雜** (或 **CCX**) 。 每個 CCX 都有自己的 L3 快取，也就是 OS 會如何看到 pNUMA/Vnuma 解決方案界限。 四個連續的 CCXs 可共用實體 DRAM 2 個通道的存取權。 

為了提供空間讓 Azure 虛擬機器在不幹擾 VM 的情況下運作，我們保留了實體 pNUMA 網域0和 16 (也就是每個 CPU 通訊端) 的第一個 CCX。 所有剩餘的30個 pNUMA 網域都會指派給 VM，此時會變成 Vnuma 解決方案。 因此，VM 會看到：

`(30 vNUMA domains) * (4 cores/vNUMA) = 120` 每個 VM 的核心 

VM 本身並不知道 pNUMA 0 和16是保留的。 它會將看到的 Vnuma 解決方案列舉為0-29，並對稱每個通訊端 15 Vnuma 解決方案、vSocket 0 上的 Vnuma 解決方案0-14，以及 Vnuma 解決方案1上的 VSocket 15-29。 在下一節中，有關于如何以這個非對稱式 NUMA 配置來執行 MPI 應用程式的最佳指示。 

進程釘選可在 HBv2 系列 Vm 上運作，因為我們會將基礎晶片依原樣公開給來賓 VM。 強烈建議將程式釘選以獲得最佳效能與一致性。 


## <a name="hardware-specifications"></a>硬體規格 

| 硬體規格          | HBv2 系列 VM                   | 
|----------------------------------|----------------------------------|
| 核心                            | 120 (SMT 已停用)                | 
| CPU                              | AMD EPYC 7742                    | 
| CPU 頻率 (非 AVX)           | ~ 3.1 GHz (單一 + 所有核心)     | 
| 記憶體                           | 4 GB/核心 (480 GB 總計)          | 
| 本機磁碟                       | 960 GB NVMe (區塊) 、480 GB SSD (分頁檔)  | 
| Infiniband                       | 200 Gb/s EDR Mellanox ConnectX-6 | 
| 網路                          | 50 Gb/s Ethernet (40 Gb/s 可) Azure 第二代 SmartNIC 使用 | 


## <a name="software-specifications"></a>軟體規格 

| 軟體規格     | HBv2 系列 VM                                            | 
|-----------------------------|-----------------------------------------------------------|
| MPI 作業大小上限            | 36000核心 (具有 singlePlacementGroup = true 的單一虛擬機器擴展集中的 300 Vm)  |
| MPI 支援                 | HPC-X、Intel MPI、OpenMPI、MVAPICH2、MPICH、Platform MPI  |
| 其他架構       | 整合通訊 X、libfabric、PGAS                  |
| Azure 儲存體支援       | 標準和 Premium 磁片 (最多8個磁片)               |
| SRIOV RDMA 的 OS 支援   | CentOS/RHEL 7.6 +、SLES 12 SP4 +、WinServer 2016 +           |
| 協調器支援        | CycleCloud，Batch                                         | 


## <a name="next-steps"></a>後續步驟

- 深入瞭解 [AMD EPYC 架構](https://bit.ly/2Epv3kC) 和 [多晶片架構](https://bit.ly/2GpQIMb)。 如需詳細資訊，請參閱 [適用于 AMD EPYC 處理器的 HPC 微調指南](https://bit.ly/2T3AWZ9)。
- 閱讀 [Azure 計算 Tech 社區 blog](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)中的最新公告和一些 HPC 範例。
- 如需執行中 HPC 工作負載較高階的架構檢視，請參閱 [Azure 上的高效能運算 (HPC)](/azure/architecture/topics/high-performance-computing/)。