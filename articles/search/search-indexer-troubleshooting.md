---
title: 解決常見搜索索引子問題
titleSuffix: Azure Cognitive Search
description: 修復 Azure 認知搜索中索引子的錯誤和常見問題，包括資料來源連接、防火牆和缺少的文檔。
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1e3692920c35a6965a23c0305aeeebfc80505d85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190921"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>解決 Azure 認知搜索中的常見索引子問題

索引子在將資料索引到 Azure 認知搜索中時可能會遇到許多問題。 失敗的主要類別包括：

* [連接到資料來源或其他資源](#connection-errors)
* [文件處理](#document-processing-errors)
* [擷取文件到索引](#index-errors)

## <a name="connection-errors"></a>連接錯誤

> [!NOTE]
> 索引子對訪問受 Azure 網路安全機制保護的資料來源和其他資源的支援有限。 目前，索引子只能通過相應的 IP 位址範圍限制機制或 NSG 規則（如果適用）訪問資料來源。 有關訪問每個受支援資料來源的詳細資訊，請參閱下文。
>
> 您可以通過 ping 其完全限定的功能變數名稱（例如 ， `<your-search-service-name>.search.windows.net`） 來查找搜索服務的 IP 位址。
>
> 您可以使用[可下載的 JSON](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files)檔`AzureCognitiveSearch`或通過[服務標記發現 API](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview)找到[服務標記](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)的 IP 位址範圍。 IP 位址範圍每週更新一次。

### <a name="configure-firewall-rules"></a>設定防火牆規則

Azure 存儲、CosmosDB 和 Azure SQL 提供了可配置的防火牆。 防火牆啟用時，不會有特定錯誤訊息。 通常，防火牆錯誤是通用的，看起來像`The remote server returned an error: (403) Forbidden``Credentials provided in the connection string are invalid or have expired`或 。

在這樣的實例中，有兩個選項允許索引子訪問這些資源：

* 通過允許**從所有網路**訪問（如果可行），禁用防火牆。
* 或者，您可以在資源的防火牆規則（IP 位址範圍限制）中允許訪問搜索服務的 IP`AzureCognitiveSearch`位址[和服務標記](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)的 IP 位址範圍。

有關為每個資料來源類型配置 IP 位址範圍限制的詳細資訊，請參閱以下連結：

* [Azure 存儲](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

**限制**：如上述 Azure 存儲文檔中所述，僅當搜索服務和存儲帳戶位於不同區域時，IP 位址範圍限制才起作用。

Azure 函數（可用作自訂 Web [Api 技能](cognitive-search-custom-skill-web-api.md)）也支援[IP 位址限制](https://docs.microsoft.com/azure/azure-functions/ip-addresses#ip-address-restrictions)。 要配置的 IP 位址清單將是搜索服務的 IP 位址和服務標記的`AzureCognitiveSearch`IP 位址範圍。

[此處](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)概述了在 Azure VM 上訪問 SQL 伺服器中資料的詳細資訊

### <a name="configure-network-security-group-nsg-rules"></a>配置網路安全性群組 （NSG） 規則

在 SQL 託管實例中訪問資料或將 Azure VM 用作[自訂 Web Api 技能](cognitive-search-custom-skill-web-api.md)的 Web 服務 URI 時，客戶無需關注特定的 IP 位址。

在這種情況下，可以將 Azure VM 或 SQL 託管實例配置為駐留在虛擬網路中。 然後，可以配置網路安全性群組來篩選可流入和流出虛擬網路子網和網路介面的網路流量類型。

`AzureCognitiveSearch`服務標記可以直接用於入站[NSG 規則](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#work-with-security-rules)，而無需查找其 IP 位址範圍。

[此處](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)概述了有關在 SQL 託管實例中訪問資料的更多詳細資訊

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB"索引"未啟用

Azure 認知搜索對 Cosmos DB 索引具有隱式依賴性。 如果在 Cosmos DB 中關閉自動索引，Azure 認知搜索將返回成功狀態，但無法索引容器內容。 如需如何檢查設定及開啟編製索引的指示，請參閱[管理 Azure Cosmos DB 中的編製索引](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal)。

## <a name="document-processing-errors"></a>文檔處理錯誤

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

* 文件僅包含掃描的影像。 具有非文字內容的 PDF Blob (例如掃描的影像 (JPG)) 不會在標準 Blob 編製索引管線中產生結果。 如果您有帶有文本元素的圖像內容，則可以使用[認知搜索](cognitive-search-concept-image-scenarios.md)來查找和提取文本。
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
* [欄位映射](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings)或[AI 擴充](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)已更改文檔，它看起來與您預期的不同。
* 請使用[查閱文件 API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) 來尋找您的文件。
