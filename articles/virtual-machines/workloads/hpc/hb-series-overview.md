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
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 7c66af5184c4a943fd4b3185a87623112fe0d954
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88691236"
---
# <a name="hb-series-virtual-machines-overview"></a>HB 系列虛擬機器總覽

在 AMD EPYC 上將高效能運算 (HPC) 應用程式效能最大化，需要一種精心的記憶體位置和進程放置。 以下將概述 AMD EPYC 架構，以及我們在 Azure 上針對 HPC 應用程式所執行的架構。 我們將使用 "pNUMA" 一詞來參考實體 NUMA 網域，並使用 "Vnuma 解決方案" 來參考虛擬 NUMA 網域。

實際上， [HB 系列](../../hb-series.md) 伺服器是 2 * 32 核心 EPYC 7551 cpu，總共有64個實體核心。 這些64核心分成16個 pNUMA 網域 (每個通訊端) 8 個，每個都有四個核心，也稱為「CPU 複雜」 (或「CCX」 ) 。 每個 CCX 都有自己的 L3 快取，也就是 OS 會如何看到 pNUMA/Vnuma 解決方案界限。 一對連續的 CCXs 會在 HB 系列伺服器) 中，共用兩個實體 DRAM (32 GB 通道的存取權。

為了提供空間讓 Azure 虛擬機器在不幹擾 VM 的情況下運作，我們會將實體 pNUMA 網域0保留 (第一個 CCX) 。 接著，我們會將 pNUMA 網域 1-15 (VM 的其餘 CCX 單位) 。 VM 將會看到：

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` 每個 VM 的核心

VM 本身並不知道 pNUMA 0 未提供給它。 VM 瞭解 pNUMA 1-15 為 Vnuma 解決方案0-14，7 Vnuma 解決方案在 vSocket 0 和 8 Vnuma 解決方案 on vSocket 1。 雖然這是非對稱的，但您的作業系統應該會正常開機並正常運作。 稍後在本指南中，我們將指示如何以最理想的方式在此非對稱 NUMA 配置上執行 MPI 應用程式。

進程釘選可在 HB 系列 Vm 上運作，因為我們會將基礎晶片依原樣公開給來賓 VM。 強烈建議將程式釘選以獲得最佳效能與一致性。

下圖顯示保留給 Azure 虛擬程式和 HB 系列 VM 的核心隔離。

![為 Azure 虛擬程式和 HB 系列 VM 保留的核心隔離](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>硬體規格

| 硬體規格                | HB 系列 VM                     |
|----------------------------------|----------------------------------|
| 核心                            | 60 (SMT 已停用)                 |
| CPU                              | AMD EPYC 7551                    |
| CPU 頻率 (非 AVX)           | ~ 2.55 GHz (單一 + 所有核心)    |
| Memory                           | 4 GB/核心 (240 GB 總計)          |
| 本機磁碟                       | 700 GB SSD                       |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 |
| 網路                          | 50 Gb 乙太網路 (40 Gb 可用) Azure 第二代 SmartNIC |

## <a name="software-specifications"></a>軟體規格

| 軟體規格           |HB 系列 VM           |
|-----------------------------|-----------------------|
| MPI 作業大小上限            | 18000核心 (具有 singlePlacementGroup = true 的單一虛擬機器擴展集中的 300 Vm)   |
| MPI 支援                 | HPC-X、Intel MPI、OpenMPI、MVAPICH2、MPICH、Platform MPI  |
| 其他架構       | 整合通訊 X、libfabric、PGAS |
| Azure 儲存體支援       | 標準和 Premium 磁片 (最多4個磁片)  |
| SRIOV RDMA 的 OS 支援   | CentOS/RHEL 7.6 +、SLES 12 SP4 +、WinServer 2016 +  |
| 協調器支援        | CycleCloud，Batch  |

## <a name="next-steps"></a>接下來的步驟

- 深入瞭解 [AMD EPYC 架構](https://bit.ly/2Epv3kC) 和 [多晶片架構](https://bit.ly/2GpQIMb)。 如需詳細資訊，請參閱 [適用于 AMD EPYC 處理器的 HPC 微調指南](https://bit.ly/2T3AWZ9)。
- 請參閱 [Azure 運算技術社群部落格](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)的最新公告和一些 HPC 範例和結果。
- 如需執行中 HPC 工作負載較高階的架構檢視，請參閱 [Azure 上的高效能運算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
