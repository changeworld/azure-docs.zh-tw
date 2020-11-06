---
title: 如何使用搜尋結果
titleSuffix: Azure Cognitive Search
description: 結構和排序搜尋結果、取得檔計數，以及將內容導覽新增至 Azure 認知搜尋中的搜尋結果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: e583cedc04113615c50cc9906cbd11a99ff48683
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421714"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中使用搜尋結果

本文說明如何取得查詢回應，其會以相符檔總數、編頁結果、排序結果，以及點擊醒目提示的詞彙傳回。

回應的結構取決於查詢中的參數：在 .NET SDK 中的 REST API 或[>.searchresults 類別](/dotnet/api/azure.search.documents.models.searchresults-1)中[搜尋檔](/rest/api/searchservice/Search-Documents)。

## <a name="result-composition"></a>結果組合

雖然搜尋檔可能包含大量欄位，但通常只需要少數幾個欄位，就能代表結果集中的每份檔。 在查詢要求中，附加 `$select=<field list>` 以指定回應中顯示的欄位。 欄位必須在要包含于結果中的 **索引中，** 屬性為可抓取。 

最適合的欄位包括對比和區分檔的欄位，提供足夠的資訊來邀請使用者的 click 回應。 在電子商務網站上，其可能是產品名稱、描述、品牌、色彩、大小、價格和評等。 針對飯店-範例-索引內建範例，它可能是下列範例中的欄位：

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> 如果想要在結果中包含影像檔案（例如，產品相片或標誌），請將它們儲存在 Azure 認知搜尋的外部，但在您的索引中包含欄位，以參考搜尋檔中的影像 URL。 支援結果中影像的範例索引包括 **realestate-範例-us** 示範、本 [快速入門](search-create-app-portal.md)中所述的範例，以及 [紐約城市工作示範應用程式](https://aka.ms/azjobsdemo)。

## <a name="paging-results"></a>分頁結果

根據預設，搜尋引擎會傳回最多前50個相符專案（如果查詢是全文檢索搜尋，則為搜尋分數所決定），或依任意順序傳回完全相符的查詢。

若要傳回不同數目的相符檔，請將 `$top` 和 `$skip` 參數加入至查詢要求。 下列清單說明邏輯。

+ 加入 `$count=true` 以取得索引內相符檔總數的計數。

+ 傳回一組15個相符的檔，加上總相符專案的計數： `GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ 傳回第二個集合，略過前15個來取得下一個15： `$top=15&$skip=15` 。 針對第三組15執行相同動作： `$top=15&$skip=30`

如果基礎索引正在變更，則不保證編頁查詢的結果是穩定的。 分頁 `$skip` 會變更每個頁面的值，但每個查詢都是獨立的，而且會在查詢時資料存在於索引中的目前資料檢視上運作 (換句話說，不會有結果的快取或快照集，例如在一般用途資料庫) 中找到的結果。
 
以下是您可能會收到重複專案的範例。 假設有四份檔的索引：

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
{ "id": "4", "rating": 1 }
```
 
現在假設您想要一次傳回兩個結果，並依評等排序。 您可以執行此查詢來取得結果的第一頁： `$top=2&$skip=0&$orderby=rating desc` ，產生下列結果：

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
```
 
在服務上，假設在查詢呼叫之間將第五份檔新增至索引： `{ "id": "5", "rating": 4 }` 。  不久之後，您會執行查詢來提取第二頁： `$top=2&$skip=2&$orderby=rating desc` ，並取得下列結果：

```text
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
```
 
請注意，檔2會提取兩次。 這是因為新的檔5有更高的評等值，因此它會在檔2之前排序，並放在第一頁。 雖然這種行為可能是非預期的，但通常是搜尋引擎的運作方式。

## <a name="ordering-results"></a>排序結果

針對全文檢索搜尋查詢，結果會依搜尋分數自動排名，並根據檔中的詞彙頻率和鄰近性計算，並以較高的分數，在搜尋字詞上具有更多或更強的相符檔。 

搜尋分數會傳達相關的一般意義，以反映與相同結果集中其他檔相較之下的相符強度。 分數不一定會與下一個查詢一致，因此當您使用查詢時，您可能會注意到搜尋檔的排序方式不一致。 有幾個說明原因可能會發生這種情況。

| 原因 | 描述 |
|-----------|-------------|
| 資料變動性 | 當您新增、修改或刪除檔時，索引內容會有所不同。 當索引更新經過一段時間後，會影響相符檔的搜尋分數時，會變更詞彙頻率。 |
| 多個複本 | 針對使用多個複本的服務，會以平行方式對每個複本發出查詢。 用來計算搜尋分數的索引統計資料會依據每個複本計算，並在查詢回應中合併和排序結果。 複本大多是彼此的鏡像，但統計資料可能會因為狀態的小差異而不同。 例如，一個複本可能已刪除參與其統計資料的檔，而這些檔已與其他複本合併。 一般來說，在較小的索引中，每個複本統計資料的差異比較明顯。 |
| 相同分數 | 如果有多份檔的分數相同，則可能會先出現其中任何一個。  |

### <a name="consistent-ordering"></a>一致排序

由於結果順序的彈性，您可能會想要在一致性為應用程式需求時探索其他選項。 最簡單的方法是依域值排序，例如評等或日期。 針對您想要依特定欄位排序的案例，例如評等或日期，您可以明確定義 [ `$orderby` 運算式](query-odata-filter-orderby-syntax.md)，以套用至任何索引為可 **排序** 的欄位。

另一個選項是使用 [自訂評分設定檔](index-add-scoring-profiles.md)。 評分設定檔可讓您更充分掌控搜尋結果中的專案排名，並能夠提升在特定欄位中找到的相符專案。 額外的評分邏輯可協助覆寫複本之間的微小差異，因為每份檔的搜尋分數會相距更遠。 我們建議採用此方法的 [排名演算法](index-ranking-similarity.md) 。

## <a name="hit-highlighting"></a>搜尋結果醒目提示

點擊醒目提示是指文字格式 (例如粗體或黃色反白顯示) 套用至結果中的比對字詞，讓您輕鬆地找出相符的結果。 [查詢要求](/rest/api/searchservice/search-documents)會提供搜尋醒目提示的指示。 

若要啟用搜尋結果醒目提示，請新增 `highlight=[comma-delimited list of string fields]` 以指定哪些欄位會使用醒目提示。 醒目提示適用于較長的內容欄位，例如 [描述] 欄位，其中不會立即顯示相符的內容。 只有屬性為可搜尋的欄位定義 **才符合搜尋** 結果醒目提示的條件。

根據預設，Azure 認知搜尋會傳回每個欄位最多五個醒目顯示。 您可以藉由在欄位後面附加一個整數來調整這個數位。 例如，在 `highlight=Description-10` [描述] 欄位中，最多會針對相符的內容傳回10個醒目顯示。

格式設定會套用至整個詞彙查詢。 格式的類型取決於標記、 `highlightPreTag` 和 `highlightPostTag` ，而您的程式碼會處理回應 (例如，套用粗體字字型或黃色背景) 。

在下列範例中，[描述] 欄位中找到的「sandy」、「海灘」、「海灘」、「海灘」詞彙都已標記為要反白顯示。 在引擎中觸發查詢展開的查詢（例如模糊和萬用字元搜尋）對搜尋結果醒目提示的支援有限。

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

### <a name="new-behavior-starting-july-15"></a>新行為 (從7月15日開始) 

于2020年7月15日之後建立的服務，將會提供不同的醒目提示體驗。 在該日期之前建立的服務不會在其醒目提示行為中變更。 

使用新的行為：

* 只會傳回符合完整片語查詢的片語。 「超級杯」查詢將會傳回如下所示的重點：

    ```html
    '<em>super bowl</em> is super awesome with a bowl of chips'
    ```
  請注意， *晶片* 的「詞彙」一詞沒有任何反白顯示，因為它不符合完整的片語。

當您撰寫執行搜尋結果醒目提示的用戶端程式代碼時，請注意這項變更。 請注意，除非您建立全新的搜尋服務，否則不會對您造成影響。

## <a name="next-steps"></a>後續步驟

若要快速為您的用戶端產生搜尋頁面，請考慮下列選項：

+ [應用程式](search-create-app-portal.md)產生器，在入口網站中，建立包含影像的搜尋列、多面向導覽和結果區域的 HTML 網頁。
+ 使用[c # 建立您的第一個應用程式](tutorial-csharp-create-first-app.md)是建立功能用戶端的教學課程。 範例程式碼會示範編頁查詢、搜尋結果醒目提示和排序。

有幾個程式碼範例包含 web 前端介面，您可以在這裡找到： [紐約的工作示範應用程式](https://aka.ms/azjobsdemo)、 [具有即時示範網站的 JavaScript 範例程式碼](https://github.com/liamca/azure-search-javascript-samples)，以及 [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd)。