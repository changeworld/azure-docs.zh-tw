---
title: 針對 Azure Cache for Redis 逾時進行疑難排解
description: 瞭解如何使用 Redis 的 Azure 緩存解決常見的超時問題,例如 Redis 伺服器修補和堆疊 Exchange.Redis 超時異常。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 4301a55e3f5ea5b445ef1540ee59d1b5c28ca0ed
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010812"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>針對 Azure Cache for Redis 逾時進行疑難排解

本節討論在連接到 Redis 的 Azure 緩存時發生的故障排除超時問題。

- [雷瑞斯伺服器修補](#redis-server-patching)
- [StackExchange.Redis 逾時例外狀況](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> 本指南中的數個疑難排解步驟包含如何執行 Redis 命令和監視各種效能度量的指示。 如需詳細資訊和指示，請參閱 [其他資訊](#additional-information) 一節中的文章。
>

## <a name="redis-server-patching"></a>雷瑞斯伺服器修補

Redis 的 Azure 緩存定期更新其伺服器軟體,作為其提供的託管服務功能的一部分。 此[修補](cache-failover.md)活動主要發生在場景後面。 在修補 Redis 伺服器節點的故障轉移期間,連接到這些節點的 Redis 用戶端可能會遇到臨時超時,因為在這些節點之間交換連接。 有關[故障轉移如何影響我的用戶端應用程式](cache-failover.md#how-does-a-failover-affect-my-client-application)的詳細資訊,請參閱故障轉移如何查看修補可能對您的應用程式產生的副作用以及如何改進其對修補事件的處理。

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis 逾時例外狀況

StackExchange.Redis 使用為同步`synctimeout`操作 命名的配置設置,預設值為1000毫秒。 如果同步調用此時未完成,則 StackExchange.Redis 用戶端將引發類似於以下範例的超時錯誤:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

此錯誤訊息包含的度量可協助指出問題的原因和可能的解決方案。 下表包含有關錯誤訊息度量的詳細資料。

| 錯誤訊息度量 | 詳細資料 |
| --- | --- |
| inst |在最後一個時間配量︰已發出 0 個命令 |
| mgr |套接字管理器正在`socket.select`操作 ,這意味著它要求操作系統指示有一些要做操作的套接字。 讀者沒有主動從網路閱讀,因為它認為沒有任何事情可做 |
| queue |總共有 73 個進行中的作業 |
| qu |6 個正在進行的操作位於未送出的佇列中,尚未寫入出站網路 |
| qs |67 個正在進行的操作已發送到伺服器,但回應尚未可用。 回應可能是 `Not yet sent by the server` 或 `sent by the server but not yet processed by the client.` |
| qc |正在進行的操作的 0 已看到回覆,但尚未標記為已完成,因為它們正在等待完成迴圈 |
| wr |有一個活動編寫器 (意味著 6 個未送出的請求不會被忽略)位元組/活動寫入器 |
| in |沒有作用中的讀取器，在 NIC 位元組/activereader 上可供讀取的位元組為零 |

您可以使用以下步驟調查可能的根本原因。

1. 最佳做法是,在使用 StackExchange.Redis 用戶端時,請確保使用以下模式進行連接。

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

1. 確保伺服器和用戶端應用程式位於 Azure 中的同一區域中。 例如,當快取位於美國東部,但客戶端位於美國西部且請求未`synctimeout`在間隔內完成時,您可能會收到超時,或者當您從本地開發電腦進行調試時,您可能會獲得超時。 

    強烈建議您讓快取和用戶端位在相同的 Azure 區域中。 如果您有包含跨區域呼叫的案例，您應該將 `synctimeout` 間隔設定為大於預設值 1000 毫秒間隔的值，方法是在連接字串中加入 `synctimeout` 屬性。 下面的範例顯示了由 Azure 緩存為 Redis 提供的 StackExchange.Redis 的`synctimeout`連接字串片段, 該片段為 2000 毫秒。

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. 請確定您使用最新版的 [StackExchange.Redis NuGet 封裝](https://www.nuget.org/packages/StackExchange.Redis/)。 程式碼中的錯誤會經常修正，以便更能應付逾時問題，因此請務必要擁有最新版本。
1. 如果請求受伺服器或用戶端上的頻寬限制的約束,則完成請求需要更長的時間,並可能導致超時。 要檢視逾時是否由於伺服器上的網路頻寬,請參閱[伺服器端頻寬限制](cache-troubleshoot-server.md#server-side-bandwidth-limitation)。 要檢視逾時是否由於用戶端網路頻寬,請參閱[客戶端頻寬限制](cache-troubleshoot-client.md#client-side-bandwidth-limitation)。
1. 您是否在伺服器或用戶端上受到 CPU 限制？

   - 檢查您是否在用戶端上受到 CPU 的約束。 高 CPU`synctimeout`可能導致在 間隔內無法處理請求,並導致請求超時。移動到較大的用戶端大小或分發負載有助於控制此問題。
   - 通過監視 CPU[快取性能指標](cache-how-to-monitor.md#available-metrics-and-reporting-intervals),檢查您是否在伺服器上獲取了 CPU 綁定。 Redis 受 CPU 綁定時來自請求可能會導致這些請求超時。要解決此問題,可以在高級緩存中的多個分片中分配負載,或升級到更大的大小或定價層。 有關詳細資訊,請參閱[伺服器端頻寬限制](cache-troubleshoot-server.md#server-side-bandwidth-limitation)。
1. 伺服器上是否有命令需要很長的處理時間？ 長時間運行的命令在 Redis 伺服器上處理可能會導致超時。 關於長時間執行的指令的詳細資訊,請參閱[長時間執行的指令](cache-troubleshoot-server.md#long-running-commands)。 您可以使用 Redis-cli 用戶端或[Redis 控制台](cache-configure.md#redis-console)連接到 Redis 實例的 Azure 緩存。 然後,運行[SLOWLOG](https://redis.io/commands/slowlog)命令以查看是否有請求比預期慢。 Redis 伺服器和 StackExchange.Redis 最適合許多小型要求，而非少數幾個大型要求。 將資料分割成較小的區塊可以改善這些問題。

    有關使用 Redis-cli 和 stunnel 連接到快取的 TLS/SSL 終結點的資訊,請參閱[宣佈 ASP.NET 會作業狀態供應商以進行 Redis 預覽版本](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)) 的部落格文章。
1. 高 Redis 伺服器負載可能會導致逾時。 您可以藉由監視 `Redis Server Load` [快取效能度量](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)來監視伺服器負載。 伺服器負載為 100 (最大值) 表示 Redis 伺服器正忙碌處理要求，並沒有閒置的時間。 若要確認特定要求是否會佔用所有伺服器功能，請執行 SlowLog 命令，如上一段所述。 如需詳細資訊，請參閱「高 CPU 使用率/伺服器負載」。
1. 用戶端上是否有其他任何事件可能導致網路問題？ 常見事件包括:向上或向下擴展用戶端實例的數量、部署新版本的用戶端或啟用自動縮放。 在我們的測試中,我們發現自動縮放或向上/向下擴展可能會導致出站網路連接丟失幾秒鐘。 StackExchange.Redis 程式碼對於這類事件具有復原能力，因此將會重新連線。 重新連接時,隊列中的任何請求都可以超時。
1. 在多個對緩存的少量請求發出超時之前,是否有大量請求? 錯誤訊息中的`qs`參數告訴您有多少請求從用戶端發送到伺服器,但尚未處理回應。 這個值會不斷成長，因為 StackExchange.Redis 使用單一 TCP 連線，而且一次只能讀取一個回應。 即使第一個操作超時,也不會阻止向伺服器發送或從伺服器發送更多數據。 其他請求將被阻止,直到大型請求完成,並可能導致超時。 有一個解決方案是確保快取足以容納工作負載，並將較大的值分割成較小的區塊，以將逾時的機會降到最低。 另一個可能的解決方案是在用戶端中使用 `ConnectionMultiplexer` 物件集區，並在傳送新要求時選擇最少負載的 `ConnectionMultiplexer`。 跨多個連接物件載入應防止單個超時導致其他請求也超時。
1. 如果使用`RedisSessionStateProvider`, 請確保已正確設定重試超時。 `retryTimeoutInMilliseconds` 應高於 `operationTimeoutInMilliseconds`，否則不會發生任何重試。 在下列範例中， `retryTimeoutInMilliseconds` 已設定為 3000。 如需詳細資訊，請參閱[適用於 Azure Redis 快取的 ASP.NET 工作階段狀態提供者](cache-aspnet-session-state-provider.md)和[如何使用工作階段狀態提供者和輸出快取提供者的設定參數](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration) \(英文\)。

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

1. 透過[監視](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` 和 `Used Memory`，檢查「Azure Redis 快取」伺服器上的記憶體使用量。 如果已備有收回原則，Redis 就會在 `Used_Memory` 達到快取大小時開始收回金鑰。 理想情況下，`Used Memory RSS` 應該只稍微高於 `Used memory`。 很大的差異意味著記憶體碎片(內部或外部)。 當 `Used Memory RSS` 小於 `Used Memory` 時，則表示作業系統已交換部分快取記憶體。 如果發生此交換情況，您應該就會遇到顯著的延遲。 由於 Redis 無法控制其分配如何映射到記憶體頁,因此高通常`Used Memory RSS`是 記憶體使用量高峰的結果。 當 Redis 伺服器釋放記憶體時,分配器會佔用記憶體,但可能會或可能不會將記憶體重新將記憶體送回系統。 `Used Memory` 值和作業系統報告的記憶體耗用量可能會有差異。 記憶體可能已被Redis使用和釋放,但沒有返回給系統。 為了幫助緩解記憶體問題,您可以執行以下步驟:

   - 將快取升級到較大大小,以便不會針對系統上的記憶體限制運行。
   - 設定金鑰的到期時間，以便主動收回較舊的值。
   - 監視 `used_memory_rss` 快取計量。 當此值接近其緩存的大小時,您可能會開始看到性能問題。 如果使用高級緩存,則將數據分佈在多個分片上,或者升級到更大的緩存大小。

   有關詳細資訊,請參閱[Redis 伺服器上的記憶體壓力](cache-troubleshoot-server.md#memory-pressure-on-redis-server)。

## <a name="additional-information"></a>其他資訊

- [針對 Azure Cache for Redis 用戶端問題進行疑難排解](cache-troubleshoot-client.md)
- [針對 Azure Cache for Redis 伺服器端問題進行疑難排解](cache-troubleshoot-server.md)
- [如何效能評定和測試我快取的效能？](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [如何監視 Azure Redis 快取](cache-how-to-monitor.md)
