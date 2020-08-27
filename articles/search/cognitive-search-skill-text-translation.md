---
title: 文字翻譯認知技能
titleSuffix: Azure Cognitive Search
description: 評估文字，並針對每一筆記錄，傳回在 Azure 認知搜尋的 AI 擴充管線中轉譯為指定目的語言的文字。
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4889ecd02be1b8f59c30550b7813ed5e5935f20f
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88924689"
---
#   <a name="text-translation-cognitive-skill"></a>文字翻譯認知技能

**文字翻譯**技能會評估文字，並針對每筆記錄傳回翻譯為指定目的語言的文字。 這項技能會使用認知服務中提供的 [翻譯工具文字 API v3.0](../cognitive-services/translator/reference/v3-0-translate.md) 。

如果您預期您的檔可能並非全部都是使用一種語言，則這項功能會很有用，在這種情況下，您可以藉由轉譯文字，將文字標準化為單一語言。  它也適用于當地語系化使用案例，您可能會想要有多個語言的相同文字副本。

[翻譯工具文字 API v3.0](../cognitive-services/translator/reference/v3-0-reference.md)是非區域認知服務，這表示您的資料不保證會保留在與您 Azure 認知搜尋或附加的認知服務資源相同的區域中。

> [!NOTE]
> 當您透過增加處理頻率、新增更多文件或新增更多 AI 演算法來擴展範圍時，您必須[連結可計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中呼叫 API，以及在 Azure 認知搜尋的文件萃取階段中擷取影像時，都會產生費用。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 影像擷取定價的說明請見 [Azure 認知搜尋定價頁面](https://azure.microsoft.com/pricing/details/search/)。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.TranslationSkill

## <a name="data-limits"></a>資料限制
記錄的大小上限應該是 50,000 個字元 (以 [`String.Length`](/dotnet/api/system.string.length) 為測量單位)。 如果您需要先分割資料，再將其傳送至文字翻譯技能，請考慮使用 [文字分割技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。

| 輸入                | 描述 |
|---------------------|-------------|
| defaultToLanguageCode |  (需要) 語言代碼，將檔轉譯為未明確指定為語言的檔。 <br/> 請參閱[支援語言的完整清單](../cognitive-services/translator/language-support.md)。 |
| defaultFromLanguageCode |  (選擇性) 用來轉譯檔的語言程式碼，而不是明確指定 from 語言的檔。  如果未指定 defaultFromLanguageCode，則會使用翻譯工具文字 API 所提供的自動語言偵測來判斷來源語言。 <br/> 請參閱[支援語言的完整清單](../cognitive-services/translator/language-support.md)。 |
| suggestedFrom |  (選擇性) 在未提供 fromLanguageCode 輸入或 defaultFromLanguageCode 參數的情況下，用來轉譯檔的語言代碼，且自動語言偵測失敗。  如果未指定 suggestedFrom 語言，則會使用英文 (en) 作為 suggestedFrom 語言。 <br/> 請參閱[支援語言的完整清單](../cognitive-services/translator/language-support.md)。 |

## <a name="skill-inputs"></a>技能輸入

| 輸入名稱     | 描述 |
|--------------------|-------------|
| text | 要轉譯的文字。|
| toLanguageCode    | 字串，表示文字應翻譯成的語言。 如果未指定此輸入，將會使用 defaultToLanguageCode 來翻譯文字。 <br/>請參閱[支援語言的完整清單](../cognitive-services/translator/language-support.md)|
| fromLanguageCode  | 字串，表示文字目前的語言。 如果未指定此參數，則 defaultFromLanguageCode 會 (或自動語言偵測（如果未提供 defaultFromLanguageCode 的話）) 將用來翻譯文字。 <br/>請參閱[支援語言的完整清單](../cognitive-services/translator/language-support.md)|

## <a name="skill-outputs"></a>技能輸出

| 輸出名稱    | 描述 |
|--------------------|-------------|
| translatedText | 從 translatedFromLanguageCode 到 translatedToLanguageCode 之文字轉譯的字串結果。|
| translatedToLanguageCode  | 字串，表示文字已翻譯成的語言代碼。 當您翻譯成多種語言，而且想要能夠追蹤哪些文字是哪種語言時，會很有用。|
| translatedFromLanguageCode    | 字串，表示翻譯文字的語言代碼。 如果您選擇自動語言偵測選項，這會很有用，因為此輸出會提供該偵測的結果。|

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
如果您為 from 或 to 語言提供不支援的語言代碼，則會產生錯誤，而且不會轉譯文字。
如果您的文字是空白的，則會產生警告。
如果您的文字大於50000個字元，則只會轉譯前50000個字元，併發出警告。

## <a name="see-also"></a>另請參閱

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)