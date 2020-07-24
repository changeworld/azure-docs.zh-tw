---
title: Av2 系列
description: Av2 系列 Vm 的規格。
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 1b5b77bb9bdf679fe0fd8bf73966dd45acc80155
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87085770"
---
# <a name="av2-series"></a>Av2 系列

Av2 系列 VM 可以部署在各種不同的硬體類型和處理器上。 Av2 系列 Vm 的 CPU 效能和記憶體設定最適合用於進入層級的工作負載，例如開發和測試。 大小會進行節流，為執行中的實例提供一致的處理器效能，不論其部署所在的硬體為何。 若要判斷此大小部署所在的實體硬體，請從虛擬機器內查詢虛擬硬體。 一些使用案例範例包括開發與測試伺服器、低流量網頁伺服器、小型至中型資料庫、概念證明，以及程式碼存放庫。

ACU：100

進階儲存體：不支援

進階儲存體快取：不支援

即時移轉：支援

記憶體保留更新：支援

定價計算機和區域可用性資訊：<a href="https://azure.microsoft.com/en-us/pricing/calculator/">定價計算機</a>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大暫存儲存體輸送量： IOPS/讀取 MBps/寫入 MBps | 最大資料磁片/輸送量： IOPS | 最大 NIC | 預期的網路頻寬（Mbps）
|---|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2/2x500   | 2 | 250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>其他大小和資訊

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

定價計算機和區域可用性資訊：<a href="https://azure.microsoft.com/en-us/pricing/calculator/">定價計算機</a>

磁片類型的詳細資訊：<a href="https://docs.microsoft.com/en-us/azure/virtual-machines/linux/disks-types#ultra-ssd-preview/">磁片類型</a>

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
