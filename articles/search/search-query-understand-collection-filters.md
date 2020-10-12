---
title: 瞭解 OData 集合篩選
titleSuffix: Azure Cognitive Search
description: 瞭解 OData 集合篩選在 Azure 認知搜尋查詢中的運作方式，包括集合的唯一限制和行為。
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
ms.openlocfilehash: 6af0f2b5221a737687578e939c14cecf3be14509
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88932911"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>瞭解 Azure 認知搜尋中的 OData 集合篩選

若要[篩選](query-odata-filter-orderby-syntax.md)Azure 認知搜尋中的集合欄位，您可以使用[ `any` 和 `all` 運算子](search-query-odata-collection-operators.md)搭配**lambda 運算式**。 Lambda 運算式是參考 **範圍變數**的布林運算式。 `any`和 `all` 運算子類似于 `for` 大部分程式設計語言中的迴圈，其範圍變數接受迴圈變數的角色，而 lambda 運算式則是迴圈的主體。 在迴圈反覆運算期間，範圍變數會採用集合的「目前」值。

至少這是它在概念上的運作方式。 實際上，Azure 認知搜尋會以非常不同的方式來執行篩選，以因應 `for` 迴圈的運作方式。 在理想情況下，您不會看到這項差異，但在某些情況下則不會發生。 最後的結果是，您在撰寫 lambda 運算式時必須遵循的規則。

本文將探討 Azure 認知搜尋如何執行這些篩選器，以說明集合篩選準則的規則為何存在。 如果您要使用複雜的 lambda 運算式來撰寫高階篩選器，您可能會發現這篇文章有助於您瞭解篩選準則和原因。

如需集合篩選規則的相關資訊，包括範例，請參閱 [Azure 認知搜尋中的疑難排解 OData 集合篩選](search-query-troubleshoot-collection-filters.md)。

## <a name="why-collection-filters-are-limited"></a>為何集合篩選準則受限

所有類型的集合都不支援所有篩選功能的根本原因有三個：

1. 某些資料類型只支援特定運算子。 例如，比較布林值 `true` 和使用、等等並不合理 `false` `lt` `gt` 。
1. Azure 認知搜尋不支援類型的欄位上的相互 **關聯搜尋** `Collection(Edm.ComplexType)` 。
1. Azure 認知搜尋使用反向索引來執行所有資料類型（包括集合）上的篩選。

第一個原因只是如何定義 OData 語言和 EDM 類型系統的結果。 本文的其餘部分將更詳細地說明最後兩個。

## <a name="correlated-versus-uncorrelated-search"></a>相互關聯與不相關搜尋

在複雜物件的集合上套用多個篩選準則時，準則會相互 **關聯** ，因為它們會套用至 *集合中的每個物件*。 例如，下列篩選準則會傳回至少有一個 deluxe 房間且費率小於100的旅館：

```odata-filter-expr
    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)
```

如果篩選是不相關的，則上述篩選可能會傳回 deluxe 一個房間的 *旅館，而*不同的房間有低於100的基本費率。 這並不合理，因為 lambda 運算式的兩個子句都適用于相同的範圍變數，亦即 `room` 。 這就是這類篩選器相互關聯的原因。

不過，對於全文檢索搜尋，沒有任何方法可以參考特定的範圍變數。 如果您使用回復搜尋來發出 [完整的 Lucene 查詢](query-lucene-syntax.md) ，如下所示：

```odata-filter-expr
    Rooms/Type:deluxe AND Rooms/Description:"city view"
```

您可能會在 deluxe 一個房間的地方取得旅館，而不同的房間在描述中提及「city view」。 例如，下列 `Id` 的檔 `1` 會符合查詢：

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

原因是指的是 `Rooms/Type` 整份檔中所有已分析的詞彙 `Rooms/Type` ，而且類似于 `Rooms/Description` ，如下表所示。

如何 `Rooms/Type` 儲存以進行全文檢索搜尋：

| 字詞 `Rooms/Type` | 檔識別碼 |
| --- | --- |
| 豪華 | 1, 2 |
| 標準 | 1 |

如何 `Rooms/Description` 儲存以進行全文檢索搜尋：

| 字詞 `Rooms/Description` | 檔識別碼 |
| --- | --- |
| 庭院 | 2 |
| city | 1 |
| 花園 | 1 |
| 大型 | 1 |
| 汽車旅館 | 2 |
| 房間 | 1, 2 |
| 標準 | 1 |
| 套房 | 1 |
| 檢視 | 1 |

因此，與上述篩選器不同的是，基本上會顯示「比對房間 `Type` 等於「Deluxe 房間」且 **相同房間** 小於100的檔」 `BaseRate` ，則搜尋查詢會顯示「比對檔，其中 `Rooms/Type` 包含 "Deluxe" 一詞，且 `Rooms/Description` 具有「city view」片語。 沒有個別房間的概念，其欄位可在後者的情況下相互關聯。

