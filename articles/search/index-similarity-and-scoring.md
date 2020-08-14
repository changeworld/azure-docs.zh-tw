---
title: 相似性與評分概觀
titleSuffix: Azure Cognitive Search
description: 說明相似性和評分概念，以及開發人員可執行哪些動作來自訂評分結果。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 5b3df38e8feef2a7b9bbc090e11a669164010f32
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213199"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Azure 認知搜尋中的相似性與評分

評分是指對全文檢索搜尋查詢的搜尋結果中傳回的每個項目所做的搜尋分數計算。 分數是某個項目在目前搜尋作業的內容中有何相關性的指標。 分數越高，該項目的相關性就愈高。 在搜尋結果中，項目會根據為每個項目計算的搜尋分數由高至低排序。 

根據預設，會將回應中的前 50 個項目傳回，但您可以使用 **$top** 參數，以傳回較少或更多的項目數目 (單一回應中最多 1000 個)，並可使用 **$skip** 以取得下一組結果。

搜尋分數會根據資料和查詢的統計屬性來計算。 Azure 認知搜尋會尋找符合搜尋字詞的文件 (部分或全部，視 [searchMode](https://docs.microsoft.com/rest/api/searchservice/search-documents#searchmodeany--all-optional) 而定)，優先列出包含多個搜尋字詞例項的文件。 如果字詞在資料索引間很少見，但在文件內很常見，搜尋分數會更高。 這種計算相關性的方法基礎稱為 *TF-IDF* (或 term frequency-inverse document frequency)。

搜尋分數值可以在整個結果集內重複。 有多個命中具有相同的搜尋分數時，分數相同的項目並未定義順序，因此順序是不穩定的。 再次執行查詢，您可能會看到項目的位置移位，特別是當您使用免費服務或使用多個複本的可計費服務時。 若有兩個項目的分數完全相同，則無法保證哪個項目先出現。

如果您想要打破重複分數之間的僵局，您可以新增 **$orderby** 子句，首先依分數排序，然後再依另一個可排序欄位排序 (例如 `$orderby=search.score() desc,Rating desc`)。 如需詳細資訊，請參閱 [$orderby](https://docs.microsoft.com/azure/search/search-query-odata-orderby) \(部分機器翻譯\)。

> [!NOTE]
> `@search.score = 1.00` 表示未評分或未排序的結果集。 所有結果的分數都是一致的。 當查詢形式為模糊搜尋、萬用字元或 regex 查詢，或是 **$filter** 運算式時，就會出現未評分的結果。 

## <a name="scoring-profiles"></a>評分設定檔

您可以透過定義自訂的「評分設定檔」，來自訂不同欄位的排名方式。 評分設定檔可讓您更佳地控制搜尋結果中的項目排名。 舉例來說，您可能想根據營收潛力提升某些項目、亦或是提升新項目或庫存過久的項目。 

評分設定檔是索引定義的一部分，由加權欄位、函數和參數組成。 如需定義該設定檔的詳細資訊，請參閱[評分設定檔](index-add-scoring-profiles.md)。

<a name="scoring-statistics"></a>

## <a name="scoring-statistics-and-sticky-sessions"></a>評分統計資料和粘滯話

針對可擴縮性，Azure 認知搜尋會透過分區化程序水平散發每個索引，這表示會實際分開索引的各部分。

預設會根據「分區內資料」的統計屬性來計算文件的分數。 這種方法對大型資料主體通常不是問題，而且與根據所有分區的資訊來計算分數相比，可提供更佳的效能。 儘管如此，使用此效能最佳化會導致最終分屬不同分區中的兩份非常類似的文件 (或甚至是完全相同的文件) 最後得到不同的相關性分數。

如果您偏好根據所有分區的統計屬性來計算分數，您可以將 *scoringStatistics=global* 加入為[查詢參數](https://docs.microsoft.com/rest/api/searchservice/search-documents) (或將 *"scoringStatistics": "global"* 加入為[查詢要求](https://docs.microsoft.com/rest/api/searchservice/search-documents)的主體參數)。

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```
使用 scoringStatistics，可確保相同複本中的所有分區都提供相同的結果。 也就是說，因為不同的複本一律以最新的索引變更進行更新，所以彼此之間可能會稍有不同。 在某些情況下，您可能會想讓使用者在「查詢工作階段」期間取得更一致的結果。 在這種情況下，您可以提供 `sessionId` 作為查詢的一部分。 `sessionId` 是您建立的唯一字串，可參考唯一的使用者工作階段。

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?sessionId=[string]&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```
只要使用相同的 `sessionId`，就會儘可能嘗試以相同的複本為目標，增加使用者所見結果的一致性。 

> [!NOTE]
> 不斷重複使用相同的 `sessionId` 值可能會干擾複本間的要求負載平衡，並對搜尋服務的效能造成負面影響。 作為 sessionId 使用的值不能以 ' _ ' 字元開頭。

## <a name="similarity-ranking-algorithms"></a>相似性排名演算法

Azure 認知搜尋支援兩種不同的相似性排名演算法：*傳統的相似性*演算法，以及 *Okapi BM25* 演算法正式實作 (目前處於預覽階段)。 傳統相似性演算法是預設演算法，但從 7 月 15 日開始，該日期之後建立的任何新服務都會使用新的 BM25 演算法。 這是新服務上唯一可用的演算法。

目前，您可以指定想要使用哪個相似性排序演算法。 如需詳細資訊，請參閱[排名演算法](index-ranking-similarity.md)。

下列影片區段快轉至 Azure 認知搜尋中使用的排名演算法說明。 您可以觀看整段影片，以取得更多背景資訊。

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=322&end=643]

<a name="featuresMode-param"></a>

## <a name="featuresmode-parameter-preview"></a>featuresMode 參數 (預覽) 

[搜尋檔](https://docs.microsoft.com/rest/api/searchservice/preview-api/search-documents) 要求具有新的 [featuresMode](https://docs.microsoft.com/rest/api/searchservice/preview-api/search-documents#featuresmode) 參數，可在欄位層級提供有關相關性的其他詳細資料。 雖然 `@searchScore` 會針對檔進行匯總 (在此查詢的內容中，這份檔的相關內容) ，但您可以透過 featuresMode 取得個別欄位的資訊，如結構中所示 `@search.features` 。 結構包含查詢中使用的所有欄位， (特定欄位透過查詢中的 **searchFields** ，或在索引) 中所有 **屬性為可** 搜尋的欄位。 針對每個欄位，您會取得下列值：

+ 在欄位中找到的唯一標記數目
+ 相似性分數，或欄位內容與查詢字詞的相對程度的量值
+ 詞彙頻率，或在欄位中找到查詢字詞的次數

對於以「描述」和「標題」欄位為目標的查詢，包含的回應 `@search.features` 可能如下所示：

```json
"value": [
 {
    "@search.score": 5.1958685,
    "@search.features": {
        "description": {
            "uniqueTokenMatches": 1.0,
            "similarityScore": 0.29541412,
            "termFrequency" : 2
        },
        "title": {
            "uniqueTokenMatches": 3.0,
            "similarityScore": 1.75451557,
            "termFrequency" : 6
        }
```

您可以在 [自訂計分解決方案](https://github.com/Azure-Samples/search-ranking-tutorial) 中取用這些資料點，或使用此資訊來偵測搜尋相關性問題。


## <a name="see-also"></a>另請參閱

 [評分設定檔](index-add-scoring-profiles.md) [REST API 參考](https://docs.microsoft.com/rest/api/searchservice/)  \(部分機器翻譯\)  
 [搜尋文件 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)  \(部分機器翻譯\)  
 [Azure 認知搜尋 .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
