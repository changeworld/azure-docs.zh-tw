---
title: 有條件的認知技能
titleSuffix: Azure Cognitive Search
description: Azure 認知搜索中的條件技能支援篩選、創建預設值和合併技能集定義中的值。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b5f1fc7f877854dd06fbbe09ff82e47208fa12d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792036"
---
# <a name="conditional-cognitive-skill"></a>有條件的認知技能

條件**技能**使 Azure 認知搜索方案需要布林操作來確定要分配給輸出的資料。 這些方案包括篩選、分配預設值以及基於條件合併資料。

以下偽代碼演示了條件技能完成的任務：

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> 此技能不綁定到 Azure 認知服務 API，並且不會收取使用它的費用。 但是，您仍應[附加認知服務資源](cognitive-search-attach-cognitive-services.md)以覆蓋"免費"資源選項，該選項將您限制在每天少量的豐富。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>已評估欄位

此技能特別，因為它的輸入是計算欄位的。

以下項是運算式的有效值：

-   注釋路徑（運算式中的路徑必須按"$（"和"）分隔）
 <br/>
    範例：
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  文本（字串、數位、真、假、空） <br/>
    範例：
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  使用比較運算子的運算式（*、！*、>=、>、<=、<） <br/>
    範例：
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   使用布林運算子的運算式（&&、*、！、*） <br/>
    範例：
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   使用數位運算子的運算式 （+、 -、 \*/、 %） <br/>
    範例： 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

由於條件技能支援評估，因此可以在次要轉換方案中使用它。 例如，請參閱[技能定義 4](#transformation-example)。

## <a name="skill-inputs"></a>技能輸入
輸入會區分大小寫。

| 輸入   | 描述 |
|-------------|-------------|
| condition (條件)   | 此輸入是表示要計算的條件[的計算欄位](#evaluated-fields)。 此條件應計算為布林值 （*真*或*假*）。   <br/>  範例： <br/> "= 真實" <br/> " $（/文檔/語言） = "fr" <br/> = $（/文檔/頁//\*語言） = $（/文檔/預期語言） <br/> |
| 當真    | 此輸入是一個[計算欄位](#evaluated-fields)，表示如果條件計算為*true*時要返回的值。 常量字串應以單引號 （'和'） 返回。 <br/>示例值： <br/> "= '合同'"<br/>"= $（/文檔/合同類型）" <br/> " $（/文檔/實體/）"\* <br/> |
| 當假   | 此輸入是一個[計算欄位](#evaluated-fields)，表示如果條件計算為*false*時要返回的值。 <br/>示例值： <br/> "= '合同'"<br/>"= $（/文檔/合同類型）" <br/> " $（/文檔/實體/）"\* <br/>

## <a name="skill-outputs"></a>技能輸出
有一個輸出，簡稱為"輸出"。 如果條件為 false 或條件為*true，則*當*False*時，它傳回值。

## <a name="examples"></a>範例

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>示例技能定義 1：篩選文檔以僅返回法語文檔

如果文檔的語言為法語，則以下輸出返回句子陣列（"/文檔/法語句"）。 如果語言不是法語，則該值設置為*null*。

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
如果"/文檔/法語句"用作其他技能的*上下文*，則該技能僅在"/文檔/法蘭西句子"未設置為*null*時運行。


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>示例技能定義 2：為不存在的值設置預設值

以下輸出創建一個注釋（"/文檔/語言"），該注釋設置為文檔的語言，如果語言未設置，則設置為"es"。

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

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>示例技能定義 3：將兩個欄位的值合併為一個欄位

在此示例中，某些句子具有*franceSentiment*屬性。 每當*franceSentiment*屬性為 null 時，我們都希望使用*英語情緒*值。 我們將輸出分配給稱為*情緒*的成員（"/文檔/情緒/*/情緒"）。

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

## <a name="transformation-example"></a>轉換示例
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>示例技能定義 4：單個欄位上的資料轉換

在此示例中，我們收到介於 0 和 1 之間的*情緒*。 我們希望將其轉換為 -1 和 1 之間。 我們可以使用條件技能執行此次要轉換。

在此示例中，我們不使用技能的條件方面，因為條件始終*為 true*。

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
評估某些參數，因此您需要特別注意遵循記錄的模式。 運算式必須以等號開頭。 路徑必須以"$（"和"）分隔。 請確保將字串放在單個引號中。 這有助於賦值器區分字串和實際路徑和運算子。 此外，請確保在運算子周圍放置空白（例如，路徑中的"*"表示與乘法不同）。


## <a name="next-steps"></a>後續步驟

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
