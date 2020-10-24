---
title: 受保護資源的索引子存取
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋中，索引子的 Azure 資料存取網路層級安全性選項的概念總覽。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: bcb6e91bba367363385214806077146b1a24fe7b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92503482"
---
# <a name="indexer-access-to-content-protected-by-azure-network-security-features-azure-cognitive-search"></a>索引子存取受 Azure 網路安全性功能保護的內容 (Azure 認知搜尋) 

Azure 認知搜尋索引子可在執行期間對各種 Azure 資源進行輸出呼叫。 本文說明索引子存取受 IP 防火牆、私人端點或其他 Azure 網路層級安全性機制所保護之內容的基本概念。 在兩種情況下，索引子會進行輸出呼叫：在編制索引期間連接到資料來源，並透過技能集連接至封裝程式碼。 下表列出索引子在一般執行中可能存取的所有可能資源類型清單。

| 資源 | 在索引子執行中的用途 |
| --- | --- |
| Azure 儲存體 (blob、資料表、ADLS Gen 2)  | 資料來源 |
| Azure 儲存體 (blob、資料表)  | 技能集 (快取擴充的檔，並儲存知識存放區投影)  |
| Azure Cosmos DB (各種 Api)  | 資料來源 |
| Azure SQL Database | 資料來源 |
| Azure 虛擬機器上的 SQL Server | 資料來源 |
| SQL 受控執行個體 | 資料來源 |
| Azure Functions | 自訂 web api 技能的主機 |
| 認知服務 | 附加至技能集，用來將擴充帳單超過20份免費檔限制 |

> [!NOTE]
> 附加至技能集的認知服務資源會根據所執行的擴充並寫入搜尋索引，用於計費。 它不會用來存取認知服務 API。 從索引子的擴充管線存取認知服務 API 會透過內部安全的通道進行，其中資料會在傳輸過程中高度加密，而且永遠不會儲存。

客戶可以透過 Azure 所提供的數個網路隔離機制來保護這些資源。 除了認知服務資源之外，索引子也無法存取所有其他資源，即使它們是網路隔離的，如下表所述。

| 資源 | IP 限制 | 私人端點 |
| --- | --- | ---- |
| Azure 儲存體 (blob、資料表、ADLS Gen 2)  | 只有在儲存體帳戶和搜尋服務位於不同區域時才支援 | 支援 |
| Azure Cosmos DB-SQL API | 支援 | 支援 |
| Azure Cosmos DB-Cassandra、Mongo 和 Gremlin API | 支援 | 不支援 |
| Azure SQL Database | 支援 | 支援 |
| Azure 虛擬機器上的 SQL Server | 支援 | N/A |
| SQL 受控執行個體 | 支援 | N/A |
| Azure Functions | 支援 | 支援，僅適用于 Azure 函式的特定層級 |

