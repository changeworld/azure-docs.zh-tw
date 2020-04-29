---
title: 排名相似性演算法
titleSuffix: Azure Cognitive Search
description: 如何設定相似性演算法以嘗試排名的新相似性演算法
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: 1975c13162316b4132bae34659b1c5af8e416573
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231606"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Azure 認知搜尋中的排名演算法

> [!IMPORTANT]
> 從2020年7月15日開始，新建立的搜尋服務會自動使用 BM25 排名函式，這在大部分情況下已證實，可提供比目前預設排名更適合使用者期望的搜尋排名。 除了高階排名之外，BM25 也會根據檔案大小等因素，啟用微調結果的設定選項。  
>
> 有了這項變更，您很可能會看到搜尋結果的排序有些許變更。 對於想要測試此變更影響的使用者，BM25 演算法會在 api 版本 2019-05-06-Preview 中提供。  

本文說明如何在現有的搜尋服務上使用新的 BM25 排名演算法，以取得使用預覽 API 建立及查詢的新索引。

Azure 認知搜尋正在採用官方 Lucene 實 Okapi BM25 演算法*BM25Similarity*，這會取代先前使用的*ClassicSimilarity*實作為。 如同較舊的 ClassicSimilarity 演算法，BM25Similarity 是一種類似 TF 的函數，它會使用「詞彙頻率」（TF）和「反向檔頻率」（IDF）做為變數來計算每個檔查詢配對的相關性分數，然後用於排名。 

雖然在概念上類似于較舊的傳統相似性演算法，BM25 會在概率資訊抓取中取得其根，以在其上進行改善。 BM25 也提供先進的自訂選項，例如允許使用者決定相關性分數如何根據符合詞彙的詞彙頻率來調整。

## <a name="how-to-test-bm25-today"></a>如何立即測試 BM25

當您建立新的索引時，您可以設定**相似性**屬性來指定演算法。 您將需要使用`api-version=2019-05-06-Preview`，如下所示。

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

當這兩種演算法都可供使用時，**相似性**屬性在這段期間很有用，僅適用于現有的服務。 

| 屬性 | 描述 |
|----------|-------------|
| 相似度 | 選擇性。 有效值包括 *"#Microsoft ClassicSimilarity"* 或 *"#Microsoft". BM25Similarity "*。 <br/> 在`api-version=2019-05-06-Preview` 2020 年7月15日前建立的搜尋服務上需要或更新版本。 |

如果是在2020年7月15日之後建立的新服務，則會自動使用 BM25，而且它是唯一的相似性演算法。 如果您嘗試`ClassicSimilarity`在新的服務上設定**相似性**，則會傳回400錯誤，因為新的服務不支援該演算法。

針對在2020年7月15日前建立的現有服務，傳統的相似性仍然是預設演算法。 如果已省略**相似性**屬性或將其設定為 null，則索引會使用傳統演算法。 如果您想要使用新的演算法，您將需要設定**相似性**，如上所述。

## <a name="bm25-similarity-parameters"></a>BM25 相似性參數

BM25 相似性會加入兩個使用者可自訂的參數，以控制所計算的相關性分數。

### <a name="k1"></a>k1

*版 k1*參數會控制每個比對詞彙的詞彙頻率與檔查詢組最終相關性分數之間的縮放功能。

值為零表示「二進位模型」，其中單一比對詞彙的貢獻對所有相符的檔而言都相同，不論該詞彙在文字中出現多少次，而較大的版 k1 值則允許分數繼續增加，因為在檔中找到相同詞彙的多個實例。 根據預設，Azure 認知搜尋會針對版 k1 參數使用1.2 的值。 在我們預期有多個詞彙屬於搜尋查詢的情況下，使用較高的版 k1 值可能很重要。 在這些情況下，我們可能會想要比對只比對一次只比對單一檔的多個不同查詢詞彙的檔。 例如，查詢包含「Apollo Spaceflight」詞彙之檔的索引時，我們可能會想要降低文章的分數，其中包含「Apollo」一詞，而不會提及「Spaceflight」，相較于另一個同時明確提及「Apollo」和「Spaceflight」的文章。 
 
### <a name="b"></a>b

*B*參數會控制檔長度對相關性分數的影響。

值為0.0 表示檔的長度不會影響分數，而1.0 的值則表示對相關性分數之詞彙頻率的影響會由檔的長度正規化。 Azure 認知搜尋中用於 b 參數的預設值為0.75。 當我們想要會較長的檔時，根據檔長度將詞彙頻率正規化會很有用。 在某些情況下，較長的檔（例如完整 novel）可能包含許多不相關的詞彙，相較于更短的檔。

### <a name="setting-k1-and-b-parameters"></a>設定版 k1 和 b 參數

若要自訂 b 或版 k1 值，請只在使用 BM25 時，將它們新增為相似性物件的屬性：

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

相似性演算法只能在索引建立時設定。 這表示所使用的相似性演算法無法針對現有的索引進行變更。 當您更新使用 BM25 的現有索引定義時，可以修改 *"b"* 和 *"版 k1"* 參數。 在現有的索引上變更這些值，會讓索引至少有幾秒鐘的時間離線，導致您的索引編制和查詢要求失敗。 因此，您必須在更新要求的查詢字串中設定 "allowIndexDowntime = true" 參數：

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

## <a name="see-also"></a>請參閱  

+ [REST API 參考](https://docs.microsoft.com/rest/api/searchservice/)   
+ [將評分設定檔新增至您的索引](index-add-scoring-profiles.md)    
+ [建立索引 API](https://docs.microsoft.com/rest/api/searchservice/create-index)   
+ [Azure 認知搜尋 .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
