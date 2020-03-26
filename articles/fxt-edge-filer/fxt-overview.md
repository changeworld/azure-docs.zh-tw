---
title: Microsoft Azure FXT Edge Filer 概觀
description: 說明 Azure FXT Edge Filer 混合式儲存體快取，這是有效的封存和檔案存取加速器解決方案，可實現高效能運算
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: acf35015ff7851f7ea2a2527852be2573512e35d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "72254836"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>什麼是 Azure FXT Edge Filer 混合式儲存體快取？

Azure FXT Edge Filer 是混合式儲存體快取設備，可提供快速的檔案存取和有效的封存功能，以供進行高效能運算 (HPC) 工作。

其可搭配多個資料來源運作，不論是儲存在本機資料中心、遠端位置或雲端中都可以。 Azure FXT Edge Filer 可以為各種儲存系統中的資料提供統一的命名空間。

讓三個以上的 FXT Edge Filer 硬體裝置以叢集檔案系統的形式一起運作即可提供快取。 如需購買所需硬體的詳細資訊，請連絡 Microsoft 業務代表。 

若要深入了解，請閱讀 [Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/) 上的產品資訊和資料表。

## <a name="use-cases"></a>使用案例

Azure FXT Edge Filer 最適合針對下列工作流程來增強生產力：

* 需進行大量讀取作業的檔案存取工作流程 
* NFSv3 或 SMB2 通訊協定
* 具有 1000 到 100,000 個 CPU 核心的計算伺服器陣列

### <a name="nas-optimization-and-scaling"></a>NAS 最佳化和調整

您可以使用 Azure FXT Edge Filer 快取來順暢地存取現有 NetApp 和 Dell EMC Isilon NAS 系統。 您也可以新增 Azure Blob 或其他雲端儲存體以提供延展性，而不必在用戶端上重做資料存取流程。 

### <a name="wan-caching"></a>WAN 快取

當所需資料儲存在其他位置時，Azure FXT Edge Filer 可支援讓進階使用者快速存取檔案。 提供存取，同時又在集中的資料中心內保有備份和其他資料管理系統。 

### <a name="active-archive-in-azure-blob"></a>在 Azure Blob 中有效封存

以 Azure FXT Edge Filer 作為存取點來將資料中心擴充到雲端儲存體。 

## <a name="features"></a>特性 

有提供兩個硬體機型。 

| 模型 | DRAM | NVMe SSD | 網路連接埠 | 
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25.6 TB | 6 x 25Gb/10Gb + 2 x 1Gb |
| FXT 6400 | 768 GB | 12.8 TB | 6 x 25Gb/10Gb + 2 x 1Gb |


## <a name="next-steps"></a>後續步驟

* 請閱讀[規格](fxt-specs.md)或[安裝教學課程](fxt-install.md)來繼續了解 Azure FXT Edge Filer。
* 在 [Azure FXT Edge Filer 產品頁面](https://azure.microsoft.com/services/fxt-edge-filer/)上了解如何購買 Azure FXT Edge Filer。