---
title: DC 系列 - Azure 虛擬機器
description: 直流系列 VM 的規格。
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: d35e37e53b84d317446a93a2301fc3b703b426b7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085717"
---
# <a name="dcsv2-series"></a>DCv2 系列


DCsv2 系列可幫助保護數據和代碼在公共雲中處理時的機密性和完整性。 這些機器由最新一代英特爾 XEON E-2288G 處理器支援,配備新GX技術。 借助英特爾渦輪提升技術,這些機器可高達 5.0GHz。 DCsv2 系列實例使客戶能夠建構基於安全區的安全應用程式,以便在使用時保護其代碼和數據。

示例用例包括:機密多方數據共享、欺詐檢測、反洗錢、區塊鏈、機密使用分析、智慧分析和機密機器學習。

進階儲存:支援*

進階儲存快取:支援*

即時移轉:不支援

記憶體保留更新:不支援

*除Standard_DC8_v2



| 大小             | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量︰IOPS / MBps (以 GiB 為單位的快取大小) | 最大取消快取的磁碟輸送量︰IOPS / MBps | 最大 NIC / 預期網路頻寬 (MBps) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- DCv2 系列 VM 是第 2 代`Gen2`[VM,](./linux/generation-2.md#creating-a-generation-2-vm)僅支援 映射。
- 目前僅在英國南部、加拿大中部和美國東部提供。
- 前一代機密計算[VM:DC 系列](sizes-previous-gen.md#preview-dc-series)
- 使用[Azure 門戶](./linux/quick-create-portal.md)或[Azure 應用商店](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)創建 DCv2 VM



## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [上一代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
