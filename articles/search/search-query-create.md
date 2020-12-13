---
title: 建立基本查詢
titleSuffix: Azure Cognitive Search
description: 瞭解如何在認知搜尋中建立查詢要求、測試和程式碼所使用的工具和 Api，以及從索引設計開始查詢決策的方式。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ace887396bacf264f0ffbd186ef1349e96496786
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371123"
---
# <a name="create-a-basic-query-in-azure-cognitive-search"></a>在 Azure 認知搜尋中建立基本查詢

本文將逐步說明查詢結構。 例如，您可以使用 Postman 或 Visual Studio 程式碼，以互動方式將字串複製到入口網站中的 **搜尋 Explorer** ，或是以互動方式建立查詢。 您可以針對本文中的範例使用任何一層或版本的認知搜尋。

## <a name="choose-a-tool-or-api"></a>選擇工具或 API

從下列工具和 Api 中選擇，以建立測試或生產工作負載的查詢。

| 方法 | 說明 |
|-------------|-------------|
| 入口網站| [Search explorer (入口網站) ](search-explorer.md) 提供搜尋列和索引和 api 版本選取專案的選項。 結果會以 JSON 文件的形式傳回。 建議進行早期調查、測試和驗證。 <br/>[深入了解。](search-explorer.md) |
| Web 測試控管| [Postman 或 Visual Studio Code](search-get-started-rest.md) 是用來編寫 [搜尋檔](/rest/api/searchservice/search-documents) REST 呼叫的強大選擇。 REST API 支援 Azure 認知搜尋中的每個程式設計作業，因此您可以在投資程式碼之前，以互動方式發出要求，以瞭解其運作方式。  |
| Azure SDK | [SearchClient ( .net) ](/dotnet/api/azure.search.documents.searchclient) 可以用來查詢 c # 中的搜尋索引。  [深入了解。](search-howto-dotnet-sdk.md) <br/><br/>[SearchClient (python) ](/dotnet/api/azure.search.documents.searchclient) 可以用來查詢 python 中的搜尋索引。 [深入了解。](search-get-started-python.md) <br/><br/> 您可以使用[SearchClient (javascript) ](/dotnet/api/azure.search.documents.searchclient) ，以 javascript 查詢搜尋索引。 [深入了解。](search-get-started-javascript.md)  |

## <a name="set-up-a-search-client"></a>設定搜尋用戶端

搜尋用戶端會向搜尋服務進行驗證、傳送要求和處理回應。 查詢一律會導向單一索引的檔集合。 您無法加入索引，或建立自訂或暫存資料結構作為查詢目標。

### <a name="in-the-portal"></a>在入口網站中

Search Explorer 和其他入口網站工具都有服務的內建用戶端連線，其中包含直接存取索引和入口網站頁面中的其他物件。 存取工具、嚮導和物件時，假設您具有服務的系統管理許可權。 您可以使用 [搜尋] Explorer，將焦點指定為搜尋字串和其他參數。 

### <a name="using-rest"></a>使用 REST

若是 REST 呼叫，您可以使用 [Postman 或類似的工具](search-get-started-rest.md) 作為用戶端，以指定 [搜尋檔](/rest/api/searchservice/search-documents) 要求。 每個要求都是獨立的，因此您必須將端點 (URL 提供給服務) ，並提供系統管理員或查詢 API 金鑰以進行存取。 根據要求，URL 也可能包含索引名稱、檔集合和其他屬性。 某些屬性（例如內容類型和 API 金鑰）會在要求標頭上傳遞。 您可以在 URL 或要求主體中傳遞其他參數。 所有 REST 呼叫都需要 API 金鑰以進行驗證，以及 api 版本。

### <a name="using-azure-sdks"></a>使用 Azure Sdk

Azure Sdk 提供可保存狀態的搜尋用戶端，以允許連接重複使用。 針對查詢作業，您將具現化 SearchClient，並提供下列屬性的值：端點、索引鍵、索引。 然後，您可以呼叫搜尋方法來提供查詢字串。 

| 語言 | 用戶端 | 範例 |
|----------|--------|---------|
| C # 和 .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [在 C 中傳送您的第一個搜尋查詢#](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [在 Python 中傳送您的第一個搜尋查詢](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [在 JAVA 中傳送第一個搜尋查詢](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| JavaScript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [在 JavaScript 中傳送您的第一個搜尋查詢](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>選擇剖析器：簡單 | 完整

Azure 認知搜尋可讓您在兩個查詢剖析器之間進行選擇，以處理一般和特定的查詢。 使用簡單剖析器的要求通常是使用 [簡單查詢語法](query-simple-syntax.md)所制訂的全文檢索搜尋查詢，並選取為其在自由格式文字查詢中的速度和有效性的預設值。 此語法支援許多常見的搜尋運算子，包括 AND、OR、NOT、片語、尾碼和優先順序運算子。

[完整的 Lucene 查詢語法](query-Lucene-syntax.md#bkmk_syntax)會在您將 `queryType=full` 新增至要求時啟用，可公開廣為採用、且開發作為 [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 一部分的表達式查詢語言。 完整語法可延伸簡單語法。 您為簡單語法所撰寫的查詢，均可在完整的 Lucene 剖析器下執行。 

下列範例說明點：相同的查詢，但有不同 **`queryType`** 的設定，這會產生不同的結果。 在第一個查詢中， `^3` 會將 after `historic` 視為搜尋詞彙的一部分。 此查詢的最高排名結果是「Marquis Plaza & 套件」，其描述中有 *海洋* 。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

使用完整 Lucene 剖析器的相同查詢會解讀 `^3` 為現場詞彙增強程式。 切換剖析器會變更排名，結果中會包含 *移至* 頂端的歷程記錄。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

## <a name="enable-query-behaviors-in-an-index"></a>啟用索引中的查詢行為

索引設計和查詢設計在 Azure 認知搜尋中緊密結合。 *索引架構*（具有每個欄位的屬性）會決定您可以建立的查詢類型。

欄位上的索引屬性會設定允許的作業 - 欄位在索引中是否「可搜尋」、在結果中是否「可擷取」、是否「可排序」、是否「可篩選」，不一而足。 在範例查詢中， `"$orderby": "Rating desc"` 僅適用于索引架構中的 [評等] 欄位標示為可 *排序* 。

![旅館範例的索引定義](./media/search-query-overview/hotel-sample-index-definition.png "旅館範例的索引定義")

上述螢幕擷取畫面是 [旅館範例索引](search-get-started-portal.md)的索引屬性的部分清單。 您可以在入口網站中建立和查看整個索引架構。 如需索引屬性的詳細資訊，請參閱 [建立索引 (REST API) ](/rest/api/searchservice/create-index)。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何建立要求，請嘗試使用簡單和完整語法的範例。

+ [簡單查詢範例](search-query-simple-examples.md)
+ [建置進階查詢的 Lucene 語法查詢範例](search-query-lucene-examples.md)
+ [全文檢索搜尋如何在 Azure 認知搜尋中運作](search-lucene-query-architecture.md)