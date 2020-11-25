---
title: Azure Cache for Redis 的最佳做法
description: 藉由遵循這些最佳作法，瞭解如何有效地使用您的 Azure Cache for Redis。
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: 47c8096893742a25904f0f7e688af2fc641166d1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004308"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Azure Cache for Redis 的最佳做法 
藉由遵循這些最佳作法，您可以充分利用 Azure Cache for Redis 實例的效能和成本效益。

## <a name="configuration-and-concepts"></a>組態和概念
 * **針對生產系統使用標準層或進階層。**  基本層是單一節點系統，沒有資料複寫也沒有 SLA。 此外，請至少使用 C1 快取。  C0 快取是針對簡單的開發/測試案例所設計，因為它們有共用的 CPU 核心、很少的記憶體，而且很容易發生「有雜訊的鄰居」問題。

 * **請記住，Redis 是記憶體中的資料存放區。**  [本文將概述可能](cache-troubleshoot-data-loss.md) 發生資料遺失的一些案例。

 * **開發您的系統，讓它可以**[因修補和容錯移轉而](cache-failover.md)處理連接標誌。

 * **設定您 [的 maxmemory 保留設定](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) ，以改善** 記憶體壓力條件下的系統回應。  對於大量寫入的工作負載，或如果您要在 Redis 中儲存較大的值 (100 KB 或更大的) ，足夠的保留設定特別重要。 您應從快取大小的10% 開始，如果您有大量寫入的載入，則會增加此百分比。

 * **Redis 最適合與較小的值搭配使用**，因此請考慮將較大的資料將切分成至多個索引鍵。  在 [此 Redis 討論](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)中，會列出一些您應謹慎考慮的考慮。  閱讀 [這篇文章](cache-troubleshoot-client.md#large-request-or-response-size) 以了解較大值所造成的範例問題。

 * **在相同區域中找出您的快取實例和應用程式。**  連線到不同區域中的快取會大幅增加延遲，並減少可靠性。  雖然您可以從 Azure 外部連線，但 *在使用 Redis 做為* 快取時，不建議使用此選項。  如果您只是使用 Redis 做為索引鍵/值存放區，延遲可能不是主要的考慮。 

 * **重複使用連接。**  建立新連線的成本很高，而且會增加延遲，因此請盡可能重複使用連接。 如果您選擇建立新的連線，請務必先關閉舊的連線，再將它們釋放 (，即使是在 .NET 或 JAVA) 之類的 managed 記憶體語言中。

 * **將您的用戶端程式庫設定為使用至少15秒的 *連接逾時***，讓系統時間在較高的 CPU 條件下進行連接。  小規模的連接逾時值並不保證會在該時間範圍內建立連接。  如果發生錯誤 (高用戶端 CPU、高伺服器 CPU 等等) ，則短暫的連接逾時值會導致連接嘗試失敗。 這種行為通常會使不良狀況更糟。  較短的超時加劇會強制系統重新開機嘗試重新連接的程式，而不會導致 *連接 > 失敗 > 重試* 迴圈，而不是協助您縮短問題。 我們通常會建議您在15秒或更高的時間內保留您的連接逾時。 最好是讓您的連線嘗試在15或20秒後成功完成，而不是只在重試時快速失敗。 這種重試迴圈可能會導致您的停機時間超過一開始的時間。  
     > [!NOTE]
     > 這是 *連接嘗試* 的特定指導方針，而且與您願意等待 *作業（例如* GET 或 SET 完成）的時間無關。
 
 * **避免耗費資源的作業** -某些 Redis 作業（例如 [金鑰](https://redis.io/commands/keys) 命令） *非常* 昂貴，應予以避免。  如需詳細資訊，請參閱[長時間](cache-troubleshoot-server.md#long-running-commands)執行之命令的一些考慮

 * **使用 tls 加密** -Azure Cache for Redis 預設需要 TLS 加密通訊。  目前支援 TLS 版本1.0、1.1 和1.2。  不過，TLS 1.0 和1.1 都是在整個產業淘汰的路徑上，因此請盡可能使用 TLS 1.2。  如果您的用戶端程式庫或工具不支援 TLS，則啟用未加密的連接可 [透過 Azure 入口網站](cache-configure.md#access-ports) 或 [管理 api](/rest/api/redis/redis/update)來完成。  在無法進行加密連接的情況下，建議您將快取和用戶端應用程式放入虛擬網路中。  如需有關虛擬網路快取案例中所使用之埠的詳細資訊，請參閱此 [表格](cache-how-to-premium-vnet.md#outbound-port-requirements)。
 
 * **閒置 timeout** -Azure Redis 目前有10分鐘的連線閒置超時，所以應設定為小於10分鐘。
 
## <a name="memory-management"></a>記憶體管理
您可能想要考慮的 Redis 伺服器實例中的記憶體使用量有幾個相關事項。  以下為幾個範例：

 * **選擇適用于您應用程式的收回 [原則](https://redis.io/topics/lru-cache) 。**  Azure Redis 的預設原則是 *volatile-lru*，這表示只有設定 TTL 值的金鑰才有資格收回。  如果沒有任何金鑰具有 TTL 值，系統就不會收回任何金鑰。  如果您想要讓系統在記憶體不足的壓力下收回任何金鑰，您可能會想要考慮 *allkeys-random-lru* 原則。

 * **設定金鑰的到期日值。**  到期將會主動移除金鑰，而不是等到記憶體不足的壓力。  當收回因為記憶體不足的壓力而開始時，可能會導致您的伺服器產生額外的負載。  如需詳細資訊，請參閱 [過期](https://redis.io/commands/expire) 和 [EXPIREAT](https://redis.io/commands/expireat) 命令的說明文件。
 
## <a name="client-library-specific-guidance"></a>用戶端程式庫特定指引
 * [>stackexchange.redis. Redis ( .NET) ](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [JAVA-我應該使用哪一種用戶端？](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [萵苣 (JAVA) ](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (JAVA) ](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [ASP.NET 工作階段狀態提供者](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>何時安全地重試？
可惜的是，沒有什麼簡單的答案。  每個應用程式都需要決定哪些作業可以重試，哪些作業無法重試。  每項作業都有不同的需求和相依性的相依性。  以下是您可能會考慮的一些事項：

 * 即使 Redis 成功執行您要求它執行的命令，您還是可以取得用戶端錯誤。  例如：
     - 超時是用戶端的概念。  如果作業已連線到伺服器，即使用戶端等候，伺服器仍會執行命令。  
     - 當通訊端連接發生錯誤時，就無法得知作業是否真的在伺服器上執行。  例如，在伺服器處理要求之後，但在用戶端收到回應之前，可能會發生連接錯誤。
 *  如果我不小心執行相同的作業兩次，我的應用程式如何回應？  比方說，如果我將整數兩次遞增而不是一次，該怎麼辦？  我的應用程式是否會從多個位置寫入相同的金鑰？  如果我的重試邏輯覆寫了我的應用程式其他部分所設定的值，該怎麼辦？

如果您想要在錯誤情況下測試程式碼的運作方式，請考慮使用 [重新開機功能](cache-administration.md#reboot)。 重新開機可讓您查看連接標誌對應用程式的影響。

## <a name="performance-testing"></a>效能測試
 * **開始使用 `redis-benchmark.exe`** 在撰寫您自己的效能測試之前，先感受可能的輸送量/延遲。  您可以在 [這裡找到](https://redis.io/topics/benchmarks)Redis 基準測試檔。  請注意，redis 基準測試不支援 TLS，因此您必須先在 [入口網站中啟用非 tls 埠](cache-configure.md#access-ports) ，才能執行測試。  [您可以在這裡找到 windows 相容版本的 redis-benchmark.exe](https://github.com/MSOpenTech/redis/releases)
 * 用於測試的用戶端 VM 應位於與您的 Redis 快取實例 **相同的區域中** 。
 * 建議您針對用戶端 **使用 DV2 VM 系列**，因為它們有更好的硬體，並提供最佳結果。
 * 請確定您使用的用戶端 VM 擁有的 *計算和頻寬，至少與測試的快取數量相同* 。 
 * 如果您是在 Windows 上，請在用戶端電腦上 **啟用 VRSS** 。  [參閱此處了解詳細資訊](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11))。  範例 PowerShell 指令碼：
     >PowerShell-ExecutionPolicy 不受限制的 Enable-NetAdapterRSS 名稱 ( Get-netadapter) 。名字 
     
 * **請考慮使用 Premium 層 Redis 實例**。  這些快取大小會有較佳的網路延遲和輸送量，因為它們是在 CPU 和網路的更佳硬體上執行。
 
     > [!NOTE]
     > 我們觀察到的效能結果是在 [此處發佈](cache-planning-faq.md#azure-cache-for-redis-performance) 以供您參考。   此外，請注意 SSL/TLS 會增加一些額外負荷，因此，如果您使用傳輸加密，可能會得到不同的延遲和/或輸送量。
 
### <a name="redis-benchmark-examples"></a>Redis-Benchmark 範例
**預先測試設定**：準備快取實例，其中包含下列延遲和輸送量測試命令所需的資料。
> redis-基準測試-h yourcache.redis.cache.windows.net-a >youraccesskey-t SET-n 10-d 1024 

**若要測試延遲**：使用1k 承載測試 GET 要求。
> redis-基準測試-h yourcache.redis.cache.windows.net-a >youraccesskey-t d 1024-P 50-c 4

**若要測試輸送量：** 使用1k 承載將 GET 要求進行管線處理。
> redis-基準測試-h yourcache.redis.cache.windows.net-a >youraccesskey-t GET-n 1000000-d 1024-P 50-c 50