---
title: Lucene 查詢語法
titleSuffix: Azure Cognitive Search
description: 完整 Lucene 查詢語法的參考，如用於萬用字元、模糊搜尋、RegEx 和其他先進查詢結構的 Azure 認知搜尋中所使用。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/23/2020
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
ms.openlocfilehash: 6ea8bc2551df4f85e4b856dc9cf1c06a9bd571fd
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923444"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Azure 認知搜尋中的 Lucene 查詢語法

您可以根據特殊查詢形式的豐富 [Lucene 查詢](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 剖析器語法，針對 Azure 認知搜尋撰寫查詢：萬用字元、模糊搜尋、鄰近搜尋、正則運算式為一些範例。 在 Azure 認知搜尋中，大部分的 Lucene 查詢剖析器語法都是 [完整](search-lucene-query-architecture.md)的，但 *範圍搜尋* 的例外狀況是透過運算式在 Azure 認知搜尋中所建立 `$filter` 。 

> [!NOTE]
> 完整的 Lucene 語法用於[搜尋檔](/rest/api/searchservice/search-documents)API 之**搜尋**參數中所傳遞的查詢運算式，而不是與該 api 的[$Filter](search-filters.md)參數所使用的[OData 語法](query-odata-filter-orderby-syntax.md)混淆。 這些不同的語法有自己的規則，可供您用來建立查詢、逸出字元串等等。

## <a name="invoke-full-parsing"></a>叫用完整剖析

設定 `queryType` 搜尋參數以指定所要使用的剖析器。 有效值包括 `simple|full`，`simple` 為預設值，`full` 則用於 Lucene。 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>顯示完整語法的範例

下列範例會使用 Lucene 查詢語法 (這可從 `queryType=full` 參數輕易看出) 尋找索引中的文件。 此查詢會傳回類別欄位包含「預算」一詞以及所有可搜尋欄位包含「最近翻新」詞組的旅館。 包含「最近翻新」片語的文件會因為字詞提升值 (3) 而排在比較前面。  

`searchMode=all` 參數在此範例中具有相關性。 每當在查詢上使用運算子時，您通常都應設定 `searchMode=all`，以確保*所有*的準則均相符。

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2020-06-30&querytype=full
```

 或者，可以使用 POST：  

```
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

如需其他範例，請參閱 [在 Azure 認知搜尋中建立查詢的 Lucene 查詢語法範例](search-query-lucene-examples.md)。 如需有關指定完整查詢參數的詳細資訊，請參閱 [Azure 認知搜尋 REST API&#41;搜尋檔 &#40;](/rest/api/searchservice/Search-Documents)。

> [!NOTE]  
>  Azure 認知搜尋也支援 [簡單的查詢語法](query-simple-syntax.md)，這是一個簡單且健全的查詢語言，可用於直接關鍵字搜尋。  

##  <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a>語法基礎  

下列語法基礎適用于使用 Lucene 語法的所有查詢。  

### <a name="operator-evaluation-in-context"></a>內容中的運算子評估

位置會決定一個符號應解譯為運算子，或只是字串中的另一個字元。

例如，在 Lucene 完整語法中，波狀符號 (~) 可用於模糊搜尋與鄰近搜尋。 ~ 放在加上引號的片語後面時，將會叫用鄰近搜尋。 ~ 放在字詞結尾處時，則會叫用模糊搜尋。

放在字詞內時 (例如 "business~analyst")，該字元並不會評估為運算子。 在此情況下，假設查詢是字詞或片語查詢，會進行[語彙分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)的[全文檢索搜尋](search-lucene-query-architecture.md)將會去除 ~ 並將 "business~analyst" 一詞拆分為二：business OR analyst。

上述範例說明的是波狀符號 (~)，但每個運算子都適用相同原則。

### <a name="escaping-special-characters"></a>逸出特殊字元

若要使用任何搜尋運算子做為搜尋文字的一部分，請在字元前面加上一個反斜線 () ，以將該字元換用 `\` 。 例如，針對萬用字元搜尋 `https://` ，其中 `://` 是查詢字串的一部分，您可以指定 `search=https\:\/\/*` 。 同樣地，換用的電話號碼模式可能如下所示 `\+1 \(800\) 642\-7676` 。

需要進行轉義的特殊字元包括下列各項：  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> 雖然可讓標記保持在一起，但在編制索引期間的 [詞法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis) 可能會將它們帶出來。例如，標準 Lucene 分析器會中斷連字號、空格和其他字元的單字。 如果您需要查詢字串中的特殊字元，您可能需要分析器將它們保留在索引中。 某些選擇包括 Microsoft 自然 [語言分析器](index-add-language-analyzers.md)（可保留斷字元字組）或自訂分析器，以取得更複雜的模式。 如需詳細資訊，請參閱 [部分詞彙、模式和特殊字元](search-query-partial-matching.md)。

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>為 URL 中的 Unsafe 字元和保留字元編碼

請確定所有 Unsafe 字元和保留字元在 URL 中都已編碼。 例如，' # ' 是不安全的字元，因為它是 URL 中的片段/錨點識別碼。 此字元在 URL 中使用時必須編碼為 `%23`。 ' & ' 和 ' = ' 是保留字元的範例，因為它們會將參數分隔，並在 Azure 認知搜尋中指定值。 如需詳細資訊，請參閱 [RFC1738：統一資源定位器 (URL) ](https://www.ietf.org/rfc/rfc1738.txt) 。

Unsafe 字元包括 ``" ` < > # % { } | \ ^ ~ [ ]``。 保留字元包括 `; / ? : @ = + &`。

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a> 查詢大小限制

 您可以傳送給 Azure 認知搜尋的查詢大小有限制。 具體來說，您最多可以有 1024 個子句 (以 AND、OR 等運算子分隔的運算式)。 此外，查詢中任何個別字詞的大小也有約 32 KB 的限制。 如果您的應用程式以程式設計方式產生搜尋查詢，建議您依照此方式設計，以避免產生無大小限制的查詢。  

### <a name="precedence-operators-grouping"></a>優先順序運算子 (分組) 

 您可以使用括號，在加上括號的陳述式內加入運算子，以建立子查詢。 例如，`motel+(wifi||luxury)` 會搜尋包含 "motel" 一詞和 "wifi" 或 "luxury" (或兩者) 的文件。

欄位分組也相類似，但分組的範圍會限定於單一欄位。 例如，`hotelAmenities:(gym+(wifi||pool))` 會在 "hotelAmenities" 欄位中搜尋 "gym" 和 "wifi"，或搜尋 "gym" 和 "pool"。  

##  <a name="boolean-search"></a><a name="bkmk_boolean"></a> 布林值搜尋

 文字布林運算子 (AND、OR、NOT) 須一律全部以大寫指定。  

### <a name="or-operator-or-or-"></a>OR 運算子 `OR` 或 `||`

OR 運算子是分隔號或直立線字元。 例如：`wifi || luxury` 會搜尋包含 "wifi" 或 "luxury" (或兩者) 的文件。 OR 是預設的連接詞運算子，因此您也可以將其省略，而使 `wifi luxury` 相當於 `wifi || luxury`。

### <a name="and-operator-and--or-"></a>AND 運算子 `AND`、`&&` 或 `+`

AND 運算子是 & 符號或加號。 例如：`wifi && luxury` 會搜尋同時包含 "wifi" 和 "luxury" 的文件。 加號字元 (+) 用於需要的字詞。 例如，`+wifi +luxury` 會指定這兩個字詞必須出現單一文件的某個欄位中。

### <a name="not-operator-not--or--"></a>NOT 運算子 `NOT`、`!` 或 `-`

NOT 運算子是負號。 例如， `wifi –luxury` 會搜尋具有 `wifi` 和/或不具有該詞彙的檔 `luxury` 。

查詢要求的 **searchMode** 參數會控制使用 NOT 運算子的詞彙是否為以 and 連結或 or 運算，以及查詢中的其他詞彙 (假設 `+` `|`) 的其他詞彙沒有 or 運算子。 有效值包括 `any` 或 `all`。

`searchMode=any` 藉由包含更多結果來提高查詢的召回率，且依預設 `-` 會將其解釋為 "OR NOT"。 例如，`wifi -luxury` 會比對出包含 `wifi` 一詞的文件，或不含 `luxury` 一詞的文件。

`searchMode=all` 藉由包含較少的結果來提高查詢的精確度，而且根據預設，將會被解釋為 "AND NOT"。 例如，`wifi -luxury` 會比對出包含 `wifi` 一詞且不含 "luxury" 一詞的文件。 就 `-` 運算子而言，這算是較直覺化的行為。 因此， `searchMode=all` `searchMode=any` 如果您想要優化搜尋的精確度而不是召回率， *且* 您的使用者經常 `-` 在搜尋中使用運算子，您應該考慮使用而不是。

決定 **searchMode** 設定時，請考慮各種應用程式中查詢的使用者互動模式。 搜尋資訊的使用者比較有可能在查詢中包含運算子，而不是具有更多內建導覽結構的電子商務網站。

##  <a name="fielded-search"></a><a name="bkmk_fields"></a> 回復搜尋

您可以使用語法來定義回復搜尋作業 `fieldName:searchExpression` ，其中搜尋運算式可以是單一單字或片語，或是以括弧括住的更複雜運算式（選擇性地使用布林運算子）。 部分範例如下：  

- genre:jazz NOT history  

- artists:("Miles Davis" "John Coltrane")

如果您想要將字串視為單一實體評估，請務必將多個字串放在引號中，就此案例而言，即搜尋 `artists` 欄位中的兩個不同藝人。  

`fieldName:searchExpression` 中指定的欄位必須是 `searchable` 欄位。  如需欄位定義中索引屬性使用方式的詳細資訊，請參閱[建立索引](/rest/api/searchservice/create-index)。  

> [!NOTE]
> 使用回復搜尋運算式時，您不需要使用 `searchFields` 參數，因為每個回復搜尋運算式都有明確指定的功能變數名稱。 但是， `searchFields` 如果您想要執行查詢，其中某些部分的範圍設定為特定欄位，而且其餘部分可以套用至數個欄位，您仍然可以使用參數。 例如，查詢 `search=genre:jazz NOT history&searchFields=description` 只會比對 `jazz` `genre` 欄位，但會與 `NOT history` `description` 欄位相符。 中提供的功能變數名稱 `fieldName:searchExpression` 一律優先于 `searchFields` 參數，這就是在此範例中，我們不需要包含 `genre` 在參數中的原因 `searchFields` 。

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a> 模糊搜尋

模糊搜尋會尋找具有類似結構的相符專案，並將某個詞彙擴充到最大的50詞彙，以符合兩個或更少的距離準則。 如需詳細資訊，請參閱 [模糊搜尋](search-query-fuzzy.md)。

 若要執行模糊搜尋，請在單一文字結尾使用波狀符號 "~"，加上選擇性參數，介於 0 和 2 (預設值) 之間且指定編輯距離的數值。 例如，"blue~" 或 "blue~1" 會傳回 "blue"、"blues" 和 "glue"。

 模糊搜尋只能套用至詞彙（而不是片語），但是您可以在多部分名稱或片語中個別將波狀符號附加至每個詞彙。 例如，"Unviersty ~ ~" Wshington ~ "會符合「華盛頓大學」。
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a> 鄰近搜尋

鄰近搜尋可用來尋找文件中彼此相近的詞彙。 在片語的結尾插入波狀符號 "~"，後面加上建立鄰近界限的字數。 例如，`"hotel airport"~5` 會在文件中尋找相隔 5 個字以內的「飯店」和「機場」等字詞。  


##  <a name="term-boosting"></a><a name="bkmk_termboost"></a> 詞彙提升

提升一詞指的是如果文件包含提升詞彙，則將其評等提高，高於不包含該詞彙的文件。 這與評分設定檔的不同之處在於評分設定檔會提升特定欄位，而不是特定詞彙。  

下列範例可協助說明差異。 假設有一個評分設定檔可提升特定欄位中的相符項目，例如 [musicstoreindex 範例](index-add-scoring-profiles.md#bkmk_ex)中的 *genre*。 詞彙提升可用來進一步提升某些搜尋詞彙，使其高於其他詞彙。 例如，`rock^2 electronic` 可提升包含搜尋字詞的文件﹐使其在 genre 欄位中高於索引中的其他可搜尋欄位。 此外，包含搜尋字詞 *rock* 的文件排名會比另一個搜尋字詞 *electronic* 還高，此為字詞提升值 (2) 的結果。  

 若要提升字詞，請使用插入號 "^"，並在搜尋字詞的結尾加上提升係數 (數字)。 您也可以提升片語。 提升係數越高，該詞彙相對於其他搜尋詞彙的關聯性也越高。 根據預設，提升係數為 1。 雖然提升係數必須是正數，但是它可能會小於 1 (例如，0.20)。  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a>規則運算式搜尋  
 正則運算式搜尋會根據在 Apache Lucene 下有效的模式尋找相符項，如 [RegExp 類別](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)中所述。 在 Azure 認知搜尋中，正則運算式會包含在正斜線之間 `/` 。

 例如，若要尋找包含 "motel" 或 "hotel" 的文件，請指定 `/[mh]otel/`。 規則運算式搜尋會比對單字。

某些工具和語言會強加額外的 escape 字元需求。 若為 JSON，包含正斜線的字串會以反斜線來進行轉義： "microsoft.com/azure/" 會變成 `search=/.*microsoft.com\/azure\/.*/` `search=/.* <string-placeholder>.*/` 設定正則運算式的位置，而 `microsoft.com\/azure\/` 是具有反斜線正斜線的字串。

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a> 萬用字元搜尋

您可以針對多個 (`*`) 或單一 (`?`) 字元萬用字元搜尋使用一般辨識的語法。 例如，的查詢運算式會傳回「英數位元」 `search=alpha*` 或「字母順序」。 請注意，Lucene 查詢剖析器支援搭配使用這些符號與單一詞彙，而不是片語。

Full Lucene 語法支援前置、中置和尾碼比對。 但是，如果您只需要前置詞比對，則可以使用這兩個) 中支援的簡單語法 (前置詞比對。

尾碼比對， `*` `?` 在字串 (的位置或前面，如) 或中置比對， `search=/.*numeric./` 則需要完整的 Lucene 語法，以及正則運算式正斜線 `/` 分隔符號。 您無法使用 * 或 ? 符號做為詞彙的第一個字元，或在詞彙內（不含） `/` 。 

> [!NOTE]  
> 規則的模式比對很慢，因此您可能會想要探索替代方法，例如，針對詞彙中的字元序列建立權杖的邊緣 n 樣式 token 化。 索引會更大，但查詢可能會執行得更快，視模式結構和您要編制索引的字串長度而定。
>

### <a name="impact-of-an-analyzer-on-wildcard-queries"></a>分析器對萬用字元查詢的影響

在查詢剖析期間，視為前置詞、尾碼、萬用字元或正則運算式的查詢會依原樣傳遞至查詢樹狀結構，並略過 [詞法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)。 只有當索引包含您的查詢所指定之格式的字串時，才會找到相符專案。 在大部分的情況下，您將需要在編制索引期間保留字元串完整性的分析器，讓部分字詞和模式比對成功。 如需詳細資訊，請參閱 [Azure 認知搜尋查詢中的部分詞彙搜尋](search-query-partial-matching.md)。

如果您想要搜尋查詢 ' terminat * ' 傳回的結果包含「終止」、「終止」和「終止」之類的詞彙，請考慮這種情況。

如果您要使用 en-us (英文 Lucene) 分析器，它會套用每個詞彙的積極詞幹分析。 例如，「終止」、「終止」、「終止」全都會 token 化到索引中的權杖 ' termi '。 另一方面，使用萬用字元或模糊搜尋的查詢中的詞彙完全不會進行分析。因此，不會有符合 ' terminat * ' 查詢的結果。

另一方面，Microsoft 分析器 (在這種情況下，就是使用詞形歸併還原，而不是詞幹) 分析。 這表示所有產生的標記都應為有效的英文字。 例如，「終止」、「終止」和「終止」大多會保留在索引中，而且對於相依于萬用字元和模糊搜尋的案例而言，是較理想的選擇。

##  <a name="scoring-wildcard-and-regex-queries"></a><a name="bkmk_searchscoreforwildcardandregexqueries"></a>萬用字元和 regex 查詢的評分

Azure 認知搜尋針對文字查詢使用以頻率為基礎的評分 ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) 。 不過，針對字詞範圍可能很廣泛的萬用字元和 regex 查詢，則會忽略頻率因素，以防止罕見字詞的相符項目誤獲較高的排名。 系統對於萬用字元和 regex 搜尋的所有相符項目，會同等視之。

## <a name="see-also"></a>請參閱

+ [簡單搜尋的查詢範例](search-query-simple-examples.md)
+ [完整 Lucene 搜尋的查詢範例](search-query-lucene-examples.md)
+ [搜尋文件](/rest/api/searchservice/Search-Documents)
+ [篩選和排序的 OData 運算式語法](query-odata-filter-orderby-syntax.md)   
+ [Azure 認知搜尋中的簡單查詢語法](query-simple-syntax.md)