---
title: 設定 Redis 叢集-Premium Azure Cache for Redis
description: 了解如何建立和管理「進階」層「Azure Redis 快取」執行個體的 Redis 叢集功能
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 06/13/2018
ms.openlocfilehash: d37aa275a07586738bf7416cee6611bdc8284df3
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88004774"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>如何設定進階 Azure Redis 快取的 Redis 叢集功能
Azure Cache for Redis 有不同的快取供應項目，可讓您彈性選擇快取大小和功能，包括叢集功能、持續性及虛擬網路支援等「進階」層功能。 本文說明如何在進階「Azure Redis 快取」執行個體中設定叢集功能。

## <a name="what-is-redis-cluster"></a>Redis 叢集是什麼？
「Azure Redis 快取」提供的 Redis 叢集與 [Redis 中所實作](https://redis.io/topics/cluster-tutorial)的相同。 使用 Redis 叢集有下列優點： 

* 能夠自動分割您在多個節點之間的資料集。 
* 當節點的子集發生故障或無法與叢集的其餘部分通訊時，可以繼續作業。 
* 更多輸送量：當您增加分區數目時，輸送量會呈線性增加。 
* 更多記憶體大小：當您增加分區數目時，會呈線性增加。  

叢集化不會增加叢集快取的可用連線數目。 如需高階快取的大小、輸送量和頻寬的詳細資訊，請參閱[選擇正確的層級](cache-overview.md#choosing-the-right-tier)

在 Azure 中，會以主要/複本模型形式提供 Redis 叢集，其中每個分區都有一個具複寫功能的主要/複本組，而複寫會由「Azure Redis 快取」服務管理。 

## <a name="clustering"></a>叢集
啟用叢集功能時，是在快取建立期間於 [新的 Azure Redis 快取]**** 刀鋒視窗上啟用。 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

叢集是在 [ **Redis 叢集** ] 刀鋒視窗中所設定。

![叢集][redis-cache-clustering]

叢集中最多可包含 10 個分區。 按一下 [啟用]****，針對 [分區計數]****，滑動滑桿或鍵入一個 1 到 10 之間的數字，然後按一下 [確定]****。

每個分區都是一個由 Azure 管理的主要/複本快取組，快取總大小的計算方式，是將分區數目乘以在定價層中選取的快取大小。 

![叢集][redis-cache-clustering-selected]

建立快取後，您可以連線並使用它，就像非叢集化快取一樣，而且 Redis 會在整個快取分區散發資料。 如果[已啟用](cache-how-to-monitor.md#enable-cache-diagnostics)診斷，則會針對每個分區個別擷取計量，並可在 [Azure Redis 快取] 刀鋒視窗中[檢視](cache-how-to-monitor.md)計量。 

> [!NOTE]
> 
> 在設定叢集時，用戶端應用程式中的必要設定有些微差異。 如需詳細資訊，請參閱 [我需要對用戶端應用程式進行任何變更才能使用叢集嗎？](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

如需搭配 StackExchange.Redis 用戶端使用叢集的範例程式碼，請參閱 [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) 範例的 [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) 部分。

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>在執行中的進階快取上變更叢集大小
若要在已啟用叢集的執行中 premium 快取上變更叢集大小，請按一下 [**資源] 功能表**中的 [叢集**大小**]。

![Redis 叢集大小][redis-cache-redis-cluster-size]

若要變更叢集大小，請使用滑桿，或在 [分區計數]**** 文字方塊中輸入 1 到 10 之間的數字，然後按一下 [確定]**** 加以儲存。

增加叢集大小會增加最大輸送量和快取大小。 增加叢集大小不會增加用戶端可用的最大連線數目。

> [!NOTE]
> 調整叢集大小會執行 [MIGRATE](https://redis.io/commands/migrate) 命令，這個命令會耗用大量資源，因此為了將影響降到最低，請考慮在非尖峰時段執行此作業。 在移轉程序期間，您會看到伺服器負載出現峰值。 調整叢集大小是需要長時間執行的程序，且所需時間決定於索引鍵數目，以及與那些索引鍵相關聯之值的大小。
> 
> 

## <a name="clustering-faq"></a>叢集常見問題集
下列清單包含「Azure Redis 快取」叢集功能相關常見問題的解答。

* [我需要對我的用戶端應用程式進行任何變更才能使用叢集嗎？](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [如何在叢集中散發索引鍵？](#how-are-keys-distributed-in-a-cluster)
* [我可以建立的最大快取大小為何？](#what-is-the-largest-cache-size-i-can-create)
* [所有 Redis 用戶端都支援叢集嗎？ ](#do-all-redis-clients-support-clustering)
* [啟用叢集後，要如何連接到我的快取？](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [我可以直接連接到我的快取的個別分區嗎？](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [我可以為先前建立的快取設定叢集嗎？](#can-i-configure-clustering-for-a-previously-created-cache)
* [我可以設定基本或標準快取的叢集嗎？](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [我可以將叢集使用於 Redis ASP.NET 工作階段狀態和輸出快取提供者嗎？](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [當我使用 StackExchange.Redis 和叢集時收到 MOVE 例外狀況，該怎麼辦？](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>我需要對我的用戶端應用程式進行任何變更才能使用叢集嗎？
* 啟用叢集時，只可以使用資料庫 0。 如果用戶端應用程式使用多個資料庫，並嘗試讀取或寫入至 0 以外的資料庫，就會擲回下列例外狀況。 `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  如需詳細資訊，請參閱 [Redis 叢集規格 - 實作的子集](https://redis.io/topics/cluster-spec#implemented-subset)。
* 如果您使用 [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/)，則必須使用 1.0.481 或更新版本。 您可以使用與連接未啟用叢集的快取時所用的相同 [端點、連接埠和金鑰](cache-configure.md#properties) 來連接快取。 唯一的差別在於必須在資料庫 0 上完成所有的讀取和寫入。
  
  其他用戶端可能有不同的需求。 請參閱 [所有 Redis 用戶端都支援叢集嗎？](#do-all-redis-clients-support-clustering)
* 如果您的應用程式使用分成單一命令的多個索引鍵作業，則所有索引鍵都必須位於相同的分區。 若要將索引鍵置於相同的分區，請參閱[如何在叢集中散發索引鍵？](#how-are-keys-distributed-in-a-cluster)
* 如果您使用 Redis ASP.NET 工作階段狀態提供者，則必須使用 2.0.1 或更高版本。 請參閱 [我可以將叢集使用於 Redis ASP.NET 工作階段狀態和輸出快取提供者嗎？](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>如何在叢集中散發索引鍵？
依據 Redis [金鑰散發模型](https://redis.io/topics/cluster-spec#keys-distribution-model) 文件︰金鑰空間會分割成 16384 個位置。 每個索引鍵都會雜湊並指派給上述的其中一個位置，而這些位置散發於叢集的各個節點。 您可以設定哪個部分的索引鍵會雜湊，以確保多個索引鍵位於使用主題標籤的相同分區中。

* 具有主題標籤的金鑰 - 如果金鑰的任何部分被括在 `{` 和 `}` 中，則只有該部分的金鑰會為了判斷金鑰的主題標籤位置而進行雜湊。 例如，下列 3 個金鑰會位於相同的分區︰`{key}1`、`{key}2` 和 `{key}3`，因為只會雜湊名稱的 `key` 部分。 如需索引鍵主題標籤規格的完整清單，請參閱[索引鍵主題標籤](https://redis.io/topics/cluster-spec#keys-hash-tags)。
* 沒有主題標籤的索引鍵 - 整個索引鍵名稱都用於主題標籤。 這會導致以統計方式平均散發於快取的各個分區。

如需最佳的效能和輸送量，我們建議平均散發索引鍵。 如果您使用具有主題標籤的索引鍵，則應用程式必須負責確保平均散發索引鍵。

如需詳細資訊，請參閱[金鑰散發模型](https://redis.io/topics/cluster-spec#keys-distribution-model)、[Redis 叢集資料分區化](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding)和[金鑰主題標籤](https://redis.io/topics/cluster-spec#keys-hash-tags)。

如需搭配 StackExchange.Redis 用戶端使用叢集，並尋找相同分區中之金鑰的範例程式碼，請參閱 [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) 範例的 [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) 部分。

### <a name="what-is-the-largest-cache-size-i-can-create"></a>我可以建立的最大快取大小為何？
最大的 premium 快取大小為 120 GB。 您最多可以建立10個分區，讓您的大小上限為 1.2 TB GB。 如果您需要較大的大小，可以 [要求更多](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase)。 如需詳細資訊，請參閱 [Azure Redis 快取價格](https://azure.microsoft.com/pricing/details/cache/)。

### <a name="do-all-redis-clients-support-clustering"></a>所有 Redis 用戶端都支援叢集嗎？ 
並非所有用戶端都支援 Redis 叢集！ 請查看您所使用之程式庫的檔，以確認您使用的是支援叢集的程式庫和版本。 Stackexchange.redis. Redis 是一個程式庫，在其較新版本中支援叢集。 如需其他用戶端的詳細資訊，請參閱 [Redis 叢集教學課程](https://redis.io/topics/cluster-tutorial)的 [試用叢集](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster)一節。 

Redis 叢集通訊協定需要每個用戶端直接以群集模式連接到每個分區，同時定義新的錯誤回應，例如 ' 已移動 ' na ' CROSSSLOTS '。 嘗試使用不支援叢集模式快取叢集的用戶端，可能會導致許多移動的重新導向[例外](https://redis.io/topics/cluster-spec#moved-redirection)狀況，或只是中斷您的應用程式（如果您要進行跨位置的多金鑰要求）。

> [!NOTE]
> 如果您使用 StackExchange.Redis 做為您的用戶端，請確定您使用的是最新版的 [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) (1.0.481) 或更新版本，叢集才能正常運作。 如果您有任何關於 Move 例外狀況的問題，請參閱 [Move 例外狀況](#move-exceptions) ，以取得詳細資訊。
>

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>啟用叢集後，要如何連接到我的快取？
您可以使用與連接未啟用叢集的快取時所用的相同 [端點](cache-configure.md#properties)、[連接埠](cache-configure.md#properties)和[金鑰](cache-configure.md#access-keys)來連接快取。 Redis 會管理後端上的叢集，因此您不需從用戶端進行管理。

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>我可以直接連接到我的快取的個別分區嗎？
叢集通訊協定需要用戶端進行正確的分區連線。 因此，用戶端應該正確地為您執行此操作。 如前所述，每個分區都包含一個主要/複本快取組，統稱為快取執行個體。 您可以使用 GitHub 中 Redis 存放庫[不穩定](https://redis.io/download)分支內的 redis-cli 公用程式，連線到這些快取執行個體。 使用 `-c` 參數啟用這個版本時，會實作基本支援。 如需詳細資訊， [Playing with the cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster)請參閱 [https://redis.io](https://redis.io) [Redis cluster 教學](https://redis.io/topics/cluster-tutorial)課程中的使用叢集播放。

若是非 TLS，請使用下列命令。

```bash
Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
...
Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)
```

針對 TLS，請將取代 `1300N` 為 `1500N` 。

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>我可以為先前建立的快取設定叢集嗎？
是。 如果不是，請先確定您的快取為 premium。 接下來，您應該能夠看到叢集設定選項，包括啟用叢集的選項。 您可以在建立快取之後，或在您第一次啟用叢集之後，變更叢集大小。

   >[!IMPORTANT]
   >您無法復原啟用群集。 和已啟用叢集的快取，而且只有一個分區的行為*不同*于*不*含叢集的相同大小快取。

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>我可以設定基本或標準快取的叢集嗎？
叢集僅適用於進階快取。

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>我可以將叢集使用於 Redis ASP.NET 工作階段狀態和輸出快取提供者嗎？
* **Redis 輸出快取提供者** - 不需要變更。
* **Redis 工作階段狀態供應器** - 若要使用叢集，您必須使用 [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 或更高版本，否則會擲回例外狀況。 這是一種重大變更;如需詳細資訊，請參閱[v 2.0.0 重大變更詳細資料](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details)。

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>當我使用 StackExchange.Redis 和叢集時收到 MOVE 例外狀況，該怎麼辦？
如果您正在使用 StackExchange.Redis，並且在使用叢集時收到 `MOVE` 例外狀況，請確定您使用的是 [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) 或更新版本。 如需設定 .NET 應用程式以使用 StackExchange.Redis 的指示，請參閱 [設定快取用戶端](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)。

## <a name="next-steps"></a>後續步驟
深入瞭解 Azure Cache for Redis 功能。

* [Azure Cache for Redis Premium 服務層級](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
