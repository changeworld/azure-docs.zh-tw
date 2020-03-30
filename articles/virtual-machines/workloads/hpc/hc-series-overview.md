---
title: HC 系列 VM 概述 - Azure 虛擬機器*微軟文檔
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906738"
---
# <a name="hc-series-virtual-machine-overview"></a>HC 系列虛擬機器概述

最大化英特爾至強可擴展處理器上的 HPC 應用程式性能需要一種深思熟慮的方法來處理此新體系結構的放置。 在這裡，我們概述了我們在適用于 HPC 應用程式的 Azure HC 系列 VM 上實現它。 我們將使用術語"pNUMA"來指物理 NUMA 域，使用"vNUMA"來引用虛擬化的 NUMA 域。 同樣，我們將使用術語"pCore"來指物理 CPU 內核，使用"vCore"來指虛擬 CPU 內核。

從物理上講，HC 伺服器是 2 + 24 核英特爾 Xeon 白金 8168 CPU，總共 48 個物理內核。 每個 CPU 都是單個 pNUMA 域，並且對六個 DRAM 通道具有統一存取權限。 英特爾至強白金 CPU 具有比前幾代人（256 KB/核心 -1 MB/內核 > 1 MB/內核）的 4 倍的 L2 緩存，同時與以前的英特爾 CPU（2.5 MB/core -> 1.375 MB/core）相比，也減少了 L3 緩存。

上述拓撲也延續到 HC 系列虛擬機器管理程式配置。 為了為 Azure 虛擬機器管理程式提供在不幹擾 VM 的情況下運行的空間，我們保留 pCore0-1 和 24-25（即每個通訊端上的前 2 個 pCore）。 然後，我們將所有剩餘的內核分配 pNUMA 域到 VM。 因此，VM 將看到：

`(2 vNUMA domains) * (22 cores/vNUMA) = 44`每個 VM 的核心

VM 不知道 pCore0-1 和 24-25 沒有提供給它。 因此，它公開每個 vNUMA，就好像它本機有 22 個內核一樣。

英特爾至強白金、金和銀 CPU 還引入了模上 2D 網狀網路，用於在 CPU 插槽內外進行通信。 我們強烈建議進行流程固定，以實現最佳性能和一致性。 進程固定將在 HC 系列 VM 上工作，因為基礎晶片以正樣公開給來賓 VM。 要瞭解更多資訊，請參閱[英特爾至強 SP 架構](https://bit.ly/2RCYkiE)。

下圖顯示了為 Azure 虛擬機器管理程式和 HC 系列 VM 保留的內核的分離。

![為 Azure 虛擬機器管理程式和 HC 系列 VM 保留的內核分離](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>硬體規格

| 硬體規格          | HC 系列 VM                     |
|----------------------------------|----------------------------------|
| 核心                            | 44 （已禁用 HT）                 |
| CPU                              | 英特爾新恩鉑金 8168*        |
| CPU 頻率（非 AVX）          | 3.7 GHz（單核），2.7-3.4 GHz（所有內核） |
| 記憶體                           | 8 GB/核心（共 352 個）            |
| 本機磁碟                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR 梅拉諾克斯連接X-5* |
| 網路                          | 50 Gb 乙太網（40 Gb 可用） Azure 第二代智慧 NIC* |

## <a name="software-specifications"></a>軟體規格

| 軟體規格     | HC 系列 VM          |
|-----------------------------|-----------------------|
| 最大 MPI 作業大小            | 13200 個內核（單個 VMSS 中的 300 個 VMM，單位置組=true） |
| MPI 支援                 | MVAPICH2， OpenMPI， MPICH， 平臺 MPI， 英特爾 MPI  |
| 其他框架       | 整合通訊 X， 交換， PGAS |
| Azure 存儲支援       | Std = 高級（最多 4 個磁片） |
| 對 SRIOV RDMA 的作業系統支援   | CentOS/RHEL 7.6°， SLES 12 SP4+， WinServer 2016+ |
| Azure 迴圈雲支援    | 是                         |
| Azure 批次處理支援         | 是                         |

## <a name="next-steps"></a>後續步驟

* 詳細瞭解 Azure 中[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)和[Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc)的 HPC VM 大小。

* 在 Azure 中瞭解有關[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)的更多詳細資訊。
