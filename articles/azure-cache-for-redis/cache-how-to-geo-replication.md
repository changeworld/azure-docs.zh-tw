---
title: 如何為 Redis 設置 Azure 緩存的異地複製 |微軟文檔
description: 了解如何跨地理區域複寫您的 Azure Cache for Redis 執行個體。
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: ce50c665fa79c361f638fda4ec373d5215c407f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129424"
---
# <a name="how-to-set-up-geo-replication-for-azure-cache-for-redis"></a>如何為 Redis 設置 Azure 緩存的異地複製

異地複寫提供一個機制，可連結兩個進階層 Azure Cache for Redis 執行個體。 選擇一個緩存作為主連結緩存，另一個緩存作為輔助連結緩存。 次要連結快取會變成唯讀，而寫入主要快取的資料會複寫至次要連結快取。 這項功能可用來跨 Azure 區域複寫快取。 本文提供了為 Redis 實例配置高級層 Azure 緩存的異地複製的指南。

## <a name="geo-replication-prerequisites"></a>異地複寫的必要條件

要配置兩個緩存之間的異地複製，必須滿足以下先決條件：

- 兩個緩存都是[高級層](cache-premium-tier-intro.md)緩存。
- 兩個緩存都位於同一 Azure 訂閱中。
- 輔助連結緩存的大小與主連結緩存相同，或緩存大小更大。
- 兩個緩存都是創建並在運行狀態下創建的。

異地複製不支援某些功能：

