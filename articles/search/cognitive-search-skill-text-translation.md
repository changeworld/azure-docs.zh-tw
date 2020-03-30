---
title: 文本翻譯認知技能
titleSuffix: Azure Cognitive Search
description: 評估文本，並為每個記錄返回在 Azure 認知搜索中的 AI 擴充管道中翻譯成指定目的語言的文本。
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5089174fcfd5a97128c1f789b818243243a5282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460764"
---
#   <a name="text-translation-cognitive-skill"></a>文本翻譯認知技能

**文本翻譯**技能評估文本，並為每個記錄返回翻譯成指定目的語言的文本。 此技能使用認知服務中可用的[翻譯文本 API v3.0。](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate)

如果您希望文檔可能並非全部使用一種語言，則此功能非常有用，在這種情況下，您可以通過翻譯搜索索引，將文本正常化為單一語言。  它還可用於當地語系化用例，其中您可能希望使用多種語言的相同文本的副本。

[翻譯器文本 API v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)是非區域認知服務，這意味著您的資料不保證與 Azure 認知搜索或附加的認知服務資源保持在同一區域。

> [!NOTE]
> 隨著通過增加處理頻率、添加更多文檔或添加更多 AI 演算法來擴大範圍，您需要[附加計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中呼叫 API，以及在 Azure 認知搜尋的文件萃取階段中擷取影像時，都會產生費用。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 影像擷取定價的說明請見 [Azure 認知搜尋定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.TranslationSkill

## <a name="data-limits"></a>資料限制
記錄的最大大小應為 50，000 個字元（以[`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)） 如果需要在將資料發送到文本翻譯技能之前拆分資料，請考慮使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。

| 輸入                | 描述 |
|---------------------|-------------|
| 預設到語言代碼 | （必需）用於未顯式指定 to 語言的文檔，用於將文檔轉換為的語言代碼。 <br/> 請參閱[支援語言的完整清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)。 |
| 預設從語言代碼 | （可選）用於翻譯未顯式指定 from 語言的文檔的語言代碼。  如果未指定預設From語言代碼，則翻譯文本 API 提供的自動語言檢測將用於確定 from 語言。 <br/> 請參閱[支援語言的完整清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)。 |
| suggestedFrom | （可選）當未提供"語言代碼"輸入和預設From語言代碼參數且自動語言檢測不成功時，用於翻譯文檔的語言代碼。  如果未指定建議的 From 語言，則英語 （en） 將用作建議的 From 語言。 <br/> 請參閱[支援語言的完整清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)。 |

## <a name="skill-inputs"></a>技能輸入

| 輸入名稱     | 描述 |
|--------------------|-------------|
| text | 要翻譯的文本。|
| 到語言代碼    | 指示文本應轉換為的語言的字串。 如果未指定此輸入，則預設 ToTo 語言代碼將用於翻譯文本。 <br/>請參閱[支援語言的完整清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|
| 從語言代碼  | 指示文本當前語言的字串。 如果未指定此參數，則預設的 From 語言代碼（如果未提供預設的"從語言代碼"則自動語言檢測）將用於翻譯文本。 <br/>查看[支援語言的完整清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|

## <a name="skill-outputs"></a>技能輸出

| 輸出名稱    | 描述 |
|--------------------|-------------|
| translatedText | 文本翻譯的字串結果從翻譯的語言代碼到翻譯到翻譯到語言代碼。|
| 翻譯至語言代碼  | 指示文本已轉換為的語言代碼的字串。 如果要翻譯成多種語言，並希望能夠跟蹤哪個文本是哪種語言，則非常有用。|
| 翻譯自語言代碼    | 指示文本翻譯的語言代碼的字串。 如果您選擇自動語言檢測選項，則非常有用，因為此輸出將為您提供該檢測的結果。|

##  <a name="sample-definition"></a>範例定義

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
      }
    ]
  }
```

##  <a name="sample-input"></a>範例輸入

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "text": "We hold these truths to be self-evident, that all men are created equal."
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "text": "Estamos muy felices de estar con ustedes."
        }
    }
  ]
}
```


##  <a name="sample-output"></a>範例輸出

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>錯誤和警告
如果為 from 或 to 語言提供了不受支援的語言代碼，則建置錯誤，並且不會翻譯文本。
如果您的文字是空白的，則會產生警告。
如果文本大於 50，000 個字元，則僅翻譯前 50，000 個字元，併發出警告。

## <a name="see-also"></a>另請參閱

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
