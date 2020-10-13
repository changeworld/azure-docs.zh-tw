---
title: 排名相似性演算法
titleSuffix: Azure Cognitive Search
description: 如何設定相似性演算法來嘗試排名的新相似性演算法
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: 651e0635f0b556cd47adfccdbac59ef587570128
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535724"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Azure 認知搜尋中的排名演算法

> [!IMPORTANT]
> 自2020年7月15日起，新建立的搜尋服務將會自動使用 BM25 排名函式，在大部分情況下都經過證實，以提供比目前預設排名更佳的搜尋排名。 除了高階排名之外，BM25 也會根據檔案大小等因素，啟用微調結果的設定選項。  
>
> 有了這項變更，您很可能會看到搜尋結果的順序稍微變更。 如果您想要測試這項變更的影響，可在 api 版本 2019-05-06-Preview 和2020-06-30 中取得 BM25 演算法。  

本文說明如何使用預覽版 API，在現有的搜尋服務上使用新的 BM25 排名演算法來建立和查詢新的索引。

Azure 認知搜尋正在採用 Okapi BM25 演算法的官方 Lucene 實作為 *BM25Similarity*，這會取代先前使用的 *ClassicSimilarity* 實作為。 就像較舊的 ClassicSimilarity 演算法一樣，BM25Similarity 是 TF-IDF 的抓取函式，它會使用「詞彙頻率」 (TF) 和「反向檔頻率」 (IDF) 作為變數，以計算每個檔查詢配對的相關性分數，然後用於排名。 

雖然概念上類似于較舊的傳統相似性演算法，BM25 會在概率資訊抓取中取得其根目錄，以改善它。 BM25 也提供先進的自訂選項，例如允許使用者決定相關性分數如何隨相符詞彙的詞彙頻率進行調整。

## <a name="how-to-test-bm25-today"></a>如何立即測試 BM25

當您建立新的索引時，您可以設定 **相似性** 屬性來指定演算法。 您可以使用 `api-version=2019-05-06-Preview` ，如下所示或 `api-version=2020-06-30` 。

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

當兩個演算法都可供使用時，[ **相似性** ] 屬性在這段過渡期間很有用。 

| 屬性 | 描述 |
|----------|-------------|
| 相似度 | 選擇性。 有效的值包括 *"#Microsoft. ClassicSimilarity"* 或 *#Microsoft "BM25Similarity"*。 <br/> 于 `api-version=2019-05-06-Preview` 2020 年7月15日前建立的搜尋服務需要或更新版本。 |

若為2020年7月15日之後建立的新服務，則會自動使用 BM25，而且是唯一的相似性演算法。 如果您嘗試**similarity** `ClassicSimilarity` 在新的服務上設定相似性，將會傳回400錯誤，因為新的服務不支援該演算法。

若為2020年7月15日之前建立的現有服務，傳統相似性會維持預設演算法。 如果已省略 **相似性** 屬性，或將其設定為 null，則索引會使用傳統演算法。 如果您想要使用新的演算法，就必須設定 **相似性** ，如上所述。

## <a name="bm25-similarity-parameters"></a>BM25 相似性參數

BM25 相似性會新增兩個使用者自訂的參數，以控制計算的相關性分數。

### <a name="k1"></a>k1

*版 k1*參數會控制每個相符詞彙的詞彙頻率與檔查詢組的最終相關性分數之間的縮放功能。

值為零代表「二進位模型」，其中單一相符詞彙的占數對於所有相符的檔都相同（不論該詞彙出現在文字中多少次），而較大的版 k1 值則允許分數在檔中找到更多的相同詞彙實例時繼續增加。 根據預設，Azure 認知搜尋會針對版 k1 參數使用1.2 的值。 使用較高的版 k1 值可能很重要，因為我們預期有多個詞彙是搜尋查詢的一部分。 在這些情況下，我們可能會想要偏好符合許多不同查詢詞彙的檔，而這些檔只會比對一次，而不是多次。 例如，在查詢包含 "Apollo Spaceflight" 詞彙的檔索引時，我們可能會想要減少一篇有關希臘文神話的分數，其中包含 "Apollo" 一詞，而不提及 "Spaceflight"，相較于另一篇明確提及 "Apollo" 和 "Spaceflight" 的文章。 
 
### <a name="b"></a>b

*B*參數控制檔長度如何影響相關性分數。

值為0.0 時，表示檔的長度不會影響分數，而1.0 的值則表示在相關性分數上，詞彙頻率的影響將依檔長度正規化。 B 參數的 Azure 認知搜尋中使用的預設值是0.75。 當我們想要會較長的檔時，以檔的長度正規化詞彙頻率會很有用。 在某些情況下，較長的檔 (（例如完整的新穎) ）比較有可能包含許多不相關的詞彙（相較于較短的檔）。

### <a name="setting-k1-and-b-parameters"></a>設定版 k1 和 b 參數

若要自訂 b 或版 k1 值，只要在使用 BM25 時將它們新增為相似性物件的屬性即可：

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

相似性演算法只能在建立索引時設定。 這表示使用的相似性演算法無法針對現有的索引變更。 當您更新使用 BM25 的現有索引定義時，可以修改 *"b"* 和 *"版 k1"* 參數。 在現有的索引上變更這些值，會讓索引離線至少幾秒鐘，導致您的索引編制和查詢要求失敗。 因此，您將需要在更新要求的查詢字串中設定 "allowIndexDowntime = true" 參數：

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

## <a name="see-also"></a>另請參閱  

+ [REST API 參考](/rest/api/searchservice/)
+ [將評分設定檔新增至索引](index-add-scoring-profiles.md)
+ [建立索引 API](/rest/api/searchservice/create-index)
+ [Azure 認知搜尋 .NET SDK](/dotnet/api/overview/azure/search)