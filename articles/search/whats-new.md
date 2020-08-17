---
title: Azure 認知搜尋的新功能
description: 全新和增強功能的公告，包括將 Azure 搜尋服務重新命名為 Azure 認知搜尋。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: c5990faab1e06961a2c3d577a4677d2e1d504699
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904059"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure 認知搜尋的新功能

了解該服務的新功能。 將此頁面加入書籤，以掌握服務的最新狀態。

## <a name="feature-announcements-in-2020"></a>2020 的功能公告

### <a name="august-2020"></a>2020 年 8 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 類別 | 描述 | 可用性  |
|---------|------------------|-------------|---------------|
| [雙重加密](search-security-overview.md#encryption) | 安全性 | 在新的搜尋服務上設定客戶管理的金鑰 (CMK) 加密，以在儲存層啟用雙重加密。 建立新的服務、[將客戶管理的金鑰設定並套用至索引或同義字對應](search-security-manage-encryption-keys.md)，並從該內容的雙重加密獲益。 | 於 2020 年 8 月 1 日後在下列區域建立的所有搜尋服務中正式發行：美國西部 2、美國東部、美國中南部、US Gov 維吉尼亞州、US Gov 亞利桑那州。 使用入口網站、管理 REST API 或 SDK 來建立服務。 |

### <a name="july-2020"></a>2020 年 7 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 類別 | 描述 | 可用性  |
|---------|------------------|-------------|---------------|
| [Azure.Search.Documents 用戶端程式庫](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet) | 適用於 .NET 的 Azure SDK | Azure SDK 團隊發行了 .NET 用戶端程式庫，其設計是為了與其他 .NET 用戶端程式庫一致。 <br/><br/>版本 11 的目標為搜尋 REST api-version=2020-06-30，但尚不支援知識存放區、地理空間類型或 [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder?view=azure-dotnet)。 <br/><br/>如需詳細資訊，請參閱[快速入門：建立索引](search-get-started-dotnet.md)和[升級至 Azure.Search.Documents (v11)](search-dotnet-sdk-migration-version-11.md)。 | 正式推出。 </br> 安裝來自 NuGet 的 [Azure.Search.Documents 套件](https://www.nuget.org/packages/Azure.Search.Documents/)。 |
| [azure.search.documents 用戶端程式庫](https://docs.microsoft.com/python/api/overview/azure/search-documents-readme?view=azure-python)  | 適用於 Python 的 Azure SDK| Azure SDK 團隊發行了 Python 用戶端程式庫，其設計是為了與其他 Python 用戶端程式庫一致。 <br/><br/>版本 11 的目標為搜尋 REST api-version=2020-06-30。 | 正式推出。 </br> 安裝來自 PyPI 的 [azure-search-documents 套件](https://pypi.org/project/azure-search-documents/)。 |
| [@azure/search-documents用戶端程式庫](https://docs.microsoft.com/javascript/api/overview/azure/search-documents-readme?view=azure-node-latest)  | 適用於 JavaScript 的 Azure SDK | Azure SDK 團隊發行了 JavaScript 用戶端程式庫，其設計是為了與其他 JavaScript 用戶端程式庫一致。 <br/><br/>版本 11 的目標為搜尋 REST api-version=2020-06-30。 | 正式推出。 </br> 從 npm 安裝[@azure/search-documents 套件](https://www.npmjs.com/package/@azure/search-documents)。 |

### <a name="june-2020"></a>2020 年 6 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 類別 | 描述 | 可用性  |
|---------|------------------|-------------|---------------|
[**知識存放區**](knowledge-store-concept-intro.md) | AI 擴充 | 以 AI 擴充的索引子輸出，將內容儲存在 Azure 儲存體中，以用於其他應用程式和程序。 | 正式推出。 </br> 使用[搜尋 REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) 或更新版本，或是入口網站。 |
| [**搜尋 REST API 2020-06-30**](https://docs.microsoft.com/rest/api/searchservice/) | REST | REST API 的新穩定版本。 除了知識存放區之外，此版本還包含搜尋相關性和評分的增強功能。 | 正式推出。 |
| [**Okapi BM25 相關性演算法**](https://en.wikipedia.org/wiki/Okapi_BM25) | 查詢 | 7 月 15 日之後建立的所有新搜尋服務都會使用新的相關性排名演算法。 針對之前建立的服務，您可以藉由在索引欄位上設定 `similarity` 屬性來加入。 | 正式推出。 </br> 使用[搜尋 REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) 或更新版本，或是 REST API 2019-05-06。 |
| **executionEnvironment** | 安全性 (索引子) | 明確地將此索引子設定屬性設為 `private`，以強制透過私人端點連線到外部資料來源。 僅適用於利用 Azure Private Link 的搜尋服務。 | 正式推出。 </br> 使用[搜尋 REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) 來設定此一般設定參數。 |

### <a name="may-2020-microsoft-build"></a>2020 年 5 月 (Microsoft 組建)

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 類別 | 描述 | 可用性  |
|---------|------------------|-------------|---------------|
| [**偵錯工作階段**](cognitive-search-debug-session.md) | AI 擴充 | 偵錯工作階段提供以入口網站為基礎的介面，用來調查和解決現有技能集的問題。 在偵錯工作階段中建立的修正程式可以儲存到生產環境技能集。 開始使用[本教學課程](cognitive-search-tutorial-debug-sessions.md)。 | 公開預覽，位在入口網站中。 |
| [**適用於內建防火牆支援的 IP 規則**](service-configure-firewall.md) | 安全性 | 將搜尋服務端點的存取限制為特定 IP 位址。 | 正式推出。 </br> 使用[管理 REST API 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) 或更新版本，或是入口網站。 |
| [**私人搜尋端點的 Azure Private Link**](service-create-private-endpoint.md) | 安全性| 藉由以私人連結資源的形式執行搜尋服務，可使其在公用網際網路中受到保護，並且只能讓相同虛擬網路上的用戶端應用程式和其他 Azure 服務存取。 | 正式推出。 </br> 使用[管理 REST API 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) 或更新版本，或是入口網站。 |
| [**系統管理的識別 (預覽)** ](search-howto-managed-identities-data-sources.md) | 安全性 (索引子) | 使用 Azure Active Directory 將搜尋服務註冊為受信任的服務，即可對支援的 Azure 資料來源設定連線來進行索引。 適用於來自 Azure 資料來源 (例如 Azure SQL Database、Azure Cosmos DB 和 Azure 儲存體) 內嵌內容的[索引子](search-indexer-overview.md)。 | 公開預覽。 </br> 使用入口網站來註冊搜尋服務。 |
| [**sessionId 查詢參數**](index-similarity-and-scoring.md)，[scoringStatistics=global 參數](index-similarity-and-scoring.md#scoring-statistics) | 查詢 (相關性) | 將 sessionID 新增至查詢，以建立計算搜尋分數的工作階段，並使用 scoringStatistics=global 來收集所有分區的分數，以提供更一致的搜尋分數計算。 | 正式推出。 </br> 使用[搜尋 REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) 或更新版本，或是 REST API 2019-05-06。 |
| [**featuresMode (預覽)** ](index-similarity-and-scoring.md#featuresMode-param) | 查詢 | 新增此查詢參數來展開相關性分數，可顯示更多詳細資料：每個欄位的相似性分數、每個欄位的字詞頻率，以及每個欄位的相符唯一語彙基元數目。 您可以使用自訂評分演算法中的這些資料點。 如需示範此功能的範例，請參閱[新增機器學習服務 (LearnToRank) 以搜尋相關性](https://github.com/Azure-Samples/search-ranking-tutorial)。 | 公開預覽。 </br> 使用[搜尋 REST API 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) 或 REST API 2019-05-06-Preview。 |

### <a name="march-2020"></a>2020 年 3 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 類別 | 描述 | 可用性  |
|---------|------------------|-------------|---------------|
| [**原生 Blob 虛刪除 (預覽)** ](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) | 索引子 | Azure 認知搜尋中的 Azure Blob 儲存體索引子會辨識處於虛刪除狀態的 Blob，並在編製索引期間移除對應的搜尋文件。 | 公開預覽。 </br> 使用[搜尋 REST API 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) 和 REST API 2019-05-06-Preview，針對已啟用原生「虛刪除」的 Azure Blob 資料來源執行索引子。 |
| [**管理 REST API (2020-03-13)** ](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) | REST | 建立和管理搜尋服務的新穩定 REST API。 新增 IP 防火牆和 Private Link 支援 | 正式推出。 |

### <a name="february-2020"></a>2020 年 2 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 類別 | 描述 | 可用性  |
|---------|------------------|-------------|---------------|
| [**PII 偵測 (預覽)** ](cognitive-search-skill-pii-detection.md) | AI 擴充 | 在編制索引期間使用的新認知技能，可從輸入文字中擷取個人識別資訊，並可讓您選擇以各種方式將該文字遮罩。 | 公開預覽。 </br> 使用入口網站或[搜尋 REST API 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) 或 REST API 2019-05-06-Preview。 |
| [**自訂實體查閱 (預覽)** ](cognitive-search-skill-custom-entity-lookup.md )| AI 擴充 | 新的認知技能，可從自訂、使用者定義的單字和片語清單中尋找文字。 使用這份清單，其會以任何相符的實體標記所有文件。 此技能也支援某種程度的模糊比對，可加以套用以尋找類似但不精確的相符項目。 | 公開預覽。 </br> 使用入口網站或[搜尋 REST API 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) 或 REST API 2019-05-06-Preview。 |

### <a name="january-2020"></a>2020 年 1 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 類別 | 描述 | 可用性  |
|---------|------------------|-------------|---------------|
| [**客戶管理的加密金鑰**](search-security-manage-encryption-keys.md) |安全性 | 除了平台的內建加密之外，新增額外的加密層。 藉由使用您建立和管理的加密金鑰，您可以在承載到達搜尋服務之前，先將索引內容和同義字對應加密。 | 正式推出。 </br> 使用搜尋 REST API 2019-05-06 或更新版本。 對於受控程式碼，即使功能並未納入預覽版中，正確的套件仍然是 [.NET SDK version 8.0-preview](search-dotnet-sdk-migration-version-9.md)。 |
| [**適用於內建防火牆支援的 IP 規則 (預覽)** ](service-configure-firewall.md) | 安全性 | 將搜尋服務端點的存取限制為特定 IP 位址。 預覽 API 在 [CreateOrUpdate API](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service) 中有新的 **IpRule** 和 **NetworkRuleSet** 屬性。 此預覽功能可在幾個區域中使用。 |  使用 api-version=2019-10-01-Preview 的公開預覽版。  |
| [**私人搜尋端點的 Azure Private Link (預覽)** ](service-create-private-endpoint.md) | 安全性| 藉由以私人連結資源的形式執行搜尋服務，可使其在公用網際網路中受到保護，並且只能讓相同虛擬網路上的用戶端應用程式和其他 Azure 服務存取。 | 使用 api-version=2019-10-01-Preview 的公開預覽版。  |

## <a name="feature-announcements-in-2019"></a>2019 的功能公告

### <a name="december-2019"></a>2019 年 12 月

+ [建立示範應用程式 (預覽)](search-create-app-portal.md) 是入口網站中的新精靈，會產生可下載 HTML 檔案，並且可對索引進行查詢 (唯讀) 存取。 檔案隨附的內嵌指令碼會轉譯可操作的 "localhost" 型 Web 應用程式，並繫結至搜尋服務上的索引。 頁面可在精靈中設定，而且可以包含搜尋列、結果區域、側邊欄導覽和自動提示查詢支援。 您可以離線修改 HTML，以擴充或自訂工作流程或外觀。 示範應用程式不容易擴充，難以包含通常在生產環境中所需的安全性和裝載層。 您應該將其視為驗證和測試工具，而不是完整用戶端應用程式的捷徑。

+ [建立安全連線的私人端點 (預覽)](service-create-private-endpoint.md) 說明如何設定私人連結，以安全地連線至您的搜尋服務。 您可要求使用此預覽功能，並使用 [Azure Private Link](../private-link/private-link-overview.md) 和 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)作為解決方案的一部分。

### <a name="november-2019---ignite-conference"></a>2019 年 11 月 - Ignite 會議

+ [增量擴充 (預覽)](cognitive-search-incremental-indexing-conceptual.md) 會將快取和具狀態新增至擴充管線，讓您可以處理特定步驟或階段，而不會遺失已處理的內容。 先前對擴充管線進行的任何變更都需要完整重建。 使用增量擴充時，會保留成本昂貴的分析輸出，特別是映像分析。

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [文件擷取 (預覽)](cognitive-search-skill-document-extraction.md) 是在編制索引期間使用的認知技能，可讓您從技能集中擷取檔案內容。 過去，只有在執行技能集之前才會破解文件。 現在新增了這項技能，您也可以在技能集執行期間執行此作業。

+ [文字翻譯](cognitive-search-skill-text-translation.md) 是在索引編制期間所使用的認知技能，可評估文字，並針對每筆記錄傳回翻譯為指定目標語言的文字。

+ [Power BI 範本](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) 可以快速開始建立視覺效果，並在 Power BI 桌面的知識存放區中分析豐富的內容。 此範本是針對透過 [匯入資料精靈](knowledge-store-create-portal.md) 建立的 Azure 資料表投影而設計。

+ 索引子目前支援 [Azure Data Lake Storage Gen2 (預覽)](search-howto-index-azure-data-lake-storage.md)、[Cosmos DB Gremlin API (預覽)](search-howto-index-cosmosdb.md) 和 [Cosmos DB Cassandra API (預覽)](search-howto-index-cosmosdb.md)。 您可以使用[此表單](https://aka.ms/azure-cognitive-search/indexer-preview)註冊。 一旦接受預覽方案，就會收到一封確認電子郵件。

### <a name="july-2019"></a>2019 年 7 月

+ 已在 [Azure Government 雲端](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search)中正式推出。

<a name="new-service-name"></a>

## <a name="new-service-name"></a>新服務名稱

Azure 搜尋服務現在已重新命名為 **Azure 認知搜尋**，以反映核心作業中已展開 (但為選用功能) 的認知技能和 AI 處理的使用方式。 API 版本、NuGet 套件、命名空間和端點都沒有變更。 新的和現有的搜尋解決方案不受服務名稱變更的影響。

## <a name="service-updates"></a>服務更新

您可以在 Azure 網站中找到 Azure 認知搜尋的[服務更新公告](https://azure.microsoft.com/updates/?product=search&status=all)。