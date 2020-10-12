---
title: 疑難排解 OData 集合篩選
titleSuffix: Azure Cognitive Search
description: 瞭解在 Azure 認知搜尋查詢中解決 OData 集合篩選錯誤的方法。
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
ms.openlocfilehash: 3050f701c11773207aa6054d4d08d908d87b2ce7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88932061"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>疑難排解 Azure 認知搜尋中的 OData 集合篩選

若要[篩選](query-odata-filter-orderby-syntax.md)Azure 認知搜尋中的集合欄位，您可以使用[ `any` 和 `all` 運算子](search-query-odata-collection-operators.md)搭配**lambda 運算式**。 Lambda 運算式是套用至集合中每個元素的子篩選準則。

Lambda 運算式中並非篩選運算式的每項功能都可以使用。 有哪些功能會根據您要篩選之集合欄位的資料類型而有所不同。 如果您嘗試在 lambda 運算式中使用該內容中不支援的功能，這可能會導致錯誤。 如果您在嘗試對集合欄位撰寫複雜篩選時遇到這類錯誤，本文將協助您針對問題進行疑難排解。

## <a name="common-collection-filter-errors"></a>常見的集合篩選錯誤

下表列出嘗試執行集合篩選時可能遇到的錯誤。 當您使用 lambda 運算式內不支援之篩選條件運算式的功能時，就會發生這些錯誤。 每個錯誤都會提供一些指導方針，說明如何重寫篩選以避免錯誤。 此表格也包含本文相關章節的連結，以提供有關如何避免該錯誤的詳細資訊。

