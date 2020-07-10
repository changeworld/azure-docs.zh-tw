---
title: 針對 Azure Cache for Redis 中的資料遺失問題進行疑難排解
description: 了解如何解決 Azure Cache for Redis 的資料遺失問題，例如遺失部分金鑰、金鑰到期，或完全遺失金鑰。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: ba0430461df5ce1a2d615b819dbe5e8a36ae52b7
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86184526"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>針對 Azure Cache for Redis 中的資料遺失問題進行疑難排解

本文討論如何診斷 Azure Cache for Redis 中，可能發生的實際或認知資料遺失。

> [!NOTE]
> 本指南中的數個疑難排解步驟包含如何執行 Redis 命令和監視各種效能度量的指示。 如需詳細資訊和指示，請參閱 [其他資訊](#additional-information) 一節中的文章。
>

## <a name="partial-loss-of-keys"></a>遺失部分金鑰

Azure Cache for Redis 在金鑰儲存於記憶體後，不會隨機刪除金鑰。 不過，它確實會根據到期或收回原則，以及明確的金鑰刪除命令，來移除金鑰。 已寫入 Premium 或標準 Azure Cache for Redis 實例中主要節點的金鑰，也可能無法立即在複本上使用。 資料會以非同步和非封鎖的方式從主要複本複寫到複本。

如果您發現金鑰已從快取中消失，請檢查下列可能的原因：

| 原因 | 描述 |
|---|---|
| [金鑰到期日](#key-expiration) | 已移除索引鍵，因為它們已設定逾時。 |
| [金鑰收回](#key-eviction) | 在記憶體壓力下，會移除金鑰。 |
| [金鑰刪除](#key-deletion) | 金鑰會由明確的刪除命令移除。 |
| [非同步複寫](#async-replication) | 因為資料複寫延遲，所以複本上沒有可用的金鑰。 |

### <a name="key-expiration"></a>金鑰到期日

Azure Cache for Redis 會在金鑰已指派逾時，且已經過該期間時，自動移除金鑰。 如需 Redis 金鑰到期的詳細資訊，請參閱 [EXPIRE](https://redis.io/commands/expire) 命令文件。 您也可以使用 [SET](https://redis.io/commands/set)、[SETEX](https://redis.io/commands/setex)、[GETSET](https://redis.io/commands/getset) 和其他 **\*STORE** 命令來設定逾時值。

若要取得到期金鑰數量的統計資料，請使用 [INFO](https://redis.io/commands/info) 命令。 `Stats` 區段會顯示到期金鑰的總數。 `Keyspace` 區段提供具有逾時和平均逾時值的金鑰數目相關資訊。

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

您也可以查看快取的診斷計量，以查看金鑰遺失的時間和過期金鑰的峰值之間是否有關聯性。 如需使用 keyspace 通知或 **MONITOR** 來偵錯下列問題，請參閱[偵錯 Redis Keyspace 遺漏](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)。

### <a name="key-eviction"></a>金鑰收回

Azure Cache for Redis 需要記憶體空間來儲存資料。 它會視需要清除金鑰，以釋出可用的記憶體。 當 [INFO](https://redis.io/commands/info) 命令中的 **used_memory** 或 **used_memory_rss** 值接近設定的 **maxmemory** 設定時，Azure Cache for Redis 會根據[快取原則](https://redis.io/topics/lru-cache)，開始從記憶體中收回金鑰。

您可以使用 [INFO](https://redis.io/commands/info) 命令，監視已收回金鑰的數目：

```
# Stats

evicted_keys:13224
```

您也可以查看快取的診斷計量，以查看金鑰遺失的時間和收回金鑰的峰值之間是否有關聯性。 如需使用 keyspace 通知或 **MONITOR** 來偵錯下列問題，請參閱[偵錯 Redis Keyspace 遺漏](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)。

### <a name="key-deletion"></a>金鑰刪除

Redis 用戶端可以發出 [DEL](https://redis.io/commands/del) 或 [HDEL](https://redis.io/commands/hdel) 命令，明確地從 Azure Cache for Redis 移除金鑰。 您可以使用 [INFO](https://redis.io/commands/info) 命令，追蹤刪除作業的數目。 如果已呼叫 **DEL** 或 **HDEL** 命令，這些命令會列在 `Commandstats` 區段中。

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>非同步複寫

標準層或進階層中的任何 Azure Cache for Redis 實例都設定了主要節點和至少一個複本。 資料會使用背景處理常式，以非同步方式從主要複本複製到複本。 [redis.io](https://redis.io/topics/replication) 網站說明 Redis 資料複寫的一般運作方式。 在用戶端經常寫入 Redis 的情況下，可能會發生部分資料遺失，因為這種複寫不一定是瞬間的。 例如，如果主要複本在用戶端寫入金鑰*後*關閉 *，但在*背景處理常式有機會將該金鑰傳送至複本，則當複本接管為新的主要複本時，金鑰就會遺失。

## <a name="major-or-complete-loss-of-keys"></a>主要或完全遺失金鑰

如果大部分或所有金鑰已從快取中消失，請檢查以下可能的原因：

| 原因 | 描述 |
|---|---|
| [金鑰排清](#key-flushing) | 已手動清除金鑰。 |
| [不正確的資料庫選擇](#incorrect-database-selection) | Azure Cache for Redis 已設為使用非預設資料庫。 |
| [Redis 執行個體失敗](#redis-instance-failure) | Redis 伺服器無法使用。 |

### <a name="key-flushing"></a>金鑰排清

用戶端可以呼叫 [FLUSHDB](https://redis.io/commands/flushdb) 命令，以移除*單一*資料庫中的所有金鑰，或透過 [FLUSHALL](https://redis.io/commands/flushall)，將所有金鑰從Redis 快取中*所有*資料庫中移除。 若要找出是否已排清金鑰，請使用 [INFO](https://redis.io/commands/info) 命令。 `Commandstats` 區段會顯示是否已呼叫 **FLUSH** 命令：

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>不正確的資料庫選擇

Azure Cache for Redis 預設會使用 **db0** 資料庫。 如果您切換至另一個資料庫 (例如，**db1**)，並嘗試從中讀取金鑰，Azure Cache for Redis 在該資料庫中無法找到這些金鑰。 每個資料庫在邏輯方面是獨立的單位，並擁有不同的資料集。 使用 [SELECT](https://redis.io/commands/select) 命令以使用其他可用的資料庫，並在其中尋找金鑰。

### <a name="redis-instance-failure"></a>Redis 執行個體失敗

Redis 是記憶體內部的資料存放區。 資料保存在託管 Redis 快取的實體或虛擬機器上。 基本層中的 Azure Cache for Redis 執行個體僅在單一虛擬機器 (VM) 上執行。 如果該 VM 已關閉，您儲存在快取中的所有資料都會遺失。 

標準層和進階層中的快取，藉由使用複寫設定中的兩個虛擬機器，提供資料遺失更高的彈性。 當這類快取中的主要節點失敗時，複本節點會接管，以自動提供資料。 對於故障和更新，這些 VM 位於不同的網域中，以盡量減少兩者同時無法使用的機會。 不過，如果主要資料中心發生中斷，VM 仍可能會同時停止運作。 在這些罕見的情況下，您的資料將會遺失。

考慮使用 [Redis 資料永續性](https://redis.io/topics/persistence)和[異地複寫](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication)，以改善資料保護，避免這些基礎結構失敗。

## <a name="additional-information"></a>其他資訊

- [針對 Azure Cache for Redis 伺服器端問題進行疑難排解](cache-troubleshoot-server.md)
- [應該使用哪個 Azure Redis 快取供應項目和大小？](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [如何監視 Azure Redis 快取](cache-how-to-monitor.md)
- [如何執行 Redis 命令？](cache-faq.md#how-can-i-run-redis-commands)
