---
title: DC 系列 - Azure 虛擬機器
description: 直流系列 VM 的規格。
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: 7834c8a32d4d85fc354bac209e13f19f3b8315fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256924"
---
# <a name="preview-dcsv2-series"></a>預覽： DCv2 系列


DCsv2 系列可説明保護資料和代碼在公共雲中處理時的機密性和完整性。 這些機器由最新一代英特爾 XEON E-2288G 處理器支援，配備新GX技術。 借助英特爾渦輪提升技術，這些機器可高達 5.0GHz。 DCsv2 系列實例使客戶能夠構建基於安全區的安全應用程式，以便在使用時保護其代碼和資料。

示例用例包括機密多方資料共用、欺詐檢測、反洗錢、區塊鏈、機密流量分析、智慧分析和機密機器學習。

高級存儲：支援*

高級存儲緩存：支援*

即時移轉：不支援

記憶體保留更新：不支援

*除Standard_DC8_v2



| 大小             | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量︰IOPS / MBps (以 GiB 為單位的快取大小) | 最大取消快取的磁碟輸送量︰IOPS / MBps | 最大 NIC / 預期網路頻寬 （MBps） |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- DCv2 系列 VM 是[第 2 代 VM，](./linux/generation-2.md#creating-a-generation-2-vm)僅支援`Gen2`映射。
- 目前僅在英國南部和加拿大中部提供。
- 上一代機密計算[VM：DC 系列](sizes-previous-gen.md)
- 使用 Azure 門戶[創建 VM - 門戶創建](./linux/quick-create-portal.md)DCv2 VM



## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體優化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [上一代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
