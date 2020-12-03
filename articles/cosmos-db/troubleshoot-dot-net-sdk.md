---
title: 診斷在使用 Azure Cosmos DB .NET SDK 時的問題並進行疑難排解
description: 使用用戶端記錄和其他協力廠商工具等功能，來識別、診斷和疑難排解使用 .NET SDK 時的 Azure Cosmos DB 問題。
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 09/12/2020
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 68d9a64e388d24f2067f47282945b9561d807535
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96545922"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>診斷在使用 Azure Cosmos DB .NET SDK 時的問題並進行疑難排解
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [非同步 Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

本文涵蓋搭配 Azure Cosmos DB SQL API 帳戶使用 [.NET SDK](sql-api-sdk-dotnet.md) 時的常見問題、因應措施、診斷步驟和工具。
.NET SDK 提供用戶端邏輯標記法，以存取 Azure Cosmos DB SQL API。 此文章所說明的工具和方法，可以在您遇到任何問題時提供協助。

## <a name="checklist-for-troubleshooting-issues"></a>疑難排解問題的檢查清單

將您的應用程式移至生產環境之前，請考慮下列檢查清單。 使用檢查清單會防止您可能看到的幾個常見問題。 您也可以在問題發生時快速診斷：

*    使用最新的 [SDK](sql-api-sdk-dotnet-standard.md)。 預覽 Sdk 不應該用於生產環境。 這會防止遇到已修正的已知問題。
*    檢閱[效能祕訣](performance-tips.md)並遵循建議的做法。 這將有助於防止調整、延遲和其他效能問題。
*    啟用 SDK 記錄以協助您針對問題進行疑難排解。 啟用記錄可能會影響效能，因此最好只有在針對問題進行疑難排解時才啟用。 您可以啟用下列記錄：
*    使用 Azure 入口網站[記錄計量](./monitor-cosmos-db.md)。 入口網站度量會顯示 Azure Cosmos DB 遙測資料，這有助於判斷問題是否對應到 Azure Cosmos DB 或來自用戶端。
*    從點作業回應記錄 V2 SDK 中的 [診斷字串](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) 或 V3 sdk 中的 [診斷](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) 。
*    從所有查詢回應記錄[SQL 查詢計量](sql-api-query-metrics.md) 
*    遵循[SDK 記錄]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)的設定

