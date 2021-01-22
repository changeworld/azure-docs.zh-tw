---
title: 選擇定價層
titleSuffix: Azure Cognitive Search
description: 瞭解 Azure 認知搜尋的定價層 (或 Sku) 。 您可以在下列層級布建搜尋服務：免費、基本和標準。 標準可在各種資源設定和容量層級中使用。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: 320f02f6ece106b4d0e14293f95533aa5b4e0743
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98693446"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>選擇 Azure 認知搜尋的定價層

[建立搜尋服務](search-create-service-portal.md)的一部分，表示選擇在服務存留期內固定的定價層 (或 SKU) 。 當您建立服務時，在入口網站的 [ **選取定價層** ] 頁面中，會顯示價格或執行服務的每月預估成本。 如果您是透過 PowerShell 或 Azure CLI 來布建，則會透過參數指定階層 **`-Sku`** ，您應該檢查 [服務定價](https://azure.microsoft.com/pricing/details/search/) 以瞭解預估成本。

您選取的層會決定：

+ 服務上允許的最大索引數目和其他物件
+ 分割區的大小和速度 (實體儲存體)
+ 以固定每月費用計費的計費費率，如果您增加容量，也會遞增成本

在少數的情況下，您選擇的層會決定 [premium 功能](#premium-features)的可用性。

> [!NOTE]
> 正在尋找「Azure Sku」的相關資訊嗎？ 開始使用 [Azure 定價](https://azure.microsoft.com/pricing/) ，然後向下滾動以取得每個服務定價頁面的連結。

## <a name="tier-descriptions"></a>層描述

層級包括 **免費**、 **基本**、 **標準** 和 **儲存體優化**。 標準和儲存體優化可透過數個設定和容量提供。 下列來自 Azure 入口網站的螢幕擷取畫面顯示可在入口網站和 [ [定價] 頁面](https://azure.microsoft.com/pricing/details/search/)上找到的可用層（減去定價 (）。 

:::image type="content" source="media/search-sku-tier/tiers.png" alt-text="定價層圖表" border="true":::

**Free** 可針對較小的專案建立有限的搜尋服務，例如執行教學課程和程式碼範例。 就內部而言，系統資源會在多個訂閱者之間共用。 您無法調整免費服務或執行大量的工作負載。

**基本** 和 **標準** 是最常用的可計費層， **標準** 是預設值，因為它可讓您更靈活地調整工作負載。 您可以透過控制項下的專用資源，部署較大型的專案、將效能優化，以及增加容量。

某些層級是針對特定工作類型所設計。 例如， **標準3高密度 (S3 HD)** 是適用于 s3 的 *主機模式* ，其中基礎硬體已針對大量較小的索引進行優化，且適用于多租使用者案例。 S3 HD 具有與 S3 相同的每單位費用，但硬體已針對大量較小的索引進行快速檔案讀取優化。

**儲存體優化** 層以比標準層更低的價格提供較大的儲存體容量。 主要的取捨是較高的查詢延遲，您應該針對特定的應用程式需求進行驗證。 若要深入瞭解此層的效能考慮，請參閱 [效能與優化考慮](search-performance-optimization.md)。

您可以在 [ [定價] 頁面](https://azure.microsoft.com/pricing/details/search/)、Azure 認知搜尋文章中的 [服務限制](search-limits-quotas-capacity.md) ，以及當您布建服務時的入口網站頁面上，找到各層的詳細資訊。

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>依層級的功能可用性

大部分的功能都可在所有層級上使用，包括免費層。 在少數情況下，您選擇的層級會影響您執行功能的能力。 下表說明與服務層相關的功能條件約束。

| 特徵 | 限制 |
|---------|-------------|
| [索引](search-indexer-overview.md) | S3 HD 上無法使用索引子。  |
| [AI 擴充](search-security-manage-encryption-keys.md) | 在免費層上執行，但不建議使用。 |
| [輸出 (索引子的受控或信任身分識別) 存取](search-howto-managed-identities-data-sources.md) | 無法在免費層上使用。|
| [客戶管理的加密金鑰](search-security-manage-encryption-keys.md) | 無法在免費層上使用。 |
| [IP 防火牆存取](service-configure-firewall.md) | 無法在免費層上使用。 |
| [私人端點 (與 Azure Private Link) 整合 ](service-create-private-endpoint.md) | 針對搜尋服務的輸入連線，在免費層中無法使用。 針對其他 Azure 資源的索引子的輸出連線，在免費或 S3 HD 上無法使用。 適用于使用技能集的索引子，不適用於 Free、Basic、S1 或 S3 HD。|

除非您提供足夠的容量，否則耗用大量資源的功能可能無法正常運作。 例如，除非資料集很小，否則 [AI 擴充](cognitive-search-concept-intro.md) 具有長時間執行的技能，可在免費服務上運作。

## <a name="upper-limits"></a>上限

層級會決定服務本身的最大儲存空間，以及您可以建立的索引、索引子、資料來源、技能集和同義字對應的最大數目。 如需完全中斷所有限制，請參閱 [Azure 認知搜尋中的服務限制](search-limits-quotas-capacity.md)。 

## <a name="partition-size-and-speed"></a>分割區大小和速度

階層定價包含每個資料分割儲存體的詳細資料，範圍從 2 GB 到基本，最高可達 2 TB 的儲存體優化 (L2) 層。 其他硬體特性（例如作業速度、延遲和傳輸速率）則不會發佈，但針對特定解決方案架構設計的層級是以具有支援這些案例之功能的硬體為基礎。 如需有關資料分割的詳細資訊，請參閱[估計和管理](search-capacity-planning.md)[效能的](search-performance-optimization.md)容量和規模。

## <a name="billing-rates"></a>帳單費率

階層具有不同的計費費率，以較高的費率在較昂貴的硬體上執行，或提供更昂貴的功能。 您可以在適用于 Azure 認知搜尋的 [Azure 定價頁面](https://azure.microsoft.com/pricing/details/search/) 中找到各層的計費費率。

一旦您建立服務之後，計費費率會成為在時鐘周圍執行服務的 *固定成本* ，而如果您選擇增加更多容量，則會產生 *遞增成本* 。

搜尋服務是以資料分割 *格式 (儲存*) 的計算資源，以及查詢引擎)  (實例的 *複本* 。 一開始，會使用其中一個服務來建立服務，而且計費費率會包含這兩個資源。 不過，如果您調整容量，成本會以計費費率的遞增量增加或減少。

下列範例提供說明。 假設每個月有一個假設的計費率為 $100。 如果您將搜尋服務保持在一個資料分割的初始容量和一個複本，則 $100 是您可以預期在該月結束時支付的費用。 但是，如果您新增兩個以上的複本來達成高可用性，每月帳單會增加到 $300 ($100 （針對第一個複本分割組），後面接著 $200 兩個複本) 。

此計價模式是根據搜尋服務所使用 (SU) 的數位 *搜尋單位* 來套用帳單費率的概念。 所有服務一開始都是在一個 SU 布建，但您可以藉由新增分割區或複本來處理較大的工作負載，來增加 SU。 如需詳細資訊，請參閱 [如何估計搜尋服務的成本](search-sku-manage-costs.md)。

## <a name="next-steps"></a>後續步驟

選擇定價層的最佳方式是從最便宜的層開始，然後允許經驗和測試通知您決定要保留服務，或在較高的層級建立新的。 在接下來的步驟中，我們建議您在可容納您所建議的測試層級的層級上建立搜尋服務，然後參閱下列指導方針，以取得預估成本和容量的建議。

+ [建立搜尋服務](search-create-service-portal.md)
+ [預估成本](search-sku-manage-costs.md)
+ [估計容量](search-sku-manage-costs.md)