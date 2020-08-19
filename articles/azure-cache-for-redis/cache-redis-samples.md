---
title: Azure Cache for Redis 範例
description: 了解如何使用 Azure Cache for Redis 搭配下列程式碼範例：連線到快取、讀取和寫入快取中的資料、ASP.NET Azure Cache for Redis 提供者。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-dotnet
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 553850173f463a05b13768eb3b9e17703bfa2886
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212298"
---
# <a name="azure-cache-for-redis-samples"></a>Azure Cache for Redis 範例
本主題提供「Azure Redis 快取」範例清單，其中涵蓋下列這類案例：連線至快取、對快取進行資料讀取和寫入，以及使用 ASP.NET「Azure Redis 快取」提供者。 有些範例是可下載的專案，有些則提供逐步指導並包括程式碼片段，但沒有可下載專案的連結。

## <a name="hello-world-samples"></a>Hello World 範例
本節中的範例說明一些基本概念，包括連線至「Azure Redis 快取」執行個體，以及使用各種語言和 Redis 用戶端對快取進行資料的讀取和寫入。

[Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) 範例示範如何使用 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET 用戶端來執行各種快取作業。

此範例示範如何：

* 使用各種連線選項
* 使用同步和非同步作業在快取中讀取和寫入物件
* 使用 Redis MGET/MSET 命令來傳回所指定金鑰的值
* 執行 Redis 交易作業
* 使用 Redis 清單和已排序的集合
* 使用 JsonConvert 序列化程式儲存 .NET 物件
* 使用 Redis 集合來實作標記
* 使用 Redis 叢集

如需詳細資訊，請參閱 GitHub 上的 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) \(英文\) 文件，如需更多使用案例，則請參閱 [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests) \(英文\) 單元測試。

[如何搭配使用 Azure Redis 快取與 Python](cache-python-get-started.md) 說明如何使用 Python 和 [redis-py](https://github.com/andymccurdy/redis-py) 用戶端來開始運用「Azure Redis 快取」。

[使用快取中的 .NET 物件](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) 說明一種將 .NET 物件序列化的方式，以便您能夠對「Azure Redis 快取」執行個體讀取和寫入這些物件。 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>使用 Azure Redis 快取作為 ASP.NET SignalR 的向外延展後擋板
[使用 Azure Redis 快取作為 ASP.NET SignalR 的向外延展後擋板](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane)範例示範如何使用「Azure Redis 快取」作為 SignalR 後擋板。 如需後擋板的詳細資訊，請參閱 [使用 Redis 的 SignalR 向外延展](https://www.asp.net/signalr/overview/performance/scaleout-with-redis)。

## <a name="azure-cache-for-redis-customer-query-sample"></a>Azure Redis 快取客戶查詢範例
本範例示範如何比較從快取中存取資料與從持續性儲存體中存取資料之間的效能。 此範例有兩個專案。

* [示範 Azure Redis 快取如何透過快取資料來改善效能](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [植入示範的資料庫和快取](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET 工作階段狀態和輸出快取
[使用 Azure Redis 快取來儲存 ASP.NET SessionState 和 OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) 範例示範如何使用「Azure Redis 快取」，藉由適用於 Redis 的 SessionState 和 OutputCache 提供者來儲存 ASP.NET「工作階段」和「輸出快取」。

## <a name="manage-azure-cache-for-redis-with-maml"></a>使用 MAML 來管理 Azure Redis 快取
[使用 Azure 管理庫來管理 Azure Redis 快取](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)範例示範如何使用「Azure 管理庫」來管理 - (建立/更新/刪除) 快取。 

## <a name="custom-monitoring-sample"></a>自訂監視範例
[存取 Azure Redis 快取監視資料](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)範例示範如何在「Azure 入口網站」外部存取「Azure Redis 快取」的監視資料。

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>使用 PHP 和 Redis 撰寫的 Twitter 風格複製品
[Retwis](https://github.com/SyntaxC4-MSFT/retwis) 範例是 Redis Hello World。 它是透過 [Predis](https://github.com/nrk/predis) 用戶端使用 Redis 和 PHP 撰寫的最小 Twitter 風格社交網路複製品。 原始碼的設計十分簡單，並且同時顯示不同的 Redis 資料結構。

## <a name="bandwidth-monitor"></a>頻寬監視器
[頻寬監視器](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) 範例可讓您監視用戶端上使用的頻寬。 若要測量頻寬，請在快取用戶端電腦上執行這個範例、呼叫快取，並觀察頻寬監視器範例所報告的頻寬。
