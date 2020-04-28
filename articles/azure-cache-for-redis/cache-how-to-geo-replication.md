---
title: 如何設定 Azure Cache for Redis 的異地複寫 |Microsoft Docs
description: 了解如何跨地理區域複寫您的 Azure Cache for Redis 執行個體。
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: ce50c665fa79c361f638fda4ec373d5215c407f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74129424"
---
# <a name="how-to-set-up-geo-replication-for-azure-cache-for-redis"></a>如何設定 Azure Cache for Redis 的異地複寫

異地複寫提供一個機制，可連結兩個進階層 Azure Cache for Redis 執行個體。 其中一個快取會選擇作為主要連結快取，另一個則做為次要連結快取。 次要連結快取會變成唯讀，而寫入主要快取的資料會複寫至次要連結快取。 這項功能可用來跨 Azure 區域複寫快取。 本文提供的指南可為您的 Premium 層 Azure Cache for Redis 實例設定異地複寫。

## <a name="geo-replication-prerequisites"></a>異地複寫的必要條件

若要設定兩個快取之間的異地複寫，必須符合下列必要條件：

- 這兩個快取都是高階[層](cache-premium-tier-intro.md)快取。
- 這兩個快取都位於相同的 Azure 訂用帳戶中。
- 次要連結快取的快取大小，或比主要連結快取更大的快取大小。
- 這兩個快取都會建立並處於執行中狀態。

異地複寫不支援某些功能：

