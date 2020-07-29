---
title: 診斷在使用 Azure Cosmos DB .NET SDK 時的問題並進行疑難排解
description: 使用用戶端記錄和其他協力廠商工具等功能，來識別、診斷及疑難排解使用 .NET SDK 時的 Azure Cosmos DB 問題。
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 06/16/2020
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 1dd6bdc66146eb7dfe155e7d1091eee5cca450a0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290913"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>診斷在使用 Azure Cosmos DB .NET SDK 時的問題並進行疑難排解

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
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
檢查[入口網站計量](monitor-accounts.md)有助於判斷其是否為用戶端問題，或服務是否有問題。 例如，如果計量包含高比率的速率限制要求（HTTP 狀態碼429），這表示要求正在進行節流處理，請檢查[要求率太大](troubleshoot-request-rate-too-large.md)一節。 

## <a name="common-error-status-codes"></a>常見錯誤狀態碼<a id="error-codes"></a>

| 狀態碼 | 說明 | 
|----------|-------------|
| 400 | 不正確的要求（取決於錯誤訊息）| 
| 401 | [未獲授權](troubleshoot-unauthorized.md) | 
| 404 | [找不到資源](troubleshoot-not-found.md) |
| 408 | [要求已超時](troubleshoot-dot-net-sdk-request-timeout.md) |
| 409 | 當現有資源已經使用為寫入作業提供的資源識別碼時，就會發生衝突。 使用資源的另一個識別碼來解決此問題，因為識別碼在具有相同分割區索引鍵值的所有檔中必須是唯一的。 |
| 410 | 已消失的例外狀況（不應違反 SLA 的暫時性失敗） |
| 412 | 前置條件失敗是作業指定了與伺服器上可用版本不同的 eTag。 它是開放式平行存取錯誤。 讀取最新版的資源及更新要求上的 eTag 之後，重試要求。
| 413 | [要求實體太大](concepts-limits.md#per-item-limits) |
| 429 | [要求太多](troubleshoot-request-rate-too-large.md) |
| 449 | 只有在寫入作業上才會發生的暫時性錯誤，而且可以安全地重試 |
| 500 | 作業失敗，因為發生未預期的服務錯誤。 請連絡支援人員。 請參閱提出[Azure 支援問題](https://aka.ms/azure-support)。 |
| 503 | [服務無法使用](troubleshoot-service-unavailable.md) | 

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT (PAT) 連接埠耗盡

如果您的應用程式部署在[Azure 虛擬機器且沒有公用 IP 位址](../load-balancer/load-balancer-outbound-connections.md)，則[azure SNAT 埠](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)預設會建立與 VM 外任何端點的連線。 從 VM 到 Azure Cosmos DB 端點所允許的連線數目會受到 [Azure SNAT 設定](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)所限制。 這種情況可能會導致連線節流、連接關閉或上述的[要求超時](troubleshoot-dot-net-sdk-request-timeout.md)。

 只有當您的 VM 具有私人 IP 位址正在連線到公用 IP 位址時，才會使用 Azure SNAT 埠。 有兩種因應措施可避免 Azure SNAT 限制（前提是您已在整個應用程式中使用單一用戶端實例）：

* 將 Azure Cosmos DB 服務端點新增至您的 Azure 虛擬機器虛擬網路。 如需詳細資訊，請參閱 [Azure 虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)。 

    啟用服務端點時，要求不再會從公用 IP 傳送到 Azure Cosmos DB。 改為傳送虛擬網路和子網路身分識別。 如果只允許公用 IP，此變更可能會導致防火牆卸除。 如果您使用防火牆，當您啟用服務端點時，請使用[虛擬網路 ACL](../virtual-network/virtual-networks-acl.md) 將子網路新增至防火牆。
* 將[公用 IP 指派給您的 AZURE VM](../load-balancer/troubleshoot-outbound-connection.md#assignilpip)。

### <a name="high-network-latency"></a><a name="high-network-latency"></a>高網路延遲
您可以使用 V2 SDK 中的[診斷字串](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet)或 V3 sdk 中的[診斷](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics)來識別高網路延遲。

如果沒有任何[超時](troubleshoot-dot-net-sdk-request-timeout.md)，而且診斷顯示單一要求，其中高延遲會明顯呈現與之間的差異 `ResponseTime` `RequestStartTime` ，就像這樣（在此範例中 >300 毫秒）：

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

### <a name="slow-query-performance"></a>查詢效能緩慢
[查詢計量](sql-api-query-metrics.md)有助於判斷查詢在大部分時間內的支出。 從查詢計量中，您可以看到它在後端與用戶端上花費了多少時間。
* 如果後端查詢會快速傳回，而在用戶端上花了很長的時間，請檢查電腦上的負載。 可能是資源不足，而且 SDK 正在等候資源可用來處理回應。
* 如果後端查詢速度緩慢，請嘗試[優化查詢](optimize-cost-queries.md)，並查看目前的[編制索引原則](index-overview.md) 

## <a name="next-steps"></a>後續步驟

* 瞭解[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)和[.Net V2](performance-tips.md)的效能指導方針
* 瞭解[以 Reactor 為基礎的 JAVA SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md)

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
