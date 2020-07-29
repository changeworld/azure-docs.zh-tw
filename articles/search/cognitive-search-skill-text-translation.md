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
ms.openlocfilehash: b35af58141dc46e0cc36efe009023c1bf52850e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85080071"
---
#   <a name="text-translation-cognitive-skill"></a>文字翻譯認知技能

**文字翻譯**技能會評估文字，而針對每筆記錄，會傳回轉譯為指定目的語言的文字。 這項技能使用認知服務中提供的[翻譯工具文字 API v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) 。

如果您預期檔可能並非全部都使用一種語言，這項功能就很有用，在這種情況下，您可以將文字標準化為單一語言，然後再進行搜尋的索引。  這也適用于當地語系化使用案例，您可能會想要有多種語言的相同文字複本。

[翻譯工具文字 API v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)是非區域認知服務，這表示您的資料不一定會與您的 Azure 認知搜尋或附加認知服務資源保持在相同的區域。

> [!NOTE]
> 當您透過增加處理頻率、新增更多文件或新增更多 AI 演算法來擴展範圍時，您必須[連結可計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中呼叫 API，以及在 Azure 認知搜尋的文件萃取階段中擷取影像時，都會產生費用。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 影像擷取定價的說明請見 [Azure 認知搜尋定價頁面](https://azure.microsoft.com/pricing/details/search/)。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.TranslationSkill

## <a name="data-limits"></a>資料限制
記錄的大小上限應該是 50,000 個字元 (以 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length) 為測量單位)。 如果您需要在將資料傳送到文字翻譯技能之前先將其分解，請考慮使用[文字分割技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。

| 輸入                | 描述 |
|---------------------|-------------|
| defaultToLanguageCode | 具備將檔翻譯成的語言代碼，適用于未明確指定為語言的檔。 <br/> 請參閱[支援語言的完整清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)。 |
| defaultFromLanguageCode | 選擇性針對未明確指定 from 語言的檔，用來轉譯檔的語言代碼。  如果未指定 defaultFromLanguageCode，則會使用翻譯工具文字 API 所提供的自動語言偵測來判斷 from 語言。 <br/> 請參閱[支援語言的完整清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)。 |
| suggestedFrom | 選擇性當未提供 fromLanguageCode 輸入或 defaultFromLanguageCode 參數，且自動語言偵測不成功時，用來轉譯檔的語言代碼。  如果未指定 suggestedFrom 語言，則會使用英文（en）作為 suggestedFrom 語言。 <br/> 請參閱[支援語言的完整清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)。 |

## <a name="skill-inputs"></a>技能輸入

| 輸入名稱     | 描述 |
|--------------------|-------------|
| text | 要轉譯的文字。|
| toLanguageCode    | 字串，表示文字應轉譯成的語言。 如果未指定此輸入，將會使用 defaultToLanguageCode 來轉譯文字。 <br/>請參閱[支援語言的完整清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|
| fromLanguageCode  | 字串，表示文字的目前語言。 如果未指定此參數，則會使用 defaultFromLanguageCode （如果未提供 defaultFromLanguageCode，則會自動偵測語言）將會用來轉譯文字。 <br/>請參閱[支援語言的完整清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|

## <a name="skill-outputs"></a>技能輸出

| 輸出名稱    | Description |
|--------------------|-------------|
| translatedText | 從 translatedFromLanguageCode 到 translatedToLanguageCode 之文字轉譯的字串結果。|
| translatedToLanguageCode  | 表示文字轉譯成之語言代碼的字串。 如果您要翻譯成多種語言，而且想要能夠追蹤哪些文字是哪種語言，就很有用。|
| translatedFromLanguageCode    | 表示文字轉譯來來源語言代碼的字串。 如果您選擇了自動語言偵測選項，此輸出將會提供該偵測的結果，這會很有用。|

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
