---
title: 透過私人端點存取安全資源的索引子
titleSuffix: Azure Cognitive Search
description: 說明如何設定要與安全資源通訊之索引子的私用端點的操作指南
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: e9944e95dd452cd00e63280ad8002141591f521e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90971429"
---
# <a name="accessing-secure-resources-via-private-endpoints"></a>透過私人端點存取安全資源

您可以設定 Azure 資源 (例如作為資料來源) 使用的儲存體帳戶，以便只能從特定的虛擬網路清單存取它們。 您也可以將它們設定為不允許任何「公用網路」存取。
客戶可以要求 Azure 認知搜尋建立 (輸出) [私人端點](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) 連線，以便透過索引子安全地存取來自這類資料來源的資料。

## <a name="shared-private-link-resources-management-apis"></a>共用 Private Link 資源管理 Api

在客戶要求 Azure 認知搜尋所建立的私人端點會存取「安全」資源，稱為 *共用的私人連結資源*。 客戶會「共用」資源 (的存取權，例如儲存體帳戶) ，其具有上線至 [Azure Private Link 服務](https://azure.microsoft.com/services/private-link/)的存取權。

Azure 認知搜尋透過搜尋管理 API 提供的功能，也就是 [建立或更新共用私人連結資源](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate)的能力。 您將使用此 API 以及其他 *共用的私人連結資源* 管理 api，從 Azure 認知搜尋索引子設定安全資源的存取權。

某些資源的私人端點連線只能透過搜尋管理 API 的預覽版本建立 (`2020-08-01-Preview`) ，在下表中是以 "preview" 標記來表示。 您可以透過預覽 API 和 GA API (建立沒有 "preview" 標記的資源 `2020-08-01`) 

以下是可從 Azure 認知搜尋建立輸出私人端點的 Azure 資源清單。 `groupId` 下表中所列的，必須完全使用 (區分大小寫的 API) ，才能建立共用的私人連結資源。

| Azure 資源 | 群組識別碼 |
| --- | --- |
| Azure 儲存體-Blob (或) ADLS Gen 2 | `blob`|
| Azure 儲存體-資料表 | `table`|
| Azure Cosmos DB-SQL API | `Sql`|
| Azure SQL Database | `sqlServer`|
| 適用於 MySQL 的 Azure 資料庫 (preview)  | `mysqlServer`|
| Azure 金鑰保存庫 | `vault` |
| Azure Functions (preview)  | `sites` |

支援輸出私人端點連線的 Azure 資源清單，也可以透過 [清單支援的 API](https://docs.microsoft.com/rest/api/searchmanagement/privatelinkresources/listsupported)進行查詢。

基於本指南的目的，混合使用 [ARMClient](https://github.com/projectkudu/ARMClient) 和 [Postman](https://www.postman.com/) 來示範 REST API 的呼叫。

> [!NOTE]
> 在本指南中，我們假設搜尋服務的名稱是__contoso-搜尋__，其存在於訂用帳戶識別碼為__00000000-0000-0000-0000-000000000000__之訂用帳戶的資源群組__contoso__中。 這項搜尋服務的資源識別碼將是 `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search`

本指南的其餘部分將說明如何設定 __contoso 搜尋__ 服務，讓其索引子可以從安全的儲存體帳戶存取資料。 `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage`

## <a name="securing-your-storage-account"></a>保護您的儲存體帳戶

將儲存體帳戶設定為 [只允許來自特定子網的存取](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network)。 透過 Azure 入口網站，如果您核取此選項，並將集合保留為空白，則表示不允許來自任何虛擬網路的流量。

   ![虛擬網路存取](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png "虛擬網路存取")

> [!NOTE]
> [受信任的 Microsoft 服務方法](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)可以用來略過這類儲存體帳戶的虛擬網路或 IP 限制，並可讓搜尋服務存取儲存體帳戶中的資料，如操作[指南](search-indexer-howto-access-trusted-service-exception.md)中所述。 不過，使用這種方法時，Azure 認知搜尋與儲存體帳戶之間的通訊會透過安全的 Microsoft 骨幹網路，透過儲存體帳戶的公用 IP 位址進行。

## <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>步驟1：建立儲存體帳戶的共用私人連結資源

進行下列 API 呼叫，要求 Azure 認知搜尋建立連至儲存體帳戶的輸出私人端點連線

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

檔案 (的內容 `create-pe.json` ，代表 API) 的要求主體，如下所示：

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

`202 Accepted`成功時傳迴響應-建立輸出私人端點的程式是長時間執行的 (非同步) 作業。 它牽涉到部署下列資源-

1. 以狀態中的私人 IP 位址配置的私人端點 `"Pending"` 。 私人 IP 位址是從配置給搜尋服務特定私用索引子執行環境之虛擬網路的位址空間取得。 在私人端點的核准下，從 Azure 認知搜尋到儲存體帳戶的任何通訊都源自于私人 IP 位址和安全的私人連結通道。
2. 資源類型的私人 DNS 區域，以為基礎 `groupId` 。 這可確保私人資源的任何 DNS 查閱都利用與私人端點相關聯的 IP 位址。

請務必 `groupId` 針對您要建立私人端點的資源類型，指定正確的。 任何不符都將導致不成功的回應訊息。

就像所有的非同步 Azure 作業一樣， `PUT` 呼叫 `Azure-AsyncOperation` 會傳回標頭值，如下所示：

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

此 URI 可定期輪詢以取得作業狀態。 建議您等到共用的私人連結資源作業狀態達到終端狀態 (也就是 `succeeded`) 再繼續。

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

## <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>步驟2a：核准儲存體帳戶的私人端點連線

> [!NOTE]
> 本節使用 Azure 入口網站逐步解說私人端點至儲存體的核准流程。 您也可以改為使用可透過儲存體資源提供者 (RP) 取得的 [REST API](https://docs.microsoft.com/rest/api/storagerp/privateendpointconnections) 。
>
> 其他提供者（例如 CosmosDB、Azure SQL server 等）也會提供類似的 RP Api 來管理私人端點連接。

在 Azure 入口網站上流覽至儲存體帳戶的 [**私人端點**連線] 索引標籤。 您應該會要求私人端點連線，並在非同步作業 __成功__) 之後， (來自先前 API 呼叫的要求訊息。

   ![私人端點核准](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png "私人端點核准")

選取 Azure 認知搜尋所建立的私人端點 (使用 [私人端點] 資料行，依先前 API 中指定的名稱來識別私人端點連線) 然後選擇 [核准]，並以適當的訊息 (訊息不會有很大的) 。 請確定私人端點連線出現，如下所示 (它可能會在1-2 分鐘的任何時間點出現，才能在入口網站上更新狀態) 

![已核准私人端點](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png "已核准私人端點")

私人端點連線要求經過核准後，即表示流量 *能夠* 流經私人端點。 私人端點核准之後 Azure 認知搜尋將會在為其建立的 DNS 區域中建立必要的 DNS 區域對應。

## <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>步驟2b：查詢共用私人連結資源的狀態

 若要確認已在核准之後更新共用的私人連結資源，請透過 [GET API](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/get)取得其狀態。

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

如果 `properties.provisioningState` 資源的是 `Succeeded` 且 `properties.status` 為 `Approved` ，則表示共用的私人連結資源可正常運作，而索引子可設定為透過私人端點進行通訊。

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

## <a name="step-3-configure-indexer-to-run-in-the-private-environment"></a>步驟3：設定要在私用環境中執行的索引子

> [!NOTE]
> 即使在私人端點連線已核准之前，也可以執行此步驟。 在授權私人端點連線之前，任何嘗試與安全 (資源通訊的索引子（例如儲存體帳戶) ）都會最後處於暫時性失敗狀態。 將無法建立新的索引子。 私人端點連線一經核准，索引子就能夠存取私人儲存體帳戶。

1. 建立指向安全儲存體帳戶和儲存體帳戶內適當容器的[資料來源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)。 以下顯示透過 Postman 執行的要求。
![建立資料來源](media\search-indexer-howto-secure-access\create-ds.png "資料來源建立")

2. 同樣地，請 [建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index) ，並選擇性地使用 REST API [建立技能集](https://docs.microsoft.com/rest/api/searchservice/create-skillset) 。

3. 建立指向上述建立之資料來源、索引和技能集的[索引子](https://docs.microsoft.com/rest/api/searchservice/create-indexer)。 此外，將索引子設定屬性設定為，以強制在私用執行環境中執行索引 `executionEnvironment` 器 `"Private"` 。
![建立索引子](media\search-indexer-howto-secure-access\create-idr.png "建立索引子")

索引子應建立成功，而且應該從儲存體帳戶透過私人端點連接來編制索引內容。 您可以透過 [索引子狀態 API](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)監視索引子的狀態。

> [!NOTE]
> 如果您已經有現有的索引子，您可以直接透過 [PUT API](https://docs.microsoft.com/rest/api/searchservice/create-indexer) 更新它們，將設定 `executionEnvironment` 為 `"Private"` 。

## <a name="troubleshooting-issues"></a>針對問題進行疑難排解

- 建立索引子時，如果建立失敗，並出現類似于「資料來源認證無效」的錯誤訊息，這表示私人端點連接尚未 *經過核准* 或無法運作。
使用 [GET API](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/get)取得共用私人連結資源的狀態。 如果已 *核准* ，請檢查 `properties.provisioningState` 資源的。 如果是 `Incomplete` ，這表示資源的某些基礎相依性無法布建- `PUT` 請重新發出要求，以「重新建立」應修正問題的共用私人連結資源。 可能需要重新核准-再次檢查資源的狀態以進行驗證。
- 如果建立索引子時沒有設定它 `executionEnvironment` ，索引子建立可能會成功，但其執行歷程記錄會顯示索引子執行失敗。 您應 [更新索引子](https://docs.microsoft.com/rest/api/searchservice/update-indexer) 來指定執行環境。
- 如果在未設定的情況下建立索引子， `executionEnvironment` 而且會成功執行，則表示 Azure 認知搜尋已決定其執行環境為搜尋服務特定的「私用」環境。 不過，這可能會根據各種不同的因素而變更， (索引子所取用的資源、搜尋服務上的負載，) 以及稍後可能會失敗的情況，我們強烈建議您將設定 `executionEnvironment` 為 `"Private"` ，以確保未來將不會失敗。
- [配額和限制](search-limits-quotas-capacity.md) 會決定可建立多少共用的私人連結資源，以及相依于搜尋服務的 SKU。

## <a name="next-steps"></a>下一步

深入瞭解私人端點：

- [什麼是私人端點？](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
- [私人端點所需的 DNS 設定](https://docs.microsoft.com/azure/private-link/private-endpoint-dns)