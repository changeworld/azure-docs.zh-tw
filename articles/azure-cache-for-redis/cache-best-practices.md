---
title: Azure Cache for Redis 的最佳做法
description: 藉由遵循這些最佳作法，瞭解如何有效地使用 Azure Cache for Redis。
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: 0ed0009bce18e2b0970b425c31d2f38cef387187
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87008314"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Azure Cache for Redis 的最佳做法 
藉由遵循這些最佳作法，您可以協助最大化 Azure Cache for Redis 實例的效能和符合成本效益的使用。

## <a name="configuration-and-concepts"></a>組態和概念
 * **針對生產系統使用標準或進階層。**  基本層是單一節點系統，沒有資料複寫也沒有 SLA。 此外，請至少使用 C1 快取。  C0 快取適用于簡單的開發/測試案例，因為它們有共用的 CPU 核心、記憶體很少，而且很容易發生「有雜訊的鄰近」問題。

 * **請記住，Redis 是記憶體內部的資料存放區。**  [本文](cache-troubleshoot-data-loss.md)概述可能發生資料遺失的部分案例。

 * **開發您的系統，讓它可以**[因修補和容錯移轉而](cache-failover.md)處理連線短暫中斷。

 * 設定**您[的 maxmemory 保留設定](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)，以**在記憶體壓力條件下改善系統回應能力。  對於大量寫入的工作負載，或如果您要在 Redis 中儲存較大的值（100 KB 或更多），就有足夠的保留設定特別重要。 您應該以快取大小的10% 作為開頭，如果您有大量寫入負載，則會增加此百分比。

 * **Redis 最適合使用較小的值**，因此，請考慮將更大的資料團到多個索引鍵。  在[此 Redis 討論](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)中，會列出一些考慮事項，您應該仔細考慮。  閱讀 [這篇文章](cache-troubleshoot-client.md#large-request-or-response-size) 以了解較大值所造成的範例問題。

 * **在相同的區域中找出您的快取實例和應用程式。**  連線到不同區域中的快取會大幅增加延遲，並減少可靠性。  雖然您可以從 Azure 外部進行連線，但在*使用 Redis 作為*快取時，並不建議這麼做。  如果您只是使用 Redis 做為索引鍵/值存放區，延遲可能不是主要考慮。 

 * **重複使用連接。**  建立新的連線既昂貴又會增加延遲，因此盡可能重複使用連接。 如果您選擇建立新的連接，請務必先關閉舊的連接，然後再釋放它們（即使是在 .NET 或 JAVA 之類的 managed 記憶體語言中）。

 * **將您的用戶端*連結*庫設定為使用至少15秒**的連線超時，讓系統時間即使在較高的 CPU 條件下也能連接。  小型連接逾時值不保證會在該時間範圍內建立連接。  如果發生錯誤（高用戶端 CPU、高伺服器 CPU 等等），則短連接逾時值會導致連線嘗試失敗。 這種行為通常會使不良的情況更糟。  較短的超時 aggravate 會強制系統重新開機嘗試重新連線的程式，而不會造成問題，而這可能會導致連線 *> 失敗 > 重試*迴圈。 我們通常會建議您在15秒或更高的時間保持連接逾時。 最好是讓連線嘗試在15或20秒後成功，而不是讓它快速地重試。 這種重試迴圈可能會導致您的停機時間，比您讓系統一開始就會更久。  
     > [!NOTE]
     > 此指導方針專屬於連線*嘗試*，而不會與您願意等候作業（例如 GET 或 SET）*完成的時間*相關。
 
 * **避免耗費資源的作業**-某些 Redis 作業（例如[KEYS](https://redis.io/commands/keys)命令）*非常*昂貴，應予以避免。  如需詳細資訊，請參閱關於[長時間執行命令](cache-troubleshoot-server.md#long-running-commands)的一些考慮

 * **使用 tls 加密**-Azure Cache for Redis 預設需要 TLS 加密通訊。  目前支援 TLS 版本1.0、1.1 和1.2。  不過，TLS 1.0 和1.1 位於整個產業的淘汰路徑，因此，請盡可能使用 TLS 1.2。  如果您的用戶端程式庫或工具不支援 TLS，則可以[透過 Azure 入口網站](cache-configure.md#access-ports)或[管理 api](https://docs.microsoft.com/rest/api/redis/redis/update)來啟用未加密的連線。  在無法進行加密連線的情況下，建議將快取和用戶端應用程式放入虛擬網路中。  如需有關在虛擬網路快取案例中使用哪些埠的詳細資訊，請參閱此[表格](cache-how-to-premium-vnet.md#outbound-port-requirements)。
 
 * **閒置超時**-Azure Redis 目前有10分鐘的連線閒置時間，因此應設定為小於10分鐘。
 
## <a name="memory-management"></a>記憶體管理
在您的 Redis 伺服器實例中，您可能會想要考慮的記憶體使用量有數個相關事項。  以下為幾個範例：

 * **選擇適用于您應用程式的收回[原則](https://redis.io/topics/lru-cache)。**  Azure Redis 的預設原則是*volatile-lru*，這表示只有已設定 TTL 值的索引鍵才符合收回資格。  如果沒有任何金鑰具有 TTL 值，系統就不會收回任何金鑰。  如果您想要讓系統在記憶體不足的壓力下可收回任何金鑰，您可能會想要考慮*allkeys-lru-lru*原則。

 * **在您的金鑰上設定到期值。**  到期將會主動移除金鑰，而不是等到發生記憶體不足的壓力。  當收回因為記憶體不足的壓力而開始執行時，可能會造成伺服器上的額外負載。  如需詳細資訊，請參閱[過期](https://redis.io/commands/expire)和[EXPIREAT](https://redis.io/commands/expireat)命令的檔。
 
## <a name="client-library-specific-guidance"></a>用戶端程式庫的特定指引
 * [Stackexchange.redis. Redis （.NET）](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [JAVA-應該使用哪一種用戶端？](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [萵苣（JAVA）](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis （JAVA）](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Asp.Net 會話狀態提供者](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>何時可以安全地重試？
可惜的是，沒有任何簡單的答案。  每個應用程式都必須決定哪些作業可以重試，哪些則不能。  每項作業都有不同的需求和金鑰間相依性。  以下是您可以考慮的一些事項：

 * 您可以取得用戶端錯誤，即使 Redis 已成功執行您要求它執行的命令也一樣。  例如：
     - 「超時」是一種用戶端概念。  如果作業已到達伺服器，伺服器將會執行此命令，即使用戶端放棄等候也一樣。  
     - 當通訊端連接發生錯誤時，無法得知作業是否確實在伺服器上執行。  例如，在伺服器處理要求之後，但在用戶端收到回應之前，可能會發生連接錯誤。
 *  如果我不小心執行相同的作業兩次，我的應用程式會如何回應？  例如，如果我遞增整數兩次而不是一次，該怎麼辦？  我的應用程式是否會從多個位置寫入相同的金鑰？  如果我的重試邏輯覆寫應用程式中其他部分所設定的值，該怎麼辦？

如果您想要測試程式碼在錯誤情況下的運作方式，請考慮使用[重新開機功能](cache-administration.md#reboot)。 重新開機可讓您查看連接短暫中斷如何影響您的應用程式。

## <a name="performance-testing"></a>效能測試
 * **開始使用 `redis-benchmark.exe` **在撰寫您自己的效能測試之前，先瞭解可能的輸送量/延遲。  Redis-基準測試檔可在[這裡找到](https://redis.io/topics/benchmarks)。  請注意，redis-基準測試不支援 TLS，因此您必須在執行測試之前，[透過入口網站啟用非 TLS 埠](cache-configure.md#access-ports)。  [您可以在這裡找到 windows 相容版本的 redis-benchmark.exe](https://github.com/MSOpenTech/redis/releases)
 * 用於測試的用戶端 VM 應該位於與您的 Redis 快取實例**相同的區域中**。
 * 我們建議您的用戶端**使用 DV2 VM 系列**，因為它們具有較佳的硬體，並會提供最佳的結果。
 * 請確定您所使用的用戶端 VM，至少具有與所測試快取相同的*計算和頻寬*。 
 * 如果您是在 Windows 上，請在用戶端電腦上**啟用 VRSS** 。  [參閱此處了解詳細資訊](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx)。  範例 powershell 腳本：
     >PowerShell-ExecutionPolicy 不受限制的 Enable-Set-netadapterrss-Name （Get-netadapter）。檔案名 
     
 * **請考慮使用 Premium 層 Redis 實例**。  這些快取大小會有更好的網路延遲和輸送量，因為它們都是在較佳的 CPU 和網路硬體上執行。
 
     > [!NOTE]
     > 我們在[此發佈](cache-faq.md#azure-cache-for-redis-performance)觀察到的效能結果，以供您參考。   此外，請注意 SSL/TLS 會增加額外負荷，因此，如果您使用傳輸加密，則可能會有不同的延遲和/或輸送量。
 
### <a name="redis-benchmark-examples"></a>Redis-基準測試範例
**測試前設定**：準備快取實例，並提供以下所列延遲和輸送量測試命令所需的資料。
> redis-基準測試-h yourcache.redis.cache.windows.net-yourAccesskey-t 設定-n 10-d 1024 

**測試延遲**：使用1k 承載測試 GET 要求。
> redis-基準測試-h yourcache.redis.cache.windows.net-yourAccesskey-t d 1024-P 50-c 4

**若要測試輸送量：** 使用1k 承載的管線 GET 要求。
> redis-基準測試-h yourcache.redis.cache.windows.net-yourAccesskey-t GET-n 1000000-d 1024-P 50-c 50
