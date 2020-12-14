---
title: 建立基本查詢
titleSuffix: Azure Cognitive Search
description: 瞭解如何在認知搜尋中建立查詢要求、測試和程式碼所使用的工具和 Api，以及從索引設計開始查詢決策的方式。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: a02d51d66b9d2b8bf3c08d4515713ecb062e0c8e
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400211"
---
# <a name="create-a-query-in-azure-cognitive-search"></a>在 Azure 認知搜尋中建立查詢

如果您是第一次建立查詢，本文將說明您需要的工具和 Api、哪些方法用來建立查詢，以及索引結構和內容如何影響查詢結果。 如需查詢要求外觀的簡介，請從 [查詢類型和組合](search-query-overview.md)著手。

## <a name="choose-tools-and-apis"></a>選擇工具和 Api

您將需要工具或 API 來建立查詢。 下列任何建議適用于測試和生產工作負載。

| 方法 | 描述 |
|-------------|-------------|
| 入口網站| [Search explorer (入口網站) ](search-explorer.md) 是 Azure 入口網站中的查詢介面，可針對基礎搜尋服務上的索引執行查詢。 入口網站會在幕後對 [搜尋檔](/rest/api/searchservice/search-documents) 作業進行 REST API 呼叫，但無法叫用自動完成、建議或檔查閱。<br/><br/> 您可以選取任何索引和 REST API 版本，包括預覽。 查詢字串可以使用簡單或完整語法，並支援所有查詢參數， (篩選、選取、searchFields 等) 。 在入口網站中，當您開啟索引時，您可以使用 [搜尋瀏覽器] 與 [並列] 索引標籤中的索引 JSON 定義一起使用，以方便存取欄位屬性。 檢查在測試查詢時可搜尋、可排序、可篩選和可 facet 的欄位。 <br/>建議進行早期調查、測試和驗證。 [深入了解。](search-explorer.md) |
| Web 測試控管| [Postman 或 Visual Studio Code](search-get-started-rest.md) 是用來編寫 [搜尋檔](/rest/api/searchservice/search-documents) 要求的強式選擇，以及任何其他要求，也就是 REST。 REST Api 支援 Azure 認知搜尋中的每個可能的程式設計作業，而且當您使用 Postman 或 Visual Studio Code 之類的工具時，您可以透過互動方式發出要求，以瞭解功能在投資程式碼之前的運作方式。 如果您在 Azure 入口網站中沒有參與者或系統管理許可權，則 web 測試控管是不錯的選擇。 只要您有搜尋 URL 和查詢 API 金鑰，您就可以使用這些工具對現有的索引執行查詢。 |
| Azure SDK | 當您準備好要撰寫程式碼時，可以在適用于 .NET、Python、JavaScript 或 JAVA 的 Azure Sdk 中使用 Azure.Search.Doc>ument 用戶端程式庫。 每個 SDK 都有自己的發行排程，但您可以在其中建立和查詢索引。 <br/><br/>[SearchClient ( .net) ](/dotnet/api/azure.search.documents.searchclient) 可以用來查詢 c # 中的搜尋索引。  [深入了解。](search-howto-dotnet-sdk.md)<br/><br/>[SearchClient (python) ](/dotnet/api/azure.search.documents.searchclient) 可以用來查詢 python 中的搜尋索引。 [深入了解。](search-get-started-python.md)<br/><br/>您可以使用[SearchClient (javascript) ](/dotnet/api/azure.search.documents.searchclient) ，以 javascript 查詢搜尋索引。 [深入了解。](search-get-started-javascript.md) |

## <a name="set-up-a-search-client"></a>設定搜尋用戶端

搜尋用戶端會向搜尋服務進行驗證、傳送要求和處理回應。 無論您使用哪一種工具或 API，搜尋用戶端都必須有下列各項：

