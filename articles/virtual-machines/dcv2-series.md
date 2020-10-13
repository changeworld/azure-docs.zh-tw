---
title: DC 系列 - Azure 虛擬機器
description: DC 系列 VM 的規格。
author: susaxen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: fccdaaab121f315f1cb98b2b6ee3bffcb9ac2b3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653004"
---
# <a name="dcsv2-series"></a>DCsv2 系列


DCv2 系列可協助在公用雲端處理資料與程式碼時，保護其機密性與完整性。 這些機器由使用 SGX 技術的最新一代 Intel XEON E-2288G 處理器所支援。 在 Intel Turbo Boost 技術加持下，這些機器最高可達到 5.0GHz。 DCsv2 系列執行個體可讓客戶建置安全的以記憶體保護區為基礎的應用程式，以在使用程式碼與資料時保護其安全。

使用案例範例包括：機密的多方資料共用、詐騙偵測、洗錢防治、區塊鏈、機密流量分析、智慧分析和機密機器學習。

[進階儲存體](premium-storage-performance.md)：支援的 *<br>
[進階儲存體](premium-storage-performance.md)快取：支援<br>
[即時移轉](maintenance-and-updates.md)：不支援<br>
[記憶體保留更新](maintenance-and-updates.md)：不支援<br>
[VM 世代支援](generation-2.md)：第1代和第2代<br>

*Standard_DC8_v2 除外

| 大小             | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量IOPS / MBps (快取大小，以 GiB 為單位) | 最大 NIC/預期的網路頻寬 (MBps) | EPC 記憶體 (MiB) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128                                                                                         | 2   | 168                                         |

- DCsv2 系列 VM 是[第 2 代 VM](./linux/generation-2.md#creating-a-generation-2-vm)且僅支援 `Gen2` 映像。
- 目前適用于 [此處](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)所列的區域。
- 先前產生的機密計算 VM：[DC 系列](sizes-previous-gen.md#preview-dc-series)
- 使用 [Azure 入口網站](./linux/quick-create-portal.md)或 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) 建立 DCsv2 VM



## <a name="other-sizes-and-information"></a>其他大小和資訊

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

定價計算機： [定價計算機](https://azure.microsoft.com/pricing/calculator/)

磁片類型的詳細資訊： [磁片類型](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。