---
title: Lucene 查詢語法
titleSuffix: Azure Cognitive Search
description: 完整的 Lucene 查詢文法的引用,如 Azure 認知搜尋中用於通配符、模糊搜索、RegEx 和其他高級查詢構造。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: 1392f69bea09996e46ad4c112474f9067ff5a63d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656905"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Azure 認知搜尋中的 Lucene 查詢文法

您可以基於豐富的[Lucene 查詢解析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)語法編寫針對 Azure 認知搜索的查詢,用於專用查詢表單:通配符、模糊搜索、鄰近搜索、正則運算式就是一些範例。 除了透過`$filter`表示式在 Azure 認知搜尋中建構*的範圍搜尋*外,許多 Lucene 查詢解析器語法[在 Azure 認知搜尋中完整地實現](search-lucene-query-architecture.md)。 

> [!NOTE]
> 完整的 Lucene 語法用於在[搜尋文件](https://docs.microsoft.com/rest/api/searchservice/search-documents)API**的搜尋**參數中傳遞的查詢運算式,不要與用於該 API [$filter](search-filters.md)參數的[OData 語法](query-odata-filter-orderby-syntax.md)混淆。 這些不同的語法具有用於建構查詢、轉義字串等自己的規則。

## <a name="how-to-invoke-full-parsing"></a>如何叫用完整剖析

設定 `queryType` 搜尋參數以指定所要使用的剖析器。 有效值包括 `simple|full`，`simple` 為預設值，`full` 則用於 Lucene。 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>顯示完整語法的範例

下列範例會使用 Lucene 查詢語法 (這可從 `queryType=full` 參數輕易看出) 尋找索引中的文件。 此查詢會傳回類別欄位包含「預算」一詞以及所有可搜尋欄位包含「最近翻新」詞組的旅館。 包含「最近翻新」片語的文件會因為字詞提升值 (3) 而排在比較前面。  

`searchMode=all` 參數在此範例中具有相關性。 每當在查詢上使用運算子時，您通常都應設定 `searchMode=all`，以確保*所有*的準則均相符。

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2019-05-06&querytype=full
```

 或者，可以使用 POST：  

```
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

有關其他範例,請參閱[Lucene 查詢文法範例,用於在 Azure 認知搜尋 中產生查詢](search-query-lucene-examples.md)。 有關指定查詢參數的完整列隊的詳細資訊,請參閱[搜尋文檔&#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)。

> [!NOTE]  
>  Azure 認知搜索還支援[簡單查詢語法](query-simple-syntax.md),這是一種簡單而可靠的查詢語言,可用於簡單的關鍵字搜索。  

##  <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a>語法基礎  
 下列語法基礎適用於所有使用 Lucene 語法的查詢。  

### <a name="operator-evaluation-in-context"></a>內容中的運算子評估

位置會決定一個符號應解譯為運算子，或只是字串中的另一個字元。

例如，在 Lucene 完整語法中，波狀符號 (~) 可用於模糊搜尋與鄰近搜尋。 ~ 放在加上引號的片語後面時，將會叫用鄰近搜尋。 ~ 放在字詞結尾處時，則會叫用模糊搜尋。

放在字詞內時 (例如 "business~analyst")，該字元並不會評估為運算子。 在此情況下，假設查詢是字詞或片語查詢，會進行[語彙分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)的[全文檢索搜尋](search-lucene-query-architecture.md)將會去除 ~ 並將 "business~analyst" 一詞拆分為二：business OR analyst。

上述範例說明的是波狀符號 (~)，但每個運算子都適用相同原則。

### <a name="escaping-special-characters"></a>逸出特殊字元

 特殊字元必須逸出，才可作為搜尋文字的一部分。 在其前面加上反斜線 (\\) 即可加以逸出。 需要逸出的特殊字元包括：  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 例如,要轉義通配子,請使用\\\*。

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>為 URL 中的 Unsafe 字元和保留字元編碼

 請確定所有 Unsafe 字元和保留字元在 URL 中都已編碼。 例如，'#' 是 Unsafe 字元，因為它是 URL 中的片段/錨點識別碼。 此字元在 URL 中使用時必須編碼為 `%23`。 "&"和"*"是保留字元的範例,因為它們在Azure認知搜索中分隔參數並指定值。 有關詳細資訊,請參閱[RFC1738:統一資源定位器 (URL)。](https://www.ietf.org/rfc/rfc1738.txt)

 Unsafe 字元包括 ``" ` < > # % { } | \ ^ ~ [ ]``。 保留字元包括 `; / ? : @ = + &`。

### <a name="precedence-operators-grouping-and-field-grouping"></a>優先順序運算子：分組和欄位分組  
 您可以使用括號，在加上括號的陳述式內加入運算子，以建立子查詢。 例如，`motel+(wifi||luxury)` 會搜尋包含 "motel" 一詞和 "wifi" 或 "luxury" (或兩者) 的文件。

欄位分組也相類似，但分組的範圍會限定於單一欄位。 例如，`hotelAmenities:(gym+(wifi||pool))` 會在 "hotelAmenities" 欄位中搜尋 "gym" 和 "wifi"，或搜尋 "gym" 和 "pool"。  

### <a name="searchmode-parameter-considerations"></a>SearchMode 參數考量  
 如 Azure `searchMode` [認知搜尋中的簡單查詢語法](query-simple-syntax.md)中所述,對查詢的影響同樣適用於 Lucene 查詢語法。 也就是說，如果您不清楚您設定參數的方式會有何影響，`searchMode` 與 NOT 運算子搭配使用時可能會產生出乎您預期的查詢結果。 如果您保留預設值 `searchMode=any`，並使用 NOT 運算子，則會以 OR 動作計算作業，而使 "New York" NOT "Seattle" 傳回所有不是 Seattle 的城市。  

##  <a name="boolean-operators-and-or-not"></a><a name="bkmk_boolean"></a>布林運算符(AND、OR、不) 
 文字布林運算子 (AND、OR、NOT) 須一律全部以大寫指定。  

### <a name="or-operator-or-or-"></a>OR 運算子 `OR` 或 `||`

OR 運算子是分隔號或直立線字元。 例如：`wifi || luxury` 會搜尋包含 "wifi" 或 "luxury" (或兩者) 的文件。 OR 是預設的連接詞運算子，因此您也可以將其省略，而使 `wifi luxury` 相當於 `wifi || luxuery`。

### <a name="and-operator-and--or-"></a>AND 運算子 `AND`、`&&` 或 `+`

AND 運算子是 & 符號或加號。 例如：`wifi && luxury` 會搜尋同時包含 "wifi" 和 "luxury" 的文件。 加號字元 (+) 用於需要的字詞。 例如，`+wifi +luxury` 會指定這兩個字詞必須出現單一文件的某個欄位中。


### <a name="not-operator-not--or--"></a>NOT 運算子 `NOT`、`!` 或 `-`

NOT 運算子是驚歎號或負號。 例如：`wifi !luxury` 會搜尋含有 "wifi" 一詞且/或不含 "luxury" 的文件。 在沒有 + 或 || 運算子的情況下，`searchMode` 選項會控制使用 NOT 運算子的字詞與查詢中的其他字詞之間應使用 AND 還是 OR 來處理。 先前提過，`searchMode` 可設為 `any` (預設值) 或 `all`。

使用 `searchMode=any` 會包含較多結果而提高查詢的召回率，且依預設 - 會解譯為 "OR NOT"。 例如，`wifi -luxury` 會比對出包含 *wifi* 一詞的文件，或不含 *luxury* 一詞的文件。

使用 `searchMode=all` 則會包含較少結果而提高查詢的精確度，且依預設 - 會解譯為 "AND NOT"。 例如，`wifi -luxury` 會比對出包含 `wifi` 一詞且不含 `luxury` 一詞的文件。 就 - 運算子而言，這算是較直覺化的行為。 因此，如果您想要最佳化搜尋的精確度而不是召回率，*且*您的使用者在搜尋中經常使用 `-` 運算子，您即應考慮選擇 `searchMode=all` 而非 `searchMode=any`。

##  <a name="query-size-limitations"></a><a name="bkmk_querysizelimits"></a>查詢大小限制  
 可以發送到 Azure 認知搜尋的查詢大小有限制。 具體來說，您最多可以有 1024 個子句 (以 AND、OR 等運算子分隔的運算式)。 此外，查詢中任何個別字詞的大小也有約 32 KB 的限制。 如果您的應用程式以程式設計方式產生搜尋查詢，建議您依照此方式設計，以避免產生無大小限制的查詢。  

##  <a name="scoring-wildcard-and-regex-queries"></a><a name="bkmk_searchscoreforwildcardandregexqueries"></a>萬用字元和 regex 查詢的評分
 Azure 認知搜尋使用基於頻率的評分 ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) 進行文本查詢。 不過，針對字詞範圍可能很廣泛的萬用字元和 regex 查詢，則會忽略頻率因素，以防止罕見字詞的相符項目誤獲較高的排名。 系統對於萬用字元和 regex 搜尋的所有相符項目，會同等視之。

##  <a name="fielded-search"></a><a name="bkmk_fields"></a>欄位搜尋  
可以使用`fieldName:searchExpression`語法定義欄位搜索操作,其中搜索表達式可以是單一單詞或短語,也可以是括弧中的更複雜的運算式,可選使用布林運算元。 部分範例如下：  

- genre:jazz NOT history  

- artists:("Miles Davis" "John Coltrane")

如果您想要將字串視為單一實體評估，請務必將多個字串放在引號中，就此案例而言，即搜尋 `artists` 欄位中的兩個不同藝人。  

`fieldName:searchExpression` 中指定的欄位必須是 `searchable` 欄位。  如需欄位定義中索引屬性使用方式的詳細資訊，請參閱[建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index)。  

> [!NOTE]
> 使用欄位搜尋表示式時,不需要使用 參數`searchFields`, 因為每個欄位搜尋表示式都有顯式指定的欄位名稱。 但是,如果要運行查詢,`searchFields`其中某些部分限定在特定欄位,其餘部分可以應用於多個字段,則仍可以使用 參數。 例如,查詢將僅`search=genre:jazz NOT history&searchFields=description`與`jazz``genre`字段匹配,而該查詢將與`NOT history``description`該欄位匹配。 中`fieldName:searchExpression`提供的欄位名稱始終優先`searchFields`於 參數,因此在此示例中,我們`genre``searchFields`不需要在 參數中包括。

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a>模糊搜尋  
 模糊搜尋會尋找具有類似建構的相符項目。 在每個 [Lucene 文件](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)中，模糊搜尋以 [Damerau-Levenshtein 距離](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance)為基礎。 模糊搜尋可將一個字詞擴充為最多 50 個符合距離準則的字詞。 

 若要執行模糊搜尋，請在單一文字結尾使用波狀符號 "~"，加上選擇性參數，介於 0 和 2 (預設值) 之間且指定編輯距離的數值。 例如，"blue~" 或 "blue~1" 會傳回 "blue"、"blues" 和 "glue"。

 模糊搜索只能應用於術語,不能應用於短語,但您可以在多部分名稱或短語中單獨將波浪線追加到每個術語中。 例如,「Wshington」的「不溫不雅」與「華盛頓大學」相媲美。
 

##  <a name="proximity-search"></a><a name="bkmk_proximity"></a>鄰近搜尋  
 鄰近搜尋可用來尋找文件中彼此相近的詞彙。 在片語的結尾插入波狀符號 "~"，後面加上建立鄰近界限的字數。 例如，`"hotel airport"~5` 會在文件中尋找相隔 5 個字以內的「飯店」和「機場」等字詞。  


##  <a name="term-boosting"></a><a name="bkmk_termboost"></a>術語提升  
 提升一詞指的是如果文件包含提升詞彙，則將其評等提高，高於不包含該詞彙的文件。 這與評分設定檔的不同之處在於評分設定檔會提升特定欄位，而不是特定詞彙。  

下列範例可協助說明差異。 假設有一個評分設定檔可提升特定欄位中的相符項目，例如 [musicstoreindex 範例](index-add-scoring-profiles.md#bkmk_ex)中的 *genre*。 詞彙提升可用來進一步提升某些搜尋詞彙，使其高於其他詞彙。 例如，`rock^2 electronic` 可提升包含搜尋字詞的文件﹐使其在 genre 欄位中高於索引中的其他可搜尋欄位。 此外，包含搜尋字詞 *rock* 的文件排名會比另一個搜尋字詞 *electronic* 還高，此為字詞提升值 (2) 的結果。  

 若要提升字詞，請使用插入號 "^"，並在搜尋字詞的結尾加上提升係數 (數字)。 您也可以提升片語。 提升係數越高，該詞彙相對於其他搜尋詞彙的關聯性也越高。 根據預設，提升係數為 1。 雖然提升係數必須是正數，但是它可能會小於 1 (例如，0.20)。  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a>規則運算式搜尋  
 規則運算式搜尋會根據正斜線 "/" 之間的內容尋找相符項目，如 [RegExp 類別](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)中所記錄。  

 例如，若要尋找包含 "motel" 或 "hotel" 的文件，請指定 `/[mh]otel/`。 規則運算式搜尋會比對單字。

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a>通配符搜尋  
 您可以使用一般辨識語法進行多個 (*) 或單一 (?) 字元的萬用字元搜尋。 請注意，Lucene 查詢剖析器支援搭配使用這些符號與單一詞彙，而不是片語。

前置搜尋使用星號 (`*`) 字元。 例如,返回"筆記本"或`search=note*`"記事本"的查詢表達式。 首碼搜索不需要完整的 Lucene 語法。 簡單語法支援此方案。

後綴搜尋 (字串`*`之前或`?`字串之前)需要完整的 Lucene 語法和正規表示式(不能使用 * 或 ? 符號作為搜索的第一個字元)。 給定術語「字母數位」,查詢表達式`search=/.*numeric.*/`( ) 將查找匹配項。

> [!NOTE]  
> 在查詢解析期間,作為前置碼、尾碼、通配符或正規表示式配製查詢按原樣傳遞給查詢樹,繞過[詞法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)。 僅當索引包含查詢指定的格式的字串時,才會找到匹配項。 在大多數情況下,在索引期間需要一個替代分析器,以保留字串完整性,以便部分術語和模式匹配成功。 有關詳細資訊,請參閱[Azure 認知搜尋查詢中的部分術語搜尋](search-query-partial-matching.md)。

## <a name="see-also"></a>另請參閱  

+ [搜尋文件](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [篩選和排序的 OData 運算式語法](query-odata-filter-orderby-syntax.md)   
+ [Azure 認知搜尋的簡單查詢語法](query-simple-syntax.md)   
