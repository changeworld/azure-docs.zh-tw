---
title: 安全地存取索引子資源
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋中，索引子的 Azure 資料存取網路層級安全性選項的概念總覽。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 85446847e8ad77bc83eea657ab17268839e0b231
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949814"
---
# <a name="indexer-access-to-data-sources-using-azure-network-security-features"></a>使用 Azure 網路安全性功能來存取資料來源的索引子

Azure 認知搜尋索引子可在執行期間對各種 Azure 資源進行輸出呼叫。 本文說明當這些資源受到 IP 防火牆、私人端點和其他網路層級安全性機制的保護時，對資源的索引子存取的概念。 在一般執行中，索引子可能會存取的可能資源類型列于下表。

| 資源 | 在索引子執行中的用途 |
| --- | --- |
| Azure 儲存體 (blob、資料表、ADLS Gen 2)  | 資料來源 |
| Azure 儲存體 (blob、資料表)  | 技能集 (快取擴充的檔，並儲存知識存放區投影)  |
| Azure Cosmos DB (各種 Api)  | 資料來源 |
| Azure SQL Database | 資料來源 |
| Azure IaaS Vm 上的 SQL server | 資料來源 |
| SQL 受控執行個體 | 資料來源 |
| Azure Functions | 自訂 web api 技能的主機 |
| 認知服務 | 附加至技能集，用來將擴充帳單超過20份免費檔限制 |

> [!NOTE]
> 附加至技能集的認知服務資源會根據所執行的擴充並寫入搜尋索引，用於計費。 它不會用來存取認知服務 API。 從索引子的擴充管線存取認知服務 API 會透過安全的通道進行，其中資料會在傳輸過程中高度加密，而且永遠不會儲存。

客戶可以透過 Azure 所提供的數個網路隔離機制來保護這些資源。 除了認知服務資源的例外狀況之外，索引子也有限制存取所有其他資源的能力，即使它們是下表所述的網路隔離。

| 資源 | IP 限制 | 私人端點 |
| --- | --- | ---- |
| Azure 儲存體 (blob、資料表、ADLS Gen 2)  | 只有在儲存體帳戶和搜尋服務位於不同區域時才支援 | 支援 |
| Azure Cosmos DB-SQL API | 支援 | 支援 |
| Azure Cosmos DB-Cassandra、Mongo 和 Gremlin API | 支援 | 不支援 |
| Azure SQL Database | 支援 | 支援 |
| Azure IaaS Vm 上的 SQL server | 支援 | N/A |
| SQL 受控執行個體 | 支援 | N/A |
| Azure Functions | 支援 | 支援，僅適用于 Azure 函式的特定 Sku |

