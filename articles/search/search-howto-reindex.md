---
title: 重建搜尋索引
titleSuffix: Azure Cognitive Search
description: 加入新的專案、更新現有的專案或檔，或刪除完整重建或部分索引的過時檔，以重新整理 Azure 認知搜尋索引。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 58b60a0eee8ab407709f33911d3c6b13ffbf301a
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77498370"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中重建索引

本文說明如何重建 Azure 認知搜尋索引、需要重建的情況，以及減輕重建對進行中查詢要求之影響的建議。

「重建」是指卸除並重新建立與索引 (包括所有欄位型反向索引) 相關聯的實體資料結構。 在 Azure 認知搜尋中，您無法卸載並重新建立個別的欄位。 若要重建索引，必須刪除所有的欄位儲存體，根據現有的或修訂過的索引結構描述來重新建立，然後填入推送至索引的資料，或從外部來源提取的資料。 

在開發期間重建索引十分常見，但您可能也需要重建生產層級的索引以配合結構變更 (如新增複雜類型或新增欄位至建議工具等等)。

## <a name="rebuild-conditions"></a>重建條件

如果下列任一條件成立，請卸載並重新建立索引。 

| 條件 | 描述 |
|-----------|-------------|
| 變更欄位定義 | 修改欄位名稱、資料類型或特定[索引屬性](https://docs.microsoft.com/rest/api/searchservice/create-index) \(英文\) (可搜尋、可篩選、可排序、可面向化) 需要完整重建。 |
| 將分析器指派給欄位 | [分析器](search-analyzers.md)定義在索引中，之後指派給欄位。 您可以隨時將新分析器定義新增至索引，但當欄位建立後，您只能*指派*分析器。 這適用於 **analyzer** 和 **indexAnalyzer** 屬性。 **searchAnalyzer** 屬性是例外狀況 (您可以將此屬性指派給現有欄位)。 |
| 更新或刪除索引中的分析器定義 | 您無法刪除或變更索引中的現有分析器組態 (分析器、權杖化工具、權杖篩選器或 char 篩選器)，除非您重建整個索引。 |
| 將欄位新增至建議工具 | 如果已有欄位存在，且您想要將它新增至[建議工具](index-add-suggesters.md)建構中，您必須重建索引。 |
| 刪除欄位 | 若要實體上移除欄位的所有追蹤，您必須重建索引。 當立即重建不可行時，您可以修改應用程式代碼來停用對「已刪除」欄位的存取，或使用[$select 查詢參數](search-query-odata-select.md)來選擇要在結果集中表示的欄位。 實體上，在您下次套用省略問題欄位的結構描述重建之前，欄位定義和內容都會保留在索引中。 |
| 切換階層 | 如果您需要更多容量，Azure 入口網站中沒有就地升級。 必須建立新的服務，而且必須在新的服務上從頭開始建立索引。 若要協助自動化此程式，您可以使用此[Azure 認知搜尋 .net 範例](https://github.com/Azure-Samples/azure-search-dotnet-samples)存放庫中的**索引備份-還原範例程式**代碼。 此應用程式會將您的索引備份至一系列 JSON 檔案，然後在您指定的搜尋服務中重新建立索引。|

## <a name="update-conditions"></a>更新條件

許多其他修改都可以進行，而不會影響現有的實體結構。 具體而言，下列變更*不*需要重建索引。 針對這些變更，您可以使用您的變更來[更新索引定義](https://docs.microsoft.com/rest/api/searchservice/update-index)。

+ 新增欄位
+ 在現有欄位上設定 [retrievable] 屬性
+ 在現有欄位上設定 [searchAnalyzer]
+ 在索引中新增新的分析器定義
+ 新增、更新或刪除評分設定檔
+ 新增、更新或刪除 CORS 設定
+ 新增、更新或刪除 synonymMaps

當您新增新的欄位時，現有已編製索引的文件會為新欄位提供 Null 值。 在未來的資料重新整理中，來自外部來源資料的值會取代 Azure 認知搜尋新增的 null。 如需有關更新索引內容的詳細資訊，請參閱[新增、更新或刪除文件](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) \(英文\)。

## <a name="how-to-rebuild-an-index"></a>如何重建索引

在開發期間，索引架構經常變更。 您可以藉由建立可使用小型代表性資料集快速刪除、重新建立及重載的索引來進行規劃。

對於已經在生產環境中的應用程式，我們建議您建立與現有索引並存執行的新索引，以避免查詢停機時間。 您的應用程式程式碼會提供重新導向至新的索引。

索引編制不會在背景中執行，而服務將會針對進行中的查詢，平衡額外的索引編制。 在編制索引期間，您可以在入口網站中[監視查詢要求](search-monitor-queries.md)，以確保查詢會及時完成。

1. 判斷是否需要重建。 如果您只是加入欄位，或變更索引中與欄位無關的部分，您可以直接[更新定義](https://docs.microsoft.com/rest/api/searchservice/update-index)，而不需要刪除、重新建立和完全重載。

1. [取得索引定義](https://docs.microsoft.com/rest/api/searchservice/get-index)，以防您需要它以供日後參考。

1. 卸載[現有的索引](https://docs.microsoft.com/rest/api/searchservice/delete-index)，假設您不會並存執行新的和舊的索引。 

   目標是該索引的任何查詢都會立即被卸除。 請記住，刪除索引是無法復原的，而是終結 fields 集合和其他結構的實體儲存體。 在卸載之前，先暫停以思考含意。 

1. [建立修訂的索引](https://docs.microsoft.com/rest/api/searchservice/create-index)，其中要求的主體包含已變更或已修改的欄位定義。

1. 使用來自外部來源的[文件載入索引](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) \(英文\)。

當您建立索引時，系統會針對索引結構描述中的每個欄位配置實體儲存體，並為每個可搜尋欄位建立反向索引。 不可搜尋的欄位可用於篩選條件或運算式中，但不會有反向索引且不是可全文或模糊搜尋。 在重建的索引上，這些反向索引會被刪除並根據您提供的索引結構描述來重新建立。

當您載入索引時，每個欄位的反向索引都會填入來自每個文件的唯一 Token 化文字，且包含相對應文件識別碼的對應。 例如，編製旅館資料集的索引時，為 City 欄位建立的反向索引可能會包含 Seattle、Portland 等字詞。 City 欄位中包含 Seattle、Portland 之文件的文件識別碼會列在字詞旁邊。 在進行任何[新增、更新或刪除](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) \(英文\) 作業時，字詞和文件識別碼也會隨之更新。

> [!NOTE]
> 如果您有嚴格的 SLA 需求，您可以考慮專為這項工作佈建新服務，其中開發和索引編製完全獨立於生產環境索引。 個別的服務會在自己的硬體上執行，排除資源競爭的可能性。 開發完成時，您可以保留新索引、將查詢重新導向至新的端點和索引，或執行已完成的程式碼，以在原始的 Azure 認知搜尋服務上發佈修改過的索引。 目前沒有機制能將準備好的索引移動到其他服務。

## <a name="check-for-updates"></a>檢查更新

第一個文件載入之後，您就可以開始查詢索引。 如果您知道文件的別碼，[查閱文件 REST API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) \(英文\) 可傳回特定文件。 若要進行更廣泛的測試，您應該等到索引完全載入，然後使用查詢來確認您預期會看到的內容。

您可以使用[搜尋瀏覽器](search-explorer.md)或 Web 測試控管（例如[Postman](search-get-started-postman.md) ）來檢查是否有更新的內容。

如果您已新增或重新命名欄位，請使用[$select](search-query-odata-select.md)來傳回該欄位： `search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>另請參閱

+ [索引子概觀](search-indexer-overview.md)
+ [大規模索引大型資料集](search-howto-large-index.md)
+ [在入口網站中編製索引](search-import-data-portal.md)
+ [Azure SQL Database 索引子](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB 索引子](search-howto-index-cosmosdb.md)
+ [Azure Blob 儲存體索引子](search-howto-indexing-azure-blob-storage.md)
+ [Azure 資料表儲存體索引子](search-howto-indexing-azure-tables.md)
+ [Azure 認知搜尋中的安全性](search-security-overview.md)