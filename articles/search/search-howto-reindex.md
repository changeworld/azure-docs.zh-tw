---
title: 重建搜索索引
titleSuffix: Azure Cognitive Search
description: 添加新元素、更新現有元素或文檔，或刪除完全重建或部分索引中的過時文檔，以刷新 Azure 認知搜索索引。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 58b60a0eee8ab407709f33911d3c6b13ffbf301a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77498370"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>如何在 Azure 認知搜索中重建索引

本文介紹如何重建 Azure 認知搜索索引、需要重建的情況，以及用於緩解重建對持續查詢請求的影響的建議。

「重建」** 是指卸除並重新建立與索引 (包括所有欄位型反向索引) 相關聯的實體資料結構。 在 Azure 認知搜索中，不能刪除和重新創建單個欄位。 若要重建索引，必須刪除所有的欄位儲存體，根據現有的或修訂過的索引結構描述來重新建立，然後填入推送至索引的資料，或從外部來源提取的資料。 

在開發期間重建索引十分常見，但您可能也需要重建生產層級的索引以配合結構變更 (如新增複雜類型或新增欄位至建議工具等等)。

## <a name="rebuild-conditions"></a>重建條件

如果以下任一條件為 true，則刪除並重新創建索引。 

| 條件 | 描述 |
|-----------|-------------|
| 變更欄位定義 | 修改欄位名稱、資料類型或特定[索引屬性](https://docs.microsoft.com/rest/api/searchservice/create-index) \(英文\) (可搜尋、可篩選、可排序、可面向化) 需要完整重建。 |
| 將分析器指派給欄位 | [分析器](search-analyzers.md)定義在索引中，之後指派給欄位。 您可以隨時將新分析器定義新增至索引，但當欄位建立後，您只能*指派*分析器。 這適用於 **analyzer** 和 **indexAnalyzer** 屬性。 **searchAnalyzer** 屬性是例外狀況 (您可以將此屬性指派給現有欄位)。 |
| 更新或刪除索引中的分析器定義 | 您無法刪除或變更索引中的現有分析器組態 (分析器、權杖化工具、權杖篩選器或 char 篩選器)，除非您重建整個索引。 |
| 將欄位新增至建議工具 | 如果已有欄位存在，且您想要將它新增至[建議工具](index-add-suggesters.md)建構中，您必須重建索引。 |
| 刪除欄位 | 若要實體上移除欄位的所有追蹤，您必須重建索引。 當立即重新生成不可行時，您可以修改應用程式代碼以禁用對"已刪除"欄位的訪問，或使用[$select查詢參數](search-query-odata-select.md)選擇結果集中表示哪些欄位。 實體上，在您下次套用省略問題欄位的結構描述重建之前，欄位定義和內容都會保留在索引中。 |
| 切換階層 | 如果需要更多容量，Azure 門戶中沒有就地升級。 必須創建新服務，並且必須在新服務上從頭開始生成索引。 為了説明自動化此過程，可以在此[Azure 認知搜索 .NET 示例回購](https://github.com/Azure-Samples/azure-search-dotnet-samples)中使用**索引備份還原**示例代碼。 此應用程式將備份索引到一系列 JSON 檔，然後在指定的搜索服務中重新創建索引。|

## <a name="update-conditions"></a>更新條件

可以在不影響現有物理結構的情況下進行許多其他修改。 具體而言，以下更改*不需要*重新生成索引。 對於這些更改，您可以使用更改[更新索引定義](https://docs.microsoft.com/rest/api/searchservice/update-index)。

+ 新增欄位
+ 在現有欄位上設定 [retrievable]**** 屬性
+ 在現有欄位上設定 [searchAnalyzer]****
+ 在索引中新增新的分析器定義
+ 新增、更新或刪除評分設定檔
+ 新增、更新或刪除 CORS 設定
+ 新增、更新或刪除 synonymMaps

當您新增新的欄位時，現有已編製索引的文件會為新欄位提供 Null 值。 在將來的資料刷新時，來自外部來源資料的值將替換 Azure 認知搜索添加的 null。 如需有關更新索引內容的詳細資訊，請參閱[新增、更新或刪除文件](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) \(英文\)。

## <a name="how-to-rebuild-an-index"></a>如何重建索引

在開發過程中，索引架構經常更改。 您可以通過創建可以刪除、重新創建和使用小型代表性資料集快速重新載入的索引來規劃它。

對於已經在生產環境中的應用程式，我們建議您建立與現有索引並存執行的新索引，以避免查詢停機時間。 應用程式代碼提供重定向到新索引。

索引不會在後臺運行，服務將平衡附加索引與正在進行的查詢。 在索引期間，您可以監視門戶中的[查詢請求](search-monitor-queries.md)，以確保查詢及時完成。

1. 確定是否需要重建。 如果只是添加欄位，或更改與欄位無關的索引的某些部分，則可能只是[更新定義](https://docs.microsoft.com/rest/api/searchservice/update-index)而無需刪除、重新創建和完全重新載入它。

1. [獲取索引定義](https://docs.microsoft.com/rest/api/searchservice/get-index)，以用於將來參考。

1. [刪除現有索引](https://docs.microsoft.com/rest/api/searchservice/delete-index)，假定您沒有並排運行新舊索引。 

   目標是該索引的任何查詢都會立即被卸除。 請記住，刪除索引是不可逆的，會破壞欄位集合和其他構造的物理存儲。 在放棄之前，請停下來思考其含義。 

1. [創建修訂索引](https://docs.microsoft.com/rest/api/searchservice/create-index)，其中請求正文包括更改或修改的欄位定義。

1. 使用來自外部來源的[文件載入索引](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) \(英文\)。

當您建立索引時，系統會針對索引結構描述中的每個欄位配置實體儲存體，並為每個可搜尋欄位建立反向索引。 不可搜尋的欄位可用於篩選條件或運算式中，但不會有反向索引且不是可全文或模糊搜尋。 在重建的索引上，這些反向索引會被刪除並根據您提供的索引結構描述來重新建立。

當您載入索引時，每個欄位的反向索引都會填入來自每個文件的唯一 Token 化文字，且包含相對應文件識別碼的對應。 例如，編製旅館資料集的索引時，為 City 欄位建立的反向索引可能會包含 Seattle、Portland 等字詞。 City 欄位中包含 Seattle、Portland 之文件的文件識別碼會列在字詞旁邊。 在進行任何[新增、更新或刪除](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) \(英文\) 作業時，字詞和文件識別碼也會隨之更新。

> [!NOTE]
> 如果您有嚴格的 SLA 需求，您可以考慮專為這項工作佈建新服務，其中開發和索引編製完全獨立於生產環境索引。 個別的服務會在自己的硬體上執行，排除資源競爭的可能性。 開發完成後，您可以將新索引保留到位，將查詢重定向到新的終結點和索引，或者運行已完成的代碼以在原始 Azure 認知搜索服務上發佈修訂索引。 目前沒有機制能將準備好的索引移動到其他服務。

## <a name="check-for-updates"></a>檢查更新

第一個文件載入之後，您就可以開始查詢索引。 如果您知道文件的別碼，[查閱文件 REST API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) \(英文\) 可傳回特定文件。 若要進行更廣泛的測試，您應該等到索引完全載入，然後使用查詢來確認您預期會看到的內容。

您可以使用[搜索資源管理器](search-explorer.md)或像[Postman](search-get-started-postman.md)這樣的 Web 測試控管來檢查更新的內容。

如果添加或重命名欄位，請使用[$select](search-query-odata-select.md)返回該欄位：`search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>另請參閱

+ [索引子概觀](search-indexer-overview.md)
+ [大規模索引大型資料集](search-howto-large-index.md)
+ [在入口網站中編製索引](search-import-data-portal.md)
+ [Azure SQL Database 索引子](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB 索引子](search-howto-index-cosmosdb.md)
+ [Azure Blob 存儲索引子](search-howto-indexing-azure-blob-storage.md)
+ [Azure 表存儲索引子](search-howto-indexing-azure-tables.md)
+ [Azure 認知搜索的安全性](search-security-overview.md)