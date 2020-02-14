---
title: 針對常見的搜尋索引子問題進行疑難排解
titleSuffix: Azure Cognitive Search
description: 修正 Azure 認知搜尋中索引子的錯誤和常見問題，包括資料來源連線、防火牆和遺失的檔。
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1e3692920c35a6965a23c0305aeeebfc80505d85
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190921"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>針對 Azure 認知搜尋中的常見索引子問題進行疑難排解

將資料編制索引至 Azure 認知搜尋時，索引子可能會遇到一些問題。 失敗的主要類別包括：

* [連接到資料來源或其他資源](#connection-errors)
* [文件處理](#document-processing-errors)
* [擷取文件到索引](#index-errors)

## <a name="connection-errors"></a>連接錯誤

> [!NOTE]
> 索引子對於存取 Azure 網路安全性機制所保護的資料來源和其他資源具有有限的支援。 目前，在適用的情況下，索引子只能透過對應的 IP 位址範圍限制機制或 NSG 規則來存取資料來源。 如需存取每個支援的資料來源的詳細資訊，請參閱下方。
>
> 您可以藉由 ping 其完整功能變數名稱（例如，`<your-search-service-name>.search.windows.net`）來找出搜尋服務的 IP 位址。
>
> 您可以使用[可下載的 JSON](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files)檔案或透過服務標籤[探索 API](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview)，找出 `AzureCognitiveSearch`[服務](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)標籤的 IP 位址範圍。 每週會更新 IP 位址範圍。

### <a name="configure-firewall-rules"></a>設定防火牆規則

Azure 儲存體，CosmosDB 和 Azure SQL 會提供可設定的防火牆。 防火牆啟用時，不會有特定錯誤訊息。 一般來說，防火牆錯誤是泛型的，看起來像 `The remote server returned an error: (403) Forbidden` 或 `Credentials provided in the connection string are invalid or have expired`。

有2個選項可讓索引子存取這類實例中的這些資源：

* 藉由允許來自**所有網路**的存取（如果可行）來停用防火牆。
* 或者，您可以在資源的防火牆規則（IP 位址範圍限制）中，允許存取搜尋服務的 IP 位址和 `AzureCognitiveSearch`[服務](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)標籤的 ip 位址範圍。

您可以從下列連結找到為每個資料來源類型設定 IP 位址範圍限制的詳細資訊：

* [Azure 儲存體](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

**限制**：如上述 Azure 儲存體的檔中所述，只有在您的搜尋服務和儲存體帳戶位於不同區域時，IP 位址範圍限制才會生效。

Azure 函式（可用來做為[自訂 Web Api 技能](cognitive-search-custom-skill-web-api.md)）也支援[IP 位址限制](https://docs.microsoft.com/azure/azure-functions/ip-addresses#ip-address-restrictions)。 要設定的 IP 位址清單會是搜尋服務的 IP 位址，以及 `AzureCognitiveSearch` 服務標籤的 IP 位址範圍。

[此處](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)概述在 Azure VM 上存取 SQL server 資料的詳細資訊

### <a name="configure-network-security-group-nsg-rules"></a>設定網路安全性群組（NSG）規則

存取 SQL 受控實例中的資料，或使用 Azure VM 做為[自訂 Web Api 技能](cognitive-search-custom-skill-web-api.md)的 WEB 服務 URI 時，客戶不需要擔心特定的 IP 位址。

在這種情況下，您可以將 Azure VM 或 SQL 受控實例設定為位於虛擬網路中。 然後，您可以設定網路安全性群組來篩選可以流入和流出虛擬網路子網和網路介面的網路流量類型。

`AzureCognitiveSearch` 服務標籤可以直接用於輸入[NSG 規則](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#work-with-security-rules)，而不需要查閱其 IP 位址範圍。

如需存取 SQL 受控實例中資料的詳細資訊，請參閱[這裡](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB 「編制索引」未啟用

Azure 認知搜尋具有 Cosmos DB 索引的隱含相依性。 如果您關閉 Cosmos DB 中的自動編制索引，Azure 認知搜尋會傳回成功的狀態，但無法為容器內容編制索引。 如需如何檢查設定及開啟編製索引的指示，請參閱[管理 Azure Cosmos DB 中的編製索引](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal)。

## <a name="document-processing-errors"></a>檔處理錯誤

### <a name="unprocessable-or-unsupported-documents"></a>無法處理或不支援的文件

Blob 索引子會[記錄明確支援哪些文件格式](search-howto-indexing-azure-blob-storage.md#SupportedFormats)。 有時候，Blob 儲存體容器會包含不支援的文件。 有時候則會包含有問題的文件。 您可以透過[變更設定選項](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)來避免您的索引子在這些文件上停止：

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>遺漏文件內容

Blob 索引子會[從容器的 Blob 中尋找並擷取文字](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs)。 擷取文字的某些問題包括：

* 文件僅包含掃描的影像。 具有非文字內容的 PDF Blob (例如掃描的影像 (JPG)) 不會在標準 Blob 編製索引管線中產生結果。 如果您的影像內容包含文字元素，您可以使用[認知搜尋](cognitive-search-concept-image-scenarios.md)來尋找並解壓縮文字。
* Blob 索引子已設為只為中繼資料建立索引。 若要擷取內容，Blob 索引子必須設為[同時擷取內容和中繼資料](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)：

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>索引錯誤

### <a name="missing-documents"></a>遺漏文件

索引子會從[資料來源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)尋找文件。 有時候索引內可能會遺漏應為其建立索引的資料來源文件。 導致這些錯誤的幾個常見原因如下：

* 尚未為文件建立索引。 檢查入口網站以了解成功的索引子執行。
* 文件已在索引子執行後更新。 若您的索引子是根據[排程](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule)執行，它最後將重新執行並擷取文件。
* 資料來源中指定的[查詢](/rest/api/searchservice/create-data-source)排除了文件。 若文件並非資料來源的一部分，索引子將無法為文件建立索引。
* [欄位](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings)對應或[AI 擴充](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)已變更檔，看起來與您所預期的不同。
* 請使用[查閱文件 API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) 來尋找您的文件。
