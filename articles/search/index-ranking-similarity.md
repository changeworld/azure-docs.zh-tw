---
title: 排名相似性演算法
titleSuffix: Azure Cognitive Search
description: 如何設置相似性演算法以嘗試新的相似性演算法進行排名
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: c327440649300533c94c2a1956e3c45f433c9780
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409969"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Azure 認知搜索中的排名演算法

> [!IMPORTANT]
> 從 2020 年 7 月 15 日起，新創建的搜索服務將使用 BM25 排名功能，在大多數情況下，該功能已證明可提供與使用者期望更好的搜索排名，而不是當前的預設排名。  除了卓越的排名之外，BM25 還支援基於文檔大小等因素調整結果的配置選項。  
>
> 有了此更改，您很可能會看到搜尋結果的順序略有變化。   對於那些想要測試此更改的影響的使用者，我們在 2019-05-06-預覽 API 中提供了啟用 BM25 在新索引上評分的能力。  

本文介紹如何更新 2020 年 7 月 15 日之前創建的服務，以使用新的 BM25 排名演算法。

Azure 認知搜索將使用 Okapi BM25 演算法*BM25 相似性*的官方 Lucene 實現，該演算法將取代以前使用*的經典相似性*實現。 與較舊的經典相似性演算法一樣，BM25相似性是一種類似于TF-IDF的檢索函數，它使用術語頻率 （TF） 和反向文檔頻率 （IDF） 作為變數來計算每個文檔查詢對的相關性分數，然後計算用於排名。 雖然BM25在概念上與舊的經典相似性演算法相似，但它在概率資訊檢索中具有根，以改進它。 BM25 還提供高級自訂選項，例如允許使用者決定相關性分數如何與匹配術語的術語頻率一起縮放。

## <a name="how-to-test-bm25-today"></a>今天如何測試BM25

創建新索引時，可以設置"相似性"屬性。 您需要使用*2019-05-06 預覽*版，如下所示。

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

對於在 2020 年 7 月 15 日之前創建的服務：如果省略相似性或設置為 null，則索引將使用舊的經典相似性演算法。

對於 2020 年 7 月 15 日之後創建的服務：如果省略相似性或設置為 null，則索引將使用新的 BM25 相似性演算法。

您還可以顯式將相似性值設置為以下兩個值之一 *："#Microsoft.Azure.Search.Classic.經典相似性"* 或 *"#Microsoft.Azure.Search.BM25 相似性"。*


## <a name="bm25-similarity-parameters"></a>BM25 相似性參數

BM25 相似性添加了兩個使用者可自訂的參數來控制計算的相關性分數：

### <a name="k1"></a>k1

*k1*參數控制每個匹配術語的術語頻率與文檔查詢對的最終相關性分數之間的縮放函數。

值零表示"二進位模型"，其中單個匹配項的貢獻對於所有匹配文檔都是相同的，而該術語在文本中出現的次數如何，而較大的 k1 值允許分數繼續隨著更多時間增加而增加同一術語的實例在文檔中找到。 預設情況下，Azure 認知搜索對 k1 參數使用值 1.2。 在我們希望多個術語成為搜索查詢的一部分的情況下，使用較高的 k1 值可能很重要。 在這些情況下，我們可能希望支援與搜索的許多不同的查詢詞匹配的文檔，這些查詢詞僅與單個查詢詞匹配多次。 例如，在查詢包含術語"阿波羅太空飛行"的文檔索引時，我們可能希望降低一篇關於希臘神話的文章的分數，該文章包含"阿波羅"一詞幾十次，而沒有提到"太空飛行"，與另一篇文章，明確提到"阿波羅"和"太空飛行"只有幾次。 
 
### <a name="b"></a>b

*b*參數控制文檔的長度如何影響相關性分數。

值 0.0 表示文檔的長度不會影響分數，而值 1.0 表示術語頻率對相關性分數的影響將按文檔的長度進行正常化。 Azure 認知搜索 b 參數使用的預設值為 0.75。 在我們想要懲罰較長的文檔的情況下，按文檔長度將術語頻率正常化非常有用。 在某些情況下，與較短的文檔相比，較長的文檔（如完整的小說）更有可能包含許多不相關的術語。

### <a name="setting-k1-and-b-parameters"></a>設置 k1 和 b 參數

要自訂 b 或 k1 值，只需將它們作為屬性添加到相似性物件時，使用 BM25：

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

相似性演算法只能在索引創建時設置。 這意味著不能更改現有索引所使用的相似性演算法。 在更新使用 BM25 的現有索引定義時，可以修改 *"b"* 和 *"k1"* 參數。 更改現有索引上的這些值將使索引離線至少幾秒鐘，從而導致索引和查詢請求失敗。 因此，您需要在更新要求的查詢字串中設置"allowIndexDowntime_true"參數：

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```


## <a name="see-also"></a>另請參閱  

 [Azure 認知搜索 REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [將評分設定檔添加到索引](index-add-scoring-profiles.md)    
 [創建索引&#40;Azure 認知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
  [Azure 認知搜索 .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