> [!NOTE]
> 如果您想要查看已新增至 Azure 認知搜尋的相互關聯搜尋支援，請針對 [此使用者語音專案](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections)投票。

## <a name="inverted-indexes-and-collections"></a>反向索引和集合

您可能已經注意到，對複雜集合的 lambda 運算式所做的限制遠低於簡單集合（例如、 `Collection(Edm.Int32)` `Collection(Edm.GeographyPoint)` 等等）。 這是因為 Azure 認知搜尋會將複雜的集合儲存為子檔的實際集合，而簡單的集合則不會儲存為集合。

例如，假設有一個可篩選的字串集合欄位，例如 `seasons` 線上零售商的索引。 上傳到此索引的某些檔可能如下所示：

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

欄位的值 `seasons` 會儲存在稱為 **反向索引**的結構中，如下所示：

| 詞彙 | 檔識別碼 |
| --- | --- |
| spring | 1, 2 |
| 夏天 | 1 |
| 秋天 | 1, 2 |
| 冬天 | 2，3 |

此資料結構的設計目的是要以絕佳的速度回答一個問題：有哪些檔會顯示指定的字詞？ 回答此問題的方式，會比對集合進行迴圈的一般相等檢查更類似。 事實上，這就是為什麼字串集合的原因，Azure 認知搜尋只允許在的 `eq` lambda 運算式內使用做為比較運算子 `any` 。

從相等的形式建立，接下來我們將探討如何將相同範圍變數上的多個相等檢查結合在一起 `or` 。 這種方式適用于代數和數量詞的分配 [屬性](https://en.wikipedia.org/wiki/Existential_quantification#Negation)。 此運算式：

```odata-filter-expr
    seasons/any(s: s eq 'winter' or s eq 'fall')
```

相當於：

```odata-filter-expr
    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')
```

而且每一個 `any` 子運算式都可以使用反向索引來有效率地執行。 此外，由於 [數量詞的負負法則](https://en.wikipedia.org/wiki/Existential_quantification#Negation)，此運算式：

```odata-filter-expr
    seasons/all(s: s ne 'winter' and s ne 'fall')
```

相當於：

```odata-filter-expr
    not seasons/any(s: s eq 'winter' or s eq 'fall')
```

這就是為什麼可以搭配和使用的原因 `all` `ne` `and` 。

> [!NOTE]
> 雖然詳細資料已超出本檔的範圍，但這些相同的原則也會延伸至 [地理空間點集合的距離和交集測試](search-query-odata-geo-spatial-functions.md) 。 這就是為什麼 `any` ：
>
> - `geo.intersects` 不可為否定
> - `geo.distance`必須使用或進行比較 `lt``le`
> - 運算式必須結合，而 `or` 不是 `and`
>
> 反向規則適用于 `all` 。

篩選支援 `lt` 、 `gt` 、 `le` 和 `ge` 運算子的資料類型集合（例如）時，可以使用更多的運算式 `Collection(Edm.Int32)` 。 具體而言，您可以使用和 `and` `or` 中 `any` 的，只要基礎比較運算式使用來合併為 **範圍比較** ，就可以使用 `and` 進一步結合 `or` 。 此布林運算式的結構稱為 [Disjunctive 一般表單 (DNF) ](https://en.wikipedia.org/wiki/Disjunctive_normal_form)，也稱為「Or of and」。 相反 `all` 地，適用于這些資料類型的 lambda 運算式必須採用 [組成的一般格式， (my.cnf) ](https://en.wikipedia.org/wiki/Conjunctive_normal_form)，也稱為 "and of or"。 Azure 認知搜尋允許這類範圍比較，因為它可以有效率地使用反向索引來執行它們，就像可以針對字串進行快速詞彙查閱一樣。

總而言之，以下是 lambda 運算式中允許的經驗法則：

- 在內部 `any` ，一定會允許 *正面的檢查* ，例如相等、範圍比較、或與 `geo.intersects` `geo.distance` `lt` 或 `le` (將 "接近程度" 視為檢查距離) 的相等。
- 在中 `any` ， `or` 一律允許。 您只能 `and` 針對可以表達範圍檢查的資料類型使用，而且只有在您使用 or Of and (DNF) 時才可使用。
- 在中 `all` ，規則是反轉的--只允許 *負的檢查* ，您可以使用 `and` always，而且只能用於 `or` 以 or (my.cnf) and 的範圍檢查。

在實務上，這些是您最可能要使用的篩選器類型。 不過，瞭解所能實現的範圍仍有説明。

如需允許何種篩選準則的特定範例，請參閱 [如何撰寫有效的集合篩選](search-query-troubleshoot-collection-filters.md#bkmk_examples)。

## <a name="next-steps"></a>接下來的步驟  

- [疑難排解 Azure 認知搜尋中的 OData 集合篩選](search-query-troubleshoot-collection-filters.md)
- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](/rest/api/searchservice/Search-Documents)