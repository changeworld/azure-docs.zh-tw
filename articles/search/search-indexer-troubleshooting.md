---
title: 針對常見的搜尋索引子問題進行疑難排解
titleSuffix: Azure Cognitive Search
description: 修正 Azure 認知搜尋中索引子的錯誤和常見問題，包括資料來源連接、防火牆和遺失的檔。
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7eadc9121c54b636fa8b42579284d4018043e1c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91355120"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>針對 Azure 認知搜尋中常見的索引子問題進行疑難排解

索引子可能會在將資料編制索引至 Azure 認知搜尋時遇到一些問題。 失敗的主要類別包括：

* [連接至資料來源或其他資源](#connection-errors)
* [檔處理](#document-processing-errors)
* [擷取文件到索引](#index-errors)

## <a name="connection-errors"></a>連接錯誤

> [!NOTE]
> 索引子對於存取資料來源和其他受 Azure 網路安全性機制保護的資源，提供有限的支援。 目前，索引子只能透過對應的 IP 位址範圍限制機制或 NSG 規則來存取資料來源（適用時）。 存取每個支援資料來源的詳細資料可在下方找到。
>
> 您可以 ping 搜尋服務的完整功能變數名稱，以找出您的搜尋服務的 IP 位址 (例如 `<your-search-service-name>.search.windows.net`) 。
>
> 您可以 `AzureCognitiveSearch` 使用[可下載的 JSON](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)檔案，或透過[服務標記探索 API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview)，找出[服務](../virtual-network/service-tags-overview.md#available-service-tags)標籤的 IP 位址範圍。 每週都會更新 IP 位址範圍。

### <a name="configure-firewall-rules"></a>設定防火牆規則

Azure 儲存體、CosmosDB 和 Azure SQL 提供可設定的防火牆。 防火牆啟用時，不會有特定錯誤訊息。 防火牆錯誤通常是一般的，且看起來像是 `The remote server returned an error: (403) Forbidden` 或 `Credentials provided in the connection string are invalid or have expired` 。

有2個選項可讓索引子在這類實例中存取這些資源：

* 藉由允許從 **所有網路** 存取 (（如果可行) ）來停用防火牆。
* 或者，您可以在資源的防火牆規則中，允許存取搜尋服務的 IP 位址和服務標籤的 IP 位址範圍 `AzureCognitiveSearch` [service tag](../virtual-network/service-tags-overview.md#available-service-tags) (ip 位址範圍限制) 。

您可以從下列連結中找到每個資料來源類型的 IP 位址範圍限制設定詳細資料：

* [Azure 儲存體](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Azure SQL](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**限制**：如同上述檔中所述的 Azure 儲存體，如果您的搜尋服務和儲存體帳戶位於不同的區域，IP 位址範圍限制才會運作。

可作為 [自訂 Web Api 技能](cognitive-search-custom-skill-web-api.md) 的 Azure 函式 () 也支援 [IP 位址限制](../azure-functions/ip-addresses.md#ip-address-restrictions)。 要設定的 IP 位址清單會是您搜尋服務的 IP 位址，以及服務標籤的 IP 位址範圍 `AzureCognitiveSearch` 。

[本文概述在](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)Azure VM 上存取 SQL server 資料的詳細資料

### <a name="configure-network-security-group-nsg-rules"></a> (NSG) 規則設定網路安全性群組

存取 SQL 受控實例中的資料，或使用 Azure VM 作為 [自訂 Web Api 技能](cognitive-search-custom-skill-web-api.md)的 WEB 服務 URI 時，客戶不需要考慮特定的 IP 位址。

在這種情況下，Azure VM 或 SQL 受控實例可以設定為位於虛擬網路內。 然後，您可以設定網路安全性群組，以篩選可流入和流出虛擬網路子網和網路介面的網路流量類型。

`AzureCognitiveSearch`服務標記可以直接用於輸入[NSG 規則](../virtual-network/manage-network-security-group.md#work-with-security-rules)中，而不需要查閱其 IP 位址範圍。

在 SQL 受控實例中存取資料的詳細 [資訊如下所述](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>未啟用 CosmosDB 「編制索引」

Azure 認知搜尋對 Cosmos DB 索引編制具有隱含相依性。 如果您在 Cosmos DB 中關閉自動編制索引，Azure 認知搜尋會傳回成功狀態，但無法為容器內容編制索引。 如需如何檢查設定及開啟編製索引的指示，請參閱[管理 Azure Cosmos DB 中的編製索引](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal)。

## <a name="document-processing-errors"></a>檔處理錯誤

### <a name="unprocessable-or-unsupported-documents"></a>無法處理或不支援的文件

Blob 索引子會[記錄明確支援哪些文件格式](search-howto-indexing-azure-blob-storage.md#SupportedFormats)。 有時候，Blob 儲存體容器會包含不支援的文件。 有時候則會包含有問題的文件。 您可以透過[變更設定選項](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)來避免您的索引子在這些文件上停止：

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>遺漏文件內容

Blob 索引子會[從容器的 Blob 中尋找並擷取文字](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs)。 擷取文字的某些問題包括：

* 文件僅包含掃描的影像。 具有非文字內容的 PDF Blob (例如掃描的影像 (JPG)) 不會在標準 Blob 編製索引管線中產生結果。 如果您有包含文字元素的影像內容，您可以使用 [認知搜尋](cognitive-search-concept-image-scenarios.md) 來尋找並解壓縮文字。
* Blob 索引子已設為只為中繼資料建立索引。 若要擷取內容，Blob 索引子必須設為[同時擷取內容和中繼資料](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex)：

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>索引錯誤

### <a name="missing-documents"></a>遺漏文件

索引子會從[資料來源](/rest/api/searchservice/create-data-source)尋找文件。 有時候索引內可能會遺漏應為其建立索引的資料來源文件。 導致這些錯誤的幾個常見原因如下：

* 尚未為文件建立索引。 檢查入口網站以了解成功的索引子執行。
* 檢查您的 [變更追蹤](/rest/api/searchservice/create-data-source#data-change-detection-policies) 值。 如果您的高水位線值是設定為未來時間的日期，則索引子會略過日期小於此值的任何檔。 您可以使用 [索引子狀態](/rest/api/searchservice/get-indexer-status#indexer-execution-result)中的 ' initialTrackingState ' 和 ' finalTrackingState ' 欄位來瞭解索引子的變更追蹤狀態。
* 文件已在索引子執行後更新。 若您的索引子是根據[排程](/rest/api/searchservice/create-indexer#indexer-schedule)執行，它最後將重新執行並擷取文件。
* 資料來源中指定的[查詢](/rest/api/searchservice/create-data-source)排除了文件。 若文件並非資料來源的一部分，索引子將無法為文件建立索引。
* [欄位](/rest/api/searchservice/create-indexer#fieldmappings) 對應或 [AI 擴充](./cognitive-search-concept-intro.md) 已變更檔，看起來會與您預期的不同。
* 請使用[查閱文件 API](/rest/api/searchservice/lookup-document) 來尋找您的文件。