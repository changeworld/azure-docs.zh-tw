---
title: 疑難排解 OData 集合篩選
titleSuffix: Azure Cognitive Search
description: 瞭解解決 Azure 認知搜尋查詢中 OData 集合篩選錯誤的方法。
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
ms.openlocfilehash: e82fa00226c964d5ba774cdf06f5b0f3898bdc55
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74113087"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>針對 Azure 認知搜尋中的 OData 集合篩選進行疑難排解

若要[篩選](query-odata-filter-orderby-syntax.md)Azure 認知搜尋中的集合欄位，您可以使用[ `any` 和 `all` 運算子](search-query-odata-collection-operators.md)搭配**lambda 運算式**。 Lambda 運算式是套用至集合中每個專案的子篩選準則。

並非篩選運算式的每項功能都可在 lambda 運算式內使用。 可用的功能會因您要篩選之集合欄位的資料類型而有所不同。 如果您嘗試在該內容中不支援的 lambda 運算式中使用功能，這可能會導致錯誤。 如果您在嘗試撰寫集合欄位的複雜篩選時遇到這類錯誤，本文將協助您針對問題進行疑難排解。

## <a name="common-collection-filter-errors"></a>常見的集合篩選器錯誤

下表列出嘗試執行集合篩選時可能會遇到的錯誤。 當您使用 lambda 運算式內不支援的篩選條件運算式的功能時，就會發生這些錯誤。 每個錯誤都會提供一些指引，說明如何重寫篩選以避免發生錯誤。 此表格也包含本文相關章節的連結，提供如何避免該錯誤的詳細資訊。

