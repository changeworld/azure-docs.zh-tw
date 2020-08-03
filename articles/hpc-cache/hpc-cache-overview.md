---
title: Azure HPC Cache 概觀
description: 說明 Azure HPC Cache，這是高效能運算的檔案存取加速器解決方案
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 07/16/2020
ms.author: v-erkel
ms.custom: references_regions
ms.openlocfilehash: 8c8223872ec3ef1d875e10ead8f327db01a924c4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87053675"
---
# <a name="what-is-azure-hpc-cache"></a>什麼是 Azure HPC Cache？

Azure HPC Cache 可加快資料存取速度，以進行高效能運算 (HPC) 工作。 藉由在 Azure 中快取檔案，Azure HPC Cache 會將雲端運算的延展性帶入您現有的工作流程。 這項服務甚至可用於透過 WAN 連結儲存資料的工作流程，例如在您的本機資料中心網路連接儲存裝置 (NAS) 環境中。

Azure HPC Cache 可輕鬆地從 Azure 入口網站啟動並進行監視。 現有的 NFS 儲存體或新的 Blob 容器可以成為其匯總命名空間的一部分，這會讓用戶端存取變得簡單，即使您變更後端儲存體目標也是如此。

## <a name="overview-video"></a>概觀影片

[![影片縮圖：Azure HPC 快取概觀 - 按一下以造訪影片頁面](media/video-1-overview.png)](https://azure.microsoft.com/resources/videos/hpc-cache-overview/)

按一下上方的影像以觀看 [簡短的 Azure HPC 快取概觀](https://azure.microsoft.com/resources/videos/hpc-cache-overview/)。

## <a name="use-cases"></a>使用案例

Azure HPC Cache 最適合針對下列工作流程來增強生產力：

* 需進行大量讀取作業的檔案存取工作流程
* 儲存在可存取 NFS 儲存體、Azure Blob 或兩者中的資料
* 最多 75,000 個 CPU 核心的計算伺服器陣列

Azure HPC Cache 可以加入至許多產業的各種不同工作流程中。 任何大量電腦需要大規模存取一組檔案，且延遲低的系統，將可受益於此服務。 下列各節提供特定的範例。

### <a name="visual-effects-vfx-rendering"></a>視覺效果 (VFX) 轉譯

在媒體和娛樂的領域中，Azure HPC Cache 可加快資料存取速度，以支援講求時效性的轉譯專案。 VFX 轉譯工作流程通常需要大量計算節點的最後處理。 這些工作流程的資料通常位於內部部署 NAS 環境中。 Azure HPC Cache 可以快取雲端中的檔案資料，以降低延遲並提高隨需轉譯的彈性。

### <a name="life-sciences"></a>生命科學

許多生命科學工作流程都可以從向外延展檔案快取中獲益。

想要將其基因分析工作流程移植到 Azure 的研究部門，可以使用 Azure HPC Cache 輕鬆地轉移它們。 因為快取提供 POSIX 檔案存取，所以不需要任何用戶端變更，即可在雲端執行其現有的用戶端工作流程。

您也可以利用 Azure HPC Cache 來改善工作 (例如，次要分析、藥理模擬或 AI 驅動的影像分析) 的效率。

### <a name="financial-services-analytics"></a>金融服務分析

Azure HPC Cache 佈署可協助加速量化的分析計算、風險分析工作負載，以及蒙地卡羅模擬分析，讓金融服務公司能夠更深入地進行策略決策。

## <a name="region-availability"></a>區域可用性

[依區域造訪 Azure 區域基礎結構產品](https://azure.microsoft.com/global-infrastructure/services/?products=hpc-cache)頁面，以了解可使用 Azure HPC Cache 的位置。

只有下列 Azure 區域支援[客戶管理的金鑰功能](customer-keys.md)：

* 美國東部
* 美國中南部
* 美國西部 2
* 歐洲西部
* US Gov 亞利桑那州
* US Gov 維吉尼亞州

## <a name="service-availability"></a>服務可用性

您必須針對每個將與 Azure HPC Cache 搭配使用的訂用帳戶要求存取權。 此限制有助於確保服務品質。

填寫[這份表單](https://aka.ms/onboard-hpc-cache)以要求存取權。 將訂用帳戶新增至存取清單之後，即可建立快取。

## <a name="next-steps"></a>後續步驟

* 若要深入了解其功能，請參閱 [Azure HPC Cache 產品頁面](https://azure.microsoft.com/services/hpc-cache)
* 了解產品[必要條件](hpc-cache-prerequisites.md)
* 從 Azure 入口網站[建立 Azure HPC Cache](hpc-cache-create.md)
