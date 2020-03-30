---
title: 使用 Azure 門戶將資料導入搜索索引
titleSuffix: Azure Cognitive Search
description: 了解如何在 Azure 入口網站中使用匯入資料精靈，以從 Cosmos DB、Blob 儲存體、表格儲存體、SQL Database 和 Azure VM 上的 SQL Server 對 Azure 資料進行編目。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0ed2bd7f1e03d8d5fa11f7e76010d087605f0fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460692"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>導入 Azure 認知搜索的資料嚮導

Azure 門戶在 Azure 認知搜索儀表板上提供**導入資料**嚮導，用於原型設計和載入索引。 本文介紹了使用嚮導、輸入和輸出以及一些使用資訊的優點和限制。 有關使用內置示例資料單一步驟嚮導的動手指導，請參閱使用 Azure 門戶快速入門[創建 Azure 認知搜索索引](search-get-started-portal.md)。

此嚮導執行的操作包括：

1 - 連接到受支援的 Azure 資料來源。

2 - 創建索引架構，通過採樣來源資料推斷。

3 - 可選地添加 AI 濃縮，以提取或生成內容和結構。

4 - 運行嚮導以創建物件、導入資料、設置計畫和其他配置選項。

該嚮導輸出許多保存到搜索服務的物件，您可以以程式設計方式或其他工具訪問這些物件。

## <a name="advantages-and-limitations"></a>優點和限制

在編寫任何代碼之前，可以使用嚮導進行原型設計和概念驗證測試。 該嚮導連接到外部資料源，對資料進行採樣以創建初始索引，然後將資料作為 JSON 文檔導入 Azure 認知搜索上的索引。 

採樣是推斷索引架構的過程，它有一些局限性。 創建資料來源時，嚮導將選取文檔示例，以確定哪些列是資料來源的一部分。 並非所有檔都被讀取，因為對於非常大的資料來源來說，這可能需要幾個小時。 給定文檔的選擇，源中繼資料（如欄位名稱或類型）用於在索引架構中創建欄位集合。 根據來源資料的複雜性，您可能需要編輯初始架構以確保準確性，或擴展它以達到完整性。 您可以在索引定義頁上內聯進行更改。

總體而言，使用嚮導的優勢顯而易見：只要滿足要求，就可以在幾分鐘內對可查詢索引進行原型設計。 索引的某些複雜性（如將資料作為 JSON 文檔提供）由嚮導處理。

已知限制匯總如下：

+ 該嚮導不支援反覆運算或重用。 每個傳遞嚮導都會創建新的索引、技能集和索引子配置。 只能在嚮導中持久化並重用資料來源。 若要編輯或優化其他物件，必須使用 REST API 或 .NET SDK 來檢索和修改結構。

+ 源內容必須駐留在受支援的 Azure 資料來源中。

+ 採樣超過來源資料的子集。 對於大型資料來源，嚮導可能會錯過欄位。 如果採樣不足，則可能需要擴展架構或更正推斷的資料類型。

+ 門戶中公開的 AI 擴充僅限於一些內置技能。 

+ [知識存儲](knowledge-store-concept-intro.md)（由嚮導創建）僅限於幾個預設投影。 如果要保存嚮導創建的富集文檔，Blob 容器和表附帶預設名稱和結構。

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>資料來源輸入

**導入資料**嚮導使用 Azure 認知搜索索引子提供的內部邏輯連接到外部資料源，該邏輯可用於對源進行採樣、讀取中繼資料、破解文檔以讀取內容和結構，並將內容序列化為 JSON，以便隨後導入 Azure 認知搜索。

只能從單個表、資料庫檢視或等效資料結構導入，但該結構可以包括分層或嵌套子結構。 有關詳細資訊，請參閱[如何對複雜類型建模](search-howto-complex-data-types.md)。

在運行嚮導之前，應創建此表或視圖，並且必須包含內容。 出於明顯的原因，在空資料來源上運行 **"導入資料**嚮導"沒有意義。

