---
title: 如何設定 Azure Cache for Redis 的異地複寫 |Microsoft Docs
description: 了解如何跨地理區域複寫您的 Azure Cache for Redis 執行個體。
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: e4739c0c550988e1639e89a647815e5dd86b17b7
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91461337"
---
# <a name="how-to-set-up-geo-replication-for-azure-cache-for-redis"></a>如何設定 Azure Cache for Redis 的異地複寫

異地複寫提供一個機制，可連結兩個進階層 Azure Cache for Redis 執行個體。 其中一個快取會選擇作為主要連結快取，另一個則是次要連結快取。 次要連結快取會變成唯讀，而寫入主要快取的資料會複寫至次要連結快取。 主要和次要快取實例之間的資料傳輸是由 TLS 保護。 異地複寫可以用來設定跨越兩個 Azure 區域的快取。 本文提供為您的進階層 Azure Cache for Redis 實例設定異地複寫的指南。

> [!NOTE]
> 異地複寫是設計為損毀修復解決方案。 根據預設，您的應用程式會在主要區域中寫入和讀取。 您可以選擇性地將它設定為從次要區域讀取。 如果您的應用程式的其餘部分保留在主要區域中，則異地複寫不會提供自動容錯移轉，因為這些區域間的網路延遲已增加。 您必須取消連結次要快取，以管理和起始容錯移轉。 這會將它升階為新的主要實例。

## <a name="geo-replication-prerequisites"></a>異地複寫的必要條件

若要設定兩個快取之間的異地複寫，必須符合下列必要條件：