| 錯誤訊息 | 情況 | 如需相關資訊，請參閱 |
| --- | --- | --- |
| 函數 ' ismatch ' 沒有系結至範圍變數的 ' 的參數。 Lambda 運算式內僅支援系結欄位參考（[any] 或 [all]）。 請變更您的篩選準則，使 ' ismatch ' 函式位於 lambda 運算式之外，然後再試一次。 | 在 `search.ismatch` `search.ismatchscoring` lambda 運算式中使用或 | [篩選複雜集合的規則](#bkmk_complex) |
| 不正確 lambda 運算式。 發現在反覆運算類型為 Collection （Edm）之欄位的 lambda 運算式中，必須有相反或不相等的測試。 若為 ' any '，請使用 ' x eq y ' 或 ' search.in （...） ' 格式的運算式。 若為 ' all '，請使用 ' x ne y '、' not （x eq y） ' 或 ' not search.in （...） ' 格式的運算式。 | 篩選類型的欄位`Collection(Edm.String)` | [篩選字串集合的規則](#bkmk_strings) |
| 不正確 lambda 運算式。 發現不支援的複雜布林運算式格式。 若為 ' any '，請使用「Or of And」的運算式，也稱為「Disjunctive 一般表單」。 例如： ' （a 和 b） or （c 和 d） '，其中 a、b、c 和 d 是比較或相等的子運算式。 若為「全部」，請使用「And of Or」的運算式，也稱為「組成一般表單」。 例如： ' （a 或 b）和（c 或 d） '，其中 a、b、c 和 d 是比較或不相等的子運算式。 比較運算式的範例： ' x gt 5 '、' x le 2 '。 等號比較運算式的範例： ' x eq 5 '。 不等比較運算式的範例： ' x ne 5 '。 | 篩選 `Collection(Edm.DateTimeOffset)` 、 `Collection(Edm.Double)` 、 `Collection(Edm.Int32)` 或類型的欄位`Collection(Edm.Int64)` | [篩選可比較集合的規則](#bkmk_comparables) |
| 不正確 lambda 運算式。 在反覆運算類型為 Collection （GeographyPoint）之欄位的 lambda 運算式中，發現不支援的異地距離（）或地理交集（）。 對於 [任何]，請務必使用 ' lt ' 或 ' le ' 運算子比較 geo （），並確定不會否定任何地理交集（）的使用方式。 針對 [全部]，請確定您使用 ' gt ' 或 ' ge ' 運算子比較異地距離（），並確定已否定任何地理交集（）的使用。 | 篩選類型的欄位`Collection(Edm.GeographyPoint)` | [篩選 GeographyPoint 集合的規則](#bkmk_geopoints) |
| 不正確 lambda 運算式。 在反覆運算類型為 Collection （GeographyPoint）之欄位的 lambda 運算式中，不支援複雜的布林運算式。 若為 ' any '，請使用 ' or ' 聯結子運算式;不支援 ' and '。 若為 ' all '，請使用 ' and ' 聯結子運算式;不支援 ' or '。 | 篩選或類型的欄位 `Collection(Edm.String)``Collection(Edm.GeographyPoint)` | [篩選字串集合的規則](#bkmk_strings) <br/><br/> [篩選 GeographyPoint 集合的規則](#bkmk_geopoints) |
| 不正確 lambda 運算式。 找到比較運算子（' lt '、' le '、' gt ' 或 ' ge ' 的其中一個）。 在反覆運算類型為 Collection （Edm）之欄位的 lambda 運算式中，只允許等號比較運算子。 若為 ' any '，請使用 ' x eq y ' 格式的運算式。 若為 ' all '，請使用 ' x ne y ' 或 ' not （x eq y） ' 格式的運算式。 | 篩選類型的欄位`Collection(Edm.String)` | [篩選字串集合的規則](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>如何撰寫有效的集合篩選

針對每個資料類型，寫入有效集合篩選的規則會有所不同。 下列各節會藉由顯示支援哪些篩選功能的範例，以及哪些不提供下列各項來描述規則：

- [篩選字串集合的規則](#bkmk_strings)
- [篩選布林值集合的規則](#bkmk_bools)
- [篩選 GeographyPoint 集合的規則](#bkmk_geopoints)
- [篩選可比較集合的規則](#bkmk_comparables)
- [篩選複雜集合的規則](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>篩選字串集合的規則

在字串集合的 lambda 運算式內部，唯一可以使用的比較運算子是 `eq` 和 `ne` 。

> [!NOTE]
> Azure 認知搜尋不支援字串的 `lt` / `le` / `gt` / `ge` 運算子，不論是在 lambda 運算式內部或外部。

的主體只能 `any` 測試是否相等，而的主體只能測試是否不 `all` 相等。

您也可以在的主體中結合多個運算式 `or` `any` ，並 `and` 在的本文中使用 `all` 。 因為函 `search.in` 式相當於結合等號比較檢查 `or` ，所以在的主體中也允許使用 `any` 。 相反地， `not search.in` 在的主體中允許 `all` 。

例如，允許下列運算式：

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

雖然不允許使用這些運算式：

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>篩選布林值集合的規則

型別 `Edm.Boolean` 僅支援 `eq` 和 `ne` 運算子。 因此，允許結合使用檢查相同範圍變數的這類子句並不太合理， `and` / `or` 因為這一定會導致 tautologies 或您一致。

以下是允許的布林值集合篩選的一些範例：

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

不同于字串集合，布林值集合沒有任何限制可用於哪種類型的 lambda 運算式。 `eq`和都 `ne` 可以在或的主體中使用 `any` `all` 。

布林值集合不允許下列運算式：

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>篩選 GeographyPoint 集合的規則

集合中類型的值 `Edm.GeographyPoint` 無法彼此直接比較。 相反地，它們必須當做和函式的參數使用 `geo.distance` `geo.intersects` 。 `geo.distance`接著，必須使用其中一個比較運算子、、或，將函式與距離值進行比較 `lt` `le` `gt` `ge` 。 這些規則也適用于非集合 Edm. GeographyPoint 欄位。

如同字串集合， `Edm.GeographyPoint` 集合具有一些規則，可讓您瞭解如何在不同類型的 lambda 運算式中使用和結合地理空間函式：

- 您可以搭配函式使用的比較運算子 `geo.distance` 取決於 lambda 運算式的類型。 對於 `any` ，您只能使用 `lt` 或 `le` 。 對於 `all` ，您只能使用 `gt` 或 `ge` 。 您可以否定牽涉到的運算式 `geo.distance` ，但您必須變更比較運算子（ `geo.distance(...) lt x` 會變成 `not (geo.distance(...) ge x)` 並 `geo.distance(...) le x` 變成 `not (geo.distance(...) gt x)` ）。
- 在的主體中 `all` ，函式 `geo.intersects` 必須是否定的。 相反地，在的主體中，函式不得 `any` `geo.intersects` 為否定。
- 在的主體中 `any` ，可以使用來結合地理空間運算式 `or` 。 在的主體中 `all` ，這類運算式可以使用結合 `and` 。

上述限制存在的原因類似于字串集合的等號/不等限制。 請參閱[瞭解 Azure 認知搜尋中的 OData 集合篩選](search-query-understand-collection-filters.md)，以深入瞭解這些原因。

以下是允許之集合上的篩選準則範例 `Edm.GeographyPoint` ：

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

下列運算式不允許用於 `Edm.GeographyPoint` 集合：

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>篩選可比較集合的規則

本節適用于下列所有的資料類型：

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

和之類的 `Edm.Int32` 類型 `Edm.DateTimeOffset` 都支援所有六個比較運算子： `eq` 、 `ne` 、 `lt` 、 `le` 、 `gt` 和 `ge` 。 這些類型集合的 Lambda 運算式可以包含使用上述任何運算子的簡單運算式。 這同時適用于 `any` 和 `all` 。 例如，允許下列篩選準則：

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

不過，這類比較運算式如何結合成 lambda 運算式內更複雜的運算式，會有一些限制：

- 的規則 `any` ：
  - 簡單的不等比較運算式無法與任何其他運算式結合常見。 例如，允許使用此運算式：
    - `ratings/any(r: r ne 5)`

    但此運算式不是：
    - `ratings/any(r: r ne 5 and r gt 2)`

    雖然此運算式是允許的，但它並不實用，因為條件重迭：
    - `ratings/any(r: r ne 5 or r gt 7)`
  - 包括 `eq` 、 `lt` 、 `le` 、或的簡單比較運算式 `gt` `ge` 可以與結合 `and` / `or` 。 例如：
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - 與（連接詞）結合的比較運算式 `and` 可以使用進一步結合 `or` 。 這個表單在布林邏輯中是所謂的「[Disjunctive Normal form](https://en.wikipedia.org/wiki/Disjunctive_normal_form)」（DNF）。 例如：
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- 的規則 `all` ：
  - 簡單的等號比較運算式無法常見與任何其他運算式合併使用。 例如，允許使用此運算式：
    - `ratings/all(r: r eq 5)`

    但此運算式不是：
    - `ratings/all(r: r eq 5 or r le 2)`

    雖然此運算式是允許的，但它並不實用，因為條件重迭：
    - `ratings/all(r: r eq 5 and r le 7)`
  - 包括 `ne` 、 `lt` 、 `le` 、或的簡單比較運算式 `gt` `ge` 可以與結合 `and` / `or` 。 例如：
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - 與（disjunctions）結合的比較運算式 `or` 可以使用進一步結合 `and` 。 這個表單在布林邏輯中是所謂的「[組成 Normal form](https://en.wikipedia.org/wiki/Conjunctive_normal_form)」（my.cnf）。 例如：
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>篩選複雜集合的規則

透過複雜集合的 lambda 運算式，支援比在基本型別集合上使用 lambda 運算式更具彈性的語法。 您可以在這類 lambda 運算式內使用任何篩選器結構，而您可以在其中使用外部，只有兩個例外狀況。

首先， `search.ismatch` `search.ismatchscoring` lambda 運算式內不支援函式和。 如需詳細資訊，請參閱[瞭解 Azure 認知搜尋中的 OData 集合篩選](search-query-understand-collection-filters.md)。

第二 *，不允許*參考未系結至範圍變數（稱為*自由變數*）的欄位。 例如，請考慮下列兩個對等的 OData 篩選條件運算式：

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

將允許第一個運算式，而第二個表單會被拒絕，因為未系結 `details/margin` 至範圍變數 `s` 。

此規則也會擴充至具有外部範圍內所系結變數的運算式。 這類變數對其出現的範圍而言是免費的。 例如，允許第一個運算式，而第二個對等運算式則不允許，因為對於 `s/name` 範圍變數的範圍而言是免費的 `a` ：

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

這項限制在實務上不應該是問題，因為您一律可以建立篩選準則，讓 lambda 運算式僅包含系結變數。

## <a name="cheat-sheet-for-collection-filter-rules"></a>集合篩選規則的功能提要

下表摘要說明針對每個集合資料類型來建立有效篩選的規則。

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

如需如何為每個案例建立有效篩選的範例，請參閱[如何撰寫有效的集合篩選](#bkmk_examples)。

如果您經常寫入篩選器，並瞭解來自第一個原則的規則可協助您不必記住它們，請參閱[瞭解 Azure 認知搜尋中的 OData 集合篩選](search-query-understand-collection-filters.md)。

## <a name="next-steps"></a>後續步驟  

- [瞭解 Azure 認知搜尋中的 OData 集合篩選](search-query-understand-collection-filters.md)
- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
