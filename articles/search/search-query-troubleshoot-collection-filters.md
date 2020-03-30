---
title: 故障排除 O 資料收集篩選器
titleSuffix: Azure Cognitive Search
description: 瞭解解決 Azure 認知搜索查詢中的 OData 集合篩選器錯誤的方法。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113087"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>在 Azure 認知搜索中排除 O 資料資料收集篩選器的疑難排解

要[篩選](query-odata-filter-orderby-syntax.md)Azure 認知搜索中的集合欄位，可以將[`any``all`和 運算子](search-query-odata-collection-operators.md)與**lambda 運算式**一起使用。 lambda 運算式是應用於集合的每個元素的子篩選器。

並非所有篩選器運算式的功能都可用於 lambda 運算式中。 哪些功能可用因要篩選的集合欄位的資料類型而異。 如果嘗試在 lambda 運算式中使用該上下文中不支援的功能，則可能導致錯誤。 如果您在嘗試在集合欄位上編寫複雜的篩選器時遇到此類錯誤，本文將説明您解決問題。

## <a name="common-collection-filter-errors"></a>常見集合篩選器錯誤

下表列出了嘗試執行集合篩選器時可能會遇到的錯誤。 當您使用 lambda 運算式中不支援的篩選器運算式的功能時，會發生這些錯誤。 每個錯誤都提供了有關如何重寫篩選器以避免錯誤的一些指導。 該表還包含指向本文相關部分的連結，該部分提供了有關如何避免該錯誤的詳細資訊。

