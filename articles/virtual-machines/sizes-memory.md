---
title: Azure VM 大小-記憶體 |Microsoft Docs
description: 列出 Azure 中適用于虛擬機器的不同記憶體優化大小。 列出 vCPU 數目、資料磁碟和 NIC 的相關資訊，以及此服務中各種大小之儲存體輸送量和網路頻寬的相關資訊。
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
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 5a20e9c64b6ef948167333b54b16b34e84dc0e32
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273574"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>記憶體最佳化的虛擬機器大小

記憶體最佳化的虛擬機器大小的記憶體與 CPU 比例相當高，最適合關聯式資料庫伺服器、中型到大型快取，以及記憶體內部分析。 本文提供 vCPU 數量、資料磁碟和 NIC 的相關資訊，以及此群組中各種大小之儲存體輸送量和網路頻寬的相關資訊。

- [Dv2 和 DSv2 系列](dv2-dsv2-series-memory.md)是原始 D 系列的後續操作，具備功能更強大的 CPU。 Dv2 系列的速度比 D 系列快35%。 它會在 Intel&reg; &reg; 8171M 2.1 GHz （Skylake）或 intel&reg; 2673&reg; e5-v4 2.3 g h z （Broadwell）或 intel&reg; &reg; e5-2673 V3 2.4 GHz （Haswell）處理器上執行，並使用 intel Turbo 加速技術2.0。 Dv2 系列的記憶體和磁碟組態和 D 系列一樣。

    Dv2 和 DSv2 系列適用于需要更快速的個 vcpu、更好的暫存儲存體效能，或有更高記憶體需求的應用程式。 它們為許多企業級應用程式提供了強大的組合。

- [Eav4 And Easv4 系列](eav4-easv4-series.md)利用 AMD 的2.35 版 Ghz EPYC<sup>TM</sup> 7452 處理器，以多執行緒的方式設定，最多可達 256mb L3 快取，增加執行大部分記憶體優化工作負載的選項。 Eav4 系列和 Easv4 系列具有與 Ev3 & Esv3 系列相同的記憶體和磁片設定。

- 超執行緒設定中的[Ev3 和 Esv3 系列](ev3-esv3-series.md)intel&reg; &reg; 8171M 2.1 GHz （Skylake）或&reg; intel&reg; E5-2673 v4 2.3 g h z （Broadwell）處理器，為大多數一般用途的工作負載提供更好的價值主張，並使 Ev3 與大部分其他雲端的一般用途 vm 保持一致。 記憶體已擴充（從 7 GiB/vCPU 到 8 GiB/vCPU），而磁片和網路限制已針對每個核心調整，以配合移至超執行緒。 Ev3 承襲了 D/Dv2 系列的高記憶體 VM 大小。

- [M 系列](m-series.md)提供高 vCPU 計數（最多128個 vcpu）和海量儲存體（最多 3.8 TiB）。 這也適用于極大的資料庫或其他受益于高 vCPU 計數和海量儲存體的應用程式。

- [Mv2 系列](mv2-series.md)提供雲端中任何 VM 的最高 vCPU 計數（最多416個 vcpu）和最大記憶體（最多 11.4 TiB）。 這適用于極大的資料庫或其他受益于高 vCPU 計數和海量儲存體的應用程式。

Azure 計算服務所提供的虛擬機器大小不受特定硬體類型限制，而且為單一客戶專用。 這些虛擬機器大小最適合需要與其他客戶高度隔離，且涉及合規性和法規要求等元素的工作負載。 客戶也可以選擇使用 [Azure 的巢狀虛擬機器支援](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)，進一步細分這些隔離虛擬機器的資源。 請參閱下列適用于您的隔離 VM 選項的虛擬機器系列頁面。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [計算最佳化](sizes-compute.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [上一代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
