---
title: 診斷在使用 Azure Cosmos DB .NET SDK 時的問題並進行疑難排解
description: 使用用戶端記錄和其他協力廠商工具等功能，來識別、診斷及疑難排解使用 .NET SDK 時的 Azure Cosmos DB 問題。
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/06/2020
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e389df7cfe0e228030d2d0f730fc5e671ad4c052
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927627"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>診斷在使用 Azure Cosmos DB .NET SDK 時的問題並進行疑難排解

> [!div class="op_single_selector"]
> * [JAVA SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [非同步 Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

本文涵蓋使用[.NET SDK](sql-api-sdk-dotnet.md)搭配 AZURE COSMOS DB SQL API 帳戶時的常見問題、因應措施、診斷步驟和工具。
.NET SDK 提供用戶端邏輯標記法來存取 Azure Cosmos DB SQL API。 此文章所說明的工具和方法，可以在您遇到任何問題時提供協助。

## <a name="checklist-for-troubleshooting-issues"></a>疑難排解問題的檢查清單
將應用程式移至生產環境之前，請考慮下列檢查清單。 使用檢查清單可防止您可能會看到的幾個常見問題。 您也可以在發生問題時快速診斷：

*    使用最新的[SDK](sql-api-sdk-dotnet-standard.md)。 預覽 Sdk 不應用於生產環境。 這將導致無法達到已修正的已知問題。
*    檢閱[效能祕訣](performance-tips.md)並遵循建議的做法。 這將有助於防止調整、延遲和其他效能問題。
*    啟用 SDK 記錄以協助您針對問題進行疑難排解。 啟用記錄可能會影響效能，所以最好只在針對問題進行疑難排解時才啟用。 您可以啟用下列記錄：
    *    使用 Azure 入口網站來[記錄計量](monitor-accounts.md)。 入口網站計量會顯示 Azure Cosmos DB 的遙測，這有助於判斷問題是否對應到 Azure Cosmos DB 或是否來自用戶端。
    *    從點作業回應中，記錄 V2 SDK 中的[診斷字串](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring)或 V3 sdk 中的[診斷](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics)。
    *    記錄所有查詢回應中的[SQL 查詢計量](sql-api-query-metrics.md) 
    *    遵循適用于[SDK 記錄]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)的設定

查看此文章的[常見問題和因應措施](#common-issues-workarounds)一節。

查看已主動監視的[GitHub 問題區段](https://github.com/Azure/azure-cosmos-dotnet-v2/issues)。 查看您是否有任何含有已提出因應措施的類似問題。 如果您找不到解決方案，請提出 GitHub 問題。 您可以開啟緊急問題的支援滴答。


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>常見問題和因應措施

### <a name="general-suggestions"></a>一般建議
* 請盡可能在您的 Azure Cosmos DB 帳戶所在的相同 Azure 區域中執行您的應用程式。 
* 由於用戶端電腦上的資源不足，您可能會遇到連線/可用性問題。 建議您在執行 Azure Cosmos DB 用戶端的節點上監視 CPU 使用率，並相應增加/相應放大（如果它們在高負載狀態下執行）。

### <a name="check-the-portal-metrics"></a>檢查入口網站計量
檢查[入口網站計量](monitor-accounts.md)有助於判斷其是否為用戶端問題，或服務是否有問題。 例如，如果計量包含高比率的速率限制要求（HTTP 狀態碼429），這表示要求正在進行節流處理，請檢查[要求率太大]一節。 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>要求超時
RequestTimeout 通常會在使用 Direct/TCP 時發生，但在閘道模式中可能會發生。 這些錯誤是常見的已知原因，以及如何修正問題的建議。

* CPU 使用率偏高，這會造成延遲和/或要求超時。 客戶可以相應增加主機電腦，以提供更多資源，或將負載分散到更多電腦上。
* 通訊端/埠可用性可能較低。 在 Azure 中執行時，使用 .NET SDK 的用戶端可以達到 Azure SNAT （PAT）埠耗盡的狀態。 若要減少遇到此問題的機會，請使用最新版本2.x 或3.x 的 .NET SDK。 這是建議一律執行最新 SDK 版本的範例。
* 建立多個 DocumentClient 實例可能會導致連接爭用和超時問題。 請遵循[效能秘訣](performance-tips.md)，並在整個進程中使用單一 DocumentClient 實例。
* 使用者有時會看到較高的延遲或要求超時，因為其集合已布建能力不佳、後端節流要求，而用戶端會在內部重試。 檢查[入口網站計量](monitor-accounts.md)。
* Azure Cosmos DB 會將整體布建的輸送量平均分散到實體分割區。 檢查入口網站計量，以查看工作負載是否遇到熱[分割](partition-data.md)區索引鍵。 這會導致已耗用的匯總輸送量（RU/秒）顯示在已布建的 ru 底下，但使用的單一分割區輸送量（RU/秒）將會超過布建的輸送量。 
* 此外，2.0 SDK 會將通道語義新增至 direct/TCP 連接。 一個 TCP 連線會同時用於多個要求。 在特定情況下，這可能會導致兩個問題：
    * 高程度的平行存取可能會導致通道上的爭用。
    * 大型要求或回應可能會導致通道上的標頭封鎖，以及惡化爭用，即使是相對較低的並行程度也一樣。
    * 如果案例落在這兩個類別中的任何一種（或懷疑有高 CPU 使用率），這些都是可能的緩和措施：
        * 嘗試相應增加/相應放大應用程式。
        * 此外，您可以透過[追蹤](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md)接聽程式來捕獲 SDK 記錄檔，以取得更多詳細資料。

### <a name="high-network-latency"></a><a name="high-network-latency"></a>高網路延遲
您可以使用 V2 SDK 中的[診斷字串](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet)或 V3 sdk 中的[診斷](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics)來識別高網路延遲。

如果沒有任何[超時](#request-timeouts)，而且診斷顯示單一要求，其中高延遲會明顯呈現與`ResponseTime` `RequestStartTime`之間的差異，就像這樣（在此範例中 >300 毫秒）：

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

此延遲可能會有多個原因：

* 您的應用程式未在與您 Azure Cosmos DB 帳戶相同的區域中執行。
* 您的[PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations)或[ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion)設定不正確，且正嘗試連接到您的應用程式目前執行所在的不同區域。
* 網路介面上的瓶頸可能是因為流量過高。 如果應用程式是在 Azure 虛擬機器上執行，則有可能的因應措施：
    * 請考慮使用[已啟用加速網路的虛擬機器](../virtual-network/create-vm-accelerated-networking-powershell.md)。
    * [在現有的虛擬機器上啟用加速網路](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)。
    * 請考慮使用[較高的終端虛擬機器](../virtual-machines/windows/sizes.md)。

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT (PAT) 連接埠耗盡

如果您的應用程式部署在[Azure 虛擬機器且沒有公用 IP 位址](../load-balancer/load-balancer-outbound-connections.md#defaultsnat)，則[azure SNAT 埠](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)預設會建立與 VM 外任何端點的連線。 從 VM 到 Azure Cosmos DB 端點所允許的連線數目會受到 [Azure SNAT 設定](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)所限制。 這種情況可能會導致連線節流、連接關閉或上述的[要求超時](#request-timeouts)。

 只有當您的 VM 具有私人 IP 位址正在連線到公用 IP 位址時，才會使用 Azure SNAT 埠。 有兩種因應措施可避免 Azure SNAT 限制（前提是您已在整個應用程式中使用單一用戶端實例）：

* 將 Azure Cosmos DB 服務端點新增至您的 Azure 虛擬機器虛擬網路。 如需詳細資訊，請參閱 [Azure 虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)。 

    啟用服務端點時，要求不再會從公用 IP 傳送到 Azure Cosmos DB。 改為傳送虛擬網路和子網路身分識別。 如果只允許公用 IP，此變更可能會導致防火牆卸除。 如果您使用防火牆，當您啟用服務端點時，請使用[虛擬網路 ACL](../virtual-network/virtual-networks-acl.md) 將子網路新增至防火牆。
* 將[公用 IP 指派給您的 AZURE VM](../load-balancer/load-balancer-outbound-connections.md#assignilpip)。

### <a name="http-proxy"></a>HTTP Proxy
如果您使用 HTTP Proxy，請確定它可以支援在 SDK `ConnectionPolicy` 中設定的連線數目。
否則就會遇到連線問題。

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>要求率太大
「要求速率太大」或錯誤碼429表示您的要求正在進行節流，因為已使用的輸送量（RU/秒）已超過布[建的輸送量](set-throughput.md)。 SDK 會根據指定的[重試原則](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)自動重試要求。 如果您經常遇到此失敗，請考慮增加集合的輸送量。 檢查[入口網站的計量](use-metrics.md)，以查看您是否收到429錯誤。 請檢查您的資料[分割索引鍵](partitioning-overview.md#choose-partitionkey)，以確保它會產生儲存體和要求磁片區的平均分佈。 

### <a name="slow-query-performance"></a>查詢效能緩慢
[查詢計量](sql-api-query-metrics.md)有助於判斷查詢在大部分時間內的支出。 從查詢計量中，您可以看到它在後端與用戶端上花費了多少時間。
* 如果後端查詢會快速傳回，而在用戶端上花了很長的時間，請檢查電腦上的負載。 可能是資源不足，而且 SDK 正在等候資源可用來處理回應。
* 如果後端查詢速度緩慢，請嘗試[優化查詢](optimize-cost-queries.md)，並查看目前的[編制索引原則](index-overview.md) 

### <a name="http-401-the-mac-signature-found-in-the-http-request-is-not-the-same-as-the-computed-signature"></a>HTTP 401：在 HTTP 要求中找到的 MAC 簽章與計算的簽章不同
如果您收到下列401錯誤訊息：「在 HTTP 要求中找到的 MAC 簽章與計算的簽章不同」。 這可能是由下列案例所造成。

1. 金鑰已輪替，且未遵循[最佳作法](secure-access-to-data.md#key-rotation)。 這通常是這種情況。 視 Cosmos DB 帳戶大小而定，Cosmos DB 帳戶金鑰輪替可能需要幾秒鐘到數秒的時間。
   1. 401在金鑰輪替後不久就會看到 MAC 簽章，而且最終會在沒有任何變更的情況下停止。 
2. 應用程式上的金鑰設定錯誤，因此金鑰與帳戶不相符。
   1. 401 MAC 簽章問題會一致，並會在所有呼叫時發生
3. 建立容器時有競爭條件。 應用程式實例在完成容器建立之前，嘗試存取容器。 當應用程式正在執行時，最常見的案例是在應用程式執行時，刪除並重新建立具有相同名稱的容器。 SDK 會嘗試使用新的容器，但容器建立仍在進行中，因此沒有金鑰。
   1. 401建立容器之後，很快就會出現 MAC 簽章問題，而且只有在完成容器建立後才會發生。
 
 ### <a name="http-error-400-the-size-of-the-request-headers-is-too-long"></a>HTTP 錯誤400。 要求標頭的大小太長。
 標頭的大小已增加至大，且超過允許的大小上限。 建議您一律使用最新的 SDK。 請務必使用[至少版本 3.x](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/changelog.md)或2.x，這會在例外狀況[訊息中加入](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md)標頭大小追蹤。

都會
 1. 會話權杖成長過大。 會話權杖會隨著容器中增加的分割區數目成長。
 2. 接續 token 已成長為大型。 不同的查詢會有不同的接續 token 大小。
 3. 這是由會話權杖和接續 token 的組合所造成。

解決方案：
   1. 遵循[效能秘訣](performance-tips.md)，並將應用程式轉換成 DIRECT + TCP 連線模式。 Direct + TCP 沒有標頭大小限制，例如 HTTP 會避免這個問題。
   2. 如果會話權杖是原因，則暫時的緩和措施是重新開機應用程式。 重新開機應用程式實例將會重設會話權杖。 如果例外狀況在重新開機後停止，則會確認會話權杖是原因。 最後，它會變回會造成例外狀況的大小。
   3. 如果應用程式無法轉換成 Direct + TCP，而會話權杖是原因，則可以藉由變更用戶端[一致性層級](consistency-levels.md)來進行緩和。 會話權杖僅用於會話一致性，這是 Cosmos DB 的預設值。 任何其他一致性層級都不會使用會話權杖。 
   4. 如果應用程式無法轉換成 Direct + TCP，而接續 token 是原因，請嘗試設定 ResponseContinuationTokenLimitInKb 選項。 您可以在 FeedOptions for v2 或 v3 中的 QueryRequestOptions 找到選項。

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[要求率太大]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
