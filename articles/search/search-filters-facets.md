---
title: 應用程式中搜尋導覽的 Facet 篩選
titleSuffix: Azure Cognitive Search
description: 依使用者安全性身分識別、地理位置或數值來篩選準則，以減少 Azure 認知搜尋（Microsoft Azure 上託管的雲端搜尋服務）中查詢的搜尋結果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 852d8f8f85536dc62dd792e5727dd7ec0571ba29
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87084206"
---
# <a name="how-to-build-a-facet-filter-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中建立 facet 篩選 

多面向導覽用於在搜尋應用程式中對查詢結果進行自我導向篩選，其中您的應用程式會提供用來將搜尋範圍限定于檔群組的 UI 控制項（例如，類別或品牌），而 Azure 認知搜尋則提供資料結構來備份體驗。 本文將快速檢閱用於建立多面向導覽結構以支持您想要提供之搜尋體驗的基本步驟。 

> [!div class="checklist"]
> * 選擇用以篩選和設定 Facet 的欄位
> * 在欄位上設定屬性
> * 建置索引和載入資料
> * 將 Facet 篩選條件新增至查詢
> * 處理結果

Facet 是動態且會在查詢中傳回。 搜尋回應會為它們提供用來瀏覽結果的 Facet 類別。 如果您不熟悉 Facet，下列範例是多面向導覽結構的說明。

  ![顯示搜尋對話方塊的影像，其中包含依商務標題分組的篩選搜尋結果。 箭號表示結果是顯示在 facet 導覽結構中的 facet。](./media/search-filters-facets/facet-nav.png)

多面向導覽的新手，想要更詳細說明嗎？ 瞭解[如何在 Azure 認知搜尋中執行](search-faceted-navigation.md)多面向導覽。

## <a name="choose-fields"></a>選擇欄位

Facet 可透過單一值欄位以及集合來計算。 最適合多面向導覽的欄位具有低基數：在搜尋主體的檔中重複使用的少數相異值（例如，色彩、國家/地區或品牌名稱的清單）。 

當您藉由將屬性設定為來建立索引時，會逐欄位啟用 facet `facetable` `true` 。 您通常也應該將 `filterable` 這類欄位的屬性設定為， `true` 讓您的搜尋應用程式可以根據使用者選取的 facet 來篩選這些欄位。 

使用 REST API 建立索引時，可能會在多面向導覽中使用的任何[欄位類型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)，預設會標示為 `facetable` ：

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ 數值欄位類型： `Edm.Int32` 、 `Edm.Int64` 、`Edm.Double`
+ 上述類型的集合（例如 `Collection(Edm.String)` 或 `Collection(Edm.Double)` ）

您不能 `Edm.GeographyPoint` 在多面向 `Collection(Edm.GeographyPoint)` 導覽中使用或欄位。 Facet 最適用于具有低基數的欄位。 由於地理座標的解析，在指定的資料集中，任何兩組共座標都是相等的。 因此，不支援針對地理座標使用 Facet。 您必須依位置來為城市或區域欄位設定 Facet。

## <a name="set-attributes"></a>設定屬性

控制如何使用欄位的索引屬性會新增至索引中的個別欄位定義。 在下列範例中，基數較低的欄位（適用于 facet）包含： `category` （飯店、motel、hostel）、 `tags` 和 `rating` 。 在下列範例中，這些欄位已 `filterable` `facetable` 明確設定和屬性，以供說明之用。 

> [!Tip]
> 適用於效能和儲存體最佳化的最佳做法是，針對永遠不應作為 Facet 使用的欄位關閉 Facet。 具體而言，唯一值的字串欄位（例如識別碼或產品名稱）應該設定為， `"facetable": false` 以防止其在多面向導覽中的意外（且無效）使用。


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
> 此索引定義是從[使用 REST API 建立 Azure 認知搜尋索引](https://docs.microsoft.com/azure/search/search-create-index-rest-api)複製而來。 在欄位定義中，除了外表差異之外，其餘的都一樣。 `filterable`和 `facetable` 屬性會明確新增至 `category` 、、、 `tags` `parkingIncluded` `smokingAllowed` 和 `rating` 欄位。 在實務 `filterable` 上， `facetable` 使用 REST API 時，預設會在這些欄位上啟用和。 使用 .NET SDK 時，必須明確地啟用這些屬性。

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

當終端使用者按一下 facet 值時，click 事件的處理常式應該使用篩選運算式來實現使用者的意圖。 在給定 `category` facet 的情況下，按一下類別 "motel" 時，會使用 `$filter` 可選取該類型的住宿運算式來執行。 當使用者按一下 [motel] 表示只顯示 motels 時，應用程式所傳送的下一個查詢會包含 `$filter=category eq 'motel'` 。

如果使用者從類別 Facet 選取值，下列程式碼片段就會將類別 新增至篩選條件。

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

如果使用者按一下集合欄位的 facet 值（例如「集區」 `tags` 值），您的應用程式應該使用下列篩選語法：`$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>祕訣和因應措施

### <a name="initialize-a-page-with-facets-in-place"></a>就地將具有 Facet 的頁面初始化

如果您想要就地將具有 Facet 的頁面初始化，則可以在頁面初始化過程中傳送查詢，以植入具有最初 Facet 結構的頁面。

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>以非同步方式保留篩選結果的多面向導覽結構

Azure 認知搜尋中的 facet 導覽的其中一項挑戰是，facet 僅存在於目前的結果中。 實務上常會保留一組靜態的 Facet，讓使用者可以反向瀏覽，回溯步驟以透過搜尋內容探索替代路徑。 

儘管這是一個常見使用案例，但並不是多面向導覽目前預設提供的功能。 需要靜態 Facet 的開發人員通常可藉由發出兩個篩選查詢來解決限制：一個將範圍設為結果，另一個則基於導覽目的用來建立靜態的 Facet 清單。

## <a name="see-also"></a>另請參閱

+ [Azure 認知搜尋中的篩選](search-filters.md)
+ [建立索引 REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [搜尋檔 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