|  選取項目 | 描述 |
| ---------- | ----------- |
| **現有的資料來源** |如果搜索服務中已定義了索引子，則可能具有可重用的現有資料來源定義。 在 Azure 認知搜索中，資料來源物件僅由索引子使用。 您可以以程式設計方式或通過 **"導入資料嚮導"** 創建資料來源物件，並根據需要重用它們。|
| **樣品**| Azure 認知搜索提供了兩個內置示例資料來源，這些資料來源用於教程和快速入門：房地產 SQL 資料庫和託管在 Cosmos DB 上的酒店資料庫。 有關基於"Hotels"示例的演練，請參閱[Azure 門戶快速入門中的創建索引](search-get-started-portal.md)。 |
| [**Azure SQL Database**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |您可以在頁面上或透過 ADO.NET 連接字串指定服務名稱、具有讀取權限的資料庫使用者認證以及資料庫名稱。 選擇連接字串選項以檢視或自訂屬性。 <br/><br/>提供資料列集的資料表或檢視必須在頁面上指定。 連線成功後會出現此選項，並透過下拉式清單供您選取。|
| **Azure VM 上的 SQL 伺服器** |指定完整服務名稱、使用者識別碼與密碼以及資料庫作為連接字串。 若要使用此資料來源，您必須先前就已在本機存放區中安裝用來加密連線的憑證。 有關說明，請參閱[SQL VM 連接到 Azure 認知搜索](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)。 <br/><br/>提供資料列集的資料表或檢視必須在頁面上指定。 連線成功後會出現此選項，並透過下拉式清單供您選取。 |
| [**Azure 宇宙資料庫**](search-howto-index-cosmosdb.md)|需求包括帳戶、資料庫和集合。 集合中的所有文件將會包含在索引中。 您可以定義要拼平或篩選行集的查詢，或者將查詢留空。 此嚮導中不需要查詢。|
| [**Azure Blob 存儲**](search-howto-indexing-azure-blob-storage.md) |需求包括儲存體帳戶和容器。 如果 Blob 名稱為了分組而遵循虛擬命名慣例，您可以指定名稱的虛擬目錄部分做為容器下的資料夾 (選擇性)。 如需詳細資訊，請參閱[為 Blob 儲存體編製索引](search-howto-indexing-azure-blob-storage.md)。 |
| [**Azure 表存儲**](search-howto-indexing-azure-tables.md) |需求包括儲存體帳戶和資料表名稱。 您可以指定要擷取資料表子集的查詢 (選擇性)。 如需詳細資訊，請參閱[為表格儲存體編製索引](search-howto-indexing-azure-tables.md)。 |

## <a name="wizard-output"></a>嚮導輸出

在後臺，嚮導創建、配置和調用以下物件。 嚮導運行後，您可以在門戶頁中找到其輸出。 服務的"概述"頁包含索引、索引子、資料來源和技能集的清單。 索引定義可以在門戶中以完整的 JSON 查看。 對於其他定義，可以使用[REST API](https://docs.microsoft.com/rest/api/searchservice/)獲取特定物件。

| Object | 描述 | 
|--------|-------------|
| [資料來源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | 將連接資訊保留到來源資料（包括憑據）。 資料來源物件僅與索引子一起使用。 | 
| [索引](https://docs.microsoft.com/rest/api/searchservice/create-index) | 用於全文檢索搜尋和其他查詢的物理資料結構。 | 
| [技能集](https://docs.microsoft.com/rest/api/searchservice/create-skillset) | 一套完整的指令，用於操作、轉換和塑造內容，包括分析和從影像檔中提取資訊。 除了非常簡單和有限的結構外，它還包括對提供豐富功能的認知服務資源的引用。 或者，它還可能包含知識存儲定義。  | 
| [索引子](https://docs.microsoft.com/rest/api/searchservice/create-indexer)  | 指定資料來源、目標索引、可選技能集、可選計畫以及用於錯誤處理和 Base-64 編碼的可選配置設置的設定物件。 |


## <a name="how-to-start-the-wizard"></a>如何啟動嚮導

導入資料嚮導從服務概述頁上的命令列啟動。

1. 在 [Azure 入口網站](https://portal.azure.com)中，從儀表板開啟搜尋服務分頁，或在服務清單中[尋找服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

2. 在頂端的 [服務概觀] 頁面中，按一下 [匯入資料]****。

   ![門戶中的導入資料命令](./media/search-import-data-portal/import-data-cmd2.png "啟動匯入資料精靈")

還可以從其他 Azure 服務（包括 Azure Cosmos DB、Azure SQL 資料庫和 Azure Blob 存儲）啟動**導入資料**。 在服務概述頁上的左側功能窗格中查找 **"添加 Azure 認知搜索**"。

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>如何在嚮導中編輯或完成索引架構

嚮導生成不完整的索引，該索引將填充從輸入資料來源獲取的文檔。 如需功能索引，請確定您已定義下列項目。

1. 欄位清單是否完整？ 添加未採樣的新欄位，並刪除任何不向搜索體驗添加值或不會在[篩選器運算式](search-query-odata-filter.md)或[評分設定檔](index-add-scoring-profiles.md)中使用的欄位。

1. 資料類型是否適合傳入資料？ Azure 認知搜索支援[實體資料模型 （EDM） 資料類型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)。 對於 Azure SQL 資料，有佈局等效值的[映射圖表](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping)。 有關更多背景，請參閱[欄位映射和轉換](search-indexer-field-mappings.md)。

1. 你有一個欄位可以作為*關鍵*？ 此欄位必須是 Edm.string，並且必須唯一標識文檔。 對於關係資料，它可能映射到主鍵。 對於 blob，它可能是 。 `metadata-storage-path` 如果欄位值包含空格或連字號，您必須在 [建立索引子]**** 步驟的 [進階選項]**** 底下設定 [Base-64 編碼金鑰]**** 選項，以隱藏這些字元的驗證檢查。

1. 設置屬性以確定如何在索引中使用該欄位。 

   使用此步驟，因為屬性決定了索引中欄位的物理運算式。 如果以後（甚至是以程式設計方式）更改屬性，幾乎總是需要刪除並重建索引。 核心屬性（如**可搜索**和**可檢索）**[對存儲的影響可以忽略不計](search-what-is-an-index.md#index-size)。 啟用篩選器和使用建議器會增加存儲要求。 
   
   + **可搜索**支援全文檢索搜尋。 用於自由格式查詢或查詢運算式的每個欄位都必須有這個屬性。 針對您標示為 **Searchable** 的每個欄位，系統會建立反向索引。

   + **可檢索返回**搜尋結果中的欄位。 提供內容來搜尋結果的每個欄位都必須有這個屬性。 設定此欄位對索引大小的影響很小。

   + **可篩選**允許在篩選器運算式中引用該欄位。 用於 **$filter** 運算式的每個欄位都必須有這個屬性。 篩選條件運算式會用來進行精準比對。 因為文字字串會維持不變，所以需要額外的儲存體才能容納逐字的內容。

   + **面表**啟用分面導航欄位。 只有同時標示為 **Filterable** 的欄位才可以標示為 **Facetable**。

   + **可排序**允許該欄位在排序中使用。 用於 **$Orderby** 運算式的每個欄位都必須有這個屬性。

1. 你需要[詞法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)嗎？ 對於**可搜索**的 Edm.字串欄位，如果需要語言增強的索引和查詢，可以設置**分析器**。 

   預設值是「標準 Lucene」**，但如果您想要使用 Microsoft 的分析器來進行進階的語彙處理 (例如，解析不規則的名詞和動詞形式)，則可以選擇「Microsoft 英文」**。 只能在門戶中指定語言分析器。 使用自訂分析器或非語言分析器（如關鍵字、模式等）必須以程式設計方式完成。 有關分析器的詳細資訊，請參閱[添加語言分析器](search-language-support.md)。

1. 您是否需要自動完成或建議結果形式的排位前行功能？ 選擇**建議程式**核取方塊以啟用[排位前查詢建議，並在所選欄位中自動完成](index-add-suggesters.md)。 建議者會增加索引中的權杖化術語數，從而消耗更多的存儲空間。


## <a name="next-steps"></a>後續步驟

瞭解嚮導的優點和局限性的最佳方法是逐步完成它。 以下快速入門將指導您完成每個步驟。

> [!div class="nextstepaction"]
> [使用 Azure 門戶創建 Azure 認知搜索索引](search-get-started-portal.md)