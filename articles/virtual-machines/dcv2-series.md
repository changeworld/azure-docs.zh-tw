---
title: DC 系列-Azure 虛擬機器
description: DC 系列 Vm 的規格。
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: 431c1b1211165f43feb7fe1f93c73c2bf141e004
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871809"
---
# <a name="dcsv2-series"></a>DCsv2 系列


DCsv2 系列可協助保護您的資料和程式碼在公用雲端中進行處理時的機密性和完整性。 這些機器是以最新一代的 Intel 2288G 處理器和 SGX 技術為後盾。 使用 Intel Turbo 加速技術，這些機器最多可達 5.0 GHz。 DCsv2 系列實例可讓客戶建立安全的記憶體保護區型應用程式，以在使用中時保護其程式碼和資料。

使用案例範例包括：機密的多方資料共用、詐騙偵測、反 money laundering、區塊鏈、機密流量分析、智慧分析和機密機器學習。

進階儲存體：支援 *

進階儲存體快取：支援 *

即時移轉：不支援

記憶體保留更新：不支援

* Standard_DC8_v2 除外



| 大小             | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量︰IOPS / MBps (以 GiB 為單位的快取大小) | 最大取消快取的磁碟輸送量︰IOPS / MBps | 最大 Nic/預期的網路頻寬（MBps） | EPC 記憶體（MiB） |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 （21）                                                            | 1600/24                                   | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 （43）                                                            | 3200/48                                   | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 （86）                                                            | 6400/96                                   | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 （172）                                                         | 12800/192                                 | 2   | 168                                         |

- DCsv2 系列 Vm 是[第2代 vm](./linux/generation-2.md#creating-a-generation-2-vm) ，只支援`Gen2`映射。
- 目前僅適用于英國南部、加拿大中部及美國東部。
- 先前產生的機密計算 Vm： [DC 系列](sizes-previous-gen.md#preview-dc-series)
- 使用[Azure 入口網站](./linux/quick-create-portal.md)或[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)建立 DCsv2 vm



## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [上一代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
