---
title: 如何使用搜尋結果
titleSuffix: Azure Cognitive Search
description: 結構和排序搜尋結果、取得檔計數，以及在 Azure 認知搜尋中將內容導覽新增至搜尋結果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: fd102706d1fa6c33d8962a5d1caf5aa3e41b231d
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146189"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中使用搜尋結果

本文說明如何取得傳回的查詢回應，其中包含相符檔的總計數、分頁結果、排序的結果，以及按反白顯示的詞彙。

回應的結構是由查詢中的參數所決定： REST API 中的[搜尋檔](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)，或 .net SDK 中的[DocumentSearchResult 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1)。

## <a name="result-composition"></a>結果組合

雖然搜尋檔可能包含大量欄位，但通常只需要少數幾個來表示結果集中的每個檔。 在查詢要求中，附加 `$select=<field list>` 以指定要在回應中顯示的欄位。 在要包含在結果中的**索引中，** 必須將欄位的屬性設為可抓取。 

最適合的欄位包括在檔之間進行對比和區別的工作，提供足夠的資訊來邀請使用者的按下回應。 在電子商務網站上，它可能是產品名稱、描述、品牌、色彩、大小、價格和評等。 針對飯店-範例索引內建範例，它可能是下列範例中的欄位：

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> 如果想要在結果中包含影像檔案（例如產品相片或標誌），請將它們儲存在 Azure 認知搜尋之外，但在索引中包含欄位，以參考搜尋檔中的影像 URL。 結果中支援影像的範例索引包括 realestate-**範例-us**示範、本[快速入門](search-create-app-portal.md)中的精選，以及[紐約市工作示範應用程式](https://aka.ms/azjobsdemo)。

## <a name="paging-results"></a>分頁結果

根據預設，搜尋引擎最多會傳回前50個相符專案（如果查詢是全文檢索搜尋，則是由搜尋分數決定），或是完全相符查詢的任意順序。

若要傳回不同數目的相符檔，請將 `$top` 和 `$skip` 參數新增至查詢要求。 下列清單說明邏輯。

+ 新增 `$count=true` 以取得索引中相符檔總數的計數。

+ 傳回15個相符檔的第一組，加上總相符專案的計數：`GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ 傳回第二個集合，略過前15個來取得接下來的15個： `$top=15&$skip=15` 。 針對第三組15執行相同動作：`$top=15&$skip=30`

如果基礎索引正在變更，則分頁查詢的結果不保證穩定。 分頁會變更 `$skip` 每個頁面的值，但每個查詢都是獨立的，而且會在查詢時以現有資料的目前觀點來操作 (換句話說，沒有結果的快取或快照集，例如在一般用途資料庫) 中找到的結果。
 
以下是您可能會取得重複專案的範例。 假設有四個檔的索引：

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
{ "id": "4", "rating": 1 }
```
 
現在假設您想要一次傳回兩個結果，並依評等排序。 您會執行此查詢來取得結果的第一頁： `$top=2&$skip=0&$orderby=rating desc` ，產生下列結果：

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
```
 
在服務上，假設在查詢呼叫之間，會將第五份檔加入至索引： `{ "id": "5", "rating": 4 }` 。  之後，您可以執行查詢來提取第二頁： `$top=2&$skip=2&$orderby=rating desc` ，並取得下列結果：

```text
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
```
 
請注意，檔2會提取兩次。 這是因為新檔5的評等值較大，因此它會在檔2之前排序，並落在第一頁。 雖然這種行為可能不是預期的，但它通常是搜尋引擎的運作方式。

## <a name="ordering-results"></a>排序結果

對於全文檢索搜尋查詢，結果會依搜尋分數自動排序，並根據檔中的詞彙頻率和鄰近性來計算，並以較高的分數到達具有搜尋詞彙更多或更強相符的檔。 

搜尋分數可傳達相關性的一般意義，與相同結果集中的其他檔相較之下，反映比對的強度。 分數不一定會與下一個查詢一致，因此當您使用查詢時，您可能會注意到搜尋檔的排序方式不會有些許差異。 有幾個說明可能會發生這種情況。

| 原因 | 描述 |
|-----------|-------------|
| 資料變動性 | 當您新增、修改或刪除檔時，索引內容會有所不同。 當索引更新在一段時間內處理時，將會變更詞彙頻率，這會影響相符檔的搜尋分數。 |
| 多個複本 | 對於使用多個複本的服務，會以平行方式對每個複本發出查詢。 用來計算搜尋分數的索引統計資料是依據每個複本計算，並在查詢回應中合併和排序結果。 複本大部分都是鏡像，但統計資料可能會因為狀態的小差異而有所不同。 例如，一個複本可能已刪除對其統計資料造成影響的檔，而這些檔已合併到其他複本。 一般來說，在較小的索引中，每個複本統計資料的差異會更明顯。 |
| 相同分數 | 如果有多個檔具有相同的分數，則其中任何一項可能會先出現。  |

### <a name="consistent-ordering"></a>一致的順序

根據結果排序中的彈性，如果一致性是應用程式需求，您可能會想要探索其他選項。 最簡單的方法是依域值排序，例如 [評等] 或 [日期]。 針對您想要依特定欄位排序的案例，例如評等或日期，您可以明確定義[ `$orderby` 運算式](query-odata-filter-orderby-syntax.md)，這可以套用至任何索引為可**排序**的欄位。

另一個選項是使用[自訂評分設定檔](index-add-scoring-profiles.md)。 計分設定檔可讓您更充分掌控搜尋結果中的專案排名，並能夠提升特定欄位中找到的相符專案。 額外的計分邏輯有助於覆寫複本之間的差異，因為每份檔的搜尋分數會相距較遠。 我們建議採用這種方法的[排名演算法](index-ranking-similarity.md)。

## <a name="hit-highlighting"></a>搜尋結果醒目提示

搜尋反白顯示指的是文字格式設定 (例如粗體或黃色反白顯示) 套用至結果中的相符詞彙，讓您更容易找到相符的內容。 [查詢要求](https://docs.microsoft.com/rest/api/searchservice/search-documents)上會提供搜尋反白顯示指示。 

若要啟用搜尋醒目提示，請新增 `highlight=[comma-delimited list of string fields]` 來指定哪些欄位將使用反白顯示。 反白顯示適用于較長的內容欄位，例如 [描述] 欄位，其中比對不會立即明顯符合。 只有屬性為可搜尋的欄位**定義符合搜尋**醒目提示的資格。

根據預設，Azure 認知搜尋會針對每個欄位傳回最多五個重點。 您可以在欄位後面加上一個整數來調整這個數位。 例如，在 `highlight=Description-10` [描述] 欄位中，最多會針對相符的內容傳回10個重點。

將格式套用至整個詞彙查詢。 格式的類型取決於標記、 `highlightPreTag` 和 `highlightPostTag` ，而且您的程式碼會處理回應 (例如，套用粗體字型或黃色背景) 。

在下列範例中，在 [描述] 欄位中找到的 "sandy"、"海灘"、"海灘"、"浮水" 詞彙會標記為反白顯示。 在引擎中觸發查詢展開的查詢（例如模糊和萬用字元搜尋）對搜尋反白顯示的支援有限。

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2020-06-30 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>從7月15日開始 (的新行為) 

2020年7月15日之後建立的服務將提供不同的醒目提示體驗。 在該日期之前建立的服務在其反白顯示行為中不會變更。 

具有新的行為：

* 只會傳回符合完整片語查詢的片語。 「超級杯」查詢會傳回如下所示的重點：

    ```html
    '<em>super bowl</em> is super awesome with a bowl of chips'
    ```
  請注意，晶片的「*杯*」一詞沒有任何反白顯示，因為它不符合完整的片語。

當您撰寫會執行反白顯示的用戶端程式代碼時，請注意這項變更。 請注意，除非您建立全新的搜尋服務，否則這不會對您造成影響。

## <a name="next-steps"></a>後續步驟

若要為您的用戶端快速產生搜尋頁面，請考慮下列選項：

+ [應用程式](search-create-app-portal.md)產生器，在入口網站中，使用搜尋列、多面向導覽和包含影像的結果區域來建立 HTML 網頁。
+ [在 c # 中建立第一個應用程式](tutorial-csharp-create-first-app.md)是建立功能用戶端的教學課程。 範例程式碼示範分頁查詢、搜尋反白顯示和排序。

有幾個程式碼範例包含 web 前端介面，您可以在這裡找到：[紐約的作業示範應用程式](https://aka.ms/azjobsdemo)、 [JavaScript 使用即時示範網站的範例程式碼](https://github.com/liamca/azure-search-javascript-samples)，以及[CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd)。
