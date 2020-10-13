---
title: 功能概觀
titleSuffix: Azure Cognitive Search
description: 探索 Azure 認知搜尋的功能類別。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: 286d826f61a6c99396ef04af0a0855567bc4d1e4
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951378"
---
# <a name="features-of-azure-cognitive-search"></a>Azure 認知搜尋的功能

Azure 認知搜尋提供全文搜尋引擎、持續性儲存體的搜尋索引、在編制索引期間使用的整合式 AI，以解壓縮更多文字和結構，以及 Api 和工具。 下表摘要列出依類別分類的功能。 如需認知搜尋與其他搜尋技術如何比較的詳細資訊，請參閱 [什麼是 Azure 認知搜尋？](search-what-is-azure-search.md)。

## <a name="indexing-features"></a>索引功能

| 類別&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 特性 |
|-------------------|----------|
| 資料來源 | 搜尋索引可以接受來自任何來源的文字，但前提是它是以 JSON 檔的形式提交。 <br/><br/> [**索引子**](search-indexer-overview.md) 會自動從支援的 Azure 資料來源內嵌資料，並處理 JSON 序列化。 連線 [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)、[Azure Cosmos DB](search-howto-index-cosmosdb.md) 或 [Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md)，以擷取主要資料存放區中可搜尋的內容。 Azure Blob 索引子可執行「文件破解」以[從主要檔案格式擷取文字](search-howto-indexing-azure-blob-storage.md)，包括 Microsoft Office、PDF 和 HTML 文件。 |
| 階層式和巢狀資料結構 | [**複雜**](search-howto-complex-data-types.md) 型別和集合可讓您在搜尋索引中建立幾乎任何類型 JSON 結構的模型。 一對多和多對多基數可以透過集合、複雜類型和複雜類型的集合，以原生方式表示。|
| 語言分析 | 分析器是在編製索引和搜尋作業期間用於文字處理的元件。 根據預設，您可以使用一般用途的標準 Lucene 分析器，或使用語言分析器、您設定的自訂分析器或其他預先定義的分析器（以您所需的格式產生權杖）來覆寫預設值。 <br/><br/>Lucene 或 Microsoft 的[**語言分析器**](index-add-language-analyzers.md) (英文) 可用來以智慧方式處理特定語言的語言學，包括動詞時態、性別、不規則複數名詞 (例如 ' mouse' 與 'mice')、拆解複合字、斷字 (針對沒有空格的語言) 等。 <br/><br/>[**自訂的詞法分析器**](index-add-custom-analyzers.md) 用於複雜的查詢表單，例如語音比對和正則運算式。<br/><br/> |

## <a name="ai-enrichment-and-knowledge-mining"></a>AI 擴充和知識挖掘

| 類別&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 特性 |
|-------------------|----------|
|在編制索引期間的 AI 處理 | 用於影像和文字分析的 [**AI 擴充**](cognitive-search-concept-intro.md)可以套用至索引管線，以便從原始內容擷取文字資訊。 [內建技巧](cognitive-search-predefined-skills.md)的範例包括光學字元辨識 (讓掃描過的 JPEG 可搜尋)、實體辨識 (識別組織、名稱或位置)，以及關鍵片語辨識。 您也可以[將自訂技巧編碼](cognitive-search-create-custom-skill-example.md)，以附加至管線。 您也可以[整合 Azure Machine Learning 撰寫技能](./cognitive-search-tutorial-aml-custom-skill.md)。 |
| 在非搜尋案例中儲存擴充的內容以供分析和取用 | [**知識存放區**](knowledge-store-concept-intro.md) 是索引管線的替代輸出。 您可以根據設定，將索引管線所建立的擴充檔傳送至儲存在 Azure Blob 儲存體或資料表儲存體中的知識存放區，而不是將 token 化詞彙傳送至索引。 知識存放區是從以 AI 為基礎的索引編制 (技能集) 所建立。 知識存放區的目的是要支援下游分析或處理。 使用知識存放區中的新資訊和結構，您可以將它附加至機器學習程式，或從 Power BI 連接以探索資料。<br/><br/> |
| 快取的內容 | 累加[**式擴充 (預覽) **](cognitive-search-incremental-indexing-conceptual.md)將處理變更為僅限對管線進行特定編輯變更的檔，並使用未變更之管線元件的快取內容。 |

