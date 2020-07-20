---
title: 瞭解 OData 集合篩選
titleSuffix: Azure Cognitive Search
description: 瞭解 OData 集合篩選如何在 Azure 認知搜尋查詢中運作的機制，包括集合特有的限制和行為。
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
ms.openlocfilehash: 861e011c4bd368a274998859170e78cf444400a8
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206170"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>瞭解 Azure 認知搜尋中的 OData 集合篩選

若要[篩選](query-odata-filter-orderby-syntax.md)Azure 認知搜尋中的集合欄位，您可以使用[ `any` 和 `all` 運算子](search-query-odata-collection-operators.md)搭配**lambda 運算式**。 Lambda 運算式是參考**範圍變數**的布林運算式。 `any`和 `all` 運算子類似于大部分程式 `for` 設計語言中的迴圈，其中的範圍變數接受迴圈變數的角色，而 lambda 運算式則是迴圈的主體。 在迴圈反覆運算期間，範圍變數會採用集合的「目前」值。

至少這就是它在概念上的運作方式。 實際上，Azure 認知搜尋會以非常不同的方式來執行篩選，以因應 `for` 迴圈的工作方式。 在理想的情況下，您不會看到這項差異，但在某些情況下則不會。 最終結果是您在撰寫 lambda 運算式時必須遵循的規則。

本文說明如何藉由探索 Azure 認知搜尋如何執行這些篩選器，來提供集合篩選器的規則。 如果您使用複雜的 lambda 運算式來撰寫「高級篩選」，您可能會發現這篇文章有助您瞭解篩選準則和原因。

如需集合篩選器規則的相關資訊（包括範例），請參閱[在 Azure 認知搜尋中針對 OData 集合篩選進行疑難排解](search-query-troubleshoot-collection-filters.md)。

## <a name="why-collection-filters-are-limited"></a>集合篩選的限制為何

並非所有類型的集合都支援所有篩選功能的基本原因有三個：

1. 某些資料類型只支援特定的運算子。 例如，比較布林值 `true` 和 `false` 使用 `lt` 、等等並不合理 `gt` 。
1. Azure 認知搜尋不支援在類型的欄位上進行相互**關聯的搜尋** `Collection(Edm.ComplexType)` 。
1. Azure 認知搜尋會使用反向索引來執行所有資料類型（包括集合）的篩選。

第一個原因只是定義 OData 語言和 EDM 型別系統的結果。 本文的其餘部分將更詳細地說明最後兩個。

## <a name="correlated-versus-uncorrelated-search"></a>相互關聯與不相關的搜尋

將多個篩選準則套用至複雜物件的集合時，準則會相互**關聯**，因為它們會套用至*集合中的每個物件*。 例如，下列篩選會傳回至少有一個 deluxe 房間的旅館，其費率低於100：

```odata-filter-expr
    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)
```

如果無法與篩選相關，則上述篩選可能會傳回一個會議室已*deluxe，而*不同房間的基本費率低於100的飯店。 這沒什麼意義，因為 lambda 運算式的兩個子句都適用于相同的範圍變數，亦即 `room` 。 這就是這類篩選器相互關聯的原因。

不過，如果是全文檢索搜尋，就無法參考特定的範圍變數。 如果您使用回復搜尋來發出如下列的[完整 Lucene 查詢](query-lucene-syntax.md)：

```odata-filter-expr
    Rooms/Type:deluxe AND Rooms/Description:"city view"
```

您可能會在 deluxe 某個房間時取得飯店，而不同的房間在描述中提及「城市視圖」。 例如，下列與 `Id` 的檔 `1` 會符合查詢：

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

這是指在 `Rooms/Type` 整份檔中參考欄位的所有已分析詞彙 `Rooms/Type` ，類似于 `Rooms/Description` ，如下表所示。

如何 `Rooms/Type` 儲存以供全文檢索搜尋之用：

| 中的詞彙`Rooms/Type` | 檔識別碼 |
| --- | --- |
| deluxe | 1, 2 |
| 標準 | 1 |

如何 `Rooms/Description` 儲存以供全文檢索搜尋之用：

| 中的詞彙`Rooms/Description` | 檔識別碼 |
| --- | --- |
| courtyard | 2 |
| city | 1 |
| 園 | 1 |
| 大型 | 1 |
| 汽車旅館 | 2 |
| 房間 | 1, 2 |
| 標準 | 1 |
| 整套 | 1 |
| 檢視 | 1 |

因此，與上方的篩選器不同的是，基本上會顯示「比對房間 `Type` 等於 ' Deluxe 室 ' 的檔，且**相同的房間** `BaseRate` 小於100」，搜尋查詢會顯示「比對具有「Deluxe」詞彙的檔 `Rooms/Type` ，並 `Rooms/Description` 具有「城市視圖」片語。 不會有個別房間的概念，其欄位可以在後者的情況下相互關聯。