| 錯誤訊息 | 情況 | 如需相關資訊，請參閱 |
| --- | --- | --- |
| 函數"ismatch"沒有綁定到範圍變數 ''s 的參數。 lambda 運算式（"任何"或"全部"）內僅支援綁定欄位引用。 請更改篩選器，以便"ismatch"函數位于 lambda 運算式之外，然後重試。 | 使用`search.ismatch`或`search.ismatchscoring`位於 lambda 運算式中 | [篩選複雜集合的規則](#bkmk_complex) |
| 不正確 lambda 運算式。 找到了相等性或不等式測試，其中在 lambda 運算式中預期相反，該運算式在類型集合（Edm.String）的欄位上進行遍比。 對於"任意"，請使用表單"x eq y"或"search.in（...）"的運算式。 對於"所有"，請使用表單"x ne y"、"不是（x eq y）"或"不search.in（...）"的運算式。 | 在類型欄位上篩選`Collection(Edm.String)` | [篩選字串集合的規則](#bkmk_strings) |
| 不正確 lambda 運算式。 找到了一種不支援的複雜布林運算式形式。 對於"任意"，請使用"AND 的 OR"，也稱為"分離法形"的運算式。 例如：'（a 和 b）或 （c 和 d）'，其中 a、b、c 和 d 是比較或相等子運算式。 對於"所有"，請使用"OD"的運算式，也稱為"組織正常形式"。 例如：'（a 或 b）和（c 或 d）'，其中 a、b、c 和 d 是比較或不等式子運算式。 比較運算式的示例："x gt 5"，"x le 2"。 相等運算式的示例："x eq 5"。 不等式運算式的示例："x ne 5"。 | 篩選類型`Collection(Edm.DateTimeOffset)`欄位`Collection(Edm.Double)`， `Collection(Edm.Int32)`、 或`Collection(Edm.Int64)` | [篩選可比集合的規則](#bkmk_comparables) |
| 不正確 lambda 運算式。 在 lambda 運算式中找到了不支援的地理.距離（） 或地理.intersects（） 的使用，該運算式在類型集合（Edm.地理點）的欄位中進行遍比。 對於"任意"，請確保使用"lt"或"le"運算子比較地理.距離（），並確保不否定地理.intersects（）的任何用法。 對於"所有"，請確保使用"gt"或"ge"運算子比較地理.距離（），並確保否定了地理.intersects（）的任何用法。 | 在類型欄位上篩選`Collection(Edm.GeographyPoint)` | [篩選地理點集合的規則](#bkmk_geopoints) |
| 不正確 lambda 運算式。 在類型集合（Edm.地理點）的欄位上反覆運算的 lambda 運算式中不支援複雜的布林運算式。 對於"任意"，請將子運算式與"or"聯接;不支援"和" 對於"所有"，請將子運算式與"和"聯接;不支援"或"。 | 篩選類型`Collection(Edm.String)`或`Collection(Edm.GeographyPoint)` | [篩選字串集合的規則](#bkmk_strings) <br/><br/> [篩選地理點集合的規則](#bkmk_geopoints) |
| 不正確 lambda 運算式。 找到比較運算子（"lt"、"le"、gt'或"ge"）。 在類型集合（Edm.String）的欄位中反覆運算的 lambda 運算式中，只允許使用相等運算子。 對於"任意"，請使用表單"x eq y"的運算式。 對於"所有"，請使用表單"x ne y"或"不是（x eq y）"的運算式。 | 在類型欄位上篩選`Collection(Edm.String)` | [篩選字串集合的規則](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>如何編寫有效的集合篩選器

編寫有效集合篩選器的規則對於每種資料類型都不同。 以下各節通過顯示支援哪些篩選器功能的示例來描述規則，哪些功能不支援：

- [篩選字串集合的規則](#bkmk_strings)
- [篩選布林集合的規則](#bkmk_bools)
- [篩選地理點集合的規則](#bkmk_geopoints)
- [篩選可比集合的規則](#bkmk_comparables)
- [篩選複雜集合的規則](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>篩選字串集合的規則

在字串集合的 lambda 運算式中，唯一可以使用的比較運算子是`eq`和`ne`。

> [!NOTE]
> Azure 認知搜索不支援字串的`lt`/`le`/`gt`/`ge`運算子，無論是在 lambda 運算式內部還是外部。

身體`any`只能測試平等，而身體`all`只能測試不平等。

也可以通過 在`or`的正文中和`any``and`在 的正文中組合多個運算式。 `all` 由於函數`search.in`等效于將相等檢查與`or`，因此在 正文中也允許它。 `any` 相反，`not search.in`允許在 的正文中`all`。

例如，允許這些運算式：

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

而不允許這些運算式：

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>篩選布林集合的規則

類型`Edm.Boolean`僅支援 和`eq``ne`運算子。 因此，允許組合此類子句以檢查同一範圍變數`and`/`or`並沒有多大意義，因為這總是會導致tautos 或矛盾。

下面是布林集合上允許的篩選器的一些示例：

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

與字串集合不同，布林集合對可在哪種類型的 lambda 運算式中使用運算子沒有限制。 `eq`和`ne`都可以在`any`或`all`的正文中使用 。

布林集合不允許使用以下運算式：

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>篩選地理點集合的規則

不能直接比較`Edm.GeographyPoint`集合中的類型值。 相反，它們必須用作 和`geo.distance``geo.intersects`函數的參數。 反過來`geo.distance`，必須使用比較運算子`lt`之一`le`將`gt`函數與距離值進行比較。 `ge` 這些規則也適用于非集合 Edm.地理點欄位。

與字串集合一`Edm.GeographyPoint`樣，集合對於如何在不同類型的 lambda 運算式中使用和組合地理空間函數有一些規則：

- 可以使用`geo.distance`函數的比較運算子取決於 lambda 運算式的類型。 對於`any`，只能`lt`使用 或`le`。 對於`all`，只能`gt`使用 或`ge`。 您可以否定涉及`geo.distance``geo.distance(...) lt x`的運算式，但必須更改比較運算子（變為`not (geo.distance(...) ge x)``geo.distance(...) le x`和 變為`not (geo.distance(...) gt x)`）。
- 在`all`的正文中，`geo.intersects`函數必須被否定。 相反，在 體中`any`，`geo.intersects`不能否定函數。
- 在`any`正文中，可以使用 進行地理空間運算式組合`or`。 在 正文中`all`，可以使用 將此類運算式組合使用`and`。

上述限制的存在與字串集合的相等性/不等式限制類似。 請參閱[Azure 認知搜索中瞭解 OData 集合篩選器](search-query-understand-collection-filters.md)，以便深入瞭解這些原因。

下面是允許的集合上的`Edm.GeographyPoint`篩選器的一些示例：

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

集合不允許`Edm.GeographyPoint`使用以下運算式：

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>篩選可比集合的規則

本節適用于以下所有資料類型：

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

類型（如`Edm.Int32``Edm.DateTimeOffset`和支援所有六個比較運算子）：、、、、、、`eq``ne``lt``le``gt`和`ge`。 對這些類型集合的 Lambda 運算式可以使用這些運算子中的任何一個包含簡單運算式。 這同時適用于`any`和`all`。 例如，允許這些篩選器：

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

但是，對於如何將此類比較運算式組合到 lambda 運算式中的更複雜的運算式存在限制：

- 的規則`any`：
  - 簡單的不等式運算式不能與任何其他運算式有益地結合。 例如，允許此運算式：
    - `ratings/any(r: r ne 5)`

    但此運算式不是：
    - `ratings/any(r: r ne 5 and r gt 2)`

    雖然允許此運算式，但它沒有用處，因為條件重疊：
    - `ratings/any(r: r ne 5 or r gt 7)`
  - 涉及`eq``lt`、、`le``gt``ge`或 可以`and`/`or`與 相約的簡單比較運算式與 。 例如：
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - 比較運算式與`and`（連合） 可以進一步組合`or`使用 。 此表單在布林邏輯中稱為"[分離法態形式](https://en.wikipedia.org/wiki/Disjunctive_normal_form)"（DNF）。 例如：
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- 的規則`all`：
  - 簡單的相等運算式不能與任何其他運算式有益地結合。 例如，允許此運算式：
    - `ratings/all(r: r eq 5)`

    但此運算式不是：
    - `ratings/all(r: r eq 5 or r le 2)`

    雖然允許此運算式，但它沒有用處，因為條件重疊：
    - `ratings/all(r: r eq 5 and r le 7)`
  - 涉及`ne``lt`、、`le``gt``ge`或 可以`and`/`or`與 相約的簡單比較運算式與 。 例如：
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - 比較運算式與`or`（分詞） 結合可以使用 進一步`and`組合使用 。 此表單在布林邏輯中稱為"[組織法態形式](https://en.wikipedia.org/wiki/Conjunctive_normal_form)"（CNF）。 例如：
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>篩選複雜集合的規則

在複雜集合上使用的 Lambda 運算式支援比 lambda 運算式在基元類型集合上更靈活的語法。 您可以在此類 lambda 運算式內使用任何篩選器構造，以便可以在一個運算式外部使用，只有兩個例外。

首先，lambda `search.ismatch` `search.ismatchscoring`運算式中不支援函數和不支援這些函數。 有關詳細資訊，請參閱[Azure 認知搜索 中瞭解 OData 集合篩選器](search-query-understand-collection-filters.md)。

其次，不允許引用不*綁定*到範圍變數（所謂的*可用變數*）的欄位。 例如，請考慮以下兩個等效的 OData 篩選器運算式：

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

將允許第一個運算式，而第二個表單將被拒絕，`details/margin`因為未綁定到範圍變數`s`。

此規則還擴展到具有在外部作用域中綁定變數的運算式。 此類變數在出現範圍方面是免費的。 例如，允許第一個運算式，而不允許第二個等效運算式，因為`s/name`範圍變數`a`的範圍是免費的：

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

在實踐中，這種限制不應成為問題，因為始終可以構造篩選器，以便 lambda 運算式僅包含綁定變數。

## <a name="cheat-sheet-for-collection-filter-rules"></a>用於收集篩選器規則的備忘單

下表總結了為每個集合資料類型構造有效篩選器的規則。

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

有關如何為每個情況構造有效篩選器的示例，請參閱[如何編寫有效的集合篩選器](#bkmk_examples)。

如果經常編寫篩選器，並且從第一原則中瞭解規則將不僅僅有助於記住它們，請參閱[Azure 認知搜索 中瞭解 OData 集合篩選器](search-query-understand-collection-filters.md)。

## <a name="next-steps"></a>後續步驟  

- [瞭解 Azure 認知搜索中的 OData 集合篩選器](search-query-understand-collection-filters.md)
- [Azure 認知搜索中的篩選器](search-filters.md)
- [Azure 認知搜索的 OData 運算式語言概述](query-odata-filter-orderby-syntax.md)
- [用於 Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)
- [搜索文檔&#40;Azure 認知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