- 異地複寫不支援持續性。
- 如果兩個快取都已啟用叢集，而且有相同數目的分區，則支援叢集。
- 支援相同 VNET 中的快取。
- 支援不同 Vnet 中的快取，但有一些注意事項。 如需詳細資訊，請參閱[我可以在 VNET 中搭配使用異地複寫與我](#can-i-use-geo-replication-with-my-caches-in-a-vnet)的快取嗎？

設定異地複寫之後，下列限制適用于您的連結快取配對：

- 次要連結快取是唯讀的；您可以從中讀取，但無法寫入任何資料。 
- 任何將連結新增之前就已在次要連結快取中的資料都會加以移除。 不過，如果稍後移除異地複寫，則複寫的資料會保留在次要連結快取中。
- 當快取連結時，您無法[調整](cache-how-to-scale.md)任一快取。
- 如果快取已啟用叢集，您就無法[變更分區的數目](cache-how-to-premium-clustering.md)。
- 您無法啟用任一個快取的持續性。
- 您可以從任一快取[匯出](cache-how-to-import-export-data.md#export)。
- 您無法匯[入](cache-how-to-import-export-data.md#import)到次要連結快取中。
- 您無法刪除其中一個連結快取或包含它們的資源群組，直到您取消連結快取為止。 如需詳細資訊，請參閱[當我嘗試刪除連結快取時，作業失敗的原因？](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- 如果快取位於不同的區域，則網路輸出成本適用于跨區域移動的資料。 如需詳細資訊，請參閱[跨 Azure 區域複寫我的資料需要多少費用？](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- 主要與次要連結快取之間不會進行自動容錯移轉。 如需有關如何容錯移轉用戶端應用程式的詳細資訊和資訊，請參閱[容錯移轉至次要連結快取如何運作？](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>新增異地複寫連結

1. 若要將兩個快取連結在一起以進行異地複寫，請第一個按一下您要作為主要連結快取之快取的 [資源] 功能表中的 [**異地**複寫]。 接下來，按一下 [**異地**複寫] 分頁中的 [新增快取複寫**連結**]。

    ![新增連結](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. 從 [**相容**的快取] 清單中，按一下您想要的次要快取名稱。 如果您的次要快取未顯示在清單中，請確認已符合次要快取的[地理複寫必要條件](#geo-replication-prerequisites)。 若要依區域篩選快取，請按一下地圖中的區域，只在 [**相容**的快取] 清單中顯示那些快取。

    ![異地複寫相容的快取](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    您也可以使用操作功能表來啟動連結程式或查看次要快取的詳細資料。

    ![異地複寫操作功能表](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. 按一下 [連結]**** 可將兩個快取連結在一起，並開始複寫流程。

    ![連結快取](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. 您可以在 [異地複寫]**** 刀鋒視窗上檢視複寫流程的進度。

    ![連結狀態](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    您也可以在 [概觀]**** 刀鋒視窗上檢視主要和次要快取的連結狀態。

    ![快取狀態](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    一旦複寫程序完成之後，[連結狀態]**** 會變為 [成功]****。

    ![快取狀態](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    主要連結快取在連結程式期間仍可供使用。 在連結程式完成之前，無法使用次要連結快取。

## <a name="remove-a-geo-replication-link"></a>移除異地複寫連結

1. 若要移除兩個快取之間的連結並停止異地複寫，請按一下 [**異地**複寫] 分頁中的 [**取消**連結快取]。
    
    ![取消連結快取](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    取消連結流程完成時，次要快取就可供讀取和寫入。

>[!NOTE]
>移除異地複寫連結時，主要連結快取中複寫的資料會保留在次要快取中。
>
>

## <a name="geo-replication-faq"></a>異地複寫常見問題集

- [我可以使用異地複寫搭配標準或基本層快取嗎？](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [在連結或取消連結流程期間，快取是否可供使用？](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [可以同時連結兩個以上的快取嗎？](#can-i-link-more-than-two-caches-together)
- [可以將不同 Azure 訂用帳戶中的兩個快取加以連結嗎？](#can-i-link-two-caches-from-different-azure-subscriptions)
- [可以將兩個不同大小的快取加以連結嗎？](#can-i-link-two-caches-with-different-sizes)
- [可以在啟用叢集的情況下使用異地複寫嗎？](#can-i-use-geo-replication-with-clustering-enabled)
- [我可以在 VNET 中使用異地複寫搭配我的快取嗎？](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [哪個複寫排程適用於 Redis 異地複寫？](#what-is-the-replication-schedule-for-redis-geo-replication)
- [異地複寫需要花費多久的時間？](#how-long-does-geo-replication-replication-take)
- [是否保證複寫復原點？](#is-the-replication-recovery-point-guaranteed)
- [我可以使用 PowerShell 或 Azure CLI 來管理異地複寫嗎？](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [跨 Azure 區域複寫我的資料需要多少費用？](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [當我嘗試刪除連結快取時，作業失敗的原因？](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [要將次要連結快取用於哪個區域？](#what-region-should-i-use-for-my-secondary-linked-cache)
- [容錯移轉至次要連結快取如何運作？](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>我可以使用異地複寫搭配標準或基本層快取嗎？

否，異地複寫僅適用于進階層快取。

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>在連結或取消連結流程期間，快取是否可供使用？

- 連結時，主要連結快取會在連結程式完成時保持可用。
- 連結時，除非連結程式完成，否則無法使用次要連結快取。
- 取消連結時，當取消連結程式完成時，這兩個快取仍然可供使用。

### <a name="can-i-link-more-than-two-caches-together"></a>可以同時連結兩個以上的快取嗎？

不可以，您只能將兩個快取連結在一起。

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>可以將不同 Azure 訂用帳戶中的兩個快取加以連結嗎？

否，這兩個快取必須位於相同的 Azure 訂用帳戶。

### <a name="can-i-link-two-caches-with-different-sizes"></a>可以將兩個不同大小的快取加以連結嗎？

是，前提是次要連結快取超過主要連結快取。

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>可以在啟用叢集的情況下使用異地複寫嗎？

是，前提是這兩個快取具有相同的分區數目。

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>我可以在 VNET 中使用異地複寫搭配我的快取嗎？

是，支援 Vnet 中的快取異地複寫，但有幾點需要注意：

- 支援在相同 VNET 中多個快取之間的異地複寫。
- 也支援不同 Vnet 中的快取之間的異地複寫。
  - 如果 Vnet 位於相同的區域中，您可以使用[vnet 對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)或 VPN 閘道的[vnet 對 vnet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V)連線來加以連接。
  - 如果 Vnet 位於不同的區域中，則不支援使用 VNET 對等互連進行異地複寫，因為具有基本內部負載平衡器的條件約束。 如需 VNET 對等互連條件約束的詳細資訊，請參閱[虛擬網路對等互連-需求和條件約束](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints)。 建議的解決方案是使用 VPN 閘道的 VNET 對 VNET 連線。

使用[此 Azure 範本](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)，您可以將兩個異地複寫的快取快速部署到與 VPN 閘道 VNET 對 vnet 連線連接的 vnet 中。

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>哪個複寫排程適用於 Redis 異地複寫？

複寫是連續且非同步，不會依特定排程進行。 對主要複本進行的所有寫入都會以立即且非同步方式複寫到次要資料庫上。

### <a name="how-long-does-geo-replication-replication-take"></a>異地複寫需要花費多久的時間？

複寫是累加、非同步和連續，所花費的時間與跨區域的延遲沒有太大差異。 在某些情況下，次要快取可能需要從主要複本進行完整的資料同步處理。 在此情況下，複寫時間取決於下列因素的數目：主要快取的負載、可用的網路頻寬，以及區域間的延遲。 我們找到完整 53-GB 異地複寫配對的複寫時間可以介於5到10分鐘之間。

### <a name="is-the-replication-recovery-point-guaranteed"></a>是否保證複寫復原點？

若是異地複寫模式中的快取，則會停用持續性。 如果取消連結異地複寫的配對（例如客戶起始的容錯移轉），次要連結的快取會將其同步的資料保持在該時間點為止。 在這種情況下，不保證任何復原點。

若要取得復原點，請從任一快取[匯出](cache-how-to-import-export-data.md#export)。 您之後可以將匯[入](cache-how-to-import-export-data.md#import)到主要的連結快取中。

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>我可以使用 PowerShell 或 Azure CLI 來管理異地複寫嗎？

是，您可以使用 Azure 入口網站、PowerShell 或 Azure CLI 來管理異地複寫。 如需詳細資訊，請參閱[PowerShell](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache)檔或[Azure CLI](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest)檔。

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>跨 Azure 區域複寫我的資料需要多少費用？

使用異地複寫時，主要連結快取中的資料會複寫至次要連結快取。 如果兩個連結快取位於相同的區域中，則不會收取資料傳輸的費用。 如果兩個連結快取位於不同的區域中，則資料傳輸費用就是在任一區域移動資料的網路輸出成本。 如需詳細資訊，請參閱[頻寬定價詳細資料](https://azure.microsoft.com/pricing/details/bandwidth/)。

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>當我嘗試刪除連結快取時，作業失敗的原因？

在您移除異地複寫連結之前，無法刪除異地複寫的快取及其資源群組。 如果您嘗試將包含一個或兩個連結快取的資源群組刪除，就會將資源群組中的其他資源刪除，但資源群組會保留在 `deleting` 狀態，而資源群組中的任何連結快取會維持 `running` 狀態。 若要完全刪除資源群組和其中的已連結快取，請取消連結快取，如[移除異地複寫連結](#remove-a-geo-replication-link)中所述。

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>要將次要連結快取用於哪個區域？

一般來說，建議您的快取存在於與存取該快取的應用程式相同的 Azure 區域中。 針對具有不同主要和備用區域的應用程式，建議您的主要和次要快取存在於那些相同的區域中。 如需配對區域的詳細資訊，請參閱[最佳做法 – Azure 配對的區域](../best-practices-availability-paired-regions.md)。

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>容錯移轉至次要連結快取如何運作？

異地複寫快取不支援跨 Azure 區域自動容錯移轉。 在嚴重損壞修復案例中，客戶應該以協調的方式，在備份區域中啟動整個應用程式堆疊。 讓個別應用程式元件決定何時要自行切換到其備份，可能會對效能產生負面影響。 Redis 的其中一個主要優點是，它是非常低延遲的存放區。 如果客戶的主應用程式與快取位於不同的區域，則新增的來回時間可能會對效能造成明顯的影響。 基於這個理由，我們會避免因為暫時性可用性問題而自動容錯移轉。

若要啟動客戶起始的容錯移轉，請先取消快取的連結。 然後，將您的 Redis 用戶端變更為使用（先前連結）次要快取的連接端點。 當兩個快取未連結時，次要快取會再次變成一般的讀寫快取，並直接接受來自 Redis 用戶端的要求。

## <a name="next-steps"></a>後續步驟

深入了解 [Azure Cache for Redis 進階層](cache-premium-tier-intro.md)。
