---
title: 瞭解 O 資料收集篩選器
titleSuffix: Azure Cognitive Search
description: 瞭解 OData 集合篩選器在 Azure 認知搜索查詢中的工作方式，包括集合特有的限制和行為。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: f6e8ed5baef9b8594bb1fe03942e831fd8264a56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113063"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>瞭解 Azure 認知搜索中的 OData 集合篩選器

要[篩選](query-odata-filter-orderby-syntax.md)Azure 認知搜索中的集合欄位，可以將[`any``all`和 運算子](search-query-odata-collection-operators.md)與**lambda 運算式**一起使用。 Lambda 運算式是引用**範圍變數**的布林運算式。 在大多數`any`程式設計`all`語言中，和 運算子`for`類似于迴圈，範圍變數充當迴圈變數的角色，lambda 運算式作為迴圈的主體。 範圍變數在迴圈反覆運算期間採用集合的"當前"值。

至少，這就是它在概念上的工作方式。 實際上，Azure 認知搜索以與迴圈的工作方式`for`非常不同的方式實現篩選器。 理想情況下，這種差異對您來說是不可見的，但在某些情況下，它不是。 最終結果是，在編寫 lambda 運算式時，必須遵循一些規則。

本文通過探索 Azure 認知搜索如何執行這些篩選器來解釋為什麼集合篩選器的規則存在。 如果您正在編寫具有複雜 lambda 運算式的高級篩選器，您可能會發現本文有助於深入瞭解篩選器中可能的內容以及原因。

有關集合篩選器的規則（包括示例）的資訊，請參閱[Azure 認知搜索 中的故障排除 OData 集合篩選器](search-query-troubleshoot-collection-filters.md)。

## <a name="why-collection-filters-are-limited"></a>為什麼收集篩選器受到限制

並非所有類型的集合都支援所有篩選器功能，原因有三個：

1. 某些資料類型僅支援某些運算子。 例如，比較布林`true`值並使用`false` `lt`、`gt`等是沒有意義的。
1. Azure 認知搜索不支援對類型**correlated search**`Collection(Edm.ComplexType)`欄位的相關搜索。
1. Azure 認知搜索使用倒索引對所有類型的資料（包括集合）執行篩選器。

第一個原因只是 OData 語言和 EDM 類型系統定義方式的結果。 本文的其餘部分將更詳細地解釋後兩個。

## <a name="correlated-versus-uncorrelated-search"></a>相關搜索與不相關搜索

在複雜物件集合上應用多個篩選準則時，這些條件**是相關的**，因為它們應用於*集合中的每個物件*。 例如，以下篩檢程式將返回至少有一間價格低於 100 的豪華客房的酒店：

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

如果過濾*不相關*，上述篩檢程式可能會返回酒店，其中一個房間是豪華，另一個房間的基本費率低於100。 這沒有意義，因為 lambda 運算式的兩個子句都適用于同一範圍變數，即`room`。 這就是為什麼這樣的篩檢程式是相關的。

但是，對於全文檢索搜尋，無法引用特定範圍變數。 如果使用現場搜索來發出[完整的 Lucene 查詢](query-lucene-syntax.md)，如下所示：

    Rooms/Type:deluxe AND Rooms/Description:"city view"

您可能會在一間客房豪華的地方重新入住酒店，而描述中提到"城市景觀"時，可能會找到其他客房。 例如，下面的文檔與`Id`的`1`匹配查詢：

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

原因是，`Rooms/Type`它是指整個文檔中`Rooms/Type`欄位的所有分析術語，同樣，`Rooms/Description`如下表所示。

如何`Rooms/Type`存儲全文檢索搜尋：

| 術語在`Rooms/Type` | 文檔文檔 |
| --- | --- |
| 豪華 | 1, 2 |
| 標準 | 1 |

如何`Rooms/Description`存儲全文檢索搜尋：

| 術語在`Rooms/Description` | 文檔文檔 |
| --- | --- |
| 庭院 | 2 |
| city | 1 |
| 花園 | 1 |
| 大型 | 1 |
| 汽車旅館 | 2 |
| 聊天室 | 1, 2 |
| 標準 | 1 |
| 套房 | 1 |
| 檢視 | 1 |

因此，與上面的篩檢程式不同，它基本上說"匹配檔，其中`Type`一個房間等於'豪華房間 **'，同一房間**`BaseRate`少於100"，搜索查詢說"匹配檔有`Rooms/Type`術語'豪華'，有`Rooms/Description`短語"城市視圖"。 在後一種情況下，沒有單獨的房間的概念，其欄位可以相關。

> [!NOTE]
> 如果要查看將相關搜索的支援添加到 Azure 認知搜索，請投票支援[此使用者語音專案](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections)。