- 異地複製不支援持久性。
- 如果兩個緩存都啟用了群集，並且分片數相同，則支援群集。
- 支援同一 VNET 中的緩存。
- 警告支援不同 VNET 中的緩存。 有關詳細資訊，請參閱[是否可以將異地複製與 VNET 中的緩存一起使用？](#can-i-use-geo-replication-with-my-caches-in-a-vnet)

配置異地複製後，以下限制將應用於連結的緩存對：

- 次要連結快取是唯讀的；您可以從中讀取，但無法寫入任何資料。 
- 任何將連結新增之前就已在次要連結快取中的資料都會加以移除。 但是，如果以後刪除了異地複製，則複製的資料將保留在輔助連結緩存中。
- 在連結緩存時，無法[縮放](cache-how-to-scale.md)任一緩存。
- 如果緩存已啟用群集，則無法[更改分片數](cache-how-to-premium-clustering.md)。
- 您無法啟用任一個快取的持續性。
- 可以從任一緩存[匯出](cache-how-to-import-export-data.md#export)。
- 無法[導入](cache-how-to-import-export-data.md#import)輔助連結緩存。
- 在取消連結緩存之前，不能刪除連結的緩存或包含連結的資源組。 如需詳細資訊，請參閱[當我嘗試刪除連結快取時，作業失敗的原因？](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- 如果緩存位於不同的區域，則網路出口成本將應用於跨區域移動的資料。 如需詳細資訊，請參閱[跨 Azure 區域複寫我的資料需要多少費用？](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- 主連結緩存和輔助連結緩存之間不會發生自動容錯移轉。 有關如何容錯移轉用戶端應用程式的詳細資訊和資訊，請參閱[容錯移轉到輔助連結緩存的工作原理？](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>添加異地複製連結

1. 要將兩個緩存連結在一起進行異地複製，請從要作為主要連結緩存的緩存的資源功能表中按一下 **"異地複製**"。 接下來，按一下"從**異地複製**邊欄選項卡**添加緩存複製連結**"。

    ![新增連結](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. 從 **"相容緩存**"清單中按一下預期輔助緩存的名稱。 如果輔助緩存未顯示在清單中，請驗證是否滿足輔助緩存的[異地複製先決條件](#geo-replication-prerequisites)。 要按區域篩選緩存，請按一下地圖中的區域以僅顯示 **"相容緩存**"清單中的緩存。

    ![異地複寫相容的快取](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    您還可以使用內容功能表啟動連結過程或查看有關輔助緩存的詳細資訊。

    ![異地複寫操作功能表](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. 按一下 [連結]**** 可將兩個快取連結在一起，並開始複寫流程。

    ![連結快取](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. 您可以在 [異地複寫]**** 刀鋒視窗上檢視複寫流程的進度。

    ![連結狀態](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    您也可以在 [概觀]**** 刀鋒視窗上檢視主要和次要快取的連結狀態。

    ![快取狀態](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    一旦複寫程序完成之後，[連結狀態]**** 會變為 [成功]****。

    ![快取狀態](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    主連結緩存仍可用於連結過程。 在連結過程完成之前，輔助連結緩存不可用。

## <a name="remove-a-geo-replication-link"></a>刪除異地複製連結

1. 要刪除兩個緩存之間的連結並停止異地複製，請按一下 **"異地複製**"邊欄選項卡中的 **"取消連結"緩存**。
    
    ![取消連結快取](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    取消連結流程完成時，次要快取就可供讀取和寫入。

>[!NOTE]
>刪除異地複製連結後，主連結緩存中的複製資料將保留在輔助緩存中。
>
>

## <a name="geo-replication-faq"></a>異地複寫常見問題集

- [是否可以將異地複製與標準層或基本層緩存一起使用？](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [在連結或取消連結流程期間，快取是否可供使用？](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [可以同時連結兩個以上的快取嗎？](#can-i-link-more-than-two-caches-together)
- [可以將不同 Azure 訂用帳戶中的兩個快取加以連結嗎？](#can-i-link-two-caches-from-different-azure-subscriptions)
- [可以將兩個不同大小的快取加以連結嗎？](#can-i-link-two-caches-with-different-sizes)
- [是否可以在啟用群集時使用異地複製？](#can-i-use-geo-replication-with-clustering-enabled)
- [是否可以將異地複製與 VNET 中的緩存一起使用？](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [哪個複寫排程適用於 Redis 異地複寫？](#what-is-the-replication-schedule-for-redis-geo-replication)
- [異地複寫需要花費多久的時間？](#how-long-does-geo-replication-replication-take)
- [是否保證複寫復原點？](#is-the-replication-recovery-point-guaranteed)
- [我可以使用 PowerShell 或 Azure CLI 來管理異地複製嗎？](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [跨 Azure 區域複寫我的資料需要多少費用？](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [當我嘗試刪除連結快取時，作業失敗的原因？](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [要將次要連結快取用於哪個區域？](#what-region-should-i-use-for-my-secondary-linked-cache)
- [容錯移轉至次要連結快取如何運作？](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>是否可以將異地複製與標準層或基本層緩存一起使用？

否，異地複製僅適用于高級層緩存。

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>在連結或取消連結流程期間，快取是否可供使用？

- 連結時，主連結緩存在連結過程完成時保持可用。
- 連結時，輔助連結緩存在連結過程完成之前不可用。
- 取消連結時，兩個緩存在取消連結過程完成時保持可用。

### <a name="can-i-link-more-than-two-caches-together"></a>可以同時連結兩個以上的快取嗎？

不可以，您只能將兩個緩存連結在一起。

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>可以將不同 Azure 訂用帳戶中的兩個快取加以連結嗎？

否，這兩個快取必須位於相同的 Azure 訂用帳戶。

### <a name="can-i-link-two-caches-with-different-sizes"></a>可以將兩個不同大小的快取加以連結嗎？

是，前提是次要連結快取超過主要連結快取。

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>是否可以在啟用群集時使用異地複製？

是，前提是這兩個快取具有相同的分區數目。

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>是否可以將異地複製與 VNET 中的緩存一起使用？

是的，VNET 中的緩存的異地複製受警告支援：

- 支援在相同 VNET 中多個快取之間的異地複寫。
- 還支援不同 VNET 中緩存之間的異地複製。
  - 如果 VNET 位於同一區域，則可以使用[VNET 對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)或[VPN 閘道 VNET 到 VNET 連接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V)來連接它們。
  - 如果 VNET 位於不同的區域，則不支援使用 VNET 對等互連進行異地複製，因為存在基本內部負載等化器的約束。 有關 VNET 對等約束的詳細資訊，請參閱[虛擬網路 - 對等互連 - 要求和約束](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints)。 建議的解決方案是使用 VPN 閘道 VNET 到 VNET 連接。

[使用此 Azure 範本](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)，可以快速將兩個異地複製緩存部署到與 VPN 閘道 VNET 到 VNET 連接連接的 VNET 中。

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>哪個複寫排程適用於 Redis 異地複寫？

複製是連續的和非同步，不會在特定計劃上發生。 對主項執行的所有寫入都會在輔助副本上即時和非同步複製。

### <a name="how-long-does-geo-replication-replication-take"></a>異地複寫需要花費多久的時間？

複製是增量的、非同步和連續的，所佔用的時間與跨區域的延遲沒有太大區別。 在某些情況下，可能需要輔助緩存對來自主資料庫的資料進行完全同步。 在這種情況下，複製時間取決於一些因素，例如：主緩存上的負載、可用的網路頻寬和區域間延遲。 我們發現，完整 53 GB 異地複製對的複製時間可能介於 5 到 10 分鐘之間。

### <a name="is-the-replication-recovery-point-guaranteed"></a>是否保證複寫復原點？

對於地理複製模式下的緩存，將禁用持久性。 如果異地複製對未連結（如客戶啟動的容錯移轉），輔助連結緩存會將其同步的資料保留到該時間點。 在這種情況下，不保證復原點。

要獲取復原點，請從任一緩存[匯出](cache-how-to-import-export-data.md#export)。 以後可以[導入](cache-how-to-import-export-data.md#import)到主連結緩存。

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>我可以使用 PowerShell 或 Azure CLI 來管理異地複製嗎？

可以，可以使用 Azure 門戶、PowerShell 或 Azure CLI 進行異地複製。 有關詳細資訊，請參閱[PowerShell 文檔](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache)或[Azure CLI 文檔](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest)。

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>跨 Azure 區域複寫我的資料需要多少費用？

使用異地複製時，來自主連結緩存的資料將複製到輔助連結緩存。 如果兩個連結的緩存位於同一區域，則資料傳輸不收取任何費用。 如果兩個連結的緩存位於不同的區域，則資料傳輸費用是跨任一區域移動的資料的網路出口成本。 有關詳細資訊，請參閱[頻寬定價詳細資訊](https://azure.microsoft.com/pricing/details/bandwidth/)。

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>當我嘗試刪除連結快取時，作業失敗的原因？

在刪除異地複製連結之前，無法在連結時刪除異地複製緩存及其資源組。 如果您嘗試將包含一個或兩個連結快取的資源群組刪除，就會將資源群組中的其他資源刪除，但資源群組會保留在 `deleting` 狀態，而資源群組中的任何連結快取會維持 `running` 狀態。 要完全刪除資源組及其內的連結緩存，請取消連結緩存，如[刪除異地複製連結](#remove-a-geo-replication-link)中所述。

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>要將次要連結快取用於哪個區域？

通常，建議將緩存與訪問它的應用程式存在於同一 Azure 區域中。 對於具有單獨主和回退區域的應用程式，建議主緩存和輔助緩存存在於這些相同的區域中。 如需配對區域的詳細資訊，請參閱[最佳做法 – Azure 配對的區域](../best-practices-availability-paired-regions.md)。

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>容錯移轉至次要連結快取如何運作？

異地複製緩存不支援跨 Azure 區域自動容錯移轉。 在災害復原方案中，客戶應在備份區域中協調地啟動整個應用程式堆疊。 讓單個應用程式元件自行決定何時切換到其備份可能會對性能產生負面影響。 Redis 的主要優點之一是它是一個非常低的延遲存儲。 如果客戶的主要應用程式位於與其緩存不同的區域中，則增加的往返時間將對性能產生顯著影響。 因此，我們避免由於暫時性可用性問題而自動故障。

要啟動客戶啟動的容錯移轉，首先取消連結緩存。 然後，更改 Redis 用戶端以使用（以前連結的）輔助緩存的連接終結點。 當兩個緩存取消連結時，輔助緩存將再次成為常規讀寫緩存，並直接接受來自 Redis 用戶端的請求。

## <a name="next-steps"></a>後續步驟

深入了解 [Azure Cache for Redis 進階層](cache-premium-tier-intro.md)。
