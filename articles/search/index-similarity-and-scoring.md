---
title: 相似性和計分總覽
titleSuffix: Azure Cognitive Search
description: 說明相似性和計分的概念，以及開發人員可自訂評分結果的功能。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 4b02039c86f43e6bebed58dfff475816f09a3da1
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890135"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Azure 認知搜尋中的相似性和計分

計分是指在全文檢索搜尋查詢的搜尋結果中傳回的每個專案的搜尋分數計算。 分數是某個項目在目前搜尋作業的內容中有何相關性的指標。 分數越高，該項目的相關性就愈高。 在搜尋結果中，項目會根據為每個項目計算的搜尋分數由高至低排序。 

根據預設，回應中會傳回前50，但您可以使用 **$top**參數，在單一回應中傳回較小或較大數目的專案（最多1000個），然後 **$skip**以取得下一組結果。

搜尋分數會根據資料和查詢的統計屬性來計算。 Azure 認知搜尋會尋找符合搜尋詞彙的檔（部分或全部，視[searchMode](https://docs.microsoft.com/rest/api/searchservice/search-documents#searchmodeany--all-optional)而定），優先列出包含多個搜尋詞彙實例的檔。 如果詞彙在資料索引中很罕見，但在檔中是常見的，搜尋分數會更高。 這種計算相關性的方法基礎稱為*TF-IDF 或*「詞彙頻率-反向檔頻率」。

搜尋分數值可以在整個結果集內重複。 當多個叫用相同的搜尋分數時，將不會定義相同計分專案的順序，而且也不穩定。 再次執行查詢，您可能會看到專案移位位置，特別是當您使用免費服務或具有多個複本的可計費服務時。 若有兩個項目的分數完全相同，則無法保證哪個項目先出現。

如果您想要中斷重複分數之間的系結，您可以依分數加入 **$orderby**子句，然後依另一個可排序的欄位排序（例如`$orderby=search.score() desc,Rating desc`）。 如需詳細資訊，請參閱[$orderby](https://docs.microsoft.com/azure/search/search-query-odata-orderby)。

> [!NOTE]
> `@search.score = 1.00`表示未評分或未排序的結果集。 分數在所有結果中都是一致的。 當查詢表單為模糊搜尋、萬用字元或 RegEx 查詢，或 **$filter**運算式時，就會發生未計分的結果。 

## <a name="scoring-profiles"></a>評分設定檔

您可以定義自訂*評分設定檔*，來自訂不同欄位的排名方式。 評分設定檔可讓您更佳地控制搜尋結果中的項目排名。 舉例來說，您可能想根據營收潛力提升某些項目、亦或是提升新項目或庫存過久的項目。 

評分設定檔是索引定義的一部分，由加權欄位、函數和參數組成。 如需定義它的詳細資訊，請參閱[評分設定檔](index-add-scoring-profiles.md)。

## <a name="scoring-statistics"></a>計分統計資料

針對擴充性，Azure 認知搜尋會透過分區化程式水準散發每個索引，這表示索引的某些部分會實際分開。

根據預設，檔的分數是依據*分區內*資料的統計屬性計算而得。 這種方法通常不是大型資料主體的問題，而且比起根據所有分區的資訊來計算分數，它提供的效能較佳。 話雖如此，使用這項效能優化可能會導致兩個非常相似的檔（或甚至相同的檔）在不同的分區中最後有不同的相關性分數。

如果您想要根據所有分區的統計屬性來計算分數，您可以新增*scoringStatistics = global*做為[查詢參數](https://docs.microsoft.com/rest/api/searchservice/search-documents)（或新增 *"scoringStatistics"： "global"* 做為[查詢要求](https://docs.microsoft.com/rest/api/searchservice/search-documents)的主體參數）。

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global
  Content-Type: application/json
  api-key: [admin key]  
```

> [!NOTE]
> `scoringStatistics`參數需要管理 api 金鑰。

## <a name="similarity-ranking-algorithms"></a>相似性排名演算法

Azure 認知搜尋支援兩種不同的相似性排名演算法：*傳統的相似性*演算法和*Okapi BM25*演算法的官方實作為（目前處於預覽階段）。 傳統相似性演算法是預設演算法，但從7月15日開始，任何在該日期之後建立的新服務都會使用新的 BM25 演算法。 這會是新服務上唯一可用的演算法。

目前，您可以指定想要使用的相似性排序演算法。 如需詳細資訊，請參閱[排名演算法](index-ranking-similarity.md)。

下列影片區段會快速轉寄至 Azure 認知搜尋中使用的排名演算法說明。 您可以觀看完整的影片，以取得更多背景資訊。

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=322&end=643]

## <a name="see-also"></a>另請參閱

 [計分設定檔](index-add-scoring-profiles.md) [REST API 參考](https://docs.microsoft.com/rest/api/searchservice/)   
 [搜尋檔 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)   
 [Azure 認知搜尋 .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