## <a name="query-and-user-experience"></a>查詢和使用者體驗

| 類別&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 特性 |
|-------------------|----------|
|自由格式文字檢索 | [**全文檢索搜尋**](search-lucene-query-architecture.md)是大部分以搜尋為基礎之應用程式的主要使用案例。 您可以使用 Azure 搜尋服務支援的語法制訂查詢。 <br/><br/>[**簡單查詢語法**](query-simple-syntax.md)可提供邏輯運算子、片語搜尋運算子、後置運算子、優先順序運算子。<br/><br/>[**完整的 Lucene 查詢語法**](query-lucene-syntax.md) 包含簡單語法中的所有作業，包含模糊搜尋、鄰近搜尋、詞彙提升和正則運算式的擴充功能。|
| 相關性 | [**簡單評分**](index-add-scoring-profiles.md)是 Azure 認知搜尋的主要優點。 評分設定檔用於將相關性模型化為文件本身中的值函數。 例如，您可能想要新推出的產品或折扣的產品，出現在搜尋結果中較高的位置。 您也可以根據您所追蹤並個別儲存的客戶搜尋喜好設定，使用標記進行個人化計分來建置計分設定檔。 |
| 地區搜尋 | Azure 認知搜尋可處理、篩選和顯示地理位置。 可讓使用者根據搜尋結果與實體位置的鄰近程度來瀏覽資料。 如需詳細資訊，請[觀看此影片](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) (英文) 或[檢閱此範例](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) (英文)。 |
| 篩選與 Facet | 透過單一查詢參數便可啟用[**多面向導覽**](search-faceted-navigation.md) (英文)。 Azure 認知搜尋會傳回多面向導覽結構，可作為類別清單背後的程式碼，用於自我引導篩選 (例如，依價格範圍或品牌來篩選類別目錄項目)。 <br/><br/> [**篩選**](query-odata-filter-orderby-syntax.md) (英文) 可用於將多面向導覽納入應用程式的 UI、增強查詢編寫，以及根據使用者或開發人員指定的準則進行篩選。 請使用 OData 語法建立篩選。 |
| 使用者體驗 | 您可以在搜尋列中針對預先輸入的查詢啟用 [**自動完成**](search-autocomplete-tutorial.md)。 <br/><br/>[**搜尋建議**](/rest/api/searchservice/suggesters)也會在搜尋列中排除部分的文字輸入，但結果是您索引中的實際文件，而不是查詢字詞。 <br/><br/>與對等字詞相關聯的[**同義字**](search-synonyms.md)，讓使用者不必提供替代字詞，就能以隱含方式擴充查詢範圍。 <br/><br/>[**搜尋結果醒目提示**](/rest/api/searchservice/Search-Documents)會將文字格式套用至搜尋結果中的相符關鍵字。 您可以選擇哪些欄位傳回醒目提示的文字片段。<br/><br/>我們會透過索引結構描述針對多個欄位提供[**排序**](/rest/api/searchservice/Search-Documents) (英文)，然後使用單一搜尋參數在查詢階段進行切換。<br/><br/> 透過 Azure 認知搜尋為您的搜尋結果提供的微調控制項，可輕鬆地對搜尋結果進行[**分頁**](search-pagination-page-layout.md)和節流。  <br/><br/>|

## <a name="security-features"></a>安全性功能

