---
title: 診斷在使用 Azure Cosmos DB .NET SDK 時的問題並進行疑難排解
description: 使用用戶端日誌記錄和其他協力廠商工具等功能在使用 .NET SDK 時識別、診斷和排除 Azure Cosmos DB 問題。
author: j82w
ms.service: cosmos-db
ms.date: 03/11/2020
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5f92d98630c6fb875babeb907f92732b0c24bb52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137949"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>診斷在使用 Azure Cosmos DB .NET SDK 時的問題並進行疑難排解
本文介紹在將[.NET SDK](sql-api-sdk-dotnet.md)與 Azure Cosmos DB SQL API 帳戶一起使用時，常見問題、解決方法、診斷步驟和工具。
.NET SDK 提供用戶端邏輯表示形式來訪問 Azure Cosmos DB SQL API。 此文章所說明的工具和方法，可以在您遇到任何問題時提供協助。

## <a name="checklist-for-troubleshooting-issues"></a>故障排除問題檢查表：
在將應用程式移動到生產中之前，請考慮以下檢查表。 使用檢查表將防止您可能會看到幾個常見問題。 您還可以快速診斷出現問題時：

*    使用最新的[SDK](sql-api-sdk-dotnet-standard.md)。 預覽 SDK 不應用於生產。 這將防止觸及已修復的已知問題。
*    檢閱[效能祕訣](performance-tips.md)並遵循建議的做法。 這將有助於防止縮放、延遲和其他性能問題。
*    啟用 SDK 日誌記錄可説明您解決問題。 啟用日誌記錄可能會影響性能，因此最好僅在疑難排解時啟用它。 您可以啟用以下日誌：
    *    使用 Azure 門戶[記錄指標](monitor-accounts.md)。 門戶指標顯示 Azure Cosmos DB 遙測，這有助於確定問題是否與 Azure Cosmos DB 相對應，或者問題是否來自用戶端。
    *    從點操作回應在 V2 SDK 中記錄[診斷字串](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring)或在 V3 SDK 中的[診斷](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics)。
    *    從所有查詢回應中記錄[SQL 查詢指標](sql-api-query-metrics.md) 
    *    按照 SDK[日誌記錄]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)的設置進行操作

