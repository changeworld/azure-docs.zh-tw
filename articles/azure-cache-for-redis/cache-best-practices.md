---
title: Azure Cache for Redis 的最佳做法
description: 瞭解如何通過遵循這些最佳實踐有效地使用 Azure 緩存進行 Redis。
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: 105a3996753a1d1c2d71846cc8bad574e4498acf
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478618"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Azure Cache for Redis 的最佳做法 
通過遵循這些最佳實務,可以説明最大化 Redis 實例的 Azure 緩存的性能和成本效益。

## <a name="configuration-and-concepts"></a>組態和概念
 * **對生產系統使用標準層或高級層。**  基本層是單個節點系統,沒有數據複製,也沒有 SLA。 此外，請至少使用 C1 快取。  C0 緩存適用於簡單的開發/測試方案,因為它們具有共用 CPU 內核、很少記憶體,並且容易出現"嘈雜的鄰居"問題。

 * **請記住,Redis 是記憶體中的數據存儲。**  [本文](cache-troubleshoot-data-loss.md)概述了可能發生數據丟失的一些方案。

 * **開發您的系統,以便它可以處理連線波動**[,因為修補和故障轉移](cache-failover.md)。

 * **設定[最大記憶體預留設定](cache-configure.md#maxmemory-policy-and-maxmemory-reserved),以提高記憶體壓力條件下的系統回應能力**。  對於寫重工作負載或在 Redis 中存儲較大的值(100 KB 或更多),足夠的預留設置尤其重要。 您應該從快取大小的 10% 開始,如果負載重,則應增加此百分比。

 * **Redis 最適合較小的值**,因此請考慮將較大的數據切入多個鍵。  [在這次 Redis 討論中](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/),列出了一些注意事項,您應該仔細考慮。  閱讀 [這篇文章](cache-troubleshoot-client.md#large-request-or-response-size) 以了解較大值所造成的範例問題。

 * **在同一區域中尋找緩存實例和應用程式。**  連線到不同區域中的快取會大幅增加延遲，並減少可靠性。  雖然可以從 Azure 外部進行連線,但不建議*在使用 Redis 作為快取時*。  如果您只使用 Redis 作為金鑰 / 值儲存,則延遲可能不是主要問題。 

 * **重用連接。**  創建新連接成本高昂,並會增加延遲,因此請盡可能重複使用連接。 如果選擇創建新連接,請確保在釋放舊連接之前關閉它們(即使在託管記憶體語言(如 .NET 或 JAVA 中)。

 * **將用戶端庫配置為使用至少 15 秒*的連接超時***,即使系統在更高的 CPU 條件下也能進行連接。  小型連接超時值不保證連接是在該時間範圍內建立的。  如果出現問題(用戶端 CPU 高、伺服器 CPU 高等),則連接超時值短將導致連接嘗試失敗。 這種行為往往使糟糕的情況變得更糟。  較短的超時不會提供説明,而是通過強制系統重新啟動嘗試重新連接的過程來加劇問題,這可能導致*連接->故障 ->重试*迴圈。 我們通常建議您在 15 秒或更高級別時離開連接超時。 最好讓您的連接嘗試在 15 或 20 秒後成功,而不是讓連接嘗試迅速失敗,以便重試。 這樣的重試迴圈可能會導致中斷持續的時間比最初讓系統使用更長時間的時間要長。  
     > [!NOTE]
     > 本指南特定於*連接嘗試*,與您願意等待 GET 或 SET 等*操作*完成的時間無關。
 
 * **避免昂貴的操作**- 某些 Redis 操作(如[KEYS](https://redis.io/commands/keys)命令)*非常昂貴*,應避免使用。  有關詳細資訊,請參閱有關[長時間運行命令的](cache-troubleshoot-server.md#long-running-commands)一些注意事項

 * **使用 TLS 加密**- 預設情況下,Redis 的 Azure 快取需要 TLS 加密通訊。  當前支援 TLS 版本 1.0、1.1 和 1.2。  但是,TLS 1.0 和 1.1 正在全行業範圍內實現棄用,因此,如果可能,請使用 TLS 1.2。  如果用戶端庫或工具不支援 TLS,則可以[透過 Azure 門戶](cache-configure.md#access-ports)或管理[API](https://docs.microsoft.com/rest/api/redis/redis/update)啟用未加密的連接。  在無法進行加密連接的情況下,建議將快取和用戶端應用程式放入虛擬網路。  有關虛擬網路快取方案中使用哪些埠的詳細資訊,請參閱此[表](cache-how-to-premium-vnet.md#outbound-port-requirements)。
 
## <a name="memory-management"></a>記憶體管理
在 Redis 伺服器實例中,您可能需要考慮與記憶體使用方式相關的幾件事情。  以下為幾個範例：

 * **選擇您的應用程式[的逐出策略](https://redis.io/topics/lru-cache)。**  Azure Redis 的預設策略是*易失性 lru,* 這意味著只有具有 TTL 值集的鍵才有資格被逐出。  如果沒有密鑰具有 TTL 值,則系統不會驅逐任何密鑰。  如果希望系統允許在記憶體壓力下逐出任何密鑰,則可能需要考慮*allkeys-lru*策略。

 * **在金鑰上設置過期值。**  過期將主動刪除密鑰,而不是等待記憶體壓力。  當強制逐出由於記憶體壓力而啟動時,它可能會導致伺服器上的額外負載。  有關詳細資訊,請參閱[「過期](https://redis.io/commands/expire)」和「[過期」](https://redis.io/commands/expireat)命令的文檔。
 
## <a name="client-library-specific-guidance"></a>用戶端函式庫指定指南
 * [堆疊交換.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java - 我應該使用哪個用戶端?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [萊圖斯(爪哇)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [絕地(爪哇)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Asp.Net 工作階段狀態提供者](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>重試何時安全?
不幸的是,沒有簡單的答案。  每個應用程式都需要決定可以重試哪些操作,哪些操作不能。  每個操作都有不同的要求和密鑰間依賴關係。  以下是您可能會考慮的一些事項:

 * 即使 Redis 成功執行了請求它執行的命令,您也可以收到用戶端錯誤。  例如：
     - 超時是一個用戶端概念。  如果操作到達伺服器,即使用戶端放棄等待,伺服器也會運行該命令。  
     - 當套接字連接上出現錯誤時,無法知道該操作是否實際在伺服器上運行。  例如,連接錯誤可能發生在伺服器處理請求後,但在用戶端收到回應之前。
 *  如果我不小心運行了兩次相同的操作,我的應用程式會有什麼反應?  例如,如果我將整數增加兩次而不是一次,該怎麼辦?  我的應用程式是否從多個位置寫入同一密鑰?  如果我的重試邏輯覆蓋應用其他部分設置的值,該怎麼辦?

如果要測試程式碼在錯誤條件下的工作方式,請考慮使用[重新啟動功能](cache-administration.md#reboot)。 重新啟動允許您查看連接波動如何影響應用程式。

## <a name="performance-testing"></a>效能測試
 * **首先,在`redis-benchmark.exe`** 編寫自己的 perf 測試之前,先瞭解可能的輸送量/延遲。  Redis Bac 文件[可以在此處找到](https://redis.io/topics/benchmarks)。  請注意,redis-基準測試不支援 TLS,因此在執行測試之前,您必須[透過門戶啟用非 TLS 連接埠](cache-configure.md#access-ports)。  [可在此處找到相容的 redis-基準.exe 視窗版本](https://github.com/MSOpenTech/redis/releases)
 * 測試的用戶端 VM 應與 Redis 快取實體**的樣本**。
 * **我們建議對用戶端使用 Dv2 VM 系列**,因為它們具有更好的硬體,並且會提供最佳結果。
 * 使用您使用的用戶端 VM 有至少與測試的快取*一樣多的計算和頻寬*。 
 * 如果您在 Windows 上,則在用戶端電腦上**啟用 VRSS。**  [參閱此處了解詳細資訊](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx)。  電源殼文稿示例:
     >電源外殼 - 執行策略無限制啟用-網路適配器 RSS -名稱(獲取網路適配器)。名稱 
     
 * **請考慮使用進階層 Redis 實體 。**  這些緩存大小將具有更好的網路延遲和輸送量,因為它們在 CPU 和網路的更好硬體上運行。
 
     > [!NOTE]
     > 我們觀察到的性能結果[將在此處發佈](cache-faq.md#azure-cache-for-redis-performance),供您參考。   此外,請注意,SSL/TLS 會增加一些開銷,因此,如果您使用的是傳輸加密,您可能會獲得不同的延遲和/或輸送量。
 
### <a name="redis-benchmark-examples"></a>雷瑞斯-基準示例
**預測試設定**:使用下面列出的延遲和輸送量測試命令所需的數據準備緩存實例。
> redis-Ba.exe-h yourcache.redis.cache.windows.net -您的 Accesskey -t SET -n 10-d 1024 

**測試延遲**:使用 1k 有效負載測試 GET 請求。
> redis-Ba.exe-h yourcache.redis.cache.windows.net - 您的 Accesskey -t GET -d 1024 -P 50 -c 4

**測試輸送量:** 具有 1k 有效負載的管道 GET 請求。
> redis-基準.exe-h yourcache.redis.cache.windows.net - 您的 Accesskey -t GET -n 1000000-d 1024 -P 50 -c 50