> [!NOTE]
> 除了上面所列的選項之外，針對網路安全的 Azure 儲存體帳戶，客戶可以利用 Azure 認知搜尋是 [受信任的 Microsoft 服務](../storage/common/storage-network-security.md#trusted-microsoft-services)的事實。 這表示，如果在儲存體帳戶上啟用適當的角色型存取控制，則特定的搜尋服務可以略過儲存體帳戶的虛擬網路或 IP 限制，並且可以存取儲存體帳戶中的資料。 如需詳細資訊，請參閱 [使用信任服務例外狀況的索引子連接](search-indexer-howto-access-trusted-service-exception.md)。 如果儲存體帳戶或搜尋服務無法移至不同的區域，則可以使用此選項來取代 IP 限制路由。

選擇索引子應該使用的安全存取機制時，請考慮下列限制：

- 索引子無法連接到 [虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)。 具有認證、私用端點、信任服務和 IP 位址的公用端點，是唯一支援的索引子連接方法。
- 搜尋服務無法布建到特定的虛擬網路中，以原生方式在虛擬機器上執行。 Azure 認知搜尋不會提供這項功能。
- 當索引子利用 (輸出) 私人端點來存取資源時，可能會收取額外的 [私人連結費用](https://azure.microsoft.com/pricing/details/search/) 。

## <a name="indexer-execution-environment"></a>索引子執行環境

Azure 認知搜尋索引子可以有效地從資料來源提取內容、將擴充新增至已解壓縮的內容，並選擇性地產生投影，然後再將結果寫入搜尋索引。 視指派給索引子的職責數目而定，它可以在下列兩種環境中執行：

- 特定搜尋服務專用的環境。 在這類環境中執行的索引子會與其他工作負載共用資源， (例如其他客戶起始的索引編制或查詢工作負載) 。 一般來說，只有執行以文字為基礎之索引的索引子 (例如，請勿使用在此環境中執行的技能集) 。
- 裝載需要大量資源之索引子的多租使用者環境，例如具有技能集的索引子。 此環境是用來卸載需要大量運算的處理，讓服務專屬的資源可供例行作業使用。 此多租使用者環境是由 Microsoft 管理及保護，客戶無須支付額外費用。

針對任何指定的索引子執行，Azure 認知搜尋會決定執行索引子的最佳環境。 如果您使用 IP 防火牆來控制對 Azure 資源的存取，則瞭解執行環境將協助您設定包含兩者的 IP 範圍。

## <a name="granting-access-to-indexer-ip-ranges"></a>授與索引子 IP 範圍的存取權

如果您的索引子嘗試存取的資源僅限於一組特定的 IP 範圍，您就必須展開此集合，以包含可從中產生索引子要求的可能 IP 範圍。 如上所述，有兩種可能的環境可讓索引子執行，以及從中產生存取要求。 您將需要新增 **兩個** 環境的 IP 位址，才能讓索引子存取工作。

- 若要取得搜尋服務特定私人環境的 IP 位址，請 `nslookup` (或 `ping`) 搜尋服務 (FQDN) 的完整功能變數名稱。 例如，公用雲端中的搜尋服務 FQDN 就是 `<service-name>.search.windows.net` 。 這項資訊可在 Azure 入口網站上取得。
- 多租使用者環境的 IP 位址可透過 `AzureCognitiveSearch` 服務標記取得。 [Azure 服務](../virtual-network/service-tags-overview.md) 標籤具有每個服務的已發佈 IP 位址範圍，這可透過 [探索 API (預覽版) ](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) 或可下載的 [JSON](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)檔案中取得。 無論是哪一種情況，IP 範圍都會依區域細分-您只能挑選針對您的搜尋服務布建所在區域指派的 IP 範圍。

針對某些資料來源，您可以直接使用服務標籤，而不是列舉 IP 範圍清單 (搜尋服務的 IP 位址仍然必須明確地使用) 。 這些資料來源會藉由設定 [網路安全性群組規則](../virtual-network/network-security-groups-overview.md)來限制存取，而此規則原本就支援新增服務標籤，而不像 IP 規則（例如 Azure 儲存體、Cosmos DB、Azure SQL 等）。 支援 `AzureCognitiveSearch` 直接利用搜尋服務 IP 位址之服務標籤的資料來源如下：

- [Azure 虛擬機器上的 SQL Server](./search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md#restrict-access-to-the-azure-cognitive-search)

- [SQL 受控執行個體](./search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md#verify-nsg-rules)

如需此連線選項的詳細資訊，請參閱 [透過 IP 防火牆的索引子連接](search-indexer-howto-access-ip-restricted.md)。

## <a name="granting-access-via-private-endpoints"></a>透過私人端點授與存取權

索引子可以利用 [私人端點](../private-link/private-endpoint-overview.md) 來存取資源，也就是已鎖定的存取權，可選取虛擬網路或未啟用任何公用存取。
這項功能僅適用于可計費的搜尋服務，並具有所建立私人端點數目的限制。 如需詳細資訊，請參閱 [服務限制](search-limits-quotas-capacity.md#shared-private-link-resource-limits)。

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>步驟1：建立安全資源的私人端點

客戶應該在**共用的私人連結資源**上呼叫搜尋管理作業（ [CreateOrUpdate API](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) ），以建立其安全資源的私人端點連線 (例如儲存體帳戶) 。 超過此 (輸出) 私人端點連線的流量，只會源自搜尋服務特定「私用」索引子執行環境中的虛擬網路。

Azure 認知搜尋將會驗證此 API 的呼叫端是否具有 Azure RBAC 許可權，以核准對安全資源的私人端點連線要求。 例如，如果您要求具有唯讀許可權之儲存體帳戶的私人端點連線，此呼叫將會遭到拒絕。

### <a name="step-2-approve-the-private-endpoint-connection"></a>步驟2：核准私人端點連接

當建立共用私人連結資源的 (非同步) 作業完成時，將會以「暫止」狀態建立私人端點連接。 尚未透過連接流動流量。
接著，客戶應該會在其安全資源上找到此要求並「核准」。 一般來說，您可以透過 Azure 入口網站或透過 [REST API](/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection)來完成這項工作。

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

這些步驟會在 [透過私人端點的索引子連接](search-indexer-howto-access-private.md)中更詳細地說明。
一旦您有資源的已核准私用端點，設定為 *私* 用的索引子會嘗試透過私人端點連接取得存取權。

### <a name="limits"></a>限制

為了確保搜尋服務的最佳效能和穩定性，搜尋服務層) 級會將限制強加 (下列維度：

- 可以設定為 *私*用的索引子類型。
- 可以建立的共用私用連結資源的數目。
- 可以建立共用私用連結資源的相異資源類型數目。

這些限制記載于 [服務限制](search-limits-quotas-capacity.md)中。

## <a name="next-steps"></a>後續步驟

- [透過 IP 防火牆的索引子連接](search-indexer-howto-access-ip-restricted.md)
- [使用受信任服務例外狀況的索引子連接](search-indexer-howto-access-trusted-service-exception.md)
- [與私人端點的索引子連接](search-indexer-howto-access-private.md)