| 類別&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 特性 |
|-------------------|----------|
| 資料加密 | [**Microsoft 管理的待用加密**](search-security-overview.md#encrypted-transmissions-and-storage)會內建到內部儲存層，而且無法回復。 <br/><br/>您在 Azure Key Vault 中建立和管理的[**客戶管理加密金鑰**](search-security-manage-encryption-keys.md)，可用於索引和同義字對應的補充加密。 針對在 1 2020 年8月之後建立的服務，CMK 加密可延伸至暫存磁片上的資料，以完整雙重加密索引內容。|
| Endpoint Protection | [**輸入防火牆支援的 ip 規則**](service-configure-firewall.md) 可讓您設定搜尋服務將接受要求的 ip 範圍。<br/><br/>使用 Azure Private Link[**建立私人端點**](service-create-private-endpoint.md)，以強制所有要求通過虛擬網路。 |
| 輸出安全性 (索引子) | [**透過私人端點的資料存取**](search-indexer-howto-access-private.md) 可讓索引子連接到透過 Azure Private Link 保護的 Azure 資源。<br/><br/>[**使用受信任身分識別的資料存取**](search-howto-managed-identities-data-sources.md) ，表示外部資料源的連接字串可以省略使用者名稱和密碼。 當索引子連接至資料來源時，如果搜尋服務先前註冊為受信任的服務，則資源會允許該連接。 |

## <a name="portal-features"></a>入口網站功能

| 類別&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 特性 |
|-------------------|----------|
| 用於原型設計和檢查的工具 | [[**新增索引**](search-what-is-an-index.md)] 是入口網站中的索引設計工具，可讓您用來建立包含屬性化欄位和一些其他設定的基本架構。 儲存索引之後，您可以使用 SDK 或 REST API 填入資料，以提供資料。 <br/><br/>匯[**入資料 wizard**](search-import-data-portal.md)會建立索引、索引子、技能集和資料來源定義。 如果您的資料存在於 Azure 中，此嚮導可以節省大量時間和精力，特別是針對概念證明的調查和探索。 <br/><br/>[**Search explorer**](search-explorer.md) 可用來測試查詢和精簡評分設定檔。<br/><br/>[**建立示範應用程式**](search-create-app-portal.md) 是用來產生可用於測試搜尋體驗的 HTML 頁面。  |
| 監視和診斷 | [**啟用監視功能**](search-monitor-usage.md)，使其超越永遠可以在入口網站看到，且一目了然的計量。 自動會擷取每秒查詢次數、延遲和節流的計量，並在入口網站頁面中報告，不需要其他設定。|

## <a name="programmability"></a>可程式性

| 類別&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 特性 |
|-------------------|----------|
| REST | [**服務 REST API**](/rest/api/searchservice/) 適用于資料平面作業，包括與編制索引、查詢和 AI 擴充相關的所有作業。 您也可以使用此用戶端程式庫來取出系統資訊和統計資料。 <br/><br/>[**管理 REST API**](/rest/api/searchmanagement/) 適用于服務建立和透過 Azure Resource Manager 進行清除。 您也可以使用此 API 來管理金鑰和布建服務。|
| 適用於 .NET 的 Azure SDK | [**Azure.Search.Documents**](/dotnet/api/overview/azure/search.documents-readme) 適用于資料平面作業，包括與索引、查詢和 AI 擴充相關的所有作業。 您也可以使用此用戶端程式庫來取出系統資訊和統計資料。 <br/><br/>[**管理。搜尋**](/dotnet/api/microsoft.azure.management.search) 是為了建立服務，並透過 Azure Resource Manager 進行清除。 您也可以使用此 API 來管理金鑰和布建服務。|
| Azure SDK for Java | [**com.azure.search.documents**](/java/api/com.azure.search.documents) 適用于資料平面作業，包括與索引、查詢和 AI 擴充相關的所有作業。 您也可以使用此用戶端程式庫來取出系統資訊和統計資料。 <br/><br/>[**azure. 管理。搜尋**](/java/api/overview/azure/search/management) 是為了建立服務，並透過 Azure Resource Manager 進行清除。 您也可以使用此 API 來管理金鑰和布建服務。|
| 適用於 Python 的 Azure SDK | [**azure-搜尋-檔**](/python/api/overview/azure/search-documents-readme) 適用于資料平面作業，包括與編制索引、查詢和 AI 擴充相關的所有作業。 您也可以使用此用戶端程式庫來取出系統資訊和統計資料。 <br/><br/>[**azure 管理-搜尋**](/python/api/overview/azure/search/management) 適用于服務建立和清除 Azure Resource Manager。 您也可以使用此 API 來管理金鑰和布建服務。 |
| 適用于 JavaScript/TypeScript 的 Azure SDK | [**azure/搜尋-檔**](/javascript/api/@azure/search-documents/) 適用于資料平面作業，包括與編制索引、查詢和 AI 擴充相關的所有作業。 您也可以使用此用戶端程式庫來取出系統資訊和統計資料。 <br/><br/>[**azure/arm-搜尋**](/javascript/api/@azure/arm-search/) 是為了建立服務，並透過 Azure Resource Manager 進行清除。 您也可以使用此 API 來管理金鑰和布建服務。 |

## <a name="see-also"></a>另請參閱

+ [認知搜尋的新功能](whats-new.md)

+ [認知搜尋中的預覽功能](search-api-preview.md)