---
title: HB 系列 VM 概述 - Azure 虛擬機器 |微軟文檔
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
ms.openlocfilehash: 62e4d3dbd7357f8c98df3307c1c8fe52cbed1c5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707767"
---
# <a name="hb-series-virtual-machines-overview"></a>HB 系列虛擬機器概述

在 AMD EPYC 上最大化高性能計算 （HPC） 應用程式性能需要深思熟慮的記憶體位置和過程放置方法。 下面我們概述了 AMD EPYC 體系結構以及我們在 Azure 上為 HPC 應用程式實現該體系結構。 我們將使用術語"pNUMA"來指物理 NUMA 域，使用"vNUMA"來引用虛擬化的 NUMA 域。

從物理上講，HB 系列是 2 + 32 核 EPYC 7551 CPU，共 64 個物理內核。 這 64 個內核分為 16 個 pNUMA 域（每個通訊端 8 個），每個域是 4 個內核，稱為"CPU 複合體"（或"CCX"）。 每個 CCX 都有自己的 L3 緩存，這是作業系統如何看到 pNUMA/vNUMA 邊界。 一對連續的 CCX 共用對物理 DRAM 的兩個通道（HB 系列伺服器中 32 GB 的 DRAM）的訪問。

為了為 Azure 虛擬機器管理程式提供在不幹擾 VM 的情況下運行的空間，我們保留物理 pNUMA 域 0（第一個 CCX）。 然後，我們為 VM 分配 pNUMA 域 1-15（剩餘的 CCX 單元）。 VM 將看到：

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`每個 VM 的核心

VM 本身不知道 pNUMA 0 沒有給出它。 VM 將 pNUMA 1-15 理解為 vNUMA 0-14，在 vSocket 0 上使用 7 vNUMA，在 vSocket 1 上使用 8 個 vNUMA。 雖然這是不對稱的，但作業系統應啟動並正常運行。 在本指南的後面部分，我們指導如何最好地運行 MPI 應用程式在此不對稱的 NUMA 佈局上。

進程固定將在 HB 系列 VM 上工作，因為我們將基礎晶片作為正樣公開給來賓 VM。 我們強烈建議進行流程固定，以實現最佳性能和一致性。

有關[AMD EPYC 架構](https://bit.ly/2Epv3kC)和[多晶片架構](https://bit.ly/2GpQIMb)LinkedIn的更多瞭解。 有關詳細資訊，請參閱[AMD EPYC 處理器的 HPC 調諧指南](https://bit.ly/2T3AWZ9)。

下圖顯示了為 Azure 虛擬機器管理程式和 HB 系列 VM 保留的內核的分離。

![為 Azure 虛擬機器管理程式和 HB 系列 VM 保留的內核分離](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>硬體規格

| HW 規格                | HB 系列 VM                     |
|----------------------------------|----------------------------------|
| 核心                            | 60 （SMT 禁用）                |
| CPU                              | AMD EPYC 7551*                   |
| CPU 頻率（非 AVX）          | ±2.55 GHz（單芯+所有內核）   |
| 記憶體                           | 4 GB/核心（共 240 個）            |
| 本機磁碟                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR 梅拉諾克斯連接X-5* |
| 網路                          | 50 Gb 乙太網（40 Gb 可用） Azure 第二代智慧 NIC* |

## <a name="software-specifications"></a>軟體規格

| 軟體規格           |HB 系列 VM           |
|-----------------------------|-----------------------|
| 最大 MPI 作業大小            | 6000 個內核（100 個虛擬機器規模集） 12000 個內核（200 個虛擬機器規模集）  |
| MPI 支援                 | MVAPICH2， OpenMPI， MPICH， 平臺 MPI， 英特爾 MPI  |
| 其他框架       | 整合通訊 X， 交換， PGAS |
| Azure 存儲支援       | Std = 高級（最多 4 個磁片） |
| 對 SRIOV RDMA 的作業系統支援   | CentOS/RHEL 7.6°， SLES 12 SP4+， WinServer 2016+  |
| Azure 迴圈雲支援    | 是                         |
| Azure 批次處理支援         | 是                         |

## <a name="next-steps"></a>後續步驟

* 詳細瞭解 Azure 中[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)和[Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc)的 HPC VM 大小。

* 在 Azure 中瞭解有關[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)的更多詳細資訊。