| 屬性 | 描述 |
|------------|-------------|
| 端點 | 搜尋服務的 URL 可定址格式如下： `https://[service-name].search.windows.net` 。 |
|  (系統管理員或查詢) 的 API 存取金鑰 | 驗證搜尋服務的要求。 |
| 索引名稱 | 查詢一律會導向單一索引的檔集合。 您無法加入索引，或建立自訂或暫存資料結構作為查詢目標。 |
| API 版本 | REST 呼叫明確需要要求 `api-version` 上的。 相反地，Azure SDK 中的用戶端程式庫會根據特定的 REST API 版本進行版本設定。 針對 Sdk， `api-version` 是隱含的。 |

### <a name="in-the-portal"></a>在入口網站中

Search Explorer 和其他入口網站工具都有服務的內建用戶端連線，其中包含直接存取索引和入口網站頁面中的其他物件。 存取工具、嚮導和物件需要參與者角色中的成員資格或更高的服務。 

### <a name="using-rest"></a>使用 REST

若是 REST 呼叫，您可以使用 [Postman 或類似的工具](search-get-started-rest.md) 作為用戶端，以指定 [搜尋檔](/rest/api/searchservice/search-documents) 要求。 每個要求都是獨立的，因此您必須在每個要求上提供端點、索引名稱和 API 版本。 其他屬性、內容類型和 API 金鑰會在要求標頭上傳遞。 

您可以使用 POST 或 GET 來查詢索引。 POST （使用要求主體中指定的參數）比較容易使用。 如果您使用 POST，請務必包含 `docs/search` 在 URL 中：

```http
POST https://myservice.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*"
}
```

### <a name="using-azure-sdks"></a>使用 Azure Sdk

如果您使用的是 Azure SDK，您將會在程式碼中建立用戶端。 所有的 Sdk 都會提供可保存狀態的搜尋用戶端，讓您能夠重複使用連接。 針對查詢作業，您將具現化， **`SearchClient`** 並提供下列屬性的值：端點、索引鍵、索引。 然後，您可以呼叫 **`Search method`** 以傳入查詢字串。 

| 語言 | 用戶端 | 範例 |
|----------|--------|---------|
| C # 和 .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [在 C 中傳送您的第一個搜尋查詢#](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [在 Python 中傳送您的第一個搜尋查詢](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [在 JAVA 中傳送第一個搜尋查詢](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| JavaScript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [在 JavaScript 中傳送您的第一個搜尋查詢](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>選擇剖析器：簡單 | 完整

如果您的查詢是全文檢索搜尋，則會使用剖析器來處理搜尋參數的內容。 Azure 認知搜尋提供兩個查詢剖析器。 簡單的剖析器瞭解 [簡單的查詢語法](query-simple-syntax.md)。 此剖析器已選取為其在自由格式文字查詢中的速度和有效性的預設值。 此語法支援一般搜尋運算子 (AND、OR、NOT) 用於詞彙和片語搜尋，以及前置詞 (`*`) 搜尋 (如 "海 *" 表示西雅圖和 Seaside) 。 一般建議是先嘗試簡單的剖析器，然後在應用程式需求呼叫以取得更強大的查詢時，繼續進行完整剖析器。