查看此文章的[常見問題和因應措施](#common-issues-workarounds)一節。

查看主動監視的 [GitHub 問題區段](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) 。 查看您是否有任何含有已提出因應措施的類似問題。 如果您找不到解決方案，請提出 GitHub 問題。 您可以開啟緊急問題的支援滴答。


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>常見問題和因應措施

### <a name="general-suggestions"></a>一般建議
* 盡可能在與 Azure Cosmos DB 帳戶相同的 Azure 區域中執行您的應用程式。 
* 由於用戶端電腦上的資源不足，您可能會遇到連線能力/可用性問題。 建議您在執行 Azure Cosmos DB 用戶端的節點上監視 CPU 使用率，並在高負載執行時相應增加/相應放大。

### <a name="check-the-portal-metrics"></a>檢查入口網站計量
檢查 [入口網站度量](./monitor-cosmos-db.md) 將有助於判斷它是否為用戶端問題，或是服務是否有問題。 例如，如果計量包含高比率的速率限制要求 (HTTP 狀態碼 429) 這表示要求正在進行節流處理，然後檢查 [要求率太大](troubleshoot-request-rate-too-large.md) 的區段。 

## <a name="common-error-status-codes"></a>常見的錯誤狀態碼 <a id="error-codes"></a>

| 狀態碼 | 說明 | 
|----------|-------------|
| 400 | 錯誤的要求 (取決於錯誤訊息) | 
| 401 | [未授權](troubleshoot-unauthorized.md) | 
| 404 | [找不到資源](troubleshoot-not-found.md) |
| 408 | [要求超時](troubleshoot-dot-net-sdk-request-timeout.md) |
| 409 | 當現有資源採用寫入作業的資源識別碼時，就會發生衝突失敗。 使用資源的另一個識別碼來解決此問題，因為識別碼在具有相同資料分割索引鍵值的所有檔中必須是唯一的。 |
| 410 |  (暫時性失敗而不應違反 SLA 的例外狀況)  |
| 412 | 前置條件失敗是指作業指定了與伺服器上可用版本不同的 eTag。 它是開放式平行存取錯誤。 讀取最新版的資源及更新要求上的 eTag 之後，重試要求。
| 413 | [要求實體太大](concepts-limits.md#per-item-limits) |
| 429 | [要求太多](troubleshoot-request-rate-too-large.md) |
| 449 | 只有寫入作業才會發生的暫時性錯誤，而且可以安全地重試 |
| 500 | 因為發生未預期的服務錯誤，所以作業失敗。 請連絡支援人員。 請參閱歸檔 [Azure 支援問題](https://aka.ms/azure-support)。 |
| 503 | [服務無法使用](troubleshoot-service-unavailable.md) | 

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT (PAT) 連接埠耗盡

如果您的應用程式部署在 [沒有公用 IP 位址的 Azure 虛擬機器](../load-balancer/load-balancer-outbound-connections.md)上，則 [azure SNAT 埠](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) 預設會建立與您 VM 以外任何端點的連線。 從 VM 到 Azure Cosmos DB 端點所允許的連線數目會受到 [Azure SNAT 設定](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)所限制。 這種情況可能會導致連線節流、連接關閉，或上述的 [要求超時](troubleshoot-dot-net-sdk-request-timeout.md)。

 只有當您的 VM 有私人 IP 位址連接到公用 IP 位址時，才會使用 Azure SNAT 埠。 有兩種因應措施可避免 Azure SNAT 限制 (前提是您已在整個應用程式) 中使用單一用戶端實例：

* 將 Azure Cosmos DB 服務端點新增至您的 Azure 虛擬機器虛擬網路。 如需詳細資訊，請參閱 [Azure 虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)。 

    啟用服務端點時，要求不再會從公用 IP 傳送到 Azure Cosmos DB。 改為傳送虛擬網路和子網路身分識別。 如果只允許公用 IP，此變更可能會導致防火牆卸除。 如果您使用防火牆，當您啟用服務端點時，請使用[虛擬網路 ACL](/previous-versions/azure/virtual-network/virtual-networks-acl) 將子網路新增至防火牆。
* 將 [公用 IP 指派給您的 AZURE VM](../load-balancer/troubleshoot-outbound-connection.md#assignilpip)。

### <a name="high-network-latency"></a><a name="high-network-latency"></a>高網路延遲
您可以使用 V2 SDK 中的 [診斷字串](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?preserve-view=true&view=azure-dotnet) 或 V3 sdk 中的 [診斷](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?preserve-view=true&view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) ，來識別高網路延遲。

如果沒有任何 [超時](troubleshoot-dot-net-sdk-request-timeout.md) 存在，而且診斷顯示單一要求，其中高延遲在和之間的差異很明顯 `ResponseTime` ，例如 `RequestStartTime` ( # B0 300 毫秒的範例中) ：

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

此延遲可能有多個原因：

* 您的應用程式未在與 Azure Cosmos DB 帳戶相同的區域中執行。
* 您的 [PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) 或 [ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) 設定不正確，且正在嘗試連接到您的應用程式目前執行所在的不同區域。
* 網路介面上可能會有瓶頸，因為有高流量。 如果應用程式是在 Azure 虛擬機器上執行，則有可能的因應措施：
    * 請考慮使用 [已啟用加速網路的虛擬機器](../virtual-network/create-vm-accelerated-networking-powershell.md)。
    * [在現有的虛擬機器上啟用加速網路](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)。
    * 請考慮使用 [較高的終端虛擬機器](../virtual-machines/sizes.md)。

### <a name="common-query-issues"></a>常見的查詢問題

[查詢計量](sql-api-query-metrics.md)有助於判斷查詢在大部分時間內的花費。 您可以從查詢計量查看在後端與用戶端之間花費多少時間。 深入瞭解 [查詢效能的疑難排解](troubleshoot-query-performance.md)。

* 如果後端查詢快速傳回，而且在用戶端上花費很長的時間，請檢查機器上的負載。 資源可能沒有足夠的資源，且 SDK 正在等候資源可用來處理回應。
* 如果後端查詢速度很慢，請嘗試 [優化查詢](troubleshoot-query-performance.md) 並查看目前的 [索引編制原則](index-overview.md)

    > [!NOTE]
    > 為了改善效能，我們建議您使用 Windows 64 位元的主機處理序。 SQL SDK 包含原生 ServiceInterop.dll，可在本機剖析和最佳化查詢。 ServiceInterop.dll 只能在 Windows x64 平台上受到支援。 針對 Linux 和其他不支援 ServiceInterop.dll 的平台，則會對閘道進行額外的網路呼叫，以取得最佳化的查詢。

如果您遇到下列錯誤： `Unable to load DLL 'Microsoft.Azure.Cosmos.ServiceInterop.dll' or one of its dependencies:` 而且使用的是 windows，則應該升級至最新的 windows 版本。

## <a name="next-steps"></a>後續步驟

* 瞭解[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)和[.Net V2](performance-tips.md)的效能指導方針
* 瞭解[以 Reactor 為基礎的 JAVA SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md)

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list