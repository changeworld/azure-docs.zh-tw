---
title: 使用 Azure 入口網站將資料匯入搜尋索引
titleSuffix: Azure Cognitive Search
description: 瞭解如何使用 Azure 入口網站中的 [匯入資料] wizard，從 Cosmos DB、Blob 儲存體、資料表儲存體、SQL Database、SQL 受控執行個體和 Azure Vm 上的 SQL Server 編目 Azure 資料。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8d786f1ebadc961ab367fdcc9b27c4d829a68400
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85321376"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Azure 認知搜尋的匯入資料嚮導

此 Azure 入口網站會在 Azure 認知搜尋儀表板上提供 [匯**入資料**] 嚮導，用於原型化和載入索引。 本文涵蓋使用 wizard、輸入和輸出，以及一些使用資訊的優點和限制。 如需使用內建範例資料逐步執行嚮導的實際操作指南，請參閱[使用 Azure 入口網站快速入門建立 Azure 認知搜尋索引](search-get-started-portal.md)。

此 wizard 執行的作業包括：

1-連接到支援的 Azure 資料來源。

2-建立索引架構，並藉由取樣來源資料來推斷。

3-（選擇性）新增 AI 擴充以解壓縮或產生內容和結構。

4-執行嚮導來建立物件、匯入資料、設定排程和其他設定選項。

此 wizard 會輸出一些儲存到您搜尋服務的物件，您可以用程式設計方式或在其他工具中存取。

## <a name="advantages-and-limitations"></a>優點和限制

撰寫任何程式碼之前，您可以使用 wizard 來進行原型設計和概念證明測試。 此 wizard 會連接到外部資料源、範例資料以建立初始索引，然後將資料當做 JSON 檔匯入 Azure 認知搜尋的索引中。 

取樣是一種用來推斷索引架構的進程，而且有一些限制。 建立資料來源時，嚮導會挑選檔範例，以決定哪些資料行是資料來源的一部分。 並非所有檔案都是讀取的，因為非常大的資料來源可能需要數小時的時間。 指定檔的選項時，會使用來源中繼資料（例如功能變數名稱或類型）在索引架構中建立 fields 集合。 視來源資料的複雜度而定，您可能需要編輯初始架構以取得精確度，或將其延伸以確保完整性。 您可以在 [索引定義] 頁面上以內嵌方式進行變更。

整體來說，使用 wizard 的優點很明顯：只要符合需求，您就可以在幾分鐘內建立可查詢的索引原型。 編制索引的一些複雜性（例如提供 JSON 檔的資料）是由 wizard 處理。

已知的限制摘要如下：

+ 此 wizard 不支援反復專案或重複使用。 每次通過 wizard 時，都會建立新的索引、技能集和索引子設定。 只有資料來源可以保存並在嚮導內重複使用。 若要編輯或精簡其他物件，您必須使用 REST Api 或 .NET SDK 來取出和修改結構。

+ 來源內容必須位於支援的 Azure 資料來源中。

+ 取樣是針對來源資料的子集。 對於大型資料來源，嚮導可能會遺漏欄位。 如果取樣不足，您可能需要擴充架構，或更正推斷的資料類型。

+ 在入口網站中公開的 AI 擴充僅限於幾項內建技能。 

+ 可由 wizard 建立的[知識存放區](knowledge-store-concept-intro.md)僅限於少數的預設投射。 如果您想要儲存由嚮導所建立的擴充檔，則 blob 容器和資料表會隨附預設名稱和結構。

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>資料來源輸入

「匯**入資料**」嚮導會使用 Azure 認知搜尋索引子所提供的內部邏輯來連接到外部資料源，這些是配備來取樣來源、讀取中繼資料、破解檔以讀取內容和結構，以及將內容序列化為 JSON 以進行後續匯入至 Azure 認知搜尋。

您只能從單一資料表、資料庫檢視或對等的資料結構匯入，但結構可以包含階層式或嵌套子結構。 如需詳細資訊，請參閱[如何建立複雜類型的模型](search-howto-complex-data-types.md)。

在執行 wizard 之前，您應該先建立這個單一資料表或視圖，而且它必須包含內容。 基於明顯的理由，在空的資料來源上執行「匯**入資料**」 wizard 沒有意義。