| 錯誤訊息 | 情況 | 如需相關資訊，請參閱 |
| --- | --- | --- |
| 函數 ' ismatch ' 沒有系結至範圍變數 ' 的參數。 Lambda 運算式內只支援系結欄位參考 ( ' any ' 或 ' all ' ) 。 請變更您的篩選，讓 ' ismatch ' 函式位於 lambda 運算式之外，然後再試一次。 | 在 `search.ismatch` `search.ismatchscoring` lambda 運算式中使用或 | [篩選複雜集合的規則](#bkmk_complex) |
| 不正確 lambda 運算式。 找到相等或不相等的測試，在反覆運算型別集合的欄位時，lambda 運算式中必須有相對應的 (Edm. 字串) 。 若為 ' any '，請使用 ' x eq y ' 或 ' search.in ( ... ) ' 格式的運算式。 若為 ' all '，請使用 ' x ne y '、' not (x eq y) ' 或 ' not search.in ( ... ) ' 形式的運算式。 | 篩選類型的欄位 `Collection(Edm.String)` | [篩選字串集合的規則](#bkmk_strings) |
| 不正確 lambda 運算式。 找到不支援的複雜布林運算式形式。 若為 ' any '，請使用 ' Or of And ' 的運算式，也稱為 Disjunctive Normal Form。 例如： ' (a 和 b) 或 (c 和 d) '，其中 a、b、c 和 d 是比較或相等子運算式。 若為 ' all '，請使用 ' And of Or ' 的運算式，也稱為組成 Normal Form。 例如： ' (a 或 b) 和 (c 或 d) '，其中 a、b、c 和 d 是比較或不相等的子運算式。 比較運算式的範例： ' x gt 5 '、' x le 2 '。 等號比較運算式的範例： ' x eq 5 '。 不等比較運算式的範例： ' x ne 5 '。 | 篩選類型 `Collection(Edm.DateTimeOffset)` 、 `Collection(Edm.Double)` 、 `Collection(Edm.Int32)` 或的欄位 `Collection(Edm.Int64)` | [篩選可比較的集合規則](#bkmk_comparables) |
| 不正確 lambda 運算式。 找到不支援的異地. 距離 ( # A1 或 geo， ( # A3 在反覆運算型別集合 (GeographyPoint) 的欄位上進行交集。 若為 ' any '，請務必使用 ' lt ' 或 ' le ' 運算子來比較地理位置 ( # A1，並確定 ( # A3 的任何地理交集用法都不會否定。 針對 [全部]，請務必使用 ' gt ' 或 ' ge ' 運算子來比較地理位置 ( # A1，並確定 ( # A3 的任何地理位置交集都是否定的。 | 篩選類型的欄位 `Collection(Edm.GeographyPoint)` | [篩選 GeographyPoint 集合的規則](#bkmk_geopoints) |
| 不正確 lambda 運算式。 Lambda 運算式不支援複雜的布林運算式，這些運算式會逐一查看集合 (GeographyPoint) 的類型欄位。 若為 ' any '，請使用 ' or ' 聯結子運算式;不支援 ' 和 '。 若為 ' all '，請使用 ' and ' 聯結子運算式;不支援 ' or '。 | 篩選類型或的欄位 `Collection(Edm.String)``Collection(Edm.GeographyPoint)` | [篩選字串集合的規則](#bkmk_strings) <br/><br/> [篩選 GeographyPoint 集合的規則](#bkmk_geopoints) |
| 不正確 lambda 運算式。 找到比較運算子 (' lt '、' le '、' gt ' 或 ' ge ' ) 其中一個。 Lambda 運算式中只允許相等運算子，可逐一查看類型集合的欄位 (Edm. 字串) 。 若為 ' any '，請使用 ' x eq y ' 形式的運算式。 若為 ' all '，請使用 ' x ne y ' 或 ' not (x eq y) ' 格式的運算式。 | 篩選類型的欄位 `Collection(Edm.String)` | [篩選字串集合的規則](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>如何寫入有效的集合篩選

針對每個資料類型，撰寫有效集合篩選的規則都不一樣。 下列各節將藉由顯示支援哪些篩選功能的範例來說明這些規則，哪些不支援：

- [篩選字串集合的規則](#bkmk_strings)
- [篩選布林值集合的規則](#bkmk_bools)
- [篩選 GeographyPoint 集合的規則](#bkmk_geopoints)
- [篩選可比較的集合規則](#bkmk_comparables)
- [篩選複雜集合的規則](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>篩選字串集合的規則

在字串集合的 lambda 運算式中，唯一可以使用的比較運算子為 `eq` 和 `ne` 。

> [!NOTE]
> Azure 認知搜尋不支援字串的 `lt` / `le` / `gt` / `ge` 運算子，不論是在 lambda 運算式內部或外部。

的主體只能 `any` 測試是否相等，而的本文只能測試是否不 `all` 相等。

您也可以在的主體中結合多個運算式 `or` `any` ，並 `and` 在的主體中結合 `all` 。 因為函 `search.in` 式相當於結合等號檢查與 `or` ，所以在的主體中也可以使用此函數 `any` 。 相反地， `not search.in` 在的主體中允許 `all` 。

例如，允許下列運算式：

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

雖然不允許這些運算式：

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>篩選布林值集合的規則

此類型 `Edm.Boolean` 僅支援 `eq` 和 `ne` 運算子。 因此，允許將檢查相同範圍變數的這類子句結合在一起，就不會有太大的意義， `and` / `or` 因為這一律會導致 tautologies 或您一致。

以下是允許的布林值集合篩選準則範例：

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

不同于字串集合，布林值集合沒有可在哪種 lambda 運算式類型中使用的運算子限制。 `eq`和都 `ne` 可用於或的主體中 `any` `all` 。

布林值集合不允許下列運算式：

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>篩選 GeographyPoint 集合的規則

集合中的類型值 `Edm.GeographyPoint` 無法彼此直接比較。 相反地，它們必須當做和函式的參數使用 `geo.distance` `geo.intersects` 。 接著，函式 `geo.distance` 必須使用其中一個比較運算子 `lt` 、、或來與距離值進行 `le` 比較 `gt` `ge` 。 這些規則也適用于非集合的 GeographyPoint 欄位。

就像字串集合一樣， `Edm.GeographyPoint` 集合也有一些規則，可讓您在不同類型的 lambda 運算式中使用和合併地理空間函數：

- 您可以搭配函數使用的比較運算子 `geo.distance` 取決於 lambda 運算式的類型。 若為 `any` ，您只能使用 `lt` 或 `le` 。 若為 `all` ，您只能使用 `gt` 或 `ge` 。 您可以對涉及的運算式進行否定 `geo.distance` 運算，但您必須將比較運算子 (`geo.distance(...) lt x` 變成， `not (geo.distance(...) ge x)` 然後 `geo.distance(...) le x` 才 `not (geo.distance(...) gt x)`) 。
- 在的主體中 `all` ，函式 `geo.intersects` 必須是否定的。 相反地，在的主體中 `any` ， `geo.intersects` 函數不能是否定的。
- 在的主體中 `any` ，可以使用合併地理空間運算式 `or` 。 在的主體中 `all` ，可以使用結合這類運算式 `and` 。

有上述限制的原因類似于字串集合的等號/不等限制。 若要深入瞭解這些原因，請參閱 [瞭解 Azure 認知搜尋中的 OData 集合篩選](search-query-understand-collection-filters.md) 。

以下是允許的集合上的一些篩選範例 `Edm.GeographyPoint` ：

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

集合不允許如下的運算式 `Edm.GeographyPoint` ：

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>篩選可比較的集合規則

本節適用于下列所有資料類型：

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

和等類型 `Edm.Int32` `Edm.DateTimeOffset` 都支援所有六個比較運算子： `eq` 、 `ne` 、 `lt` 、 `le` 、 `gt` 和 `ge` 。 這些類型的集合上的 Lambda 運算式，可以使用任何運算子來包含簡單的運算式。 這同時適用于 `any` 和 `all` 。 例如，允許下列篩選：

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

不過，這類比較運算式如何結合成 lambda 運算式內更複雜的運算式，會有一些限制：

- 規則 `any` ：
  - 簡單的不等比較運算式無法常見與任何其他運算式結合。 例如，允許此運算式：
    - `ratings/any(r: r ne 5)`

    但這個運算式不是：
    - `ratings/any(r: r ne 5 and r gt 2)`

    雖然這是允許的運算式，但它並不實用，因為條件會重迭：
    - `ratings/any(r: r ne 5 or r gt 7)`
  - `eq`與、、 `lt` `le` 、 `gt` 或結合的簡單比較運算式 `ge` 可以結合 `and` / `or` 。 例如：
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - 結合 `and` (結合) 的比較運算式可以使用進一步組合 `or` 。 此形式在布林值邏輯中已知為 "[Disjunctive Normal form](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF) 。 例如：
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- 規則 `all` ：
  - 簡單的等號比較運算式無法常見與任何其他運算式結合。 例如，允許此運算式：
    - `ratings/all(r: r eq 5)`

    但這個運算式不是：
    - `ratings/all(r: r eq 5 or r le 2)`

    雖然這是允許的運算式，但它並不實用，因為條件會重迭：
    - `ratings/all(r: r eq 5 and r le 7)`
  - `ne`與、、 `lt` `le` 、 `gt` 或結合的簡單比較運算式 `ge` 可以結合 `and` / `or` 。 例如：
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - 結合 `or` (disjunctions) 的比較運算式可以使用進一步組合 `and` 。 此形式在布林值邏輯中已知為 "[組成 Normal form](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (my.cnf) 。 例如：
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>篩選複雜集合的規則

複雜集合上的 lambda 運算式支援更具彈性的語法，而非基元型別集合的 lambda 運算式。 您可以在這類 lambda 運算式內使用任何可以在外部使用的篩選準則，但只有兩個例外狀況。

首先， `search.ismatch` `search.ismatchscoring` lambda 運算式內不支援函數和。 如需詳細資訊，請參閱 [Azure 認知搜尋中的瞭解 OData 集合篩選](search-query-understand-collection-filters.md)。

其次，參考未系結 *至範圍* 變數 (的欄位，) 不允許的 *自由變數* 。 例如，請考慮下列兩個對等的 OData 篩選運算式：

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

第一個運算式將被允許，而第二個表單將被拒絕，因為未系結 `details/margin` 至範圍變數 `s` 。

此規則也會延伸至具有在外部範圍內系結之變數的運算式。 這類變數在出現的範圍中是免費的。 例如，允許第一個運算式，但不允許第二個對等運算式，因為 `s/name` 範圍變數的範圍是免費的 `a` ：

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

這項限制不會造成問題，因為一律可以建立篩選準則，讓 lambda 運算式只包含系結的變數。

## <a name="cheat-sheet-for-collection-filter-rules"></a>集合篩選規則的功能提要

下表摘要說明每個集合資料類型的有效篩選器的規則。

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

如需如何為每個案例建立有效篩選的範例，請參閱 [如何撰寫有效的集合篩選](#bkmk_examples)。

如果您經常撰寫篩選，並瞭解來自第一個原則的規則，將可協助您不只是循它們，請參閱 [瞭解 Azure 認知搜尋中的 OData 集合篩選](search-query-understand-collection-filters.md)。

## <a name="next-steps"></a>接下來的步驟  

- [瞭解 Azure 認知搜尋中的 OData 集合篩選](search-query-understand-collection-filters.md)
- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](/rest/api/searchservice/Search-Documents)