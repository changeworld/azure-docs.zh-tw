---
title: 模式語法參考-LUIS
description: 建立實體以從 Language Understanding （LUIS）應用程式中的使用者語句，將重要資料解壓縮。 用戶端應用程式會使用已解壓縮的資料。
ms.topic: reference
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: a0139cf5ef424288c41c436fb63313494404f841
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684542"
---
# <a name="pattern-syntax"></a>模式語法

模式語法是語句的範本。 範本應該包含您想要比對的單字和實體，以及您想要忽略的單字和[標點符號](luis-reference-application-settings.md#punctuation-normalization)。 它**不是**一個規則運算式。

> [!CAUTION]
> 模式只包含機器學習實體父系，而不是子實體。

模式中的實體是用大括弧 `{}` 括住。 模式可以包含實體，以及具有角色的實體。 [Pattern。 any](luis-concept-entity-types.md#patternany-entity)是僅用於模式的實體。

模式語法支援下列語法：

|函式|語法|巢狀層級|範例|
|--|--|--|--|
|實體| {}-大括弧|2|其中的格式為 {entity-name}？|
|選用|[]-方括弧<BR><BR>任何選擇性和群組組合的嵌套層級上有3個限制 |2|問號是選擇性的 [？]|
|群組|（）-括弧|2|為（a \| b）|
|或| \|-分隔號（管線）<br><br>在一個群組中的分隔號（或）上有2個限制 |-|其中的格式為（{格式名稱-簡短} &#x7c; {格式名稱-long} &#x7c; {表單編號}）|
|語句的開始和/或結束|^-插入號|-|^ 開始語句<br>語句已完成 ^<br>具有 {number} 個實體 ^ 的整個語句之 ^ strict 常值比對|

## <a name="nesting-syntax-in-patterns"></a>在模式中嵌套語法

**選擇性**的語法（以方括弧括住）可以嵌套兩個層級。 例如： `[[this]is] a new form` 。 這個範例允許下列語句：

|Nested 選擇性語句範例|說明|
|--|--|
|這是新的表單|符合模式中的所有文字|
|是新的表單|符合模式中的外部選擇性單字和非選擇性單字|
|新表單|僅符合必要的單字|

使用括弧的**群組**語法可以嵌套兩個層級。 例如： `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )` 。 這項功能可讓三個實體中的任何一個相符。

如果 Entity1 是具有「來源」（西雅圖）和「目的地」（Cairo）等角色的位置，而「實體2」是來自清單實體（RedWest）的已知建築物名稱，則下列語句會對應到此模式：

|嵌套群組語句範例|說明|
|--|--|
|RedWest-C|符合外部群組實體|
|Seattle|符合其中一個內部群組實體|
|Cairo|符合其中一個內部群組實體|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>使用選擇性語法為群組嵌套限制

使用**選擇性**語法**分組**的組合具有3個嵌套層級的限制。

|允許|範例|
|--|--|
|是|（[（test1 &#x7c; test2）] &#x7c; test3）|
|否|（[（[test1] &#x7c; test2）] &#x7c; test3）|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>使用 or-ing 語法來嵌套群組的限制

**群組**與**或-ing**語法的組合具有2個分隔號的限制。

|允許|範例|
|--|--|
|是|（test1 &#x7c; test2 &#x7c; （test3 &#x7c; test4））|
|否|（test1 &#x7c; test2 &#x7c; test3 &#x7c; （test4 &#x7c; test5）） |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>將實體新增至模式範本的語法
若要將實體新增至模式範本，請用大括弧括住實體名稱，例如 `Who does {Employee} manage?`。

|模式與實體|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>將實體和角色新增至模式範本的語法
實體角色以 `{entity:role}` 的形式表示，其格式為實體名稱後接冒號，然後接著角色名稱。 若要將具有角色的實體新增至模式範本，請用大括弧括住實體名稱和角色名稱，例如 `Book a ticket from {Location:Origin} to {Location:Destination}`。

|模式與實體角色|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>將 Pattern.any 新增至模式範本的語法
Pattern.any 實體可讓您將變動長度的實體新增至模式。 只要遵循模式範本，Pattern.any 就可以是任意長度。

若要將 **Pattern.any** 實體新增至模式範本，請用大括弧括住 Pattern.any 實體，例如 `How much does {Booktitle} cost and what format is it available in?`。

|模式與 Pattern.any 實體|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|模式中的書名|
|--|
|How much does **steal this book** cost and what format is it available in?|
|How much does **ask** cost and what format is it available in?|
|How much does **The Curious Incident of the Dog in the Night-Time** cost and what format is it available in?|

書籍標題的單字不會對 LUIS 造成混淆，因為 LUIS 會根據模式來知道書名的結尾。任何實體。

## <a name="explicit-lists"></a>明確清單

透過撰寫 API 建立[明確清單](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8)，以便在下列情況中允許例外狀況：

* 您的模式包含[模式。](luis-concept-entity-types.md#patternany-entity)
* 而且該模式語法允許根據語句來進行不正確的實體解壓縮。

例如，假設您有一個模式，其包含選用語法 `[]` 和實體語法 `{}`，且以錯誤擷取資料的方式合併在一起。

請考慮使用模式 `[find] email about {subject} [from {person}]'。

在下列語句中，系統會正確及錯誤地擷取 **subject** 和 **person** 實體：

|語句|單位|正確擷取|
|--|--|:--:|
|email about dogs from Chris|subject=dogs<br>person=Chris|✔|
|email about the man from La Mancha|subject=the man<br>person=La Mancha|X|

在上表中，主旨應該是 `the man from La Mancha` （書籍標題），但因為主旨包含選擇性的單字，所以不 `from` 會正確預測標題。

若要修正模式的這個例外狀況，請使用[撰寫明確清單的 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8)，新增 `the man from la mancha` 作為符合 {subject} 實體的明確清單。

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>在範本語句中標記選用文字的語法
您可以使用規則運算式的方括弧語法 `[]`，在語句中標記選用文字。 選用文字最多只能在方括弧中套嵌兩個大括弧。

|模式與選用文字|意義|
|--|--|
|`[find] email about {subject} [from {person}]`|`find`和 `from {person}` 是選擇性的|
|' 可以協助我 [？]|標點符號是選擇性的|

`?`應該忽略標點符號（、 `!` 、 `.` ），而且您必須在模式中使用方括弧語法來忽略它們。

## <a name="next-steps"></a>後續步驟

深入瞭解模式：

* [如何新增模式](luis-how-to-model-intent-pattern.md)
* [如何新增模式。任何實體](luis-how-to-add-entities.md#add-a-patternany-entity)
* [模式概念](luis-concept-patterns.md)

瞭解如何在 json 回應中傳回[情感](luis-reference-prebuilt-sentiment.md)。