- 這兩個快取都是 [Premium 層](cache-overview.md#service-tiers) 快取。
- 這兩個快取都位於相同的 Azure 訂用帳戶中。
- 次要連結快取的快取大小，或較大的快取大小，與主要連結快取的大小相同。
- 這兩個快取都會建立並處於執行中狀態。

異地複寫不支援某些功能：

- 異地複寫不支援持續性。
- 如果這兩個快取已啟用叢集，且具有相同的分區數目，則支援叢集。
- 支援相同 VNET 中的快取。
- 不同 Vnet 中的快取會受到支援，但有一些注意事項。 如需詳細資訊，請參閱 [我可以在 VNET 中使用異地](#can-i-use-geo-replication-with-my-caches-in-a-vnet) 複寫搭配我的快取？。

設定異地複寫之後，下列限制適用于您的連結快取配對：

- 次要連結快取是唯讀的；您可以從中讀取，但無法寫入任何資料。 
- 任何將連結新增之前就已在次要連結快取中的資料都會加以移除。 但是，如果稍後移除異地複寫，則複寫的資料會保留在次要連結快取中。
- 當快取連結時，您無法 [調整](cache-how-to-scale.md) 其中一個快取。
- 如果快取已啟用叢集，您就無法 [變更分區數目](cache-how-to-premium-clustering.md) 。
- 您無法啟用任一個快取的持續性。
- 您可以從任一快取 [匯出](cache-how-to-import-export-data.md#export) 。
- 您無法匯 [入](cache-how-to-import-export-data.md#import) 至次要連結快取。
- 您無法刪除連結快取或包含它們的資源群組，直到您取消連結快取為止。 如需詳細資訊，請參閱[當我嘗試刪除連結快取時，作業失敗的原因？](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- 如果快取位於不同的區域，則會將網路輸出成本套用至跨區域移動的資料。 如需詳細資訊，請參閱[跨 Azure 區域複寫我的資料需要多少費用？](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- 主要與次要連結快取之間不會進行自動容錯移轉。 如需有關如何容錯移轉用戶端應用程式的詳細資訊和詳細資訊，請參閱 [容錯移轉至次要連結快取如何運作？](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>新增異地複寫連結

1. 若要將兩個快取連結在一起以進行異地複寫，請第一個從您想要作為主要連結快取的快取的 [資源] 功能表中，按一下 [ **異地** 複寫]。 接著，按一下 [新增**異地**複寫] 分頁的 [新增快取複寫]**連結**。

    ![新增連結](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. 從 **相容** 的快取清單中，按一下您想要的次要快取名稱。 如果您的次要快取未顯示在清單中，請確認已符合次要快取的 [異地複寫必要條件](#geo-replication-prerequisites) 。 若要依區域篩選快取，請按一下對應中的區域，只顯示 **相容** 快取清單中的快取。

    ![異地複寫相容的快取](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    您也可以使用操作功能表來啟動連結程式，或查看次要快取的詳細資料。

    ![異地複寫操作功能表](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. 按一下 [連結]**** 可將兩個快取連結在一起，並開始複寫流程。

    ![連結快取](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. 您可以在 [異地複寫]**** 刀鋒視窗上檢視複寫流程的進度。

    ![連結狀態](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    您也可以在 [概觀]**** 刀鋒視窗上檢視主要和次要快取的連結狀態。

    ![快取狀態](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    一旦複寫程序完成之後，[連結狀態]**** 會變為 [成功]****。

    ![快取狀態](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    主要連結快取在連結程式期間仍可供使用。 在連結處理常式完成之前，無法使用次要連結快取。

## <a name="remove-a-geo-replication-link"></a>移除異地複寫連結

1. 若要移除兩個快取之間的連結並停止異地複寫，請按一下 [ **取消連結** 來自 **異地** 複寫的快取] 分頁。
    
    ![取消連結快取](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    取消連結流程完成時，次要快取就可供讀取和寫入。

>[!NOTE]
>移除異地複寫連結時，主要連結快取中的複寫資料仍會保留在次要快取中。
>
>

## <a name="geo-replication-faq"></a>異地複寫常見問題集

- [我可以搭配標準或基本層快取使用異地複寫嗎？](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [在連結或取消連結流程期間，快取是否可供使用？](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [可以同時連結兩個以上的快取嗎？](#can-i-link-more-than-two-caches-together)
- [可以將不同 Azure 訂用帳戶中的兩個快取加以連結嗎？](#can-i-link-two-caches-from-different-azure-subscriptions)
- [可以將兩個不同大小的快取加以連結嗎？](#can-i-link-two-caches-with-different-sizes)
- [是否可以在啟用叢集的情況下使用異地複寫？](#can-i-use-geo-replication-with-clustering-enabled)
- [我可以使用異地複寫搭配 VNET 中的快取嗎？](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [哪個複寫排程適用於 Redis 異地複寫？](#what-is-the-replication-schedule-for-redis-geo-replication)
- [異地複寫需要花費多久的時間？](#how-long-does-geo-replication-replication-take)
- [是否保證複寫復原點？](#is-the-replication-recovery-point-guaranteed)
- [我可以使用 PowerShell 或 Azure CLI 來管理異地複寫嗎？](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [跨 Azure 區域複寫我的資料需要多少費用？](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [當我嘗試刪除連結快取時，作業失敗的原因？](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [要將次要連結快取用於哪個區域？](#what-region-should-i-use-for-my-secondary-linked-cache)
- [容錯移轉至次要連結快取如何運作？](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>我可以搭配標準或基本層快取使用異地複寫嗎？

否，異地複寫僅適用于進階層快取。

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>在連結或取消連結流程期間，快取是否可供使用？

- 連結時，主要連結快取會在連結進程完成時保持可用。
- 連結時，在連結處理常式完成之前，無法使用次要連結快取。
- 取消連結時，在取消連結的程式完成時，這兩個快取會保持可用。

### <a name="can-i-link-more-than-two-caches-together"></a>可以同時連結兩個以上的快取嗎？

否，您只能將兩個快取連結在一起。

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>可以將不同 Azure 訂用帳戶中的兩個快取加以連結嗎？

否，這兩個快取必須位於相同的 Azure 訂用帳戶。

### <a name="can-i-link-two-caches-with-different-sizes"></a>可以將兩個不同大小的快取加以連結嗎？

是，前提是次要連結快取超過主要連結快取。

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>是否可以在啟用叢集的情況下使用異地複寫？

是，前提是這兩個快取具有相同的分區數目。

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>我可以使用異地複寫搭配 VNET 中的快取嗎？

是，支援 Vnet 中的快取異地複寫，但有一些注意事項：

- 支援在相同 VNET 中多個快取之間的異地複寫。
- 也支援不同 Vnet 中的快取之間的異地複寫。
  - 如果 Vnet 位於相同的區域，您可以使用 [vnet 對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 或 [VPN 閘道 vnet 對 vnet 連線來](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V)進行連接。
  - 如果 Vnet 位於不同的區域，則會支援使用 VNET 對等互連的異地複寫，但是 VNET 1 (區域 1) 的用戶端 VM 將無法透過其 DNS 名稱存取 VNET)  (2 中的快取，因為基本的內部負載平衡器具有條件約束。 如需 VNET 對等互連條件約束的詳細資訊，請參閱 [虛擬網路對等互連-需求和條件約束](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints)。 建議的解決方案是使用 VPN 閘道的 VNET 對 VNET 連線。
  
您可以使用 [此 Azure 範本](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)，快速地將兩個異地複寫的快取部署到與 VPN 閘道 VNET 對 vnet 連線連線的 VNET 中。

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>哪個複寫排程適用於 Redis 異地複寫？

複寫是連續且非同步，且不會在特定排程上進行。 所有對主資料庫所做的寫入都會立即以非同步方式複寫到次要複本。

### <a name="how-long-does-geo-replication-replication-take"></a>異地複寫需要花費多久的時間？

複寫是累加、非同步和連續的，而所花費的時間與跨區域的延遲並無太大差異。 在某些情況下，次要快取可能需要從主要複本進行完整的資料同步處理。 在此情況下，複寫時間取決於下列因素的數目：主要快取上的負載、可用的網路頻寬，以及區域間延遲。 我們找到完整 53 GB 異地複寫配對的複寫時間可以在5到10分鐘之間的任何位置。

### <a name="is-the-replication-recovery-point-guaranteed"></a>是否保證複寫復原點？

若是異地複寫模式中的快取，則會停用持續性。 如果未連結異地複寫的配對（例如客戶起始的容錯移轉），次要連結快取就會將其同步處理的資料保留到該時間點。 在這種情況下，不保證復原點。

若要取得復原點，請從任一快取 [匯出](cache-how-to-import-export-data.md#export) 。 您稍後可以在主要連結快取中匯 [入](cache-how-to-import-export-data.md#import) 。

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>我可以使用 PowerShell 或 Azure CLI 來管理異地複寫嗎？

是的，您可以使用 Azure 入口網站、PowerShell 或 Azure CLI 來管理異地複寫。 如需詳細資訊，請參閱 [PowerShell](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) 檔或 [Azure CLI](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest)檔。

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>跨 Azure 區域複寫我的資料需要多少費用？

使用異地複寫時，主要連結快取的資料會複寫到次要連結快取。 如果兩個連結快取位於相同的區域，則資料傳輸不會收取任何費用。 如果兩個連結快取位於不同的區域，則資料傳輸費用是資料在任一區域間移動的網路輸出成本。 如需詳細資訊，請參閱 [頻寬定價詳細資料](https://azure.microsoft.com/pricing/details/bandwidth/)。

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>當我嘗試刪除連結快取時，作業失敗的原因？

除非您移除異地複寫連結，否則無法在連結時刪除異地複寫的快取和其資源群組。 如果您嘗試將包含一個或兩個連結快取的資源群組刪除，就會將資源群組中的其他資源刪除，但資源群組會保留在 `deleting` 狀態，而資源群組中的任何連結快取會維持 `running` 狀態。 若要完全刪除資源群組和其中的連結快取，請將快取取消連結，如 [移除異地複寫連結](#remove-a-geo-replication-link)中所述。

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>要將次要連結快取用於哪個區域？

一般情況下，建議您的快取存在於與存取它的應用程式相同的 Azure 區域中。 針對具有個別主要和備用區域的應用程式，建議您的主要和次要快取存在於這些相同區域中。 如需配對區域的詳細資訊，請參閱[最佳做法 – Azure 配對的區域](../best-practices-availability-paired-regions.md)。

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>容錯移轉至次要連結快取如何運作？

異地複寫的快取不支援跨 Azure 區域自動容錯移轉。 在嚴重損壞修復案例中，客戶應該以協調的方式在其備份區域中顯示整個應用程式堆疊。 讓個別應用程式元件決定何時自行切換至其備份，可能會對效能造成負面影響。 Redis 的主要優點之一是，它是非常低延遲的存放區。 如果客戶的主應用程式與其快取位於不同的區域，則新增的往返時間會對效能造成明顯的影響。 基於這個理由，我們會避免因為暫時性可用性問題而自動容錯移轉。

若要啟動客戶起始的容錯移轉，請先取消連結快取。 然後，將您的 Redis 用戶端變更為使用先前連結) 次要快取 (的連接端點。 當兩個快取都未連結時，次要快取會再次變成一般讀寫快取，並直接接受來自 Redis 用戶端的要求。

## <a name="next-steps"></a>後續步驟
深入瞭解 Azure Cache for Redis 功能。

* [Azure Cache for Redis 服務層級](cache-overview.md#service-tiers)
