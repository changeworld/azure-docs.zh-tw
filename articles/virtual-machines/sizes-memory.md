---
title: Azure VM 大小 - 記憶體 | Microsoft Docs
description: 列出 Azure 中可用的不同虛擬機器記憶體最佳化大小。 列出 vCPU 數目、資料磁碟和 NIC 的相關資訊，以及此服務中各種大小之儲存體輸送量和網路頻寬的相關資訊。
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: VM 隔離,隔離的 VM,隔離,隔離的
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: ed49f5299129d16ecdd31f537064e67a4e8965a4
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289730"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>記憶體最佳化的虛擬機器大小

記憶體優化 VM 大小提供高記憶體對 CPU 比例，非常適合關係資料庫伺服器、中型至大型快取，以及記憶體內部分析。 本文提供 vCPU 數量、資料磁碟和 NIC 的相關資訊，以及此群組中各種大小之儲存體輸送量和網路頻寬的相關資訊。

- [Dv2 與 DSv2 系列](dv2-dsv2-series-memory.md)是原始 D 系列的延續，擁有更強大的 CPU。 Dv2 系列比 D 系列快約 35%。 在 Intel&reg; Xeon&reg; 8171M 2.1 GHz (Skylake) 或 Intel&reg; Xeon&reg; E5-2673 v4 2.3 GHz (Broadwell) 或 Intel&reg; Xeon&reg; E5-2673 v3 2.4 GHz (Haswell) 處理器上執行，並具有 Intel 渦輪加速技術 2.0。 Dv2 系列的記憶體和磁碟組態和 D 系列一樣。

    對於要求更快速的 vCPU、更好的暫存儲存體效能，或有更高記憶體需求的應用程式而言，Dv2 與 DSv2 系列是理想的選擇。 它們為許多企業級應用程式提供了強大的組合。

- [Eav4 與 Easv4 系列](eav4-easv4-series.md)利用採用多執行緒設定的 AMD 2.35Ghz EPYC<sup>TM</sup> 7452 處理器，具有最多 256MB 的 L3 快取，增加執行大部分記憶體最佳化工作負載的選項。 Eav4 系列和 Easv4 系列具有與 Ev3 和 Esv3 系列相同的記憶體與磁碟設定。

- [Ev3 與 Esv3 系列](ev3-esv3-series.md)搭配採用超執行緒設定的 Intel&reg; Xeon&reg; 8171M 2.1 GHz (Skylake) 或 Intel&reg; Xeon&reg; E5-2673 v4 2.3 GHz (Broadwell) 處理器，可為多數一般用途的工作負載提供更好的價值主張，並且讓 Ev3 更為符合其他多數雲端的一般用途 VM 的需求。 除了記憶體已擴充 (從 7 GiB/vCPU 到 8 GiB/vCPU)，磁碟和網路限制也已就個別核心進行調整，以符合移轉至超執行緒的需求。 Ev3 承襲了 D/Dv2 系列的高記憶體 VM 大小。

- [Ev4 和 Esv4 系列](ev4-esv4-series.md)會在超執行緒設定的第2代 Intel 最高 &reg; &reg; 白金8272CL （串聯 Lake）處理器上執行，非常適合各種需要海量儲存體的企業應用程式和功能，最高可達 504 GiB 的 RAM。 其特色為[intel &reg; Turbo 加速技術 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、 [intel &reg; 超執行緒技術](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)和[Intel &reg; 先進向量延伸模組512（Intel AVX-512）](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)。 Ev4 和 Esv4 系列不包含本機暫存磁片。 如需詳細資訊，請參閱[沒有本機暫存磁片的 AZURE VM 大小](azure-vms-no-temp-disk.md)。

- [Edv4 和 Edsv4 系列](edv4-edsv4-series.md)會在第2代 Intel 最 &reg; &reg; 高白金8272CL （串聯 Lake）處理器上執行，非常適合極大的資料庫或其他可從高 vCPU 計數和海量儲存體獲益的應用程式。 此外，這些 VM 大小包含快速、較大的本機 SSD 儲存體，適用于從低延遲、高速的本機儲存體獲益的應用程式。 具備全核心滿載可維持 3.4 GHz 的渦輪時脈速度、[Intel&reg; 渦輪加速技術 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; 超執行緒技術](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)與 [Intel&reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)。

- [M 系列](m-series.md)提供高 vCPU 計數 (最多 128 個 vCPU) 與大量記憶體 (最多 3.8 TiB)。 其也很適合用於受益於高 vCPU 計數與大量記憶體的極大型資料庫或其他應用程式。

- [Mv2 系列](mv2-series.md)可在雲端提供最高 vCPU 計數 (最多 416 個 vCPU) 與最大記憶體 (最多 11.4 TiB) 的 VM。 其很適合用於受益於高 vCPU 計數與大量記憶體的極大型資料庫或其他應用程式。

Azure 計算服務所提供的虛擬機器大小不受特定硬體類型限制，而且為單一客戶專用。 這些虛擬機器大小最適合需要與其他客戶高度隔離，且涉及合規性和法規要求等元素的工作負載。 客戶也可以選擇使用 [Azure 的巢狀虛擬機器支援](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)，進一步細分這些隔離虛擬機器的資源。 查看下面的虛擬機器系列頁面，了解您的隔離 VM 選項。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [計算最佳化](sizes-compute.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
