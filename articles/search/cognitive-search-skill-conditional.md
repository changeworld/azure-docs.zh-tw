---
title: 條件式認知技能
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋中的條件式技能可讓您篩選、建立預設值，以及合併技能集定義中的值。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b5f1fc7f877854dd06fbbe09ff82e47208fa12d0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "72792036"
---
# <a name="conditional-cognitive-skill"></a>條件式認知技能

**條件**式技能可讓需要布耳運算的 Azure 認知搜尋案例，判斷要指派給輸出的資料。 這些案例包括篩選、指派預設值，以及根據條件來合併資料。

下列虛擬代碼示範條件式技能的完成：

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> 這項技能不會系結至 Azure 認知服務 API，您不需支付使用它的費用。 不過，您仍然應該[附加認知服務資源](cognitive-search-attach-cognitive-services.md)來覆寫「免費」資源選項，這會限制您每天的少量擴充。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>評估的欄位

這項技能特別特殊，因為它的輸入是已評估的欄位。

下列專案是運算式的有效值：

-   注釋路徑（運算式中的路徑必須以 "$ （" 和 "）" 分隔）
 <br/>
    範例：
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  常值（字串、數位、true、false、null） <br/>
    範例：
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  使用比較運算子（= =、！ =、>=、>、<=、<）的運算式 <br/>
    範例：
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   使用布林運算子的運算式（&&、| |、！、^） <br/>
    範例：
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   使用數值運算子（+、-、 \*、/、%）的運算式 <br/>
    範例： 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

因為條件式技能支援評估，所以您可以在次要轉換案例中使用它。 例如，請參閱[技能定義 4](#transformation-example)。

## <a name="skill-inputs"></a>技能輸入
輸入會區分大小寫。

| 輸入   | 描述 |
|-------------|-------------|
| condition (條件)   | 此輸入是一個[評估的欄位](#evaluated-fields)，代表要評估的條件。 此條件應評估為布林值（*true*或*false*）。   <br/>  範例： <br/> "= true" <br/> "= $ （/document/language） = = ' fr '" <br/> "= $ （/document/pages/\*/language） = = $ （/document/expectedLanguage）" <br/> |
| whenTrue    | 如果條件評估為*true*，則此輸入是一個已[評估的欄位](#evaluated-fields)，代表要傳回的值。 常數位符串應該以單引號（' 和 '）來傳回。 <br/>範例值： <br/> "= ' 合約 '"<br/>"= $ （/document/contractType）" <br/> "= $ （/document/entities/\*）" <br/> |
| whenFalse   | 如果條件評估為*false*，則此輸入是一個[評估的欄位](#evaluated-fields)，代表要傳回的值。 <br/>範例值： <br/> "= ' 合約 '"<br/>"= $ （/document/contractType）" <br/> "= $ （/document/entities/\*）" <br/>

## <a name="skill-outputs"></a>技能輸出
有一個簡單的輸出，就是所謂的「輸出」。 如果條件為 false，則會傳回*whenFalse*值，如果條件為 true，則傳回*whenTrue* 。

## <a name="examples"></a>範例

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>範例技能定義1：篩選檔以只傳回法文檔

如果檔的語言為法文，下列輸出會傳回句子的陣列（"/document/frenchSentences"）。 如果語言不是法文，此值會設定為*null*。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
如果使用 "/document/frenchSentences" 做為另一項技能的*內容*，只有在 "/document/frenchSentences" 未設定為*null*時，才會執行該技能。


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>範例技能定義2：為不存在的值設定預設值

下列輸出會建立設定為檔語言的注釋（"/document/languageWithDefault"），如果未設定語言，則為 "es"。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>範例技能定義3：將兩個欄位中的值合併成一個

在此範例中，有些句子具有*frenchSentiment*屬性。 每當*frenchSentiment*屬性為 null 時，我們會想要使用*englishSentiment*值。 我們會將輸出指派給名為*情感*（"/document/sentiment/*/sentiment"）的成員。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>轉換範例
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>範例技能定義4：單一欄位上的資料轉換

在此範例中，我們會收到介於0到1之間的*情感*。 我們想要將它轉換為介於-1 和1之間。 我們可以使用條件式技能來執行此次要轉換。

在此範例中，我們不會使用技能的條件式層面，因為條件一律*為 true*。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>特殊考量
某些參數會進行評估，因此您必須特別小心遵循記載的模式。 運算式的開頭必須是等號。 路徑必須以 "$ （" 和 "）" 分隔。 請務必以單引號括住字串。 這可協助評估工具區分字串和實際的路徑和運算子。 此外，請務必在運算子前後加上空白字元（例如，路徑中的 "*" 表示與乘法不同的部分）。


## <a name="next-steps"></a>後續步驟

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