|  選取項目 | 描述 |
| ---------- | ----------- |
| **現有的資料來源** |如果您已經在搜尋服務中定義索引子，您可能會有可以重複使用的現有資料來源定義。 在 Azure 認知搜尋中，資料來源物件只會由索引子使用。 您可以用程式設計方式或透過 [匯**入資料**] wizard 來建立資料來源物件，並視需要加以重複使用。|
| **範例**| Azure 認知搜尋提供兩個內建的範例資料來源，用於教學課程和快速入門： Cosmos DB 上的房地產 SQL database 和飯店資料庫。 如需以飯店範例為基礎的逐步解說，請參閱 Azure 入口網站快速入門[中的建立索引](search-get-started-portal.md)。 |
| [**Azure SQL Database 或 SQL 受控執行個體**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |您可以在頁面上或透過 ADO.NET 連接字串指定服務名稱、具有讀取權限的資料庫使用者認證以及資料庫名稱。 選擇連接字串選項以檢視或自訂屬性。 <br/><br/>提供資料列集的資料表或檢視必須在頁面上指定。 連線成功後會出現此選項，並透過下拉式清單供您選取。|
| **Azure VM 上的 SQL Server** |指定完整服務名稱、使用者識別碼與密碼以及資料庫作為連接字串。 若要使用此資料來源，您必須先前就已在本機存放區中安裝用來加密連線的憑證。 如需指示，請參閱[Azure 認知搜尋的 SQL VM](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)連線。 <br/><br/>提供資料列集的資料表或檢視必須在頁面上指定。 連線成功後會出現此選項，並透過下拉式清單供您選取。 |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|需求包括帳戶、資料庫和集合。 集合中的所有文件將會包含在索引中。 您可以定義要壓平合併或篩選資料列集的查詢，或將查詢保留空白。 在此 wizard 中不需要查詢。|
| [**Azure Blob 儲存體**](search-howto-indexing-azure-blob-storage.md) |需求包括儲存體帳戶和容器。 如果 Blob 名稱為了分組而遵循虛擬命名慣例，您可以指定名稱的虛擬目錄部分做為容器下的資料夾 (選擇性)。 如需詳細資訊，請參閱[為 Blob 儲存體編製索引](search-howto-indexing-azure-blob-storage.md)。 |
| [**Azure 表格儲存體**](search-howto-indexing-azure-tables.md) |需求包括儲存體帳戶和資料表名稱。 您可以指定要擷取資料表子集的查詢 (選擇性)。 如需詳細資訊，請參閱[為表格儲存體編製索引](search-howto-indexing-azure-tables.md)。 |

## <a name="wizard-output"></a>Wizard 輸出

在幕後，嚮導會建立、設定和叫用下列物件。 在嚮導執行之後，您可以在入口網站頁面中找到其輸出。 服務的 [總覽] 頁面包含索引、索引子、資料來源和技能集的清單。 您可以在入口網站中以完整 JSON 來查看索引定義。 針對其他定義，您可以使用[REST API](https://docs.microsoft.com/rest/api/searchservice/)來取得特定物件。

| Object | Description | 
|--------|-------------|
| [資料來源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | 將連接資訊保存到來源資料，包括認證。 資料來源物件是以獨佔方式使用於索引子。 | 
| [索引](https://docs.microsoft.com/rest/api/searchservice/create-index) | 用於全文檢索搜尋和其他查詢的實體資料結構。 | 
| [技能集](https://docs.microsoft.com/rest/api/searchservice/create-skillset) | 一組完整的指示，用於操作、轉換和塑造內容，包括從影像檔案分析和解壓縮資訊。 除了非常簡單且有限的結構之外，它還包含提供擴充之認知服務資源的參考。 或者，它也可以包含知識存放區定義。  | 
| [索引子](https://docs.microsoft.com/rest/api/searchservice/create-indexer)  | 設定物件，指定資料來源、目標索引、選擇性的技能集、選擇性的排程，以及錯誤處理和以64編碼的選擇性配置設定。 |


## <a name="how-to-start-the-wizard"></a>如何啟動精靈

[匯入資料] wizard 會從 [服務總覽] 頁面上的命令列啟動。

1. 在 [Azure 入口網站](https://portal.azure.com)中，從儀表板開啟搜尋服務分頁，或在服務清單中[尋找服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

2. 在頂端的 [服務概觀] 頁面中，按一下 [匯入資料]****。

   ![入口網站中的匯入資料命令](./media/search-import-data-portal/import-data-cmd2.png "啟動匯入資料精靈")

您也可以從其他 Azure 服務啟動匯**入資料**，包括 Azure Cosmos DB、AZURE SQL DATABASE、SQL 受控執行個體和 Azure Blob 儲存體。 在 [服務總覽] 頁面的左側導覽窗格中，尋找 [**新增 Azure 認知搜尋**]。

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>如何在 wizard 中編輯或完成索引架構

Wizard 會產生不完整的索引，其中會填入從輸入資料來源取得的檔。 如需功能索引，請確定您已定義下列項目。

1. 欄位清單是否完成？ 加入取樣遺漏的新欄位，並移除任何不會將值加入搜尋體驗中，或不會在[篩選運算式](search-query-odata-filter.md)或[評分設定檔](index-add-scoring-profiles.md)中使用的欄位。

1. 資料類型是否適用于傳入資料？ Azure 認知搜尋支援[entity data model （EDM）資料類型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)。 針對 Azure SQL 資料，有對應的對應[圖表](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping)會配置對等的值。 如需更多背景，請參閱[欄位對應和轉換](search-indexer-field-mappings.md)。

1. 您有一個可作為*金鑰*的欄位嗎？ 此欄位必須是 Edm 字串，而且必須唯一識別檔。 對於關聯式資料，它可能會對應到主鍵。 對於 blob，它可能是 `metadata-storage-path` 。 如果欄位值包含空格或連字號，您必須在 [建立索引子]**** 步驟的 [進階選項]**** 底下設定 [Base-64 編碼金鑰]**** 選項，以隱藏這些字元的驗證檢查。

1. 設定屬性，以決定該欄位在索引中的使用方式。 

   請花時間執行此步驟，因為屬性會決定索引中欄位的實體運算式。 如果您稍後想要變更屬性，甚至以程式設計方式，您幾乎都需要卸載並重建索引。 可搜尋和可抓取**的核心****屬性，對**[儲存體的影響](search-what-is-an-index.md#index-size)也不明顯。 啟用篩選器並使用建議工具會增加儲存需求。 
   
   + **可搜尋**啟用全文檢索搜尋。 用於自由格式查詢或查詢運算式的每個欄位都必須有這個屬性。 針對您標示為 **Searchable** 的每個欄位，系統會建立反向索引。

   + **Retrievable** 「已抓取」會在搜尋結果中傳回欄位。 提供內容來搜尋結果的每個欄位都必須有這個屬性。 設定此欄位對索引大小的影響很小。

   + 可**篩選**：允許在篩選運算式中參考欄位。 用於 **$filter** 運算式的每個欄位都必須有這個屬性。 篩選條件運算式會用來進行精準比對。 因為文字字串會維持不變，所以需要額外的儲存體才能容納逐字的內容。

   + **Facetable**可讓欄位進行多面向導覽。 只有同時標示為 **Filterable** 的欄位才可以標示為 **Facetable**。

   + 可**排序**允許欄位用於排序。 用於 **$Orderby** 運算式的每個欄位都必須有這個屬性。

1. 您需要[詞法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)嗎？ 針對可搜尋的 Edm 字串欄位 **，如果**您想要使用語言增強的編制索引和查詢，您可以設定**分析器**。 

   預設值是「標準 Lucene」**，但如果您想要使用 Microsoft 的分析器來進行進階的語彙處理 (例如，解析不規則的名詞和動詞形式)，則可以選擇「Microsoft 英文」**。 只有語言分析器可以在入口網站中指定。 使用自訂分析器或非語言分析器（例如關鍵字、模式等等），必須以程式設計方式完成。 如需分析器的詳細資訊，請參閱[新增語言分析器](search-language-support.md)。

1. 您需要自動完成或建議結果形式的自動提示功能嗎？ 選取 [**建議工具**] 核取方塊，以在選取的欄位上啟用 [[自動提示查詢建議] 和 [自動完成](index-add-suggesters.md)]。 建議工具會將新增至索引中的 token 化詞彙數目，因此會耗用更多的儲存空間。


## <a name="next-steps"></a>後續步驟

瞭解 wizard 優點和限制的最佳方式，就是逐步執行它。 下列快速入門會引導您完成每個步驟。

> [!div class="nextstepaction"]
> [使用 Azure 入口網站建立 Azure 認知搜尋索引](search-get-started-portal.md)