> [!NOTE]
> 如果您想要查看已新增至 Azure 認知搜尋的相互關聯搜尋支援，請投票[此使用者的語音專案](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections)。

## <a name="inverted-indexes-and-collections"></a>反向索引和集合

您可能已經注意到，在複雜集合上，lambda 運算式的限制遠低於簡單集合（例如 `Collection(Edm.Int32)` 、等等） `Collection(Edm.GeographyPoint)` 。 這是因為 Azure 認知搜尋會將複雜的集合儲存為子檔的實際集合，而簡單的集合則不會儲存成集合。

例如，假設有一個可篩選的字串集合欄位，像是 `seasons` 在線上零售商的索引中。 上傳到此索引的某些檔可能如下所示：

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

欄位的值 `seasons` 會儲存在稱為**反向索引**的結構中，如下所示：

| 字詞 | 檔識別碼 |
| --- | --- |
| spring | 1, 2 |
| 夏令時 | 1 |
| 屬於 | 1, 2 |
| 冬季 | 2、3 |

此資料結構的設計目的是要以絕佳的速度回答一個問題：在哪個檔中出現指定的詞彙？ 要回答這個問題，其運作方式比在集合上迴圈的一般相等檢查更類似。 事實上，這就是字串集合的原因，Azure 認知搜尋只允許在的 `eq` lambda 運算式內做為比較運算子 `any` 。

以相等的方式建立，接下來我們將探討如何將相同範圍變數上的多個相等檢查與結合在一起 `or` 。 這是因為代數和數量詞的分配[屬性](https://en.wikipedia.org/wiki/Existential_quantification#Negation)。 此運算式：

```odata-filter-expr
    seasons/any(s: s eq 'winter' or s eq 'fall')
```

相當於：

```odata-filter-expr
    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')
```

而這兩個子運算式的每一個都 `any` 可以使用反向索引來有效率地執行。 此外，感謝數量詞的[負法則](https://en.wikipedia.org/wiki/Existential_quantification#Negation)，此運算式：

```odata-filter-expr
    seasons/all(s: s ne 'winter' and s ne 'fall')
```

相當於：

```odata-filter-expr
    not seasons/any(s: s eq 'winter' or s eq 'fall')
```

這就是為何可以與和搭配使用的原因 `all` `ne` `and` 。

> [!NOTE]
> 雖然詳細資料已超出本檔的範圍，但這些相同的原則也會延伸到[地理空間點集合的距離和交集測試](search-query-odata-geo-spatial-functions.md)。 這就是為什麼 `any` ：
>
> - `geo.intersects`不可以是否定的
> - `geo.distance`必須使用或進行比較 `lt``le`
> - 運算式必須結合，而 `or` 不是`and`
>
> 反向規則適用于 `all` 。

在支援 `lt` 、 `gt` 、 `le` 和 `ge` 運算子的資料類型集合上進行篩選時，允許使用更廣泛的運算式，例如 `Collection(Edm.Int32)` 。 具體而言，您可以使用和 `and` `or` 中的 `any` ，只要基礎比較運算式結合成使用的**範圍比較** `and` ，然後使用進一步結合 `or` 。 此布林運算式的結構稱為[Disjunctive 一般格式 (DNF) ](https://en.wikipedia.org/wiki/Disjunctive_normal_form)，亦稱為「Or of and」。 相反 `all` 地，針對這些資料類型的 lambda 運算式必須是[組成 Normal 格式， (my.cnf) ](https://en.wikipedia.org/wiki/Conjunctive_normal_form)，亦稱為「and of or」。 Azure 認知搜尋允許這類的範圍比較，因為它可以有效率地使用反向索引來執行它們，就像可以針對字串進行快速的詞彙查閱一樣。

總而言之，以下是 lambda 運算式中允許之功能的經驗法則：

- 在內部 `any` ，一律允許*正面檢查*，例如相等、範圍比較， `geo.intersects` 或與 `geo.distance` `lt` 或 (在 `le` 檢查距離) 時，將 "接近程度" 視為類似的相等。
- 在中 `any` ， `or` 一律允許使用。 只有在 `and` 您使用 or Of and (DNF) 時，才可以使用資料類型來表示範圍檢查。
- 在中 `all` ，規則會反轉--只允許*負的檢查*，您可以使用 `and` always，而且只能 `or` 用於以 Or (my.cnf) 的 and 表示的範圍檢查。

實際上，這些是您最可能使用的篩選器類型。 不過，瞭解可能的界限仍然很有説明。

如需允許哪些類型的篩選準則，以及哪些不支援的特定範例，請參閱[如何撰寫有效的集合篩選](search-query-troubleshoot-collection-filters.md#bkmk_examples)。

## <a name="next-steps"></a>後續步驟  

- [針對 Azure 認知搜尋中的 OData 集合篩選進行疑難排解](search-query-troubleshoot-collection-filters.md)
- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