查看此文章的[常見問題和因應措施](#common-issues-workarounds)一節。

檢查主動監視的[GitHub 問題部分](https://github.com/Azure/azure-cosmos-dotnet-v2/issues)。 查看您是否有任何含有已提出因應措施的類似問題。 如果您沒有找到解決方案，則提交 GitHub 問題。 您可以為緊急問題打開支援刻度。


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>常見問題和因應措施

### <a name="general-suggestions"></a>一般建議
* 盡可能在 Azure Cosmos DB 帳戶相同的 Azure 區域中運行應用。 
* 由於用戶端電腦上的資源不足，您可能會遇到連接/可用性問題。 我們建議在運行 Azure Cosmos DB 用戶端的節點上監視 CPU 利用率，並在高負載下運行時向上/擴展。

### <a name="check-the-portal-metrics"></a>檢查門戶指標
檢查[門戶指標](monitor-accounts.md)將有助於確定它是否是用戶端問題，還是服務出現問題。 例如，如果指標包含高速率限制請求率（HTTP 狀態碼 429），這意味著請求被限制，則檢查[請求速率過大]部分。 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>請求超時
請求超時通常在使用直接/TCP 時發生，但在閘道模式下可能發生。 這些錯誤是常見的已知原因，以及有關如何解決問題的建議。

* CPU 利用率很高，這將導致延遲和/或請求超時。 客戶可以擴展主機以提供更多資源，也可以將負載分佈在更多電腦上。
* 通訊端/埠可用性可能較低。 在 Azure 中運行時，使用 .NET SDK 的用戶端可能會命中 Azure SNAT （PAT） 埠耗盡。 要減少點擊此問題的機會，請使用 .NET SDK 的最新版本 2.x 或 3.x。 這是建議始終運行最新 SDK 版本的示例。
* 創建多個 DocumentClient 實例可能會導致連接爭用和超時問題。 遵循[性能提示](performance-tips.md)，並在整個過程中使用單個 DocumentClient 實例。
* 使用者有時會看到延遲提升或請求超時，因為它們的集合調配不足，後端限制請求，並且用戶端在內部重試。 檢查[門戶指標](monitor-accounts.md)。
* Azure Cosmos DB 在物理分區之間均勻分佈總預配輸送量。 檢查門戶指標以查看工作負載是否遇到熱[分區鍵](partition-data.md)。 這將導致聚合消耗的輸送量 （RU/s） 似乎處於預配的 RU 之下，但單個分區消耗的輸送量 （RU/s） 將超過預配輸送量。 
* 此外，2.0 SDK 將通道語義添加到直接/TCP 連接。 一個 TCP 連接同時用於多個請求。 這可能導致特定情況下出現兩個問題：
    * 高度併發會導致通道上的爭用。
    * 大型請求或回應可能導致通道上的線頭阻塞，並加劇爭用，即使併發程度相對較低也是如此。
    * 如果以下兩個類別中的任何一個（或者懷疑 CPU 利用率高），則這些可能是緩解措施：
        * 嘗試向上/縮小應用程式。
        * 此外，可以通過[跟蹤攔截器](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md)捕獲 SDK 日誌以獲取更多詳細資訊。

### <a name="high-network-latency"></a><a name="high-network-latency"></a>高網路延遲
通過使用 V2 SDK 中的[診斷字串](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet)或 V3 SDK 中的[診斷](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics)，可以識別高網路延遲。

如果不存在[超時](#request-timeouts)，並且診斷顯示單個請求，其中高延遲在`ResponseTime`和`RequestStartTime`之間的差異上明顯顯示，例如 （>300 毫秒在此示例中）：

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

此延遲可能有多種原因：

* 應用程式未與 Azure Cosmos 資料庫帳戶在同一區域中運行。
* [首選位置](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations)或[應用程式區域](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion)配置不正確，並嘗試連接到應用程式當前運行所在的其他區域。
* 由於流量高，網路介面上可能存在瓶頸。 如果應用程式在 Azure 虛擬機器上運行，則可能存在解決方法：
    * 請考慮使用[啟用了加速網路的虛擬機器](../virtual-network/create-vm-accelerated-networking-powershell.md)。
    * [在現有虛擬機器上啟用加速網路](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)。
    * 考慮使用[更高端的虛擬機器](../virtual-machines/windows/sizes.md)。

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT (PAT) 連接埠耗盡

如果應用部署在 Azure[虛擬機器上，沒有公共 IP 位址](../load-balancer/load-balancer-outbound-connections.md#defaultsnat)，則預設情況下[Azure SNAT 埠](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)建立與 VM 外部任何終結點的連接。 從 VM 到 Azure Cosmos DB 端點所允許的連線數目會受到 [Azure SNAT 設定](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)所限制。 這種情況可能導致連接限制、連接關閉或上述[請求超時](#request-timeouts)。

 僅當 VM 具有私人 IP 位址連接到公共 IP 位址時，才會使用 Azure SNAT 埠。 有兩種解決方法可以避免 Azure SNAT 限制（前提是您已在整個應用程式中使用單個用戶端實例）：

* 將 Azure Cosmos DB 服務端點新增至您的 Azure 虛擬機器虛擬網路。 如需詳細資訊，請參閱 [Azure 虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)。 

    啟用服務端點時，要求不再會從公用 IP 傳送到 Azure Cosmos DB。 改為傳送虛擬網路和子網路身分識別。 如果只允許公用 IP，此變更可能會導致防火牆卸除。 如果您使用防火牆，當您啟用服務端點時，請使用[虛擬網路 ACL](../virtual-network/virtual-networks-acl.md) 將子網路新增至防火牆。
* 將[公共 IP 分配給 Azure VM](../load-balancer/load-balancer-outbound-connections.md#assignilpip)。

### <a name="http-proxy"></a>HTTP Proxy
如果您使用 HTTP Proxy，請確定它可以支援在 SDK `ConnectionPolicy` 中設定的連線數目。
否則就會遇到連線問題。

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>要求率太大
"請求速率太大"或錯誤代碼 429 表示您的請求被限制，因為消耗的輸送量 （RU/s） 已超過[預配輸送量](set-throughput.md)。 SDK 將根據指定的[重試策略](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)自動重試請求。 如果經常出現此故障，請考慮提高集合上的輸送量。 檢查[門戶的指標](use-metrics.md)，查看是否收到 429 個錯誤。 查看[分區金鑰](partitioning-overview.md#choose-partitionkey)，以確保它能夠均勻分佈存儲和請求卷。 

### <a name="slow-query-performance"></a>查詢性能降低
[查詢指標](sql-api-query-metrics.md)將有助於確定查詢大部分時間的花費位置。 從查詢指標中，您可以看到在後端與用戶端上花費了多少。
* 如果後端查詢返回速度很快，並且花費大量時間在用戶端上檢查電腦上的負載。 可能沒有足夠的資源，SDK 正在等待資源可用於處理回應。
* 如果後端查詢速度較慢[，請嘗試優化查詢](optimize-cost-queries.md)並查看當前[索引策略](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[要求率太大]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list