> [!NOTE]
> 除了上面所列的選項之外，針對受網路保護的 Azure 儲存體帳戶，客戶可以利用 Azure 認知搜尋是 [受信任的 Microsoft 服務](../storage/common/storage-network-security.md#trusted-microsoft-services)的事實。 這表示，如果在儲存體帳戶上啟用適當的角色型存取控制，則特定的搜尋服務可以略過儲存體帳戶的虛擬網路或 IP 限制，並且可以存取儲存體帳戶中的資料。 詳細資料可在「操作 [指南](search-indexer-howto-access-trusted-service-exception.md)」中取得。 如果儲存體帳戶或搜尋服務無法移至不同的區域，則可以使用此選項來取代 IP 限制路由。

選擇索引子應該使用的安全存取機制時，請考慮下列限制：

- 所有 Azure 資源都不支援[服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)。
- 搜尋服務無法布建到特定的虛擬網路中-Azure 認知搜尋不會提供這項功能。
- 當索引子利用 (輸出) 私人端點來存取資源時，可能會收取額外的 [私人連結費用](https://azure.microsoft.com/pricing/details/search/) 。

## <a name="indexer-execution-environment"></a>索引子執行環境

Azure 認知搜尋索引子可以有效地從資料來源提取內容、將擴充新增至已解壓縮的內容，並選擇性地產生投影，然後再將結果寫入搜尋索引。 視指派給索引子的職責數目而定，它可以在下列兩種環境中執行：

- 特定搜尋服務專用的環境。 在這類環境中執行的索引子會與其他工作負載共用資源， (例如其他客戶起始的索引編制或查詢工作負載) 。 通常，只有不需要太多資源的索引子 (例如，請勿使用在此環境中執行的技能集) 。
- 多租使用者環境，裝載資源耗用的索引子，例如具有技能集的索引子。 耗用資源的資源會在此環境中執行，以提供最佳效能，同時確保搜尋服務資源適用于其他工作負載。 此多租使用者環境是由 Azure 認知搜尋管理及保護，客戶無須額外付費。

針對任何指定的索引子執行，Azure 認知搜尋會決定執行索引子的最佳環境。

## <a name="granting-access-to-indexer-ip-ranges"></a>授與索引子 IP 範圍的存取權

如果您的索引子嘗試存取的資源僅限於一組特定的 IP 範圍，您就必須展開此集合，以包含可從中產生索引子要求的可能 IP 範圍。 如上所述，有兩種可能的環境可讓索引子執行，以及從中產生存取要求。 您將需要新增 __兩個__ 環境的 IP 位址，才能讓索引子存取工作。

- 若要取得搜尋服務特定私人環境的 IP 位址，請 `nslookup` (或 `ping`) 搜尋服務 (FQDN) 的完整功能變數名稱。 例如，公用雲端中的搜尋服務 FQDN 就是 `<service-name>.search.windows.net` 。 這項資訊可在 Azure 入口網站上取得。
- 多租使用者環境的 IP 位址可透過 `AzureCognitiveSearch` 服務標記取得。 [Azure 服務](../virtual-network/service-tags-overview.md) 標籤具有每個服務的已發佈 IP 位址範圍，這可透過 [探索 API (預覽版) ](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) 或可下載的 [JSON](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)檔案中取得。 無論是哪一種情況，IP 範圍都會依區域細分-您只能挑選針對您的搜尋服務布建所在區域指派的 IP 範圍。

針對某些資料來源，您可以直接使用服務標籤，而不是列舉 IP 範圍清單 (搜尋服務的 IP 位址仍然必須明確地使用) 。 這些資料來源會藉由設定 [網路安全性群組規則](../virtual-network/network-security-groups-overview.md)來限制存取，而這種規則原本就支援新增服務標籤，而不像 IP 規則（例如 Azure 儲存體、CosmosDB、Azure SQL 等）。 `AzureCognitiveSearch` 除了搜尋服務 IP 位址，也支援直接利用服務標記的功能：

- [IaaS Vm 上的 SQL server](./search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md#restrict-access-to-the-azure-cognitive-search)

- [SQL 受控實例](./search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md#verify-nsg-rules)

詳細資料將在作法 [指南](search-indexer-howto-access-ip-restricted.md)中說明。

## <a name="granting-access-via-private-endpoints"></a>透過私人端點授與存取權

索引子可以利用 [私人端點](../private-link/private-endpoint-overview.md) 來存取資源，也就是已鎖定的存取權，可選取虛擬網路或未啟用任何公用存取。
這項功能僅適用于付費服務，其所建立的私人端點數目有限制。 有關限制的詳細資料記載于 [Azure 搜尋服務限制頁面](search-limits-quotas-capacity.md)中。

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>步驟1：建立安全資源的私人端點

客戶應該呼叫搜尋管理作業、 [建立或更新 *共用的私人連結資源* API](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) ，才能建立其安全資源的私人端點連線 (例如儲存體帳戶) 。 超過此 (輸出) 私人端點連線的流量，只會源自搜尋服務特定「私用」索引子執行環境中的虛擬網路。

Azure 認知搜尋會驗證此 API 的呼叫端是否有權核准對安全資源的私人端點連接要求。 例如，如果您要求私人端點連線到您沒有存取權的儲存體帳戶，此呼叫將會遭到拒絕。

### <a name="step-2-approve-the-private-endpoint-connection"></a>步驟2：核准私人端點連接

當建立共用私人連結資源的 (非同步) 作業完成時，將會以「暫止」狀態建立私人端點連接。 尚未透過連接流動流量。
接著，客戶應該會在其安全資源上找到此要求並「核准」。 一般而言，這可以透過入口網站或透過 [REST API](/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection)完成。

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>步驟3：在「私用」環境中強制執行索引子

核准的私人端點允許來自搜尋服務的撥出電話具有某種形式的網路層級存取限制的資源 (例如，設定為僅從特定虛擬網路存取的儲存體帳戶資料來源) 成功。
這表示任何能夠透過私人端點連接至這類資料來源的索引子都將成功。
如果私人端點未獲核准，或索引子沒有利用私用端點連接，則索引子執行將會在中結束 `transientFailure` 。

若要讓索引子可透過私人端點連接來存取資源，必須將索引子的設定為，以 `executionEnvironment` `"Private"` 確保所有索引子執行都能利用私人端點。 這是因為私人端點是在私人搜尋服務特定環境內布建的。

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

這些步驟會在操作 [指南](search-indexer-howto-access-private.md)中更詳細地說明。
一旦您有資源的已核准私用端點，設定為 *私* 用的索引子會嘗試透過私人端點連接取得存取權。

### <a name="limits"></a>限制

為了確保搜尋服務的最佳效能和穩定性，搜尋服務 SKU) 會將限制強加 (下列維度：

- 可以設定為 *私*用的索引子類型。
- 可以建立的共用私用連結資源的數目。
- 可以建立共用私用連結資源的相異資源類型數目。

這些限制記載于 [服務限制](search-limits-quotas-capacity.md)中。