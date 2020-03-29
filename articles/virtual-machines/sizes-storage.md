---
title: Azure VM 大小 - 存儲 |微軟文檔
description: 列出 Azure 中可用於虛擬機器的不同存儲優化大小。 列出有關此系列中大小的資料 CPU、資料磁片和 NIC 的數量以及存儲輸送量和網路頻寬的資訊。
services: virtual-machines
documentationcenter: ''
author: sasha-melamed
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 5a611dc288fc18a14f6000689f9f9b49d4b3feb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913331"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>儲存體最佳化的虛擬機器大小

存儲優化的 VM 大小提供高磁片輸送量和 IO，是大資料、SQL、NoSQL 資料庫、資料倉儲和大型事務資料庫的理想選擇。  示例包括卡珊多拉、蒙戈DB、雲泰拉和雷裡斯。 本文提供有關每個優化大小的 vCPU、資料磁片和 NIC 的數量以及本機存放區輸送量和網路頻寬的資訊。

[Lsv2 系列](lsv2-series.md)具有高輸送量、低延遲、直接映射的本地 NVMe 存儲，在[AMD EPYC<sup>TM</sup> 7551 處理器](https://www.amd.com/en/products/epyc-7000-series)上運行，所有核心提升為 2.55GHz，最大提升為 3.0GHz。 Lsv2 系列 VM 提供採用同時多執行緒設定且具有 8 到 80 個 vCPU 的大小。  每一 vCPU 有 8 GiB 記憶體，每 8 個 vCPU 有一個 1.92 TB NVMe SSD M.2 裝置，在 L80s v2 上最高可達 19.2 TB (10 x 1.92 TB)。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [計算最佳化](sizes-compute.md)
- [記憶體優化](sizes-memory.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [上一代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。

瞭解如何優化[適用于 Windows](windows/storage-performance.md)或[Linux](linux/storage-performance.md)的 Lsv2 系列虛擬機器的性能。
