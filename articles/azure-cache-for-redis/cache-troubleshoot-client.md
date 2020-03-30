---
title: 針對 Azure Cache for Redis 用戶端問題進行疑難排解
description: 瞭解如何使用 Redis 的 Azure 緩存解決常見的用戶端問題，例如 Redis 用戶端記憶體壓力、流量突發、CPU 高、頻寬受限、請求大或回應大小大。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: ace953fcb278604cb64eef463753f0f2622d3d24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277943"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>針對 Azure Cache for Redis 用戶端問題進行疑難排解

本節討論由於應用程式使用的 Redis 用戶端上的情況而出現的疑難排解問題。

- [Redis 用戶端上的記憶體壓力](#memory-pressure-on-redis-client)
- [交通突發](#traffic-burst)
- [用戶端 CPU 使用量很高](#high-client-cpu-usage)
- [用戶端頻寬限制](#client-side-bandwidth-limitation)
- [大請求或回應大小](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Redis 用戶端上的記憶體壓力

用戶端電腦上的記憶體壓力會導致各種性能問題，從而延遲來自緩存的回應的處理。 當記憶體壓力來襲時，系統可能會將資料頁到磁片。 此「分頁錯誤」 __ 會使系統明顯變慢。

要檢測用戶端上的記憶體壓力，：

- 監視電腦上的記憶體使用方式，以確保它不會超過可用記憶體。
- 監視用戶端`Page Faults/Sec`的效能計數器。 在正常操作期間，大多數系統都有一些頁面故障。 與請求超時對應的分頁錯誤中的尖峰可以指示記憶體壓力。

用戶端上的高記憶體壓力可以通過以下幾種方式緩解：

- 深入瞭解記憶體使用模式，以減少用戶端上的記憶體消耗。
- 將用戶端 VM 升級到具有更多記憶體的較大大小。

## <a name="traffic-burst"></a>交通突發

流量暴增加上不當的 `ThreadPool` 設定，可能會導致延遲處理 Redis 伺服器已傳送但用戶端上尚未使用的資料。

使用[示例`ThreadPoolLogger`](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs)監視`ThreadPool`統計資訊隨時間的變化。 您可以使用`TimeoutException`來自 StackExchange.Redis 的消息來進一步調查：

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

在前面的例外中，有幾個問題很有趣：

- 請注意，在 `IOCP` 區段和 `WORKER` 區段中，`Busy` 值大於 `Min` 值。 這種差異意味著您的`ThreadPool`設置需要調整。
- 您也可以查看 `in: 64221`。 此值表示已在用戶端的內核通訊端層接收了 64，211 個位元組，但應用程式尚未讀取。 這種差異通常意味著您的應用程式（例如，StackExchange.Redis）不像伺服器向您發送資料那樣快速地從網路讀取資料。

您可以[配置`ThreadPool`"設置"，](cache-faq.md#important-details-about-threadpool-growth)以確保執行緒池在突發情況下快速擴展。

## <a name="high-client-cpu-usage"></a>用戶端 CPU 使用量很高

高用戶端 CPU 使用率表示系統無法跟上要求它完成的工作。 即使緩存快速發送回應，用戶端可能無法及時處理回應。

使用 Azure 門戶中提供的指標或通過電腦上的效能計數器監視用戶端的系統範圍 CPU 使用方式。 請注意，不要監視*進程*CPU，因為單個進程的 CPU 使用率較低，但系統範圍的 CPU 可能很高。 請監看與逾時對應的 CPU 使用量暴增。 高 CPU 還可能導致`in: XXX`錯誤訊息中的`TimeoutException`高值，如[流量突發](#traffic-burst)部分所述。

> [!NOTE]
> StackExchange.Redis 1.1.603 和更新版本在 `TimeoutException` 錯誤訊息中包含 `local-cpu` 度量。 請確定您使用最新版的 [StackExchange.Redis NuGet 封裝](https://www.nuget.org/packages/StackExchange.Redis/)。 程式碼中的錯誤會經常修正，以便更能應付逾時問題，因此請務必要擁有最新版本。
>

要降低用戶端的高 CPU 使用率：

- 調查導致 CPU 峰值的原因。
- 將用戶端升級到具有更多 CPU 容量的較大 VM 大小。

## <a name="client-side-bandwidth-limitation"></a>用戶端頻寬限制

用戶端機器可用的網路頻寬多寡可能會有限制 (視其架構而定)。 如果用戶端通過重載網路容量超過可用頻寬，則用戶端上的資料處理速度不會像伺服器發送資料那樣快。 這種情況可能會導致逾時。

使用[示例`BandwidthLogger`](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)監視頻寬使用方式隨時間的變化。 此程式碼可能無法在具有限制權限的某些環境 (例如 Azure 網站) 中順利執行。

為了緩解、減少網路頻寬消耗或將用戶端 VM 大小增加到具有更多網路容量的用戶端 VM 大小。

## <a name="large-request-or-response-size"></a>大請求或回應大小

大型的要求/回應可能會導致逾時。 例如，假設您在用戶端上配置的超時值為 1 秒。 應用程式同時 (使用相同的實體網路連線) 要求兩個金鑰 (例如 'A' 和 'B')。 大多數用戶端都支援請求"管道"，其中兩個請求"A"和"B"都一個接一個地發送，無需等待其回應。 伺服器會以相同順序傳回回應。 如果回應"A"很大，它可以佔用以後請求的大部分超時。

在下面的示例中，請求"A"和"B"將快速發送到伺服器。 伺服器開始快速發送回應"A"和"B"。 由於資料傳輸時間，回應"B"必須在回應"A"超時後等待，即使伺服器回應迅速。

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

此要求/回應並不容易測量。 您可以檢測用戶端代碼以跟蹤大型請求和回應。

大回應尺寸的解析度各不相同，但包括：

1. 針對大量小值（而不是幾個大值）優化應用程式。
    - 比較好的解決方案是將資料分割成相關的較小值。
    - 請參閱帖子["redis 的理想值大小範圍是什麼？100 KB 是否太大？](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)有關建議使用較小值的詳細資訊。
1. 增加 VM 的大小以獲得更高的頻寬功能
    - 用戶端或伺服器 VM 上的頻寬越多可能會縮短更大回應的資料傳輸時間。
    - 將兩台電腦上的當前網路使用方式與當前 VM 大小的限制進行比較。 僅伺服器上或用戶端上的頻寬可能不足。
1. 增加應用程式使用的連線物件數。
    - 使用迴圈方法對不同的連線物件發出請求。

## <a name="additional-information"></a>其他資訊

- [針對 Azure Cache for Redis 伺服器端問題進行疑難排解](cache-troubleshoot-server.md)
- [如何效能評定和測試我快取的效能？](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
