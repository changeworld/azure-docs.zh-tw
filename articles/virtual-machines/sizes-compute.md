---
title: Azure VM 大小 - 計算優化 |微軟文件
description: 列出 Azure 中虛擬機器可用的不同計算最佳化大小。 列出有關此系列中大小的數據 CPU、資料磁碟和 NIC 的數量以及儲存輸送量和網路頻寬的資訊。
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: e08d593f641c42f9ad605fda013206e70a34e52f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269630"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>計算最佳化的虛擬機器大小

計算優化的 VM 大小具有較高的 CPU 記憶體比。 這些大小適用於中流量 Web 伺服器、網路設備、批次處理和應用程式伺服器。 本文提供有關 vCPU、數據磁碟和 NIC 的數量的資訊。 它還包括有關此分組中每個大小的存儲輸送量和網路頻寬的資訊。

[Fsv2 系列](fsv2-series.md)基於英特爾® Xeon ®白金 8168 處理器。 它具有持續全芯渦輪時鐘速度為 3.4 GHz 和最大單核渦輪頻率為 3.7 GHz。 英特爾® AVX-512 指令是英特爾可擴展處理器上的新指令。 這些指令在單精度和雙精度浮點操作上為向量處理工作負載提供高達 2 倍的性能提升。 換句話說,對於任何計算工作負載來說,它們都非常快。

Fsv2 系列的每小時訂價較低，在 Azure 產品組合中，就每一 vCPU 的「Azure 計算單位」(ACU) 而言，具有最佳的價格/性能比表現。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [上一代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
