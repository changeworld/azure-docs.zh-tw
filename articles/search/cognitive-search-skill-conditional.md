---
title: 條件式認知技能
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋中的條件式技能可篩選、建立預設值，以及合併技能集定義中的值。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b5f1fc7f877854dd06fbbe09ff82e47208fa12d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "72792036"
---
# <a name="conditional-cognitive-skill"></a>條件式認知技能

**條件**式技能可進行 Azure 認知搜尋案例，這些案例需要布林值運算來判斷要指派給輸出的資料。 這些案例包括篩選、指派預設值，以及根據條件來合併資料。

下列虛擬虛擬會示範條件式技能的完成：

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> 這項技能未系結至 Azure 認知服務 API，因此您不需支付其使用費用。 不過，您仍然應該 [附加認知服務資源](cognitive-search-attach-cognitive-services.md) 來覆寫「免費」資源選項，以限制您每天的少量擴充。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>評估的欄位

這項技能很特殊，因為它的輸入是評估欄位。

下列專案是運算式的有效值：

-   運算式中 (路徑的注釋路徑必須以 "$ (" 和 ") " 分隔 ) 
 <br/>
    範例：
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  常值 (字串、數位、true、false、null)  <br/>
    範例：
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  使用比較運算子的運算式 (= =、！ =、>=、>、<=、<)  <br/>
    範例：
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   使用布林運算子的運算式 ( # A2、| |、！、^)  <br/>
    範例：
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   使用數值運算子的運算式 (+、-、 \* 、/、% )  <br/>
    範例： 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

由於條件式技能支援評估，因此您可以在次要轉換案例中使用它。 例如，請參閱 [技能定義 4](#transformation-example)。

## <a name="skill-inputs"></a>技能輸入
輸入會區分大小寫。

| 輸入   | 描述 |
|-------------|-------------|
| condition (條件)   | 此輸入是 [評估欄位](#evaluated-fields) ，代表要評估的條件。 此條件應評估為布林值 (*true* 或 *false*) 。   <br/>  範例： <br/> "= true" <br/> "= $ (/document/language) = = ' fr '" <br/> "= $ (/document/pages/ \* /language) = = $ (/document/expectedLanguage) " <br/> |
| whenTrue    | 此輸入是 [評估欄位](#evaluated-fields) ，代表條件評估為 *true*時要傳回的值。 常數位符串應該以單引號括住 ( ' 和 ' ) 。 <br/>範例值： <br/> "= ' 合約 '"<br/>"= $ (/document/contractType) " <br/> "= $ (/document/entities/ \*) " <br/> |
| whenFalse   | 此輸入是 [評估欄位](#evaluated-fields) ，代表條件評估為 *false*時要傳回的值。 <br/>範例值： <br/> "= ' 合約 '"<br/>"= $ (/document/contractType) " <br/> "= $ (/document/entities/ \*) " <br/>

## <a name="skill-outputs"></a>技能輸出
有一個單純稱為「輸出」的輸出。 如果條件為 false，則會傳回 *whenFalse* 值，如果條件為 true，則會傳回 *whenTrue* 。

## <a name="examples"></a>範例

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>範例技能定義1：篩選檔以只傳回法文檔

下列輸出會傳回句子的陣列 ( "/document/frenchSentences" ) 如果檔的語言是法文。 如果語言不是法文，此值會設定為 *null*。

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
如果使用 "/document/frenchSentences" 做為另一項技能的 *內容* ，則只有在 "/document/frenchSentences" 未設定為 *null*時，該技能才會執行。


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>範例技能定義2：為不存在的值設定預設值

下列輸出會建立批註 ( "/document/languageWithDefault" ) 設定為檔的語言，如果未設定語言，則為 "es"。

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

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>範例技能定義3：將兩個欄位中的值合併成一個欄位

在此範例中，某些句子具有 *frenchSentiment* 屬性。 每當 *frenchSentiment* 屬性為 null 時，我們會想要使用 *englishSentiment* 值。 我們會將輸出指派給稱為 *情感* ( "/document/sentiment/*/sentiment" ) 的成員。

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

在此範例中，我們會收到介於0和1之間的 *情感* 。 我們想要將它轉換成介於-1 和1之間。 我們可以使用條件式技能來執行此次要轉換。

在此範例中，我們不會使用技能的條件式層面，因為條件一律 *為 true*。

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
某些參數會進行評估，因此您必須特別小心遵循記載的模式。 運算式的開頭必須是等號。 路徑必須以 "$ (" 和 ") " 分隔。 請務必以單引號括住字串。 這可協助評估工具區分字串和實際的路徑與運算子。 此外，請務必在運算子前後加上空格 (例如，路徑中的 "*" 表示與 [乘) ] 不同的內容。


## <a name="next-steps"></a>後續步驟

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
