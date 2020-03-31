---
title: 如何使用搜尋結果
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜索中構建和排序搜尋結果、獲取文檔計數並將內容導航添加到搜尋結果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 124f1ce3d30ce87d5e9d8fa027e5a7d6c0b3cb17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481597"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>如何在 Azure 認知搜索中處理搜尋結果
本文會講解如何實作搜尋結果頁面的標準項目，例如次數總計、擷取文件、排序次序和導覽。 向搜尋結果提供資料或資訊的頁面相關選項通過發送到 Azure 認知搜索服務的[搜索文檔](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)請求指定。 

在 REST API 中，要求會包含 GET 命令、路徑和查詢參數，這些會對服務通知要求是什麼，以及如何制訂回應。 在.NET SDK 中，對等 API 是 [DocumentSearchResult 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1)。

要快速生成用戶端的搜尋網頁，請流覽以下選項：

+ 使用門戶中[的應用程式產生器](search-create-app-portal.md)創建具有搜索欄、分面導航和結果區域的 HTML 頁面。
+ 按照[在 C# 教程中創建第一個應用](tutorial-csharp-create-first-app.md)來創建功能用戶端。

幾個代碼示例包括一個 Web 前端介面，您可以在這裡找到：[紐約市工作演示應用程式](https://aka.ms/azjobsdemo)[，JavaScript 示例代碼與即時演示網站](https://github.com/liamca/azure-search-javascript-samples)， 和[認知搜索FrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd)。

> [!NOTE]
> 有效的要求包含一些項目，例如服務 URL 及路徑、HTTP 動詞命令、`api-version` 等。 為求簡單明瞭，我們縮減此範例，只突顯與分頁相關的語法。 有關請求語法的詳細資訊，請參閱[Azure 認知搜索 REST API](https://docs.microsoft.com/rest/api/searchservice)。
>

## <a name="total-hits-and-page-counts"></a>總點擊數和頁面計數

顯示從查詢傳回的結果總數，然後以較小的區塊傳回這些結果，幾乎對所有搜尋頁面都相當基本。

![][1]

在 Azure 認知搜索中，`$count`使用`$top`和`$skip`參數來返回這些值。 下面的示例顯示了一個示例請求，請求在稱為"線上目錄"的索引上的總點擊次數，`@odata.count`返回于 ：

    GET /indexes/online-catalog/docs?$count=true

開始在第一頁擷取文件，以 15 個為一組，同時顯示總點擊數：

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

分頁結果需要 `$top` 和 `$skip`，其中 `$top` 指定有多少項目要批次傳回，而 `$skip` 指定有多少項目要略過。 在下列範例中，每個頁面顯示下 15 個項目，表示在 `$skip` 參數中的遞增跳躍。

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>配置

在搜尋結果頁面中，您可能會想要顯示縮圖、欄位子集以及完整產品頁面的連結。

 ![][2]

在 Azure 認知搜索中，`$select`將使用 和[搜索 API 請求](https://docs.microsoft.com/rest/api/searchservice/search-documents)來實現此體驗。

若要傳回並排版面配置的欄位子集：

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

不能直接搜尋影像和媒體檔案，且應儲存在其他儲存體平台，例如 Azure Blob 儲存體，以降低成本。 在索引和文件中，請定義儲存外部內容 URL 位址的欄位。 然後您可以使用此欄位做為影像參考。 此影像的 URL 應位於此文件中。

若要擷取 **onClick** 事件的產品描述頁面，請使用 [查閱文件](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) 來傳入此文件的金鑰以進行擷取。 此金鑰的資料類型為 `Edm.String`。 在此範例中為 *246810*。

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>根據相關性、評等或價格排序

排序次序通常預設為相關性，但也常見到立即可用的替代排序次序，好讓客戶可迅速重新將現有的結果排列為不同的排名次序。

 ![][3]

在 Azure 認知搜索中，排序基於`$orderby`運算式，對於索引為`"Sortable": true.`"子句"`$orderby`的所有欄位都是 OData 運算式。 如需語法的資訊，請參閱[篩選子句和 order-by 子句的 OData 運算式語法](query-odata-filter-orderby-syntax.md)。

相關性和評分設定檔密切相關。 您可使用預設評分，這會依賴文字分析和統計資料來對所有結果排名次序，與搜尋詞彙有更多或更密切符合的文件會有更高的評分。

替代的排序次序通常和 **onClick** 事件有關，這會回呼建置此排序次序的方法。 例如指定頁面項目如下：

 ![][4]

您可建立一個方法，它會接受所選取的排序選項做為輸入，然後對於和該選項相關的準則傳回已排序的清單。

 ![][5]

> [!NOTE]
> 雖然預設評分對大多數案例都已足夠，但我們建議改用自訂評分設定檔來建立相關性。 自訂評分設定檔給您提升項目的方式，這會對您的企業更有助益。 有關詳細資訊，請參閱[添加評分設定檔](index-add-scoring-profiles.md)。
>

## <a name="hit-highlighting"></a>搜尋結果醒目提示

您可以將格式應用於搜尋結果中的匹配項，從而輕鬆發現匹配項。 [查詢請求](https://docs.microsoft.com/rest/api/searchservice/search-documents)上提供了命中突出顯示說明。 

格式應用於整個術語查詢。 對部分術語（如模糊搜索或萬用字元搜尋）的查詢（如導致引擎中查詢擴展）不能使用命中突出顯示。

```http
POST /indexes/hotels/docs/search?api-version=2019-05-06 
    {  
      "search": "something",  
      "highlight": "Description"  
    }
```

> [!IMPORTANT]
> 2020 年 7 月 15 日之後創建的服務將提供不同的突出顯示體驗。 該日期之前創建的服務在突出顯示行為中不會改變。 使用此更改，將僅返回與完整短語查詢匹配的短語。 此外，還可以指定為高光返回的片段大小。
>
> 編寫實現命中突出顯示的用戶端代碼時，請注意此更改。 請注意，除非您創建全新的搜索服務，否則這不會影響您。

## <a name="faceted-navigation"></a>多面向導覽

搜尋導覽常見於結果頁面上，通常位於頁面的一側或頂端。 在 Azure 認知搜索中，分面導航基於預定義的篩選器提供自導搜索。 有關詳細資訊[，請參閱 Azure 認知搜索中的分面導航](search-faceted-navigation.md)。

## <a name="filters-at-the-page-level"></a>頁面層級的篩選器

如果解決方案設計包含特定類型內容的專用搜尋網頁（例如，在頁面頂部列出了部門的線上零售應用程式），則可以在**onClick**事件旁邊插入[篩選器運算式](search-filters.md)，以打開處於預篩選狀態的頁面。

您可以傳送篩選器，但不一定要有搜尋運算式。 例如，下列要求會篩選品牌名稱，只傳回符合該名稱的文件。

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

有關`$filter`運算式的詳細資訊[，請參閱搜索文檔（Azure 認知搜索 API）。](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

## <a name="see-also"></a>另請參閱

- [Azure 認知搜尋 REST API](https://docs.microsoft.com/rest/api/searchservice)
- [索引操作](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [文檔操作](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Azure 認知搜索中的分面導航](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
