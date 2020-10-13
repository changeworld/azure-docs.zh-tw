---
title: Azure VM 大小-計算優化 |Microsoft Docs
description: 列出 Azure 中虛擬機器可用的不同計算優化大小。 列出 vCPU 數目、資料磁碟和 NIC 的相關資訊，以及此服務中各種大小之儲存體輸送量和網路頻寬的相關資訊。
author: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 58ade8dfdde2c906cbe7c61a232f2643708e19b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87832462"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>計算最佳化的虛擬機器大小

計算優化的 VM 大小具有高 CPU 對記憶體的比率。 這些大小適用于中流量 web 伺服器、網路設備、批次處理及應用程式伺服器。 本文提供個 vcpu 數目、資料磁片和 Nic 的相關資訊。 它也包含此群組中每個大小之儲存體輸送量和網路頻寬的相關資訊。

[Fsv2 系列](fsv2-series.md)會在第2代 Intel®) ®白金級 8272CL (串聯 Lake 處理器和 Intel® Skylake®白金級 8168 () 處理器上執行。 它有一個持續的核心 Turbo 頻率速度 3.4 GHz，以及最大的單核心 turbo 頻率 3.7 GHz。 Intel® AVX-512 指示是 Intel 可擴充處理器的新指示。 這些指示提供最多2倍的效能，可在單精確度和雙精度浮點數運算上進行向量處理工作負載。 換句話說，對於任何運算工作負載而言，它們都是非常快速的。

Fsv2 系列的每小時訂價較低，在 Azure 產品組合中，就每一 vCPU 的「Azure 計算單位」(ACU) 而言，具有最佳的價格/性能比表現。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。

如需 Azure 如何命名其 Vm 的詳細資訊，請參閱 [azure 虛擬機器大小的命名慣例](./vm-naming-conventions.md)。