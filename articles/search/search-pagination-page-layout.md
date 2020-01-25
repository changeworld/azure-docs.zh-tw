---
title: 如何使用搜尋結果
titleSuffix: Azure Cognitive Search
description: 結構和排序搜尋結果、取得檔計數，以及在 Azure 認知搜尋中將內容導覽新增至搜尋結果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: c32e58a43b5409fd9f8ede536167d185270c6a22
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721569"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中使用搜尋結果
本文會講解如何實作搜尋結果頁面的標準項目，例如次數總計、擷取文件、排序次序和導覽。 將資料或資訊提供給搜尋結果的頁面相關選項，會透過傳送至 Azure 認知搜尋服務的[搜尋檔](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)要求來指定。 

在 REST API 中，要求會包含 GET 命令、路徑和查詢參數，這些會對服務通知要求是什麼，以及如何制訂回應。 在.NET SDK 中，對等 API 是 [DocumentSearchResult 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1)。

若要為您的用戶端快速產生搜尋頁面，請探索下列選項：

+ 使用入口網站中的[應用程式](search-create-app-portal.md)產生器，建立具有搜尋列、多面向導覽和結果區域的 HTML 網頁。
+ 遵循在教學課程[中C#建立您的第一個應用程式](tutorial-csharp-create-first-app.md)，以建立功能用戶端。

有幾個程式碼範例包含 web 前端介面，您可以在這裡找到：[紐約的作業示範應用程式](https://azjobsdemo.azurewebsites.net/)、 [JavaScript 使用即時示範網站的範例程式碼](https://github.com/liamca/azure-search-javascript-samples)，以及[CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd)。

> [!NOTE]
> 有效的要求包含一些項目，例如服務 URL 及路徑、HTTP 動詞命令、`api-version` 等。 為求簡單明瞭，我們縮減此範例，只突顯與分頁相關的語法。 如需要求語法的詳細資訊，請參閱[Azure 認知搜尋 REST api](https://docs.microsoft.com/rest/api/searchservice)。
>

## <a name="total-hits-and-page-counts"></a>總點擊數和頁面計數

顯示從查詢傳回的結果總數，然後以較小的區塊傳回這些結果，幾乎對所有搜尋頁面都相當基本。

![][1]

在「Azure 認知搜尋」中，您可以使用 `$count`、`$top`和 `$skip` 參數來傳回這些值。 下列範例會顯示名為「線上-目錄」之索引的點擊總數範例要求，以 `@odata.count`傳回：

    GET /indexes/online-catalog/docs?$count=true

開始在第一頁擷取文件，以 15 個為一組，同時顯示總點擊數：

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

分頁結果需要 `$top` 和 `$skip`，其中 `$top` 指定有多少項目要批次傳回，而 `$skip` 指定有多少項目要略過。 在下列範例中，每個頁面顯示下 15 個項目，表示在 `$skip` 參數中的遞增跳躍。

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>Layout

在搜尋結果頁面中，您可能會想要顯示縮圖、欄位子集以及完整產品頁面的連結。

 ![][2]

在 Azure 認知搜尋中，您會使用 `$select` 和[搜尋 API 要求](https://docs.microsoft.com/rest/api/searchservice/search-documents)來執行這項體驗。

若要傳回並排版面配置的欄位子集：

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

不能直接搜尋影像和媒體檔案，且應儲存在其他儲存體平台，例如 Azure Blob 儲存體，以降低成本。 在索引和文件中，請定義儲存外部內容 URL 位址的欄位。 然後您可以使用此欄位做為影像參考。 此影像的 URL 應位於此文件中。

若要擷取 **onClick** 事件的產品描述頁面，請使用 [查閱文件](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) 來傳入此文件的金鑰以進行擷取。 此金鑰的資料類型為 `Edm.String`。 在此範例中為 *246810*。

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>根據相關性、評等或價格排序

排序次序通常預設為相關性，但也常見到立即可用的替代排序次序，好讓客戶可迅速重新將現有的結果排列為不同的排名次序。

 ![][3]

在「Azure 認知搜尋」中，排序是以 `$orderby` 運算式為基礎，而所有索引為 `"Sortable": true.` `$orderby` 子句為 OData 運算式的欄位。 如需語法的資訊，請參閱[篩選子句和 order-by 子句的 OData 運算式語法](query-odata-filter-orderby-syntax.md)。

相關性和評分設定檔密切相關。 您可使用預設評分，這會依賴文字分析和統計資料來對所有結果排名次序，與搜尋詞彙有更多或更密切符合的文件會有更高的評分。

替代的排序次序通常和 **onClick** 事件有關，這會回呼建置此排序次序的方法。 例如指定頁面項目如下：

 ![][4]

您可建立一個方法，它會接受所選取的排序選項做為輸入，然後對於和該選項相關的準則傳回已排序的清單。

 ![][5]

> [!NOTE]
> 雖然預設評分對大多數案例都已足夠，但我們建議改用自訂評分設定檔來建立相關性。 自訂評分設定檔給您提升項目的方式，這會對您的企業更有助益。 如需詳細資訊，請參閱 [新增評分設定檔](index-add-scoring-profiles.md)。
>

## <a name="hit-highlighting"></a>搜尋結果醒目提示

您可以將格式套用至搜尋結果中的相符詞彙，讓它更容易找到相符的。 [查詢要求](https://docs.microsoft.com/rest/api/searchservice/search-documents)上會提供搜尋反白顯示指示。 

將格式套用至整個詞彙查詢。 對部分詞彙的查詢（例如模糊搜尋或在引擎中導致查詢展開的萬用字元搜尋）無法使用命中結果醒目提示。

```http
POST /indexes/hotels/docs/search?api-version=2019-05-06 
    {  
      "search": "something",  
      "highlight": "Description"  
    }
```



## <a name="faceted-navigation"></a>多面向導覽

搜尋導覽常見於結果頁面上，通常位於頁面的一側或頂端。 在 Azure 認知搜尋中，多面向導覽會根據預先定義的篩選準則提供自我導向的搜尋。 如需詳細資訊，請參閱[Azure 認知搜尋中](search-faceted-navigation.md)的多面向導覽。

## <a name="filters-at-the-page-level"></a>頁面層級的篩選器

如果您的解決方案設計包含特定類型內容的專用搜尋頁面（例如，在頁面頂端列出部門的線上零售應用程式），您可以在**onClick**事件旁邊插入[篩選條件運算式](search-filters.md)，以在預先篩選的狀態中開啟頁面。

您可以傳送篩選器，但不一定要有搜尋運算式。 例如，下列要求會篩選品牌名稱，只傳回符合該名稱的文件。

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

如需 `$filter` 運算式的詳細資訊，請參閱[搜尋檔（Azure 認知搜尋 API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 。

## <a name="see-also"></a>另請參閱

- [Azure 認知搜尋 REST API](https://docs.microsoft.com/rest/api/searchservice)
- [索引作業](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [文件作業](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Azure 認知搜尋中的多面向導覽](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
