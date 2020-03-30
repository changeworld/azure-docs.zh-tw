---
title: Azure VM 大小 - 通用 |微軟文檔
description: 列出 Azure 中虛擬機器可用的不同通用大小。 列出有關此系列中大小的資料 CPU、資料磁片和 NIC 的數量以及存儲輸送量和網路頻寬的資訊。
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: jonbeck
ms.openlocfilehash: 4b36f456f70eb79cff1f615c7c136b8fe4b1b3d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78226713"
---
# <a name="general-purpose-virtual-machine-sizes"></a>一般用途的虛擬機器大小

一般用途的虛擬機器大小可讓 CPU 與記憶體比例達到平均。 適用於測試和開發、小型至中型資料庫，以及低至中流量 Web 伺服器。 本文提供有關通用計算產品的資訊。

- [Av2 系列](av2-series.md)VM 可以部署在各種硬體類型和處理器上。 A 系列 VM 的 CPU 效能及記憶體設定最適合初階的工作負載，例如開發及測試， 根據硬體節流大小，為執行中的執行個體提供一致的處理器效能，不論硬體部署的位置。 若要判斷此大小部署所在的實體硬體，請從虛擬機器內查詢虛擬硬體。 使用案例範例包括開發與測試伺服器、低流量網頁伺服器、中小型資料庫、概念證明以及程式碼存放庫。

  > [!NOTE]
  > A8 + A11 VM 計畫于 2021 年 3 月 3 日停用。 有關詳細資訊，請參閱[HPC 遷移指南](https://azure.microsoft.com/resources/hpc-migration-guide/)。

- [B 系列可突發](sizes-b-series-burstable.md)VM 非常適合不需要 CPU 持續完全性能的工作負載，如 Web 服務器、小型資料庫以及開發和測試環境。 這些工作負載通常具有高載的效能需求。 B 系列讓這些客戶所購買到的 VM 大小具有價格公道的基準效能，可在 VM 利用低於其基底效能時，讓 VM 執行個體累積點數。 當 VM 累積點數時，VM 可以在您的應用程式需要較高的 CPU 效能時，使用最多 100% 的 CPU 來高載高於 VM 基準。

- [Dav4 和 Dasv4 系列](dav4-dasv4-series.md)採用 AMD 的 2.35Ghz EPYC<sup>TM</sup> 7452 處理器，採用多執行緒配置，將高達 256 MB L3 緩存的 8 GB L3 緩存專用於每 8 個內核中，增加了客戶運行通用工作負載的選項。 Dav4 系列和 Dasv4 系列具有與 D & Dsv3 系列相同的記憶體和磁片配置。

- [DCv2 系列](dcv2-series.md)可説明保護資料和代碼在公共雲中處理時的機密性和完整性。 這些機器由最新一代英特爾 XEON E-2288G 處理器支援，配備新GX技術。 借助英特爾渦輪提升技術，這些機器可高達 5.0GHz。 DCv2 系列實例使客戶能夠構建基於安全區的安全應用程式，以便在代碼和資料使用時保護其代碼和資料。

- [Dv2 和 Dsv2 系列](dv2-dsv2-series.md)VM 是原始 D 系列的追隨者，具有更強大的 CPU 和最佳的 CPU 到記憶體配置，適合大多數生產工作負載。 Dv2 系列比 D 系列快約 35%。 Dv2 系列在英特爾® Xeon ® 8171M 2.1GHz（天湖）、英特爾® Xeon ® E5-2673 v4 2.3 GHz（Broadwell）或英特爾® Xeon ® E5-2673 v3 2.4 GHz （哈斯韋爾）處理器上運行，英特爾渦輪增壓技術 2.0。 Dv2 系列的記憶體和磁碟組態和 D 系列一樣。

- [Dv3 和 Dsv3 系列](dv3-dsv3-series.md)VM 在英特爾® Xeon ® 8171M 2.1GHz（Skylake）、英特爾® Xeon ® E5-2673 v4 2.3 GHz（Broadwell）或英特爾® Xeon ® E5-2673 v3 2.4 GHz （Haswell） 處理器上運行，為大多數通用工作負載提供更好的價值主張。 除了記憶體已擴充 (從 ~3.5 GiB/vCPU 到 4 GiB/vCPU)，磁碟和網路限制也已就個別核心進行調整，以符合移轉至超執行緒的需求。 Dv3 系列不再有 D/Dv2 系列的高記憶體 VM 大小，這些 VM 大小已移動到記憶體優化的[Ev3 和 Esv3 系列](ev3-esv3-series.md)。

示例 D 系列用例包括企業級應用程式、關係資料庫、記憶體緩存和分析。

## <a name="other-sizes"></a>其他大小

- [計算最佳化](sizes-compute.md)
- [記憶體優化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [上一代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。