[完整的 Lucene 查詢語法](query-Lucene-syntax.md#bkmk_syntax)（當您新增 `queryType=full` 至要求時啟用）是以[Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)剖析器為基礎。

完整語法是簡單語法的延伸模組，具有更多運算子，讓您可以建立簡單的查詢，例如模糊搜尋、萬用字元搜尋、鄰近搜尋和正則運算式。 下列範例說明點：相同的查詢，但有不同 **`queryType`** 的設定，這會產生不同的結果。 在第一個簡單的查詢中 `^3` ， `historic` 會將 after 視為搜尋詞彙的一部分。 此查詢的最高排名結果是「Marquis Plaza & 套件」，其描述中有 *海洋* 。

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

## <a name="choose-query-methods"></a>選取查詢方法

搜尋是以使用者為導向的練習，也就是從搜尋方塊收集詞彙或片語，或從頁面上的按一下事件來收集。 下表摘要說明您可以收集使用者輸入的機制，以及預期的搜尋體驗。

| 輸入 | 體驗 |
|-------|---------|
| [搜尋方法](/rest/api/searchservice/search-documents) | 使用者在搜尋方塊中輸入詞彙或片語（包含或不含運算子），然後按一下 [搜尋] 以傳送要求。 搜尋可以與相同要求上的篩選準則搭配使用，但不能用於自動完成或建議。 |
| [自動完成方法](/rest/api/searchservice/autocomplete) | 使用者輸入幾個字元，並在輸入每個新字元之後起始查詢。 回應是索引中的已完成字串。 如果提供的字串有效，使用者可以按一下 [搜尋]，將該查詢傳送至服務。 |
| [建議方法](/rest/api/searchservice/suggestions) | 如同自動完成，使用者輸入幾個字元，並產生累加式查詢。 回應是相符檔的下拉式清單，通常是由一些獨特或描述性欄位來表示。 如果有任何選項有效，使用者可以按一下其中一個，並傳回相符的檔。 |
| [多面向導覽](/rest/api/searchservice/search-documents#query-parameters) | 頁面會顯示可按一下的導覽連結或階層連結，以縮小搜尋範圍。 多面向導覽結構會根據初始查詢動態組成。 例如， `search=*` 若要填入每個可能類別所組成的多面向導覽樹狀結構。 從查詢回應建立多面向導覽結構，但它也是用來表示下一個查詢的機制。 n REST API 參考， `facets` 會記載為搜尋檔作業的查詢參數，但可以在不使用參數的情況下使用 `search` 。|
| [篩選方法](/rest/api/searchservice/search-documents#query-parameters) | 篩選準則會搭配 facet 使用，以縮小結果範圍。 您也可以在頁面後方執行篩選，例如使用特定語言的欄位來初始化頁面。 在 REST API 參考中， `$filter` 會記載為搜尋檔作業的查詢參數，但可以在不使用參數的情況下使用 `search` 。|

## <a name="know-your-field-attributes"></a>知道您的欄位屬性

如果您先前已複習過 [查詢要求的基本](search-query-overview.md)概念，您可能會忘記查詢要求上的參數取決於索引中欄位的屬性。 例如，若要在查詢、篩選或排序次序中 *使用，欄位* 必須是可搜尋、可 *篩選* 和可 *排序*。 同樣地，只有標記為 *可抓取* 的欄位才會出現在結果中。 當您開始 `search` `filter` 在要求中指定、和 `orderby` 參數時，請務必檢查屬性，以避免非預期的結果。

在 [飯店範例索引](search-get-started-portal.md)下方的入口網站螢幕擷取畫面中，只有最後兩個欄位 "LastRenovationDate" 和 "評等" 只能用在 `"$orderby"` 唯一子句中。

![旅館範例的索引定義](./media/search-query-overview/hotel-sample-index-definition.png "旅館範例的索引定義")

如需欄位屬性的描述，請參閱 [建立索引 (REST API) ](/rest/api/searchservice/create-index)。

## <a name="know-your-tokens"></a>知道您的權杖

在編制索引期間，查詢引擎會流量分析器來對字串執行文字分析，最大化在查詢時比對的可能性。 最小的字串會以較低的大小寫，但可能也會進行詞形歸併還原並停止移除 word。 較大的字串或複合字通常會以空白字元、連字號或連字號分隔，然後以個別的標記來編制索引。 

這裡所要考慮的重點是，您認為索引所包含的內容，以及實際的內容，可能會不同。 如果查詢未傳回預期的結果，您可以透過 [ [分析文字] (REST API) ](/rest/api/searchservice/test-analyzer)來檢查分析器所建立的權杖。 如需 token 化和查詢影響的詳細資訊，請參閱 [部分詞彙搜尋和具有特殊字元的模式](search-query-partial-matching.md)。

## <a name="next-steps"></a>後續步驟

既然您已經更瞭解查詢要求的建立方式，請嘗試下列快速入門以取得實際操作體驗。

+ [搜尋總管](search-explorer.md)
+ [如何在 REST 中進行查詢](search-get-started-rest.md)
+ [如何在 .NET 中進行查詢](search-get-started-dotnet.md)
+ [如何在 Python 中查詢](search-get-started-python.md)
+ [如何以 JavaScript 查詢](search-get-started-javascript.md)