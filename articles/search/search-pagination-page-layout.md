---
title: 如何使用搜尋結果
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜尋中構建和排序搜尋結果、獲取文檔計數並將內容導航添加到搜尋結果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 8543894f3f518df6b9b0054973ca1683b82e38f1
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548998"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中處理搜尋結果

本文介紹如何獲取查詢回應,該回應附帶了匹配文檔、分頁結果、排序結果和點擊突出顯示的術語的總數。

回應的結構由查詢中的參數確定:REST API 中的[搜尋文件](https://docs.microsoft.com/rest/api/searchservice/Search-Documents),或 .NET SDK 中的[DocumentSearchResult 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1)。

## <a name="result-composition"></a>結果組合

雖然搜索文檔可能由大量欄位組成,但通常只需要幾個欄位來表示結果集中的每個文檔。 在查詢請求上,追加以`$select=<field list>`指定回應中顯示的欄位。 欄位必須歸為要包含在結果中的索引中的**可檢索**欄位。 

最起作用的欄位包括那些對文件進行對比和區分的欄位,這些欄位提供了足夠的資訊來邀請使用者進行點擊式回應。 在電子商務網站上,它可能是產品名稱、描述、品牌、顏色、大小、價格和評級。 對於酒店示例索引內置示例,可能是以下示例中的欄位:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> 如果要在結果中包含圖像檔(如產品照片或徽標),請將它們存儲在 Azure 認知搜索之外,但在索引中包含一個字段以引用搜尋文檔中的圖像 URL。 支援結果中影像的範例索引包括**房地產樣本-us**演示,在此[快速入門](search-create-app-portal.md)中介紹,以及[紐約市就業演示應用程式](https://aka.ms/azjobsdemo)。

## <a name="results-returned"></a>返回的結果

默認情況下,如果查詢為全文搜索,或者按任意順序返回前 50 個匹配項,由搜索分數確定。

要返回不同數量的匹配文檔,請向`$top`查詢`$skip`請求添加和參數。 下面的清單解釋了邏輯。

+ 添加`$count=true`以獲取索引中匹配文檔總數的計數。

+ 傳回第一組 15 個符合文件以及總符合計數:`GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ 返回第二盤,跳過前15,得到下一個15: `$top=15&$skip=15`。 對於第三組 15 執行相同的操作:`$top=15&$skip=30`

如果基礎索引正在更改,則分頁查詢的結果不能保證是穩定的。 分頁會更改每個頁面`$skip`的值,但每個查詢都是獨立的,並且對查詢時在索引中存在的數據的當前視圖進行操作(換句話說,沒有緩存或快照的結果,例如在通用資料庫中找到的結果)。
 
下面是如何獲取重複項的範例。 假設索引有四個文件:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
    { "id": "4", "rating": 1 }
 
現在假設您希望結果一次返回兩個,按評級排序。 您會執行此查詢以取得結果的第一頁: `$top=2&$skip=0&$orderby=rating desc`,產生以下結果:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
 
在服務上,假設查詢呼叫之間的索引中新增了第五個文件: `{ "id": "5", "rating": 4 }`。  不久之後,您將執行查詢以取得第二頁:`$top=2&$skip=2&$orderby=rating desc`取得這些結果:

    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
 
請注意,文檔 2 被提取兩次。 這是因為新文檔 5 具有更大的評級值,因此它在文檔 2 之前排序並落在第一頁上。 雖然這種行為可能出乎意料,但它是搜尋引擎行為的典型表現。

## <a name="ordering-results"></a>排序結果

對於全文搜索查詢,結果根據文檔中的術語頻率和鄰近程度計算,結果自動按搜索分數排序,較高的分數將到搜索詞上匹配更多或更強的文檔。 搜索分數傳達一般的相關性感,相對於同一結果集中的其他文檔,並且不能保證從一個查詢到下一個查詢一致。

處理查詢時,您可能會注意到排序結果的微小差異。 有幾個解釋來解釋為什麼會發生這種情況。

| 條件 | 描述 |
|-----------|-------------|
| 資料波動性 | 當您添加、修改或刪除文檔時,索引的內容會有所不同。 隨著索引更新的處理時間的變化,期限頻率將發生變化,從而影響匹配文檔的搜索分數。 |
| 查詢執行位置 | 對於使用多個副本的服務,將並行針對每個副本發出查詢。 用於計算搜尋分數的索引統計資訊按副本計算,結果在查詢回應中合併並排序。 副本大多是彼此的鏡像,但由於狀態差異小,統計資訊可能不同。 例如,一個副本可能刪除了導致其統計資訊的文檔,這些文件從其他副本中合併。 通常,每個副本統計資訊的差異在較小的索引中更為明顯。 |
| 在相同的搜尋分數之間打平 | 當搜索文檔的分數相同時,也會發生有序結果的差異。 在這種情況下,當您重新運行同一查詢時,無法保證首先顯示哪個文檔。 |

### <a name="consistent-ordering"></a>一致的訂購

給定搜索評分的靈活性,如果結果訂單中的一致性是應用程式要求,則可能需要探索其他選項。 最簡單的方法是按欄位值(如評級或日期)進行排序。 對於要按特定欄位排序的方案(如評級或日期),可以顯式定義[`$orderby`運算式](query-odata-filter-orderby-syntax.md),該運算式可以應用於索引為**可排序**的任何欄位。

另一個選項是使用[自訂評分設定檔](index-add-scoring-profiles.md)。 評分設定檔使您能夠對搜尋結果中的專案排名進行更多控制,並能夠提升特定欄位中的匹配項。 附加評分邏輯可幫助覆蓋副本之間的細微差異,因為每個文檔的搜索分數相距較遠。 我們建議這個方法的排名[演演算法](index-ranking-similarity.md)。

## <a name="hit-highlighting"></a>搜尋結果醒目提示

點擊突出顯示是指應用於結果匹配術語的文本格式(如粗體或黃色突出顯示),便於發現匹配項。 [查詢請求](https://docs.microsoft.com/rest/api/searchservice/search-documents)上提供了命中突出顯示說明。 The search engine encloses the matching `highlightPreTag` term in tags, and `highlightPostTag`, and your code handles the response (for example, applying a bold font).

格式應用於整個術語查詢。 在下面的示例中,在"描述"欄位中找到的術語"沙","沙子","海灘","海灘"被標記為突出顯示。 對部分術語(如模糊搜索或通配符搜索)的查詢(如導致引擎中查詢擴展)不能使用命中突出顯示。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

> [!IMPORTANT]
> 2020 年 7 月 15 日之後創建的服務將提供不同的突出顯示體驗。 該日期之前創建的服務在突出顯示行為中不會改變。 使用此更改,將僅返回與完整短語查詢匹配的短語。 此外,還可以指定為高光返回的片段大小。
>
> 編寫實現命中突出顯示的客戶端代碼時,請注意此更改。 請注意,除非您創建全新的搜索服務,否則這不會影響您。

## <a name="next-steps"></a>後續步驟

要快速產生用戶端的搜尋頁,請考慮以下選項:

+ [應用程式生成器](search-create-app-portal.md)在門戶中創建一個 HTML 頁面,其中包含搜索欄、分面導航和包含圖像的結果區域。
+ [在 C# 中建立第一個應用](tutorial-csharp-create-first-app.md)是建構功能用戶端的教程。 範例代碼展示分頁查詢、點擊突出顯示和排序。

幾個程式碼範例包括網頁前端介面,您可以在這裡找到:[紐約市工作示範應用程式](https://aka.ms/azjobsdemo)[,JavaScript 範例碼與即時展示網站](https://github.com/liamca/azure-search-javascript-samples), 並[認知搜尋 FrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd)。