## <a name="inverted-indexes-and-collections"></a>反轉索引和集合

您可能已經注意到，對 lambda 運算式對複雜集合的限制遠遠少於簡單集合（如`Collection(Edm.Int32)`、`Collection(Edm.GeographyPoint)`等）的限制。 這是因為 Azure 認知搜索將複雜的集合存儲為子文檔的實際集合，而簡單集合根本不存儲為集合。

例如，請考慮一個可過濾的字串收集欄位`seasons`，如線上零售商的索引中所示。 上載到此索引的某些文檔可能如下所示：

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

`seasons`欄位的值存儲在稱為**反轉索引**的結構中，如下所示：

| 詞彙 | 文檔文檔 |
| --- | --- |
| 春天 | 1, 2 |
| 夏天 | 1 |
| 秋天 | 1, 2 |
| 冬天 | 2, 3 |

此資料結構旨在以極快的速度回答一個問題：給定術語在哪些文檔中出現？ 回答這個問題更像是一個普通相等性檢查，而不是一個集合上的迴圈。 事實上，這就是為什麼對於字串集合，Azure 認知搜索只允許`eq`作為 的 lambda 運算式內的比較`any`運算子。

從相等性構建，接下來我們將瞭解如何將同一範圍變數上的多個相等檢查與`or`進行合併。 它的工作原理是代數和限定詞[的分配屬性](https://en.wikipedia.org/wiki/Existential_quantification#Negation)。 此運算式：

    seasons/any(s: s eq 'winter' or s eq 'fall')

相當於：

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

並且可以使用反轉索引有效地`any`執行兩個子運算式中的每個子運算式。 此外，由於[限定詞的否定定律](https://en.wikipedia.org/wiki/Existential_quantification#Negation)，這句話：

    seasons/all(s: s ne 'winter' and s ne 'fall')

相當於：

    not seasons/any(s: s eq 'winter' or s eq 'fall')

這就是為什麼它可以與 和`all``ne``and`一起使用。

> [!NOTE]
> 儘管詳細資訊超出了本文檔的範圍，但這些相同的原則也擴展到[地理空間點集合的距離和交點測試](search-query-odata-geo-spatial-functions.md)。 因此，在 中`any`：
>
> - `geo.intersects`不能否定
> - `geo.distance`必須使用`lt`或`le`
> - 運算式必須與`or`不`and`
>
> 相反的規則適用于`all`。

在`lt`篩選支援 的`gt``le``ge`資料類型集合時，允許使用更廣泛的運算式，例如， `Collection(Edm.Int32)` 具體而言，只要基礎`and`比較運算式與`or``any`使用 將 合併為`and`**範圍比較**，就可以在 中使用 ，然後使用 進一步組合`or`使用 。 布林運算式的這種結構稱為[分離法態形式 （DNF），](https://en.wikipedia.org/wiki/Disjunctive_normal_form)也稱為"AND 的 OR"。 相反，對於`all`這些資料類型的 lambda 運算式必須以[組織法態形式 （CNF）](https://en.wikipedia.org/wiki/Conjunctive_normal_form)表示，也稱為"O 的 AND"。 Azure 認知搜索允許此類範圍比較，因為它可以使用反向索引高效地執行它們，就像它可以快速查找字串一樣。

總之，以下是 lambda 運算式中允許的內容的經驗法則：

- 在`any`內部 *，總是允許進行正檢查*，如相等、範圍比較`geo.intersects`，或與`geo.distance``lt`或`le`進行比較（在檢查距離時，將"親密"視為相等）。
- 裡面`any`，`or`總是允許的。 `and`您只能用於可以表示範圍檢查的資料類型，並且僅當使用 AND 的 ER （DNF） 時。
- 內部`all`，規則是顛倒的 - 只允許*進行負檢查*，您可以始終使用`and`，並且只能用於`or`表示為 OR （CNF） AND 的範圍檢查。

實際上，這些是您最有可能使用的篩選器類型。 然而，瞭解可能的界限仍然很有説明。

有關允許哪些類型的篩選器和哪些類型的篩選器的特定示例，請參閱[如何編寫有效的集合篩選器](search-query-troubleshoot-collection-filters.md#bkmk_examples)。

## <a name="next-steps"></a>後續步驟  

- [在 Azure 認知搜索中排除 O 資料資料收集篩選器的疑難排解](search-query-troubleshoot-collection-filters.md)
- [Azure 認知搜索中的篩選器](search-filters.md)
- [Azure 認知搜索的 OData 運算式語言概述](query-odata-filter-orderby-syntax.md)
- [用於 Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)
- [搜索文檔&#40;Azure 認知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
