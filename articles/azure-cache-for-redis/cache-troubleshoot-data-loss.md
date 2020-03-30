---
title: 針對 Azure Cache for Redis 中的資料遺失問題進行疑難排解
description: 瞭解如何使用 Redis 的 Azure 緩存解決資料丟失問題，例如金鑰部分丟失、金鑰過期或金鑰完全丟失。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d54506b94f076f0a3d967f88bd4e2960a1ca6396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530896"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>針對 Azure Cache for Redis 中的資料遺失問題進行疑難排解

本文討論如何診斷在 Redis 的 Azure 緩存中可能發生的實際或感知資料損失。

> [!NOTE]
> 本指南中的數個疑難排解步驟包含如何執行 Redis 命令和監視各種效能度量的指示。 如需詳細資訊和指示，請參閱 [其他資訊](#additional-information) 一節中的文章。
>

## <a name="partial-loss-of-keys"></a>金鑰部分丟失

Redis 的 Azure 緩存在金鑰存儲在記憶體中後不會隨機刪除它們。 但是，它會刪除金鑰以回應過期或逐出策略以及顯式金鑰刪除命令。 已寫入 Redis 實例的高級或標準 Azure 緩存中的主節點的金鑰可能立即在副本上不可用。 以非同步和非阻塞方式將資料從主資料庫複製到副本。

如果發現金鑰從緩存中消失，請檢查以下可能的原因：

| 原因 | 描述 |
|---|---|
| [金鑰到期日](#key-expiration) | 由於鍵上設置了超時，因此將刪除金鑰。 |
| [關鍵驅逐](#key-eviction) | 鑰匙在記憶體壓力下拆下。 |
| [金鑰刪除](#key-deletion) | 通過顯式刪除命令刪除金鑰。 |
| [非同步複製](#async-replication) | 由於資料複寫延遲，金鑰在副本上不可用。 |

### <a name="key-expiration"></a>金鑰到期日

如果為 Redis 分配了超時並且該期限已過，則 Redis 的 Azure 緩存將自動刪除金鑰。 有關 Redis 金鑰過期的詳細資訊，請參閱[過期](https://redis.io/commands/expire)命令文檔。 也可以使用**\***[SET、SETEX、GETSET](https://redis.io/commands/set)和其他存儲[SETEX](https://redis.io/commands/setex)命令來設置[GETSET](https://redis.io/commands/getset)超時值。

要獲取有關已過期的金鑰數的統計資訊，請使用[INFO](https://redis.io/commands/info)命令。 該`Stats`部分顯示過期金鑰的總數。 本節`Keyspace`提供有關具有超時的鍵數和平均超時值的詳細資訊。

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

您還可以查看緩存的診斷指標，以查看金鑰丟失時間與過期金鑰的峰值之間是否存在相關性。 有關使用金鑰空間通知或**MONITOR**調試這些類型的問題的資訊，請參閱[調試 Redis 金鑰空間未命中的](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)附錄。

### <a name="key-eviction"></a>關鍵驅逐

Redis 的 Azure 緩存需要記憶體空間來存儲資料。 它會清除金鑰，以便在必要時釋放可用記憶體。 當[INFO](https://redis.io/commands/info)命令中的**used_memory**或**used_memory_rss**值接近配置**的最大記憶體**設置時，Redis 的 Azure 緩存將根據[緩存策略](https://redis.io/topics/lru-cache)從記憶體中驅逐金鑰。

您可以使用[INFO](https://redis.io/commands/info)命令監視被逐出的金鑰數：

```
# Stats

evicted_keys:13224
```

您還可以查看緩存的診斷指標，以查看金鑰丟失時間與逐出金鑰的峰值之間是否存在相關性。 有關使用金鑰空間通知或**MONITOR**調試這些類型的問題的資訊，請參閱[調試 Redis 金鑰空間未命中的](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)附錄。

### <a name="key-deletion"></a>金鑰刪除

Redis 用戶端可以發出[DEL](https://redis.io/commands/del)或[HDEL](https://redis.io/commands/hdel)命令，以顯式從 Azure 緩存中刪除 Redis 的金鑰。 您可以使用[INFO](https://redis.io/commands/info)命令跟蹤刪除操作的數量。 如果已調用**DEL**或**HDEL**命令，則這些命令將`Commandstats`列在本節中。

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>非同步複製

標準層或高級層中 Redis 實例的任何 Azure 緩存都配置了主節點和至少一個副本。 通過使用後臺進程，資料從主資料庫非同步複製到副本。 [redis.io](https://redis.io/topics/replication)網站描述了 Redis 資料複製的總體工作原理。 對於用戶端頻繁寫入 Redis 的情況，可能會發生部分資料丟失，因為此複製保證是即時的。 例如，如果主控形狀在用戶端向其寫入金鑰*後*出現故障，但在後臺進程有機會將該金鑰發送到副本*之前*，當副本接管新主副本時，金鑰將丟失。

## <a name="major-or-complete-loss-of-keys"></a>金鑰主要丟失或完全丟失

如果大多數或所有金鑰從緩存中消失，請檢查以下可能的原因：

| 原因 | 描述 |
|---|---|
| [鍵沖洗](#key-flushing) | 金鑰已手動清除。 |
| [資料庫選擇不正確](#incorrect-database-selection) | Redis 的 Azure 緩存設置為使用非預設資料庫。 |
| [雷瑞斯實例失敗](#redis-instance-failure) | Redis 伺服器不可用。 |

### <a name="key-flushing"></a>鍵沖洗

用戶端可以調用[FLUSHDB](https://redis.io/commands/flushdb)命令來刪除*單個*資料庫中的所有金鑰，也可以[調用 FLUSHALL](https://redis.io/commands/flushall)以從 Redis 緩存中的所有資料庫中刪除*所有*金鑰。 要瞭解金鑰是否已刷新，請使用[INFO](https://redis.io/commands/info)命令。 該`Commandstats`部分顯示是否調用了 **"FLUSH"** 命令：

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>資料庫選擇不正確

預設情況下，Redis 的 Azure 緩存使用**db0**資料庫。 如果切換到其他資料庫（例如**db1），** 並嘗試從中讀取金鑰，則 Redis 的 Azure 緩存將在那裡找不到它們。 每個資料庫在邏輯上是獨立的單元，並持有不同的資料集。 使用[SELECT](https://redis.io/commands/select)命令使用其他可用資料庫，並在每個資料庫中查找金鑰。

### <a name="redis-instance-failure"></a>雷瑞斯實例失敗

Redis 是記憶體中的資料存儲。 資料保存在承載 Redis 緩存的物理或虛擬機器上。 基本層中 Redis 實例的 Azure 緩存僅在單個虛擬機器 （VM） 上運行。 如果該 VM 已關閉，則緩存中存儲的所有資料都會丟失。 

標準層和高級層中的緩存通過在複製的配置中使用兩個 VM 提供更高的抗資料丟失能力。 當此類緩存的主節點發生故障時，副本節點將接管以自動提供資料。 這些 VM 位於單獨的域上，用於故障和更新，以儘量減少同時不可用兩者的可能性。 但是，如果發生重大資料中心中斷，VM 仍可能同時關閉。 在這些罕見情況下，您的資料將丟失。

請考慮使用[Redis 資料持久性](https://redis.io/topics/persistence)和[異地複製](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication)來改進資料防護，防止這些基礎結構故障。

## <a name="additional-information"></a>其他資訊

- [針對 Azure Cache for Redis 伺服器端問題進行疑難排解](cache-troubleshoot-server.md)
- [應該使用哪個 Azure Redis 快取供應項目和大小？](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [如何監視 Azure Redis 快取](cache-how-to-monitor.md)
- [如何執行 Redis 命令？](cache-faq.md#how-can-i-run-redis-commands)
