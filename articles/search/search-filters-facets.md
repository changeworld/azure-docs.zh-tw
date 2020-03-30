---
title: 用於應用中搜索導航的分面篩選器
titleSuffix: Azure Cognitive Search
description: 按使用者安全標識、地理位置或數值篩選準則，以減少 Azure 認知搜索（Microsoft Azure 上的託管雲搜索服務）中查詢的搜尋結果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 082575a67ea43d62f322e177cff087e5bd572c27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792887"
---
# <a name="how-to-build-a-facet-filter-in-azure-cognitive-search"></a>如何在 Azure 認知搜索中構建分面篩選器 

分面導航用於對搜索應用中的查詢結果進行自導篩選，其中應用程式提供 UI 控制項，用於將搜尋範圍範圍到文檔組（例如類別或品牌），Azure 認知搜索提供資料結構支援體驗。 本文將快速檢閱用於建立多面向導覽結構以支持您想要提供之搜尋體驗的基本步驟。 

> [!div class="checklist"]
> * 選擇用以篩選和設定 Facet 的欄位
> * 在欄位上設定屬性
> * 建置索引和載入資料
> * 將 Facet 篩選條件新增至查詢
> * 處理結果

Facet 是動態且會在查詢中傳回。 搜尋回應會為它們提供用來瀏覽結果的 Facet 類別。 如果您不熟悉 Facet，下列範例是多面向導覽結構的說明。

  ![](./media/search-filters-facets/facet-nav.png)

多面向導覽的新手，想要更詳細說明嗎？ 請參閱[如何在 Azure 認知搜索中實現分面導航](search-faceted-navigation.md)。

## <a name="choose-fields"></a>選擇欄位

Facet 可透過單一值欄位以及集合來計算。 在分面導航中工作最好的欄位的基數較低：搜索語料庫中文檔中重複的少量不同值（例如，顏色、國家/地區或品牌名稱的清單）。 

當您通過將`facetable`屬性設置為`true`創建索引時，將逐欄位啟用面面。 通常，還應為此類欄位`filterable`設置屬性`true`，以便搜索應用程式可以基於最終使用者選擇的面面對這些欄位進行篩選。 

使用 REST API 創建索引時，可在分面導航中使用的任何[欄位類型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)`facetable`在預設情況下標記為：

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ 數位欄位類型： `Edm.Int32` `Edm.Int64`， ，`Edm.Double`
+ 上述類型的集合（例如，`Collection(Edm.String)`或`Collection(Edm.Double)`）

不能在`Edm.GeographyPoint`分面`Collection(Edm.GeographyPoint)`導航中使用 或 欄位。 在基數低的欄位中，面面工作最佳。 由於地理座標的解析度，在給定的集中集中，任何兩組座標都很少相等。 因此，不支援針對地理座標使用 Facet。 您必須依位置來為城市或區域欄位設定 Facet。

## <a name="set-attributes"></a>設定屬性

控制如何使用欄位的索引屬性會新增至索引中的個別欄位定義。 在下面的示例中，基數低、可用於面面的欄位包括：（`category`酒店、汽車旅館、旅館）`tags`和`rating`。 這些欄位在以下`filterable`示例中`facetable`顯式設置了 和 屬性，以便說明目的。 

> [!Tip]
> 適用於效能和儲存體最佳化的最佳做法是，針對永遠不應作為 Facet 使用的欄位關閉 Facet。 特別是，應設置唯一值（如 ID 或產品名稱）的字串欄位，`"facetable": false`以防止它們在分面導航中意外（無效）使用。


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> 此索引定義是從使用[REST API 創建 Azure 認知搜索索引複製的](https://docs.microsoft.com/azure/search/search-create-index-rest-api)。 在欄位定義中，除了外表差異之外，其餘的都一樣。 和`filterable``facetable`屬性顯式添加到 、、`category``tags``parkingIncluded`和`smokingAllowed``rating`欄位上。 實際上，在`filterable`使用`facetable`REST API 時，預設情況下將在這些欄位上啟用。這些欄位將啟用。 使用 .NET SDK 時，必須顯式啟用這些屬性。

## <a name="build-and-load-an-index"></a>建置和載入索引

有個中繼 (且可能明顯) 的步驟是，您必須先[建立並填入索引](https://docs.microsoft.com/azure/search/search-get-started-dotnet#1---create-index)，之後才能編寫查詢。 我們基於完整性而在這裡提及此步驟。 判斷索引是否可用的方法之一是在[入口網站](https://portal.azure.com)中檢查索引清單。

## <a name="add-facet-filters-to-a-query"></a>將 Facet 篩選條件新增至查詢

在應用程式的程式碼中，建構一個查詢，以指定有效查詢的所有部分，包括搜尋運算式、Facet、篩選條件、評分設定檔 (制訂要求使用的任何項目)。 下列範例會建置一個要求，根據住宿、評等及其他便利設施的類型來建立多面向導覽。

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>傳回 Click 事件的篩選結果

當最終使用者按一下分面值時，按一下事件的處理常式應使用篩選器運算式來實現使用者的意圖。 給定一`category`個分面，按一下類別"motel"是使用選擇該`$filter`類型的住宿的運算式實現的。 當使用者按一下"汽車旅館"以指示只顯示汽車旅館時，應用程式發送的下一個查詢包括`$filter=category eq 'motel'`。

如果使用者從類別 Facet 選取值，下列程式碼片段就會將類別 新增至篩選條件。

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

如果使用者按一下集合欄位（例如`tags`，值"池"）的分面值，則應用程式應使用以下篩選器語法：`$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>祕訣和因應措施

### <a name="initialize-a-page-with-facets-in-place"></a>就地將具有 Facet 的頁面初始化

如果您想要就地將具有 Facet 的頁面初始化，則可以在頁面初始化過程中傳送查詢，以植入具有最初 Facet 結構的頁面。

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>以非同步方式保留篩選結果的多面向導覽結構

Azure 認知搜索中分面導航的挑戰之一是，僅針對當前結果存在分面。 實務上常會保留一組靜態的 Facet，讓使用者可以反向瀏覽，回溯步驟以透過搜尋內容探索替代路徑。 

儘管這是一個常見使用案例，但並不是多面向導覽目前預設提供的功能。 需要靜態 Facet 的開發人員通常可藉由發出兩個篩選查詢來解決限制：一個將範圍設為結果，另一個則基於導覽目的用來建立靜態的 Facet 清單。

## <a name="see-also"></a>另請參閱

+ [Azure 認知搜索中的篩選器](search-filters.md)
+ [建立索引 REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [搜索文檔 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
