---
title: Azure VM 大小 - 記憶體 |微軟文檔
description: 列出 Azure 中虛擬機器可用的不同記憶體優化大小。 列出 vCPU 數目、資料磁碟和 NIC 的相關資訊，以及此服務中各種大小之儲存體輸送量和網路頻寬的相關資訊。
services: virtual-machines
documentationcenter: ''
author: jonbeck7
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
ms.author: jonbeck
ms.openlocfilehash: 26a44cdf3e57508cebb070669a8c1f473a59df8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77493523"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>記憶體最佳化的虛擬機器大小

記憶體最佳化的虛擬機器大小的記憶體與 CPU 比例相當高，最適合關聯式資料庫伺服器、中型到大型快取，以及記憶體內部分析。 本文提供 vCPU 數量、資料磁碟和 NIC 的相關資訊，以及此群組中各種大小之儲存體輸送量和網路頻寬的相關資訊。

- [Dv2和DSv2系列](dv2-dsv2-series-memory.md)是原始D系列的後續，具有更強大的CPU功能。 Dv2 系列比 D 系列快約 35%。 它運行在英特爾® Xeon® 8171M 2.1 GHz （Skylake） 或英特爾® Xeon® E5-2673 v4 2.3 GHz （Broadwell） 或英特爾® Xeon® E5-2673 v3 2.4 GHz （哈斯韋爾） 處理器，以及英特爾渦輪增壓技術 2.0。 Dv2 系列的記憶體和磁碟組態和 D 系列一樣。

    Dv2 和 DSv2 系列非常適合需要更快的 vCPU、更好的臨時存儲性能或具有更高記憶體需求的應用。 它們為許多企業級應用程式提供了強大的組合。

- [Eav4 和 Easv4 系列](eav4-easv4-series.md)採用 AMD 的 2.35Ghz EPYC<sup>TM</sup> 7452 處理器，採用多執行緒配置，具有高達 256MB L3 緩存，增加了運行大多數記憶體優化工作負載的選項。 Eav4 系列和 Easv4 系列與 Ev3 & Esv3 系列具有相同的記憶體和磁片配置。

- [Ev3 和 Esv3 系列](ev3-esv3-series.md)英特爾® Xeon® 8171M 2.1 GHz（天湖）或英特爾® Xeon® E5-2673 v4 2.3 GHz （Broadwell） 處理器，為大多數通用工作負載提供了更好的價值主張，使 Ev3 與大多數其他雲的通用 VM 保持一致。 記憶體已擴展（從 7 GiB/vCPU 擴展為 8 GiB/vCPU），同時每個內核對磁片和網路限制進行了調整，以便與移動到超執行緒一致。 Ev3 承襲了 D/Dv2 系列的高記憶體 VM 大小。

- [M 系列](m-series.md)提供高 vCPU 計數（最多 128 個 vCPU）和大量記憶體（高達 3.8 TiB）。 它還非常適合從高 vCPU 計數和大量記憶體中受益的超大型資料庫或其他應用程式。

- [Mv2 系列](mv2-series.md)提供雲中任何 VM 中最高的 vCPU 計數（最多 416 個 vCPU）和最大的記憶體（最多 8.19 TiB）。 它很適合用於受益於高 vCPU 計數和大量記憶體的極大型資料庫或其他應用程式。

Azure 計算服務所提供的虛擬機器大小不受特定硬體類型限制，而且為單一客戶專用。 這些虛擬機器大小最適合需要與其他客戶高度隔離，且涉及合規性和法規要求等元素的工作負載。 客戶也可以選擇使用 [Azure 的巢狀虛擬機器支援](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)，進一步細分這些隔離虛擬機器的資源。 有關隔離的 VM 選項，請參閱下面的虛擬機器系列頁面。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [計算最佳化](sizes-compute.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [上一代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。