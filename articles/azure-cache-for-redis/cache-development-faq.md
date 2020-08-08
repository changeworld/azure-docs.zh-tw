---
title: Azure Cache for Redis 開發常見問題
description: 瞭解可協助您開發 Azure Cache for Redis 的常見問題解答
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 1a0bcfadb79d6d2cb13c67b3ebadfcba97bc1fb9
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010276"
---
# <a name="azure-cache-for-redis-development-faqs"></a>Azure Cache for Redis 開發常見問題

本文提供有關如何針對 Azure Cache for Redis 進行開發之常見問題的解答。

## <a name="common-questions-and-answers"></a>常見問題和解答
本節涵蓋下列常見問題：

* [如何開始使用 Azure Redis 快取？](#how-can-i-get-started-with-azure-cache-for-redis)
* [StackExchange.Redis 設定選項的作用為何？](#what-do-the-stackexchangeredis-configuration-options-do)
* [我可以使用哪些 Azure Redis 快取用戶端？](#what-azure-cache-for-redis-clients-can-i-use)
* [Azure Redis 快取是否有本機模擬器？](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [如何執行 Redis 命令？](#how-can-i-run-redis-commands)
* [為什麼 Azure Cache for Redis 沒有 MSDN 類別庫參考？](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference)
* [是否可以使用 Azure Redis 快取作為 PHP 工作階段快取？](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [什麼是 Redis 資料庫？](#what-are-redis-databases)

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>如何開始使用 Azure Redis 快取？
有數種方式可讓您開始使用「Azure Redis 快取」。

* 您可以查看我們針對 [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)、[ASP.NET](cache-web-app-howto.md)、[Java](cache-java-get-started.md)、[Node.js](cache-nodejs-get-started.md) 和 [Python](cache-python-get-started.md) 提供的其中一套教學課程。
* 您可以觀看[如何使用 Microsoft Azure Redis 快取來建立高效能應用程式](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/) \(英文\)。
* 您可以取出符合您專案開發語言用戶端的用戶端文件，來查看如何使用 Redis。 有許多可與「Azure Redis 快取」搭配使用的 Redis 用戶端。 如需 Redis 用戶端清單，請參閱 [https://redis.io/clients](https://redis.io/clients)。

如果您還沒有 Azure 帳戶，您可以：

* [免費申請 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero)。 您將獲得能用來試用 Azure 付費服務的額度。 即使在額度用完後，您仍可保留帳戶並使用免費的 Azure 服務和功能。
* [啟用 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero)。 您的 MSDN 訂用帳戶每月會提供您額度，您可以用在 Azure 付費服務。

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>StackExchange.Redis 設定選項的作用為何？
StackExchange.Redis 有許多選項。 本節談論一些常見設定。 如需 StackExchange.Redis 選項的詳細資訊，請參閱 [StackExchange.Redis 設定](https://stackexchange.github.io/StackExchange.Redis/Configuration)。

| ConfigurationOptions | 描述 | 建議 |
| --- | --- | --- |
| AbortOnConnectFail |設定為 true 時，在網路失敗之後不會重新連線。 |設定為 false，並讓 StackExchange.Redis 自動重新連線。 |
| ConnectRetry |初始連線期間的重複連線嘗試次數。 |如需指引，請參閱下列附註。 |
| ConnectTimeout |連線作業的逾時 (毫秒)。 |如需指引，請參閱下列附註。 |

用戶端的預設值通常就已足夠。 您可以根據工作負載來微調選項。

* **重試**
  * 對於 ConnectRetry 和 ConnectTimeout，一般指引是快速檢錯，然後再試一次。 此指引是根據您的工作負載，以及用戶端發出 Redis 命令到接收回應所需的平均時間。
  * 讓 StackExchange.Redis 自動重新連線，而不檢查連線狀態，並自行重新連線。 **避免使用 ConnectionMultiplexer.IsConnected 屬性**。
  * 滾雪球 - 有時，您可能會遇到問題，但越是重試，問題卻如雪球般越滾越大，不可能解決。 如果出現滾雪球的情況，您應該考慮使用指數輪詢重試演算法 (如 Microsoft Patterns & Practices 群組所發佈的[重試一般指引](../best-practices-retry-general.md)所述)。
  
* **逾時值**
  * 請考慮您的工作負載，並據此設定值。 如果您要儲存大的值，請將逾時設定為較高的值。
  * 將 `AbortOnConnectFail` 設為 false，並讓 StackExchange.Redis 為您重新連線。
  * 使用應用程式的單一 ConnectionMultiplexer 執行個體。 您可以使用 LazyConnection 建立 Connection 屬性所傳回的單一執行個體 (如 [使用 ConnectionMultiplexer 類別連線至快取](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)所示)。
  * 將 `ConnectionMultiplexer.ClientName` 屬性設定為應用程式執行個體唯一名稱，以進行診斷。
  * 針對自訂工作負載，使用多個 `ConnectionMultiplexer` 執行個體。
      * 如果您的應用程式中有不同的負載，則可以遵循此模型。 例如：
      * 您可以有一個多工器來處理大型索引鍵。
      * 您可以有一個多工器來處理小型索引鍵。
      * 您可以設定連線逾時的不同值，以及每個所使用 ConnectionMultiplexer 的重試邏輯。
      * 在每個多工器上設定 `ClientName` 屬性，以協助診斷。
      * 此指引可以疏解每個 `ConnectionMultiplexer` 的延遲。

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>我可以使用哪些 Azure Redis 快取用戶端？
Redis 最大的好處是，有許多用戶端支援許多不同的開發語言。 如需最新的用戶端清單，請參閱 [Redis 用戶端](https://redis.io/clients)。 如需涵蓋數個不同語言和用戶端的教學課程，請參閱[如何使用 Azure Cache for Redis](cache-dotnet-how-to-use-azure-redis-cache.md) 以及目錄中的同層級文章。

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Azure Redis 快取是否有本機模擬器？
「Azure Redis 快取」沒有本機模擬器，但您可以從本機電腦的 [Redis 命令列工具](https://github.com/MSOpenTech/redis/releases/)執行 MSOpenTech 版本的 redis-server.exe，然後與其連線以取得類似本機快取模擬器的體驗，如以下範例所示：

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        // Connect to a locally running instance of Redis to simulate
        // a local cache emulator experience.
        return ConnectionMultiplexer.Connect("127.0.0.1:6379");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

如有需要，您也可以依選擇設定 [redis.conf](https://redis.io/topics/config) 檔案，以更符合線上「Azure Redis 快取」的[預設快取設定](cache-configure.md#default-redis-server-configuration)。

### <a name="how-can-i-run-redis-commands"></a>如何執行 Redis 命令？
您可以使用 [Redis 命令](https://redis.io/commands#)中列出的任何命令，但不包含 [Azure Redis 快取中不支援的 Redis 命令](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis)中列出的命令。 您有幾種方式可以執行 Redis 命令。

* 如果您有標準或進階快取，就可以使用 [Redis 主控台](cache-configure.md#redis-console)來執行 Redis 命令。 Redis 主控台提供安全的方式在 Azure 入口網站中執行 Redis 命令。
* 您也可以使用 Redis 命令列工具。 若要使用那些工具，請執行下列步驟：
* 下載 [Redis 命令列工具](https://github.com/MSOpenTech/redis/releases/)。
* 使用 `redis-cli.exe`連線至快取。 使用 -h 參數傳入快取端點，並使用 -a 傳入金鑰，如下列範例所示：
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Redis 命令列工具未使用 TLS 連接埠，但您可以遵循[如何使用 Redis 命令列工具搭配 Azure Cache for Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool)文章中的指示，使用公用程式 (例如 `stunnel`) 將工具安全地連線至 TLS 連接埠。
>
>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference"></a>為什麼 Azure Cache for Redis 沒有 MSDN 類別庫參考？
Microsoft Azure Cache for Redis 是以熱門的開放原始碼記憶體內部資料存放區 Redis 為基礎。 它可由各種不同的 [Redis 用戶端](https://redis.io/clients) 來存取，並適用許多程式設計語言。 每個用戶端都有自己的 API，可使用 [Redis 命令](https://redis.io/commands)對「Azure Redis 快取」執行個體發出呼叫。

因為每個用戶端都不同，所以 MSDN 上沒有一個集中式類別參考，每個用戶端都會維護其專屬的參考文件。 除了參考文件之外，還有數個教學課程，示範如何使用不同的語言和快取用戶端來開始使用「Azure Redis 快取」。 若要存取這些教學課程，請參閱[如何使用 Azure Cache for Redis](cache-dotnet-how-to-use-azure-redis-cache.md) 以及目錄中的同層級文章。

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>是否可以使用 Azure Redis 快取作為 PHP 工作階段快取？
是，若要使用「Azure Redis 快取」作為 PHP 工作階段快取，請在 `session.save_path` 中指定「Azure Redis 快取」執行個體的連接字串。

> [!IMPORTANT]
> 使用「Azure Redis 快取」作為 PHP 工作階段快取時，您必須將用來連線到快取的安全性金鑰進行 URL 編碼，如以下範例所示：
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> 如果金鑰未進行 URL 編碼，您可能會收到類似此訊息的例外狀況︰`Failed to parse session.save_path`
>

如需有關搭配 PhpRedis 用戶端使用「Azure Redis 快取」作為 PHP 工作階段快取的詳細資訊，請參閱 [PHP 工作階段處理常式](https://github.com/phpredis/phpredis#php-session-handler) \(英文\)。

### <a name="what-are-redis-databases"></a>什麼是 Redis 資料庫？

Redis 資料庫就是相同 Redis 執行個體內的資料邏輯分隔。 所有資料庫之間會共用快取記憶體，給定資料庫的實際記憶體耗用量取決於該資料庫中儲存的索引鍵/值。 例如，假設 C6 快取有 53 GB 的記憶體，而 P5 有 120 GB。 您可以選擇將 53 GB / 120 GB 全部放入一個資料庫，或分割給多個資料庫。 

> [!NOTE]
> 使用已啟用叢集功能的「進階 Azure Redis 快取」時，只有資料庫 0 可供使用。 這項限制是固有的 Redis 限制，並非特別針對「Azure Redis 快取」。 如需詳細資訊，請參閱[我需要對我的用戶端應用程式進行任何變更才能使用叢集嗎？](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)。
> 
> 

## <a name="next-steps"></a>後續步驟

深入瞭解其他[Azure Cache For Redis 常見問題](cache-faq.md)。
