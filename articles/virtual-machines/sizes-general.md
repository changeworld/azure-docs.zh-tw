---
title: Azure VM 大小 - 一般用途 | Microsoft Docs
description: 列出 Azure 中可用的不同虛擬機器一般用途大小。 列出 vCPU 數目、資料磁碟和 NIC 的相關資訊，以及此服務中各種大小之儲存體輸送量和網路頻寬的相關資訊。
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: mimckitt
ms.openlocfilehash: b5e597eebda6a730d301eefdbd2d7e6859f5a7cc
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248306"
---
# <a name="general-purpose-virtual-machine-sizes"></a>一般用途的虛擬機器大小

一般用途的虛擬機器大小可讓 CPU 與記憶體比例達到平均。 適用於測試和開發、小型至中型資料庫，以及低至中流量 Web 伺服器。 本文提供一般目的計算的供應項目相關資訊。

- [Av2 系列](av2-series.md) VM 可以部署在各種不同的硬體類型和處理器上。 A 系列 VM 的 CPU 效能及記憶體設定最適合初階的工作負載，例如開發及測試， 根據硬體節流大小，為執行中的執行個體提供一致的處理器效能，不論硬體部署的位置。 若要判斷此大小部署所在的實體硬體，請從虛擬機器內查詢虛擬硬體。 使用案例範例包括開發與測試伺服器、低流量網頁伺服器、中小型資料庫、概念證明以及程式碼存放庫。

  > [!NOTE]
  > A8 – A11 VM 計劃於 2021 年 3 月淘汰。 如需詳細資訊，請參閱 [HPC 移轉指南](https://azure.microsoft.com/resources/hpc-migration-guide/)。

- [B 系列可高載](sizes-b-series-burstable.md)的 VM 非常適合不需要持續性完整 CPU 效能的工作負載，例如 Web 伺服器、小型資料庫和開發與測試環境。 這些工作負載通常具有高載的效能需求。 B 系列讓這些客戶所購買到的 VM 大小具有價格公道的基準效能，可在 VM 利用低於其基底效能時，讓 VM 執行個體累積點數。 當 VM 累積點數時，VM 可以在您的應用程式需要較高的 CPU 效能時，使用最多 100% 的 CPU 來高載高於 VM 基準。

- [Dav4 和 Dasv4 系列](dav4-dasv4-series.md)是新的尺寸，採用 AMD 的 2.35Ghz EPYC<sup>TM</sup> 7452 處理器，其中包含多執行緒設定，高達 256 MB 的 L3 快取，每 8 個核心分配 8 MB 的 L3 快取，增加客戶執行其一般用途工作負載的選項。 Dav4 系列和 Dasv4 系列具有與 D 和 Dsv3 系列相同的記憶體和磁碟設定。

- [DCv2 系列](dcv2-series.md)可協助您在您的資料與程式碼在公用雲端處理時的機密性與完整性。 這些機器由最新一代的 Intel XEON E-2288G 處理器 (具 SGX 技術) 支援。 在 Intel Turbo Boost 技術加持下，這些機器最高可達到 5.0 GHz 的時脈。 DCv2 系列執行個體可讓客戶建置安全的飛地型應用程式，以在其程式碼與資料使用期間保護其安全。

- [Dv2 和 Dsv2](dv2-dsv2-series.md) 系列是原始 D 系列的升級版，搭載更強大的 CPU 及最佳的 CPU 記憶體設定，更適合大多數生產工作負載。 Dv2 系列比 D 系列快約 35%。 Dv2 系列在採用 Intel 渦輪加速技術 2.0 的 Intel® Xeon® 8171M 2.1GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell)，或 Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 處理器上執行。 Dv2 系列的記憶體和磁碟組態和 D 系列一樣。

- [Dv3 和 Dsv3 系列](dv3-dsv3-series.md)的 VM 是在具超執行緒組態的 Intel® Xeon® 8171M 2.1GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell)，或 Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 處理器上執行，為大多數一般用途的工作負載提供更好的價值主張。 除了記憶體已擴充 (從 ~3.5 GiB/vCPU 到 4 GiB/vCPU)，磁碟和網路限制也已就個別核心進行調整，以符合移轉至超執行緒的需求。 Dv3 系列不再擁有 D/Dv2 系列的高記憶體 VM，這些都已移至記憶體最佳化的 [Ev3 和 Esv3 系列](ev3-esv3-series.md)。

- [Ddv4 和 Ddsv4 系列](ddv4-ddsv4-series.md) VM 是在第 2 代 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 處理器上執行，為大部分一般用途的工作負載提供更好的價值主張。 具備全核心滿載可維持 3.4 GHz 的渦輪時脈速度，並擁有 [Intel&reg; 渦輪加速技術 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; 超執行緒技術](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)與 [Intel&reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)。 


## <a name="other-sizes"></a>其他大小

- [計算最佳化](sizes-compute.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
