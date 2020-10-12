---
title: 針對 Azure Cache for Redis 用戶端問題進行疑難排解
description: 瞭解如何解決 Azure Cache for Redis 的常見用戶端問題，例如 Redis 用戶端記憶體壓力、流量高載、高 CPU、受限頻寬、大型要求或大型回應大小。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: 122c96c95aea794fbba9cab8a9a5b867f9f34b48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008962"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>針對 Azure Cache for Redis 用戶端問題進行疑難排解

本節討論針對您的應用程式所使用的 Redis 用戶端條件所發生的問題進行疑難排解。

- [Redis 用戶端上的記憶體壓力](#memory-pressure-on-redis-client)
- [流量高載](#traffic-burst)
- [用戶端 CPU 使用量很高](#high-client-cpu-usage)
- [用戶端頻寬限制](#client-side-bandwidth-limitation)
- [大型要求或回應大小](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Redis 用戶端上的記憶體壓力

用戶端電腦上的記憶體壓力會導致各種效能問題，這些問題可能會延遲處理來自快取的回應。 當記憶體壓力達到時，系統可能會將資料分頁至磁片。 此「分頁錯誤」 __ 會使系統明顯變慢。

偵測用戶端上的記憶體壓力：

- 監視電腦上的記憶體使用量，以確保它不會超過可用的記憶體。
- 監視用戶端的 `Page Faults/Sec` 效能計數器。 在正常操作期間，大部分的系統都有一些分頁錯誤。 與要求逾時對應的分頁錯誤尖峰，可能表示記憶體壓力。

用戶端上的高記憶體壓力可減輕數種方式：

- 深入瞭解記憶體使用量模式，以降低用戶端的記憶體耗用量。
- 將您的用戶端 VM 升級為具有更多記憶體的較大大小。

## <a name="traffic-burst"></a>流量高載

流量暴增加上不當的 `ThreadPool` 設定，可能會導致延遲處理 Redis 伺服器已傳送但用戶端上尚未使用的資料。

`ThreadPool`使用[範例 `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs)監視統計資料隨著時間變更的方式。 您可以使用  `TimeoutException` 來自 >stackexchange.redis. Redis 的訊息，如下所示進一步調查：

```output
    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)
```

在上述例外狀況中，有幾個有趣的問題：

- 請注意，在 `IOCP` 區段和 `WORKER` 區段中，`Busy` 值大於 `Min` 值。 這種差異表示您的 `ThreadPool` 設定需要調整。
- 您也可以查看 `in: 64221`。 這個值表示在用戶端的核心通訊端層收到64211個位元組，但應用程式尚未讀取。 這項差異通常表示您的應用程式 (例如，>stackexchange.redis. Redis) 不會像伺服器將資料傳送給您一樣快速地從網路讀取資料。

您可以設定 [您的 `ThreadPool` 設定](cache-management-faq.md#important-details-about-threadpool-growth) ，以確定您的執行緒集區會在高載的情況下快速擴大。

## <a name="high-client-cpu-usage"></a>用戶端 CPU 使用量很高

高用戶端 CPU 使用量指出系統無法跟上要求執行的工作。 即使快取快速傳送回應，用戶端還是可能無法及時處理回應。

使用 Azure 入口網站中的可用計量或透過機器上的效能計數器，監視用戶端全系統的 CPU 使用量。 請注意不要監視 *進程* cpu，因為單一進程的 cpu 使用率可能很低，但全系統 cpu 可能很高。 請監看與逾時對應的 CPU 使用量暴增。 高 CPU 也可能會 `in: XXX` 在錯誤訊息中造成較高 `TimeoutException` 的值，如 [流量](#traffic-burst) 高載一節中所述。

> [!NOTE]
> StackExchange.Redis 1.1.603 和更新版本在 `TimeoutException` 錯誤訊息中包含 `local-cpu` 度量。 請確定您使用最新版的 [StackExchange.Redis NuGet 封裝](https://www.nuget.org/packages/StackExchange.Redis/)。 程式碼中的錯誤會經常修正，以便更能應付逾時問題，因此請務必要擁有最新版本。
>

若要降低用戶端的高 CPU 使用率：

- 調查造成 CPU 尖峰的原因。
- 將您的用戶端升級至具有更多 CPU 容量的較大 VM 大小。

## <a name="client-side-bandwidth-limitation"></a>用戶端頻寬限制

用戶端機器可用的網路頻寬多寡可能會有限制 (視其架構而定)。 如果用戶端超過可用的頻寬（藉由多載網路容量），則資料在用戶端上的處理速度不會和伺服器傳送資料的速度一樣快。 這種情況可能會導致逾時。

使用[範例 `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)監視頻寬使用量如何隨時間變化。 此程式碼可能無法在具有限制權限的某些環境 (例如 Azure 網站) 中順利執行。

若要減輕問題，請減少網路頻寬耗用量，或將用戶端 VM 大小增加到一個具有更多網路容量的大小。

## <a name="large-request-or-response-size"></a>大型要求或回應大小

大型的要求/回應可能會導致逾時。 例如，假設您在用戶端上設定的超時值為1秒。 應用程式同時 (使用相同的實體網路連線) 要求兩個金鑰 (例如 'A' 和 'B')。 大部分的用戶端都支援「管線」要求，其中的要求 ' A ' 和 ' B ' 會在另一個之後傳送，而不會等待其回應。 伺服器會以相同順序傳回回應。 如果回應 ' A ' 很大，它可能會佔用大部分的等候時間，以供稍後的要求使用。

在下列範例中，系統會將要求 ' A ' 和 ' B ' 快速傳送至伺服器。 伺服器會快速開始傳送回應 ' A ' 和 ' B '。 由於資料傳輸時間的原因，回應 ' B ' 必須等待回應 ' A ' 的時間，即使伺服器快速回應也是一樣。

```console
|-------- 1 Second Timeout (A)----------|
|-Request A-|
     |-------- 1 Second Timeout (B) ----------|
     |-Request B-|
            |- Read Response A --------|
                                       |- Read Response B-| (**TIMEOUT**)
```

此要求/回應並不容易測量。 您可以檢測用戶端程式代碼，以追蹤大型要求和回應。

大型回應大小的解決方式不同，但包含：

1. 將您的應用程式優化，以取得大量較小的值，而不是數個大型值。
    - 比較好的解決方案是將資料分割成相關的較小值。
    - 請參閱 post [什麼是理想的 redis 值大小範圍？100 KB 是否太大？](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) 以取得建議較小值之原因的詳細資訊。
1. 增加 VM 的大小以取得更高的頻寬功能
    - 用戶端或伺服器 VM 上的頻寬愈多，可能會減少較大回應的資料傳輸時間。
    - 比較兩部電腦上目前的網路使用量與目前 VM 大小的限制。 只有伺服器或用戶端上的頻寬可能不足。
1. 增加您的應用程式所使用的連線物件數目。
    - 使用迴圈配置資源方法，對不同的連線物件提出要求。

## <a name="additional-information"></a>其他資訊

- [針對 Azure Cache for Redis 伺服器端問題進行疑難排解](cache-troubleshoot-server.md)
- [如何效能評定和測試我快取的效能？](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
