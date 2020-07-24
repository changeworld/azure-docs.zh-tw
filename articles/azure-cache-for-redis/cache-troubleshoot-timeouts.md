---
title: 針對 Azure Cache for Redis 逾時進行疑難排解
description: 瞭解如何解決 Azure Cache for Redis 的常見超時問題，例如 Redis 伺服器修補和 Stackexchange.redis。 Redis 超時例外狀況。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 3d9360a4b5c5f0ef080b3de2a9d425bcdf2b2e70
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081894"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>針對 Azure Cache for Redis 逾時進行疑難排解

本節討論連接到 Azure Cache for Redis 時所發生的疑難排解超時問題。

- [Redis 伺服器修補](#redis-server-patching)
- [StackExchange.Redis 逾時例外狀況](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> 本指南中的數個疑難排解步驟包含如何執行 Redis 命令和監視各種效能度量的指示。 如需詳細資訊和指示，請參閱 [其他資訊](#additional-information) 一節中的文章。
>

## <a name="redis-server-patching"></a>Redis 伺服器修補

Azure Cache for Redis 會定期更新其伺服器軟體，做為它所提供的受控服務功能的一部分。 此[修補](cache-failover.md)活動主要會在幕後進行。 在容錯移轉期間，當 Redis 伺服器節點正在進行修補時，連接到這些節點的 Redis 用戶端可能會在這些節點之間切換連接時遇到暫時性的超時。 請參閱[容錯移轉如何影響我的用戶端應用程式](cache-failover.md#how-does-a-failover-affect-my-client-application)，以取得您的應用程式上可能有哪些副作用修補的詳細資訊，以及如何改善其修補事件的處理方式。

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis 逾時例外狀況

Stackexchange.redis： Redis 會使用名 `synctimeout` 為的設定來進行同步作業，預設值為5000毫秒。 如果同步呼叫未在這段時間內完成，則 Stackexchange.redis Redis 用戶端會擲回類似下列範例的逾時錯誤：

```output
    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)
```

此錯誤訊息包含的度量可協助指出問題的原因和可能的解決方案。 下表包含有關錯誤訊息度量的詳細資料。

| 錯誤訊息度量 | 詳細資料 |
| --- | --- |
| inst |在最後一個時間配量︰已發出 0 個命令 |
| mgr |通訊端管理員正在執行 `socket.select` ，這表示它會要求 OS 指出有一些專案要執行的通訊端。 讀取器不會主動從網路讀取，因為它不認為有任何動作可以執行 |
| queue |總共有 73 個進行中的作業 |
| qu |其中6個進行中的作業是在未傳送的佇列中，尚未寫入到輸出網路 |
| qs |67進行中的作業已傳送到伺服器，但尚未提供回應。 回應可能是 `Not yet sent by the server` 或 `sent by the server but not yet processed by the client.` |
| qc |有0個進行中的作業已看到 [回復]，但尚未標示為 [完成]，因為他們正在等候完成迴圈 |
| wr |有作用中的寫入器（表示不會忽略6個未傳送的要求） bytes/activewriters |
| in |沒有作用中的讀取器，在 NIC 位元組/activereader 上可供讀取的位元組為零 |

您可以使用下列步驟來調查可能的根本原因。

1. 最佳做法是，在使用 Stackexchange.redis. Redis 用戶端時，請確定您使用的是下列模式來進行連接。

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    如需詳細資訊，請參閱 [使用 StackExchange.Redis 來連線到快取](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)。

1. 請確定您的伺服器和用戶端應用程式位於相同的 Azure 區域中。 例如，當您的快取位於美國東部，但用戶端位於美國西部，而要求未在間隔內完成， `synctimeout` 或者您從本機開發電腦進行偵錯工具時，可能會收到超時。 

    強烈建議您讓快取和用戶端位在相同的 Azure 區域中。 如果您有包含跨區域呼叫的案例，您應該 `synctimeout` `synctimeout` 在連接字串中包含屬性，將間隔設定為高於預設 5000-ms 間隔的值。 下列範例顯示 Azure Cache for Redis 提供的 Stackexchange.redis 的連接字串程式碼片段（ `synctimeout` 2000 毫秒）。

    ```output
    synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
    ```

1. 請確定您使用最新版的 [StackExchange.Redis NuGet 封裝](https://www.nuget.org/packages/StackExchange.Redis/)。 程式碼中的錯誤會經常修正，以便更能應付逾時問題，因此請務必要擁有最新版本。
1. 如果您的要求是由伺服器或用戶端上的頻寬限制所系結，則需要較長的時間才能完成，而且可能會造成超時。 若要查看您的超時是否因為伺服器上的網路頻寬，請參閱[伺服器端頻寬限制](cache-troubleshoot-server.md#server-side-bandwidth-limitation)。 若要查看您的超時是否是因為用戶端網路頻寬所導致，請參閱[用戶端頻寬限制](cache-troubleshoot-client.md#client-side-bandwidth-limitation)。
1. 您是否在伺服器或用戶端上受到 CPU 限制？

   - 檢查您的用戶端上是否有受 CPU 的限制。 高 CPU 可能會導致要求不會在間隔內進行處理 `synctimeout` ，並導致要求超時。移至較大的用戶端大小或散佈負載有助於控制此問題。
   - 藉由監視 CPU 快取[效能](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)計量，檢查您是否要在伺服器上取得受 cpu 限制。 當 Redis 與 CPU 系結時，傳入的要求可能會導致這些要求超時。若要解決此狀況，您可以將負載分散到高階快取中的多個分區，或升級為較大的大小或定價層。 如需詳細資訊，請參閱[伺服器端頻寬限制](cache-troubleshoot-server.md#server-side-bandwidth-limitation)。
1. 伺服器上是否有命令需要很長的處理時間？ 長時間執行且在 redis 伺服器上需要很長時間來處理的命令可能會造成超時。 如需長時間執行命令的詳細資訊，請參閱[長時間執行的命令](cache-troubleshoot-server.md#long-running-commands)。 您可以使用 Redis-cli 用戶端或[Redis 主控台](cache-configure.md#redis-console)來連線到您的 Azure Cache for Redis 實例。 然後，執行[SLOWLOG](https://redis.io/commands/slowlog)命令，以查看是否有要求速度低於預期。 Redis 伺服器和 StackExchange.Redis 最適合許多小型要求，而非少數幾個大型要求。 將資料分割成較小的區塊可以改善這些問題。

    如需使用 redis-cli 和 stunnel 連接到快取的 TLS/SSL 端點的相關資訊，請參閱[宣佈 Redis 預覽版本的 ASP.NET 會話狀態提供者](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/)的 blog 文章。
1. 高 Redis 伺服器負載可能會導致逾時。 您可以藉由監視 `Redis Server Load` [快取效能度量](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)來監視伺服器負載。 伺服器負載為 100 (最大值) 表示 Redis 伺服器正忙碌處理要求，並沒有閒置的時間。 若要確認特定要求是否會佔用所有伺服器功能，請執行 SlowLog 命令，如上一段所述。 如需詳細資訊，請參閱「高 CPU 使用率/伺服器負載」。
1. 用戶端上是否有其他任何事件可能導致網路問題？ 常見事件包括：相應增加或相應減少用戶端實例數目、部署新版本的用戶端，或啟用自動調整。 在我們的測試中，我們發現自動調整或相應增加/相應減少可能會導致輸出網路連線中斷幾秒鐘。 StackExchange.Redis 程式碼對於這類事件具有復原能力，因此將會重新連線。 重新連線時，佇列中的任何要求都可能會超時。
1. 是否有大型的要求會在快取的幾個小型要求上發生？ `qs`錯誤訊息中的參數會告訴您有多少要求已從用戶端傳送到伺服器，但尚未處理回應。 這個值會不斷成長，因為 StackExchange.Redis 使用單一 TCP 連線，而且一次只能讀取一個回應。 即使第一個作業超時，它也不會阻止更多資料從伺服器傳送或傳出。 其他要求將會遭到封鎖，直到大型要求完成，而且可能會導致超時為止。 有一個解決方案是確保快取足以容納工作負載，並將較大的值分割成較小的區塊，以將逾時的機會降到最低。 另一個可能的解決方案是在用戶端中使用 `ConnectionMultiplexer` 物件集區，並在傳送新要求時選擇最少負載的 `ConnectionMultiplexer`。 在多個連線物件之間載入，應該會防止單一超時時間造成其他要求也會超時。
1. 如果您使用的是 `RedisSessionStateProvider` ，請確定您已正確設定重試超時。 `retryTimeoutInMilliseconds` 應高於 `operationTimeoutInMilliseconds`，否則不會發生任何重試。 在下列範例中， `retryTimeoutInMilliseconds` 已設定為 3000。 如需詳細資訊，請參閱[適用於 Azure Redis 快取的 ASP.NET 工作階段狀態提供者](cache-aspnet-session-state-provider.md)和[如何使用工作階段狀態提供者和輸出快取提供者的設定參數](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration) \(英文\)。

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. 透過[監視](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` 和 `Used Memory`，檢查「Azure Redis 快取」伺服器上的記憶體使用量。 如果已備有收回原則，Redis 就會在 `Used_Memory` 達到快取大小時開始收回金鑰。 理想情況下，`Used Memory RSS` 應該只稍微高於 `Used memory`。 較大的差異表示記憶體分散（內部或外部）。 當 `Used Memory RSS` 小於 `Used Memory` 時，則表示作業系統已交換部分快取記憶體。 如果發生此交換情況，您應該就會遇到顯著的延遲。 因為 Redis 無法控制其配置對應到記憶體分頁的方式，所以高 `Used Memory RSS` 通常是記憶體使用量突然增加的結果。 當 Redis 伺服器釋放記憶體時，配置器會取得記憶體，但不一定會將記憶體提供給系統。 `Used Memory` 值和作業系統報告的記憶體耗用量可能會有差異。 記憶體可能已由 Redis 使用和釋放，但不會傳回給系統。 若要協助降低記憶體問題，您可以執行下列步驟：

   - 將快取升級為較大的大小，讓您不會在系統上執行記憶體限制。
   - 設定金鑰的到期時間，以便主動收回較舊的值。
   - 監視 `used_memory_rss` 快取計量。 當此值接近其快取的大小時，您可能會開始看到效能問題。 如果您使用 premium 快取，或升級至較大的快取大小，請將資料分散到多個分區。

   如需詳細資訊，請參閱[Redis 伺服器上的記憶體壓力](cache-troubleshoot-server.md#memory-pressure-on-redis-server)。

## <a name="additional-information"></a>其他資訊

- [針對 Azure Cache for Redis 用戶端問題進行疑難排解](cache-troubleshoot-client.md)
- [針對 Azure Cache for Redis 伺服器端問題進行疑難排解](cache-troubleshoot-server.md)
- [如何效能評定和測試我快取的效能？](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [如何監視 Azure Redis 快取](cache-how-to-monitor.md)
