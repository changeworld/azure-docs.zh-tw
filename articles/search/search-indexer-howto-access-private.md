---
title: 透過私人端點的索引子連接
titleSuffix: Azure Cognitive Search
description: 設定索引子連接，以存取透過私人端點所保護之其他 Azure 資源的內容。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: ff8aa6688d8a838fa2e06d2eef546025cdd9213f
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340048"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>透過私人端點進行索引子連接

許多 Azure 資源（例如 Azure 儲存體帳戶）都可以設定為接受來自虛擬網路清單的連線，並拒絕源自公用網路的外部連線。 如果您在 Azure 認知搜尋中使用索引子來編制資料的索引，而且您的資料來源位於私人網路中，則您可以建立 [連出私人端點](../private-link/private-endpoint-overview.md) 連線來連線到資料。

此索引子連接方法受限於下列兩個需求：

+ 提供內容或程式碼的 Azure 資源，必須先前向 [Azure Private Link 服務](https://azure.microsoft.com/services/private-link/)註冊。

+ Azure 認知搜尋服務必須在基本層或更高的層級上。 這項功能無法在免費層上使用。 此外，如果您的索引子具有技能集，層級必須是標準 2 (S2) 或更高版本。 如需詳細資訊，請參閱 [服務限制](search-limits-quotas-capacity.md#shared-private-link-resource-limits)。

## <a name="shared-private-link-resources-management-apis"></a>共用 Private Link 資源管理 Api

透過 Azure 認知搜尋 Api 所建立之安全資源的私人端點稱為 *共用私用連結資源*。 這是因為您正在「共用」資源（例如儲存體帳戶）的存取權，該資源已與 [Azure Private Link 服務](https://azure.microsoft.com/services/private-link/)整合。

透過其管理 REST API，Azure 認知搜尋提供 [CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) 作業，可讓您用來設定 Azure 認知搜尋索引子的存取。

您只能使用預覽版本的搜尋管理 API (版本 *2020-08-01-preview* 或更新版本) （此為下表中指定的 *預覽版* ），來建立某些資源的私人端點連線。 您可以使用預覽或正式運作的 API 版本（ (*2020-08-01*或更新版本的) ）來建立沒有*預覽*指定的資源。

下表列出您可以從 Azure 認知搜尋建立輸出私人端點的 Azure 資源。 若要建立共用的私人連結資源，請輸入與 API 中所撰寫的 **群組識別碼** 值完全相同的值。 值會區分大小寫。

| Azure 資源 | 群組識別碼 |
| --- | --- |
| Azure 儲存體-Blob (或) ADLS Gen 2 | `blob`|
| Azure 儲存體-資料表 | `table`|
| Azure Cosmos DB-SQL API | `Sql`|
| Azure SQL Database | `sqlServer`|
| 適用於 MySQL 的 Azure 資料庫 (preview)  | `mysqlServer`|
| Azure 金鑰保存庫 | `vault` |
| Azure Functions (preview)  | `sites` |

您也可以使用 [支援的 api 清單](/rest/api/searchmanagement/privatelinkresources/listsupported)，查詢支援輸出私人端點連線的 Azure 資源。

本文的其餘部分會使用混合的 [ARMClient](https://github.com/projectkudu/ARMClient) 和 [Postman](https://www.postman.com/) api 來示範 REST API 的呼叫。

> [!NOTE]
> 本文中的範例是以下列假設為基礎：
> * 搜尋服務的名稱為_contoso-搜尋_，其存在於訂用帳戶識別碼為_00000000-0000-0000-0000-000000000000_之訂用帳戶的_contoso_資源群組中。 
> * 這項搜尋服務的資源識別碼是 _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search_。

其餘範例會示範如何設定 _contoso 搜尋_ 服務，讓其索引子可以從安全的儲存體帳戶 _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage_存取資料。

## <a name="secure-your-storage-account"></a>保護您的儲存體帳戶

將儲存體帳戶設定為 [只允許來自特定子網的存取](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)。 在 Azure 入口網站中，如果您選取此選項，並將集合保留為空白，則表示不允許來自虛擬網路的流量。

   ![[防火牆和虛擬網路] 窗格的螢幕擷取畫面，其中顯示允許存取所選網路的選項。 ](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

> [!NOTE]
> 您可以使用 [受信任的 Microsoft 服務方法](../storage/common/storage-network-security.md#trusted-microsoft-services) 來略過儲存體帳戶的虛擬網路或 IP 限制。 您也可以啟用搜尋服務來存取儲存體帳戶中的資料。 若要這樣做，請參閱 [使用受信任的服務例外狀況來存取 Azure 儲存體的索引子](search-indexer-howto-access-trusted-service-exception.md)。 
>
> 不過，當您使用此方法時，Azure 認知搜尋與儲存體帳戶之間的通訊會透過安全的 Microsoft 骨幹網路，透過儲存體帳戶的公用 IP 位址進行。

### <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>步驟1：建立儲存體帳戶的共用私人連結資源

若要要求 Azure 認知搜尋建立連至儲存體帳戶的輸出私人端點連線，請進行下列 API 呼叫： 

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

檔案 *create-pe.js* 的內容（代表 API 的要求本文）如下所示：

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

`202 Accepted`成功時傳迴響應。 建立輸出私人端點的程式是長時間執行的 (非同步) 作業。 它牽涉到部署下列資源：

* 私人端點，以狀態中的私人 IP 位址配置 `"Pending"` 。 私人 IP 位址是從針對搜尋服務專屬私用索引子配置給執行環境之虛擬網路的位址空間取得。 在私人端點的核准下，從 Azure 認知搜尋到儲存體帳戶的任何通訊都源自于私人 IP 位址和安全的私人連結通道。

* 資源類型的私人 DNS 區域，以為基礎 `groupId` 。 藉由部署此資源，您可以確保私人資源的任何 DNS 查閱都利用與私人端點相關聯的 IP 位址。

請務必 `groupId` 針對您要建立私人端點的資源類型，指定正確的。 任何不符都將導致不成功的回應訊息。

如同在所有非同步 Azure 作業中， `PUT` 呼叫會傳回如下所 `Azure-AsyncOperation` 示的標頭值：

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

您可以定期輪詢此 URI，以取得作業的狀態。 在您繼續之前，建議您先等候共用 private link 資源作業的狀態達到終端狀態 (也就是作業的狀態為 [ *成功* ]) 。

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>步驟2a：核准儲存體帳戶的私人端點連線

> [!NOTE]
> 在本節中，您會使用 Azure 入口網站來逐步完成私人端點至儲存體的核准流程。 或者，您也可以使用可透過儲存體資源提供者取得的 [REST API](/rest/api/storagerp/privateendpointconnections) 。
>
> 其他提供者（例如 Azure Cosmos DB 或 Azure SQL Server）提供類似的儲存體資源提供者 Api 來管理私人端點連接。

1. 在 [Azure 入口網站中，選取儲存體帳戶的 [ **私人端點連接** ] 索引標籤。 非同步作業成功之後，應該會要求私人端點與先前 API 呼叫中的要求訊息。

   ![Azure 入口網站的螢幕擷取畫面，其中顯示 [私人端點連接] 窗格。](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. 選取 Azure 認知搜尋建立的私人端點。 在 [ **私人端點** ] 資料行中，依先前 API 中指定的名稱來識別私人端點連接，選取 [ **核准**]，然後輸入適當的訊息。 訊息內容並不重要。 

   請確定私人端點連線出現，如下列螢幕擷取畫面所示。 可能需要一到兩分鐘的時間，才會在入口網站中更新狀態。

   ![Azure 入口網站的螢幕擷取畫面，顯示 [私人端點連線] 窗格上的 [已核准] 狀態。](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

私人端點連線要求經過核准之後，流量就 *能夠* 流經私人端點。 私人端點經核准之後，Azure 認知搜尋會在為其建立的 DNS 區域中建立必要的 DNS 區域對應。

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>步驟2b：查詢共用私人連結資源的狀態

若要確認已在核准之後更新共用的私人連結資源，請使用 [GET API](/rest/api/searchmanagement/sharedprivatelinkresources/get)取得其狀態。

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

如果 `properties.provisioningState` 資源的是 `Succeeded` 且 `properties.status` 為，則 `Approved` 表示共用 private link 資源可正常運作，而且可以將索引子設定為透過私人端點進行通訊。

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

### <a name="step-3-configure-the-indexer-to-run-in-the-private-environment"></a>步驟3：設定要在私用環境中執行的索引子

> [!NOTE]
> 您可以在私人端點連線核准之前執行此步驟。 在授權私人端點連線之前，任何嘗試與安全 (資源通訊的索引子（例如儲存體帳戶) ）都會最後處於暫時性失敗狀態。 將無法建立新的索引子。 一旦私人端點連接經過核准，索引子就可以存取私人儲存體帳戶。

1. 建立指向安全儲存體帳戶和儲存體帳戶內適當容器的[資料來源](/rest/api/searchservice/create-data-source)。 下列螢幕擷取畫面顯示 Postman 中的這項要求。

   ![顯示在 Postman 使用者介面上建立資料來源的螢幕擷取畫面。](media\search-indexer-howto-secure-access\create-ds.png )

1. 同樣地，請 [建立索引](/rest/api/searchservice/create-index) ，並選擇性地使用 REST API [建立技能集](/rest/api/searchservice/create-skillset) 。

1. 建立指向您在上一個步驟中建立之資料來源、索引和技能集的[索引子](/rest/api/searchservice/create-indexer)。 此外，將索引子設定屬性設定為，以強制在私用執行環境中執行索引子 `executionEnvironment` `private` 。

   ![顯示在 Postman 使用者介面上建立索引子的螢幕擷取畫面。](media\search-indexer-howto-secure-access\create-idr.png)

   成功建立索引子之後，它應該會開始從儲存體帳戶透過私人端點連線來編制內容的索引。 您可以使用 [索引子狀態 API](/rest/api/searchservice/get-indexer-status)來監視索引子的狀態。

> [!NOTE]
> 如果您已經有現有的索引子，您可以藉由將設定為，透過 [PUT API](/rest/api/searchservice/create-indexer) 來更新它們 `executionEnvironment` `private` 。

## <a name="troubleshooting"></a>疑難排解

- 如果您的索引子建立失敗並出現錯誤訊息（例如「資料來源認證無效」），這表示私人端點連接的狀態尚未 *核准* 或連接無法運作。 若要解決此問題： 
  * 使用 [GET API](/rest/api/searchmanagement/sharedprivatelinkresources/get)取得共用私人連結資源的狀態。 如果狀態為 [已 *核准*]，請檢查 `properties.provisioningState` 資源的。 如果這裡的狀態是 `Incomplete` ，則表示資源的某些基礎相依性無法設定。 重新發出 `PUT` 要求以重新建立共用的私人連結資源，應可修正此問題。 可能需要重新進行重新審核。 重新檢查資源的狀態，以確認問題已修正。

- 如果您在不設定其屬性的情況下建立索引子 `executionEnvironment` ，建立可能會成功，但其執行歷程記錄會顯示索引子執行失敗。 若要解決此問題：
   * [更新索引子](/rest/api/searchservice/update-indexer) 以指定執行環境。

- 如果您在未設定屬性的情況下建立索引子， `executionEnvironment` 而且它執行成功，則表示 Azure 認知搜尋已決定其執行環境是搜尋服務專屬的 *私* 用環境。 這可能會變更，視索引子取用的資源、搜尋服務上的負載和其他因素而定，而且之後可能會失敗。 若要解決此問題：
  * 強烈建議您將 `executionEnvironment` 屬性設定為， `private` 以確保未來不會失敗。

[配額和限制](search-limits-quotas-capacity.md) 會決定可建立多少共用的私人連結資源，以及相依于搜尋服務的 SKU。

## <a name="next-steps"></a>後續步驟

深入瞭解私人端點：

- [什麼是私人端點？](../private-link/private-endpoint-overview.md)
- [私人端點所需的 DNS 設定](../private-link/private-endpoint-dns.md)
