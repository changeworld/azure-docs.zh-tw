---
title: 針對 Azure Cache for Redis 用戶端問題進行疑難排解
description: 瞭解如何解決 Azure Cache for Redis 的常見用戶端問題，例如 Redis 用戶端記憶體壓力、流量高載、高 CPU、有限頻寬、大型要求或大型回應大小。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: 9317999f8862cd9930870fecaf5be44d291c07a9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85829664"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>針對 Azure Cache for Redis 用戶端問題進行疑難排解

本節討論因應用程式使用的 Redis 用戶端上的條件而發生的疑難排解問題。

- [Redis 用戶端上的記憶體壓力](#memory-pressure-on-redis-client)
- [流量高載](#traffic-burst)
- [用戶端 CPU 使用量很高](#high-client-cpu-usage)
- [用戶端頻寬限制](#client-side-bandwidth-limitation)
- [大型要求或回應大小](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Redis 用戶端上的記憶體壓力

用戶端電腦上的記憶體壓力會導致各種效能問題，而可能會延遲來自快取的回應處理。 當記憶體壓力達到時，系統可能會將資料分頁到磁片。 此「分頁錯誤」 __ 會使系統明顯變慢。

若要偵測用戶端上的記憶體壓力：

- 監視電腦上的記憶體使用量，以確保它不會超過可用的記憶體。
- 監視用戶端的 `Page Faults/Sec` 效能計數器。 在正常作業期間，大部分的系統都會發生分頁錯誤。 與要求超時對應的分頁錯誤尖峰可能表示記憶體不足的壓力。

用戶端的高記憶體壓力可以透過數種方式來減輕：

- 深入瞭解您的記憶體使用量模式，以減少用戶端上的記憶體耗用量。
- 將您的用戶端 VM 升級到具有更多記憶體的較大大小。

## <a name="traffic-burst"></a>流量高載

流量暴增加上不當的 `ThreadPool` 設定，可能會導致延遲處理 Redis 伺服器已傳送但用戶端上尚未使用的資料。

`ThreadPool`使用[範例 `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs)監視統計資料隨著時間變更的方式。 您可以使用 `TimeoutException` 來自 stackexchange.redis 的 Redis 訊息，如下所示進一步調查：

```output
    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)
```

在上述的例外狀況中，有幾個有趣的問題：

- 請注意，在 `IOCP` 區段和 `WORKER` 區段中，`Busy` 值大於 `Min` 值。 這種差異表示您的 `ThreadPool` 設定需要調整。
- 您也可以查看 `in: 64221`。 此值表示在用戶端的核心通訊端層收到64211個位元組，但應用程式尚未讀取。 這種差異通常表示您的應用程式（例如，Stackexchange.redis. Redis）在伺服器將資料傳送給您時，不會快速地從網路讀取資料。

您可以設定[您的 `ThreadPool` 設定](cache-faq.md#important-details-about-threadpool-growth)，以確定您的執行緒集區會在高載案例下快速地相應增加。

## <a name="high-client-cpu-usage"></a>用戶端 CPU 使用量很高

[高用戶端 CPU 使用量] 表示系統無法跟上要求的工作。 雖然快取會快速傳送回應，但用戶端可能無法及時處理回應。

使用 Azure 入口網站中可用的計量，或透過電腦上的效能計數器，監視用戶端全系統的 CPU 使用量。 請小心不要監視*處理*程式 CPU，因為單一進程可能會有較低的 cpu 使用量，但全系統的 cpu 可能很高。 請監看與逾時對應的 CPU 使用量暴增。 高 CPU 可能也會導致 `in: XXX` `TimeoutException` 錯誤訊息中的高值，如[流量](#traffic-burst)激增一節中所述。

> [!NOTE]
> StackExchange.Redis 1.1.603 和更新版本在 `TimeoutException` 錯誤訊息中包含 `local-cpu` 度量。 請確定您使用最新版的 [StackExchange.Redis NuGet 封裝](https://www.nuget.org/packages/StackExchange.Redis/)。 程式碼中的錯誤會經常修正，以便更能應付逾時問題，因此請務必要擁有最新版本。
>

若要降低用戶端的高 CPU 使用量：

- 調查導致 CPU 尖峰的原因。
- 將您的用戶端升級至具有更多 CPU 容量的較大 VM 大小。

## <a name="client-side-bandwidth-limitation"></a>用戶端頻寬限制

用戶端機器可用的網路頻寬多寡可能會有限制 (視其架構而定)。 如果用戶端因為多載網路容量而超過可用的頻寬，則資料在用戶端上的處理速度不會和伺服器傳送時一樣快。 這種情況可能會導致逾時。

使用[範例 `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)，監視頻寬使用量如何隨著時間變更。 此程式碼可能無法在具有限制權限的某些環境 (例如 Azure 網站) 中順利執行。

若要減輕問題，請減少網路頻寬耗用量，或將用戶端 VM 大小增加到具有更多網路容量的帳戶。

## <a name="large-request-or-response-size"></a>大型要求或回應大小

大型的要求/回應可能會導致逾時。 例如，假設您在用戶端上設定的超時值為1秒。 應用程式同時 (使用相同的實體網路連線) 要求兩個金鑰 (例如 'A' 和 'B')。 大部分的用戶端都支援「管線」要求，其中兩個要求 ' A ' 和 ' B ' 會在另一個之後傳送，而不會等待回應。 伺服器會以相同順序傳回回應。 如果回應 ' A ' 很大，它可能會花費大部分的時間來處理之後的要求。

在下列範例中，要求 ' A ' 和 ' B ' 會快速傳送到伺服器。 伺服器開始快速傳送回應 ' A ' 和 ' B '。 由於資料傳輸時間的原因，回應 ' B ' 必須等到回應 ' A ' 超時，即使伺服器快速回應也一樣。

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

此要求/回應並不容易測量。 您可以檢測您的用戶端程式代碼，以追蹤大型的要求和回應。

大型回應大小的解決方式不同，但包含：

1. 將您的應用程式優化為大量小數值，而不是幾個大數值。
    - 比較好的解決方案是將資料分割成相關的較小值。
    - 請參閱文章[redis 的理想值大小範圍為何？100 KB 是否過大？](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)如需為何建議使用較小值的詳細資訊。
1. 增加 VM 的大小以取得更高的頻寬功能
    - 用戶端或伺服器 VM 上的頻寬越多，可能會降低較大回應的資料傳輸時間。
    - 比較兩部電腦上目前的網路使用量與目前 VM 大小的限制。 只有伺服器或用戶端上的更多頻寬可能不夠。
1. 增加您的應用程式使用的連線物件數目。
    - 使用迴圈配置資源方法，在不同的連線物件上提出要求。

## <a name="additional-information"></a>其他資訊

- [針對 Azure Cache for Redis 伺服器端問題進行疑難排解](cache-troubleshoot-server.md)
- [如何效能評定和測試我快取的效能？](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
