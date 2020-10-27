---
title: Azure Cache for Redis 管理常見問題
description: 瞭解可協助您管理 Azure Cache for Redis 常見問題的解答
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: 15c7ed4ca9d04e4bb314eea8b92bef749d2369b1
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537655"
---
# <a name="azure-cache-for-redis-management-faqs"></a>Azure Cache for Redis 管理常見問題
本文提供有關如何管理 Azure Cache for Redis 的常見問題解答。

## <a name="common-questions-and-answers"></a>常見問題與答案
本節涵蓋下列常見問題：

* [何時應該啟用非 TLS/SSL 連接埠來連線至 Redis？](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)
* [生產環境的最佳作法有哪些？](#what-are-some-production-best-practices)
* [使用常見 Redis 命令時的一些考量為何？](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [如何效能評定和測試我快取的效能？](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [執行緒集區成長的重要詳細資料](#important-details-about-threadpool-growth)
* [使用 StackExchange.Redis 時啟用伺服器 GC 在用戶端上取得更多輸送量](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [連線相關的效能考量](#performance-considerations-around-connections)

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>何時應該啟用非 TLS/SSL 連接埠來連線至 Redis？
Redis 伺服器並未原生支援 SSL，但 Azure Cache for Redis 則可支援。 如果您是連線至 Azure Cache for Redis，且您的用戶端支援 TLS (例如 StackExchange.Redis)，則應該使用 TLS。

>[!NOTE]
>預設會停用新「Azure Cache for Redis」執行個體的非 TLS 連接埠。 如果您的用戶端不支援 TLS，則必須依照[在 Azure Cache for Redis 中設定快取](cache-configure.md)一文中[存取連接埠](cache-configure.md#access-ports)一節的指示來啟用非 TLS 連接埠。
>
>

Redis 工具 (例如 `redis-cli`) 未使用 TLS 連接埠，但您可以遵循[宣佈 Redis 預覽版本的 ASP.NET 工作階段狀態供應器](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/)部落格文章中的指示，使用公用程式 (例如 `stunnel`) 將工具安全地連線至 TLS 連接埠。

如需下載 Redis 工具的指示，請參閱 [如何執行 Redis 命令？](cache-development-faq.md#how-can-i-run-redis-commands) 小節。

### <a name="what-are-some-production-best-practices"></a>生產環境的最佳作法有哪些？
* [StackExchange.Redis 最佳作法](#stackexchangeredis-best-practices)
* [組態和概念](#configuration-and-concepts)
* [效能測試](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>StackExchange.Redis 最佳作法
* 將 `AbortConnect` 設定為 false，然後讓 ConnectionMultiplexer 自動重新連線。 [參閱此處了解詳細資訊](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md)。
* 重複使用 ConnectionMultiplexer - 不要對每個要求建立一個新的。 建議使用[此處顯示](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)的 `Lazy<ConnectionMultiplexer>` 模式。
* Redis 在值越小時運作得最好，因此請考慮將較大的資料切分成多個金鑰。 在[此 Redis 討論](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)中，100kb 就算很大。 閱讀 [這篇文章](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) 以了解較大值所造成的範例問題。
* 設定您的 [執行緒集區設定](#important-details-about-threadpool-growth) 以避免逾時。
* 使用至少 5 秒的預設 connectTimeout。 此間隔可讓 StackExchange.Redis 在發生網路中斷的情況下，有足夠的時間重新建立連線。
* 請注意您執行不同作業的相關效能成本。 例如， `KEYS` 命令是一種 O(n) 作業，應該盡量避免。 [Redis.io 網站](https://redis.io/commands/) 有它支援的每項作業的時間複雜度詳細資訊。 按一下每個命令，可查看每項作業的複雜度。

#### <a name="configuration-and-concepts"></a>組態和概念
* 為生產環境系統使用標準或進階層。 基本層是一個單一節點的系統，沒有資料複寫也沒有 SLA。 此外，請至少使用 C1 快取。 C0 快取通常用於簡單的開發/測試案例。
* 請記住，Redis 是一種 **記憶體內部** 資料存放區。 閱讀 [這篇文章](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) ，您就能知道資料可能遺失的案例。
* 開發您的系統，讓系統可以處理因 [修補和容錯移轉](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md)所造成的連線短暫中斷。

#### <a name="performance-testing"></a>效能測試
* 先使用 `redis-benchmark.exe` ，在撰寫您自己的效能測試之前了解可能的輸送量。 因為 `redis-benchmark` 不支援 TLS，您必須在執行測試之前，[透過 Azure 入口網站啟用非 TLS 連接埠 ](cache-configure.md#access-ports)。 例如，參閱 [如何效能評定和測試我快取的效能？](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* 用來進行測試的用戶端 VM 應該與您的「Azure Redis 快取」執行個體位於相同的區域。
* 我們建議為您的用戶端使用 Dv2 VM 系列，因為此系列有更好的硬體，能提供最佳的結果。
* 請確定您選擇的用戶端 VM 與您進行測試的快取至少有一樣多的運算和頻寬能力。
* 如果您是在 Windows 上，請在用戶端電腦上啟用 VRSS。 [參閱此處了解詳細資訊](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11))。
* 進階層 Redis 執行個體會有比較好的網路延遲和輸送量，因為是在比較好的硬體 (CPU 和網路) 上執行。

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>使用常見 Redis 命令時的一些考量為何？

* 除非您充分了解需要花很長時間才能完成特定 Redis 命令的影響，否則應避免執行這些命令。 例如，請勿在生產環境中執行 [KEYS](https://redis.io/commands/keys) 命令。 視金鑰的數目而定，系統可能需要很長的時間才會傳回。 Redis 是單一執行緒伺服器，並且一次處理一個命令。 如果您在 KEYS 之後發出其他命令，則除非 Redis 處理 KEYS 命令，否則不會處理它們。 [Redis.io 網站](https://redis.io/commands/) 有它支援的每項作業的時間複雜度詳細資訊。 按一下每個命令，可查看每項作業的複雜度。
* 索引鍵大小 - 應該使用較小的索引鍵/值還是較大的索引鍵/值？ 需視案例而定。 如果您的案例需要較大的金鑰，您可以調整 ConnectionTimeout，然後重試值並調整重試邏輯。 從 Redis 伺服器的觀點，較小的值即表示有較佳的效能。
* 這些考量不表示您無法在 Redis 中儲存較大的值；您必須注意下列考量。 延遲會比較高。 如果您有一個較大的資料集和一個較小的值，則可以使用多個 ConnectionMultiplexer 執行個體，而每個執行個體都會設定一組不同的逾時和重試值 (如先前的 [StackExchange.Redis 設定選項的作用為何](cache-development-faq.md#what-do-the-stackexchangeredis-configuration-options-do) 小節所述)。

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>如何效能評定和測試我快取的效能？
* [啟用快取診斷](cache-how-to-monitor.md#enable-cache-diagnostics)，以[監視](cache-how-to-monitor.md)您快取的健全狀況。 您可以在 Azure 入口網站中檢視度量，也可以使用您選擇的工具 [下載並檢閱](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) 它們。
* 您可以使用 redis-benchmark.exe 對 Redis 伺服器進行負載測試。
* 確定負載測試用戶端與「Azure Redis 快取」位於相同的區域。
* 使用 redis-cli.exe，並使用 INFO 命令來監視快取。
* 如果您的負載造成記憶體分散嚴重，則應該擴大為較大的快取大小。
* 如需下載 Redis 工具的指示，請參閱 [如何執行 Redis 命令？](cache-development-faq.md#how-can-i-run-redis-commands) 小節。

以下命令提供使用 redis-benchmark.exe 的範例。 如需精確的結果，請從與快取位於相同區域的 VM 執行這些命令。

* 使用 1k 承載測試進行管線處理的 SET 要求

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* 使用 1k 承載測試進行管線處理的 GET 要求。

>[!NOTE]
> 請先執行上面所示的 SET 測試來填入快取
>

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

### <a name="important-details-about-threadpool-growth"></a>執行緒集區成長的重要詳細資料
CLR 執行緒集區有兩種類型的執行緒：「背景工作」和「I/O 完成連接埠」(IOCP) 執行緒。

* 背景工作執行緒是用於處理 `Task.Run(…)` 或 `ThreadPool.QueueUserWorkItem(…)` 方法之類的作業。 需要在背景執行緒上開啟工作時，CLR 中的各種元件也會使用這些執行緒。
* 發生非同步 IO (例如從網路讀取) 時，會使用 IOCP 執行緒。

執行緒集區可視需要提供新背景工作執行緒或 I/O 完成執行緒 (而不需要任何節流)，直到它到達每個類型執行緒的「最低」設定。 根據預設，執行緒的數目下限設為系統上的處理器數目。

一旦現有 (忙碌) 執行緒的數量達到執行緒集區的「最低」執行緒數目，執行緒集區會以每 500 毫秒將一個新執行緒插入執行緒的速率節流。 通常，如果您的系統需要 IOCP 執行緒的工作暴增，系統將可快速處理該工作。 不過，如果暴增的工作超過設定的「最低」設定，部份工作的處理會發生一些延遲，因為執行緒集區會等待一或兩個狀況發生。

* 現有的執行緒變得可用來處理工作。
* 有 500 毫秒沒有現有的執行緒變得可用，因此會建立一個新的執行緒。

基本上，這表示，當忙碌執行緒數目超過「最低」執行緒數量時，在應用程式處理網路流量之前，您可能要支付 500 毫秒延遲。 此外，務必注意，當現有的執行緒處於閒置的時間超過 15 秒 (根據我的記憶)，它將會被清除，而且這個循環的擴大和縮減可以重複。

如果我們查看來自 StackExchange.Redis (建置 1.0.450 或更新版本) 的範例錯誤訊息，您會看到它現在會列印執行緒集區統計資料 (請參閱以下的 IOCP 和背景工作詳細資料)。

```
System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
IOCP: (Busy=6,Free=994,Min=4,Max=1000),
WORKER: (Busy=3,Free=997,Min=4,Max=1000)
```

在上述範例中，您可以看到 IOCP 執行緒有六個忙碌執行緒，而系統設定成允許最低四個執行緒。 在此情況下，用戶端就可能會看到兩個 500 毫秒延遲，因為 6 > 4。

請注意，如果 IOCP 或背景工作執行緒的成長發生節流，StackExchange.Redis 可能達到逾時。

#### <a name="recommendation"></a>建議

經由這項資訊，我們強烈建議客戶將 IOCP 和背景工作執行緒的「最低」組態值設定成大於預設值的數值。 對於這個值應該為何，我們無法提供一體適用的指引，因為某個應用程式的適用值對另一個應用程式來說有可能太高或太低。 這項設定也會影響複雜應用程式其他部分的效能，因此每位客戶需要微調此設定來滿足其特定需求。 200 或 300 是好的起點，那麼請測試並視需要調整。

如何設定這項設定：

* 建議使用 `global.asax.cs` 中的 [ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) 方法，以程式設計方式變更這項設定。 例如：

    ```csharp
    private readonly int minThreads = 200;
    void Application_Start(object sender, EventArgs e)
    {
        // Code that runs on application startup
        AreaRegistration.RegisterAllAreas();
        RouteConfig.RegisterRoutes(RouteTable.Routes);
        BundleConfig.RegisterBundles(BundleTable.Bundles);
        ThreadPool.SetMinThreads(minThreads, minThreads);
    }
    ```

    > [!NOTE]
    > 此方法指定的值是全域設定，會影響整個 AppDomain。 舉例來說，如果您有一部 4 核心電腦，而且想要將「minWorkerThreads」 和「minIOThreads」設為執行階段期間每個 CPU 50 個，可以使用 **ThreadPool.SetMinThreads (200, 200)** 。

* 您也可以使用 `Machine.config` (通常位於 `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\`) 中 `<processModel>` 設定元素底下的 [*minIoThreads* 或 *minWorkerThreads* 組態設定](/previous-versions/dotnet/netframework-4.0/7w2sway1(v=vs.100))來指定最低執行緒設定。 **我們通常不建議以這種方式設定最低執行緒數，因為它是全系統的設定。**

  > [!NOTE]
  > 這個組態元素中指定的值是「每一核心」設定。 例如，如果您有 4 核心的電腦，並且想要在執行階段將 *minIOThreads* 設為 200，您會使用 `<processModel minIoThreads="50"/>`。
  >

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>使用 StackExchange.Redis 時啟用伺服器 GC 在用戶端上取得更多輸送量
啟用伺服器 GC 可以最佳化用戶端，並在使用 StackExchange.Redis 時提供較佳的效能和輸送量。 如需有關伺服器 GC，以及如何加以啟用的詳細資訊，請參閱下列文件：

* [啟用伺服器 GC](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Fundamentals of Garbage Collection (記憶體回收的基本概念)](/dotnet/standard/garbage-collection/fundamentals)
* [Garbage Collection and Performance (記憶體回收與效能)](/dotnet/standard/garbage-collection/performance)

### <a name="performance-considerations-around-connections"></a>連線相關的效能考量

每個定價層都有不同的用戶端連線、記憶體和頻寬的限制。 每個快取大小都可允許以某個數目為「上限」的連線數，而每個連到 Redis 的連線則都有相關的額外負荷。 因 TLS/SSL 加密而產生的 CPU 與記憶體使用量即是這類額外負荷的其中一例。 所指定快取大小的連線數上限是假設快取負載情況為輕度。 如果來自連線額外負荷的負載「加上」來自用戶端作業的負載超過系統的容量，則即使您尚未超出目前快取大小的連線限制，快取也會發生容量問題。

如需有關每個層級之不同連線限制的詳細資訊，請參閱 [Azure Redis 快取價格](https://azure.microsoft.com/pricing/details/cache/)。 如需有關連線及其他預設組態的詳細資訊，請參閱[預設 Redis 伺服器組態](cache-configure.md#default-redis-server-configuration)。

## <a name="next-steps"></a>下一步

瞭解其他 [Azure Cache for Redis 常見問題](cache-faq.md)。