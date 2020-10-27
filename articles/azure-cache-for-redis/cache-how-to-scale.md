---
title: 如何調整 Azure Redis 快取的規模
description: 瞭解如何使用 Azure 入口網站和工具（例如 Azure PowerShell 和 Azure CLI）來調整您的 Azure Cache for Redis 實例。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 04/11/2017
ms.openlocfilehash: 332233873bfbcb2ae77f5a70b4aaa5a6102cecec
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537842"
---
# <a name="how-to-scale-azure-cache-for-redis"></a>如何調整 Azure Redis 快取的規模
「Azure Redis 快取」具有各種不同的快取供應項目，可讓您彈性選擇快取大小和功能。 建立快取之後，如果您應用程式的需求改變，您可以調整快取的大小和定價層。 本文說明如何使用 Azure 入口網站和 Azure PowerShell 與 Azure CLI 之類的工具來調整快取。

## <a name="when-to-scale"></a>調整時機
您可以使用「Azure Redis 快取」的[監視](cache-how-to-monitor.md)功能來監視快取的健康情況和效能，並協助判斷調整快取規模的時機。 

您可以監視下列度量，以協助判斷是否需要調整。

* Redis 伺服器負載
* 記憶體使用量
* 網路頻寬
* CPU 使用量

如果您判斷快取不再符合您應用程式的需求，則可以調整為適合您應用程式的較大或較小快取定價層。 如需判斷要使用哪一個快取定價層的詳細資訊，請參閱 [選擇正確的層級](cache-overview.md#choosing-the-right-tier)。

## <a name="scale-a-cache"></a>調整快取
若要調整快取，在 [Azure 入口網站](https://portal.azure.com)中 [瀏覽至快取](cache-configure.md#configure-azure-cache-for-redis-settings)，然後按一下 **[資源]** 功能表中的 **[調整]** 。

![調整](./media/cache-how-to-scale/redis-cache-scale-menu.png)

從 [選取定價層]  刀鋒視窗中選取想要的定價層，然後按一下 [選取]  。

![定價層][redis-cache-pricing-tier-blade]


您可以調整具有下列限制的不同定價層：

* 您無法從較高的定價層調整至較低的定價層。
  * 您無法從 **進階** 快取向下調整至 **標準** 或 **基本** 快取。
  * 您無法從 **標準** 快取向下調整到 **基本** 快取。
* 您可以從 **基本** 快取調整到 **標準** 快取，但您無法同時變更大小。 如果您需要不同的大小，您可以進行後續調整作業，調整到您需要的大小。
* 您無法直接從 **基本** 快取調整至 **進階** 快取。 首先，在單一調整作業中從 **基本** 調整至 **標準** ，然後在後續的調整作業中從 **標準** 調整至 **進階** 。
* 您無法從較大的大小向下調整至 **C0 (250 MB)** 的大小。
 
當快取正在調整成新的定價層時，[Azure Redis 快取]  刀鋒視窗中會顯示[正在調整規模]  狀態。

![調整大小][redis-cache-scaling]

調整完成時，狀態會從 [正在調整]  變更為 [執行中]  。

## <a name="how-to-automate-a-scaling-operation"></a>如何自動化調整作業
除了在 Azure 入口網站中調整快取執行個體之外，您還可以使用 PowerShell Cmdlet、Azure CLI 以及使用 Microsoft Azure 管理庫 (MAML) 進行調整。 

* [使用 PowerShell 進行調整](#scale-using-powershell)
* [使用 Azure CLI 進行調整](#scale-using-azure-cli)
* [使用 MAML 進行調整](#scale-using-maml)

### <a name="scale-using-powershell"></a>使用 PowerShell 進行調整

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Set-AzRedisCache](/powershell/module/az.rediscache/set-azrediscache) `Size` 修改、或屬性時，您可以使用 >set-azrediscache 指令程式，透過 PowerShell 調整您的 Azure Cache for Redis 實例 `Sku` `ShardCount` 。 下列範例示範如何將名為 `myCache` 的快取調整為 2.5 GB 快取。 

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

如需使用 PowerShell 進行調整的詳細資訊，請參閱 [使用 powershell 調整 Azure Cache for Redis](cache-how-to-manage-redis-cache-powershell.md#scale)。

### <a name="scale-using-azure-cli"></a>使用 Azure CLI 進行調整
若要使用 Azure CLI 來調整「Azure Redis 快取」執行個體的規模，請呼叫 `azure rediscache set` 命令，並視所需的調整作業而定，傳入所需的設定變更，包括新的大小、SKU 或叢集大小。

如需有關如何使用 Azure CLI 來調整規模的詳細資訊，請參閱 [變更現有 Azure Redis 快取的設定](cache-manage-cli.md#scale)。

### <a name="scale-using-maml"></a>使用 MAML 進行調整
若要使用 [Microsoft Azure 管理庫 (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/) 來調整您的「Azure Redis 快取」執行個體規模，請呼叫 `IRedisOperations.CreateOrUpdate` 方法並傳入 `RedisProperties.SKU.Capacity` 的新大小。

```csharp
    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }
```

如需詳細資訊，請參閱[使用 MAML 來管理 Azure Redis 快取](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) \(英文\) 範例。

## <a name="scaling-faq"></a>調整常見問題集
下列清單包含「Azure Redis 快取」規模調整相關常見問題的解答。

* [可以向上調整為 Premium 快取，或在其中調整、向下調整嗎？](#can-i-scale-to-from-or-within-a-premium-cache)
* [調整之後，是否必須變更我的快取名稱或存取金鑰？](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [調整運作方式如何？](#how-does-scaling-work)
* [我是否會在調整期間遺失快取中的資料？](#will-i-lose-data-from-my-cache-during-scaling)
* [我的自訂資料庫設定在調整期間會受到影響嗎？](#is-my-custom-databases-setting-affected-during-scaling)
* [是否可以在調整期間使用我的快取？](#will-my-cache-be-available-during-scaling)
* 設定異地複寫後，為什麼我不能調整快取大小或變更叢集中的分區？
* [不支援的作業](#operations-that-are-not-supported)
* [調整需要多長的時間？](#how-long-does-scaling-take)
* [如何分辨調整何時完成？](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>可以向上調整為 Premium 快取，或在其中調整、向下調整嗎？
* 您無法從 **進階** 快取向下調整至 **基本** 或 **標準** 定價層。
* 您可以將一個 **進階** 快取定價層調整為另一個定價層。
* 您無法直接從 **基本** 快取調整至 **進階** 快取。 首先，在單一調整作業中從 **基本** 調整至 **標準** ，然後在後續的調整作業中從 **標準** 調整至 **進階** 。
* 如果在建立 **進階** 快取時已啟用叢集，您可以 [變更叢集大小](cache-how-to-premium-clustering.md#cluster-size)。 如果在建立快取時未啟用叢集，之後依然可以設定叢集。
  
  如需詳細資訊，請參閱 [如何設定進階 Azure Redis 快取的叢集功能](cache-how-to-premium-clustering.md)。

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>調整之後，是否必須變更我的快取名稱或存取金鑰？
否，在調整作業期間，您的快取名稱和金鑰不會變更。

### <a name="how-does-scaling-work"></a>調整運作方式如何？
* **基本** 快取在調整為不同的大小時會關閉，並會使用新的大小佈建新的快取。 在此期間，快取無法使用，而且快取中的所有資料都會遺失。
* **基本** 快取在調整為 **標準** 快取時，會佈建複本快取，且會從主要快取將資料複製到複本快取。 調整程序期間仍可使用快取。
* 當 **標準** 快取調整為 **不同的大小** 或高階快取時，其中一個複本會關閉並重新布建至新的大小和傳送的資料，然後另一個複本會在重新布建之前執行容錯移轉，類似于在其中一個快取節點失敗時所發生的處理常式。

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>我是否會在調整期間遺失快取中的資料？
* **基本** 快取調整為新的大小時，會遺失所有資料，且無法在調整作業期間使用快取。
* **基本** 快取調整為 **標準** 快取時，通常會保留快取中的資料。
* 當 **標準** 快取調整為較大的大小或層，或者 **進階** 快取調整為較大的大小時，通常會保留所有資料。 當 **標準** 或 **進階** 快取向下調整為較小大小時，根據調整時快取中與新大小相關的資料量，資料可能會遺失。 如果縮小時遺失資料，則會使用 [allkeys-lru](https://redis.io/topics/lru-cache) 收回原則來收回金鑰。 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>我的自訂資料庫設定在調整期間會受到影響嗎？
如果您在快取建立期間為 `databases` 設定設定了自訂值，請記住某些定價層具有不同的[資料庫限制](cache-configure.md#databases)。 以下是在此案例中進行調整時的一些考量：

* 調整為較現有階層具有較低 `databases` 限制的定價層時：
  * 如果您使用預設數字 `databases`，即所有定價層為 16，則不會遺失資料。
  * 如果您要使用的自訂數字 `databases`，落在您要調整之階層限制範圍內，則會保留此 `databases` 設定，並且不會遺失資料。
  * 如果您要使用的自訂數字 `databases`，超出新階層的限制，則會將 `databases` 設定降低至新階層的限制，而且在已移除的資料庫中的所有資料都會遺失。
* 調整為具有與現有階層相同或更高 `databases` 限制的定價層時，會保留您的 `databases` 設定，而且不會遺失資料。

標準和進階快取有 99.9% SLA 的可用性時，則資料遺失沒有 SLA。

### <a name="will-my-cache-be-available-during-scaling"></a>是否可以在調整期間使用我的快取？
* **標準** 與 **進階** 快取在調整作業期間會保持可用。 不過，調整標準和進階快取時可能會發生連線中斷 (從基本調整至標準快取時也一樣)。 這些連線中斷應該很短暫，而且 Redis 用戶端應可立即重新建立其連線。
* **基本** 快取在將作業調整至不同大小的期間，會處於離線狀態。 從 **基本** 調整至 **標準** 時，基本快取仍可使用，但可能會遇到短暫的連線中斷。 若發生連線中斷，Redis 用戶端應可立即重新建立其連線。


### <a name="scaling-limitations-with-geo-replication"></a>異地複寫的調整限制

一旦將異地複寫連結新增至兩個快取之間，您就無法再起始調整作業或變更叢集內的分區數目。 您必須將快取取消連結之後，才能發出這些命令。 如需詳細資訊，請參閱 [設定異地複寫](cache-how-to-geo-replication.md)。


### <a name="operations-that-are-not-supported"></a>不支援的作業
* 您無法從較高的定價層調整至較低的定價層。
  * 您無法從 **進階** 快取向下調整至 **標準** 或 **基本** 快取。
  * 您無法從 **標準** 快取向下調整到 **基本** 快取。
* 您可以從 **基本** 快取調整到 **標準** 快取，但您無法同時變更大小。 如果您需要不同的大小，您可以進行後續調整作業，調整到您需要的大小。
* 您無法直接從 **基本** 快取調整至 **進階** 快取。 首先，在單一調整作業中從 **基本** 調整至 **標準** ，然後在後續作業中從 **標準** 調整至 **進階** 。
* 您無法從較大的大小向下調整至 **C0 (250 MB)** 的大小。

如果調整作業失敗，服務會嘗試還原作業，而且快取會還原成原始大小。


### <a name="how-long-does-scaling-take"></a>調整需要多長的時間？
調整時間取決於快取中的資料量，較大量的資料需要較長的時間才能完成。 調整大約需要20分鐘。 針對叢集快取，每個分區的調整花費大約20分鐘。

### <a name="how-can-i-tell-when-scaling-is-complete"></a>如何分辨調整何時完成？
在 Azure 入口網站中，您可以看到調整作業進行中。 調整完成時，快取的狀態會變更為 [執行中]  。

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png