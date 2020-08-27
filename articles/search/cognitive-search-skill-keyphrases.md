---
title: 關鍵片語擷取認知技能
titleSuffix: Azure Cognitive Search
description: 評估非結構化文字，並針對每一筆記錄，透過 Azure 認知搜尋中的 AI 擴充管線傳回關鍵片語清單。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 145d8265412c4efcaa2afad97feb23528a36191d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936056"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>關鍵片語擷取認知技能

**關鍵片語擷取**技能會評估非結構化的文字，並針對每一筆記錄，傳回關鍵片語的清單。 這項技能會使用認知服務中[文字分析](../cognitive-services/text-analytics/overview.md)所提供的機器學習模型。

如果您需要快速識別記錄中的主要討論要點，此功能相當有用。 例如，假設輸入文字為「The food was delicious and there were wonderful staff」，服務會傳回「food」和「wonderful staff」。

> [!NOTE]
> 當您透過增加處理頻率、新增更多文件或新增更多 AI 演算法來擴展範圍時，您必須[連結可計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中呼叫 API，以及在 Azure 認知搜尋的文件萃取階段中擷取影像時，都會產生費用。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 影像擷取定價的說明請見 [Azure 認知搜尋定價頁面](https://azure.microsoft.com/pricing/details/search/)。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>資料限制
記錄的大小上限應該是 50,000 個字元 (以 [`String.Length`](/dotnet/api/system.string.length) 為測量單位)。 如果您需要先分割資料，然後再將該資料傳送至關鍵片語擷取器，請考慮使用 [文字分割技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。

| 輸入                | 描述 |
|---------------------|-------------|
| `defaultLanguageCode` | (選用) 要套用至未明確指定語言之文件的語言代碼。  如果未指定預設語言代碼，則會使用英文 (en) 做為預設語言代碼。 <br/> 請參閱[支援語言的完整清單](../cognitive-services/text-analytics/language-support.md)。 |
| `maxKeyPhraseCount`   | (選用) 產生的關鍵片語數量上限。 |

## <a name="skill-inputs"></a>技能輸入

| 輸入  | 描述 |
|--------------------|-------------|
| `text` | 要分析的文字。|
| `languageCode`    |  此字串表示記錄的語言。 如果未指定此參數，將使用預設語言代碼來分析記錄。 <br/>請參閱[支援語言的完整清單](../cognitive-services/text-analytics/language-support.md)|

## <a name="skill-outputs"></a>技能輸出

| 輸出     | 描述 |
|--------------------|-------------|
| `keyPhrases` | 從輸入文字擷取的關鍵片語清單。 關鍵片語會依重要性順序傳回。 |


##  <a name="sample-definition"></a>範例定義

請考慮具有下欄欄位的 SQL 記錄：

```json
{
    "content": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
    "language": "en"
}
```

然後您的技能定義看起來可能像這樣：

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      {
        "name": "languageCode",
        "source": "/document/language" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
      }
    ]
  }
```

##  <a name="sample-output"></a>範例輸出

在上述範例中，您技能的輸出會寫入擴充樹狀結構中的新節點，稱為 "document/myKeyPhrases"，因為這是我們所 `targetName` 指定的。 如果您未指定，則會 `targetName` 是 "document/keyPhrases"。

#### <a name="documentmykeyphrases"></a>document/myKeyPhrases 
```json
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
```

您可以使用 "document/myKeyPhrases" 做為其他技能的輸入，或作為 [輸出欄位對應](cognitive-search-output-field-mapping.md)的來源。

## <a name="errors-and-warnings"></a>錯誤和警告
如果您提供不支援的語言代碼，則會產生錯誤，而且不會擷取關鍵片語。
如果您的文字是空白的，則會產生警告。
如果文字超過 50,000 個字元，則只會分析前 50,000 個字元，並且會發出警告。

## <a name="see-also"></a>另請參閱

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [如何定義輸出欄位對應](cognitive-search-output-field-mapping.md)