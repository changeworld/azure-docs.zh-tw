---
title: 實體辨識認知技能
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜尋中，從擴充管線中的文字解壓縮不同類型的實體。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6393c1eeaaa72d653704fcc52442bfb326dc2cdd
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472327"
---
#   <a name="entity-recognition-cognitive-skill"></a>實體辨識認知技能

**實體辨識**技能會從文字擷取實體。 這項技能會使用認知服務中[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)所提供的機器學習模型。

> [!NOTE]
> 當您透過增加處理頻率、新增更多文件或新增更多 AI 演算法來擴展範圍時，您必須[連結可計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中呼叫 API，以及在 Azure 認知搜尋的文件萃取階段中擷取影像時，都會產生費用。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 影像擷取定價的說明請見 [Azure 認知搜尋定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>資料限制
記錄的大小上限應為50000個字元，如[`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)所測量。 如果您需要先分割資料，然後再將該資料傳送至關鍵片語擷取器，請考慮使用 [文字分割技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能參數

參數區分大小寫，且均為選擇性。

| 參數名稱     | 描述 |
|--------------------|-------------|
| categories    | 應擷取的類別陣列。  可能的類別類型：`"Person"`、`"Location"`、`"Organization"`、`"Quantity"`、`"Datetime"`、`"URL"`、`"Email"`。 如果未提供任何類別，則會傳回所有類型。|
|defaultLanguageCode |  輸入文字的語言代碼。 支援下列語言： `ar, cs, da, de, en, es, fi, fr, hu, it, ja, ko, nl, no, pl, pt-BR, pt-PT, ru, sv, tr, zh-hans`。 並非所有的實體類別都支援所有語言;請參閱下面的附注。|
|minimumPrecision | 0 到 1 之間的值。 如果信賴分數（在 `namedEntities` 輸出中）低於此值，則不會傳回實體。 預設值是 0。 |
|includeTypelessEntities | 如果您想要辨識不符合目前類別的知名實體，請將設定為 `true`。 已辨識的實體會在 `entities` 複雜輸出欄位中傳回。 例如，「Windows 10」是知名實體（產品），但由於「產品」不是支援的分類，因此此實體會包含在 [實體] 輸出欄位中。 預設為 `false` |


## <a name="skill-inputs"></a>技能輸入

| 輸入名稱      | 描述                   |
|---------------|-------------------------------|
| languageCode  | 選擇性。 預設值為 `"en"`。  |
| text          | 要分析的文字。          |

## <a name="skill-outputs"></a>技能輸出

> [!NOTE]
> 並非所有的實體類別都支援所有語言。 上述語言的完整清單支援 [`"Person"`]、[`"Location"`] 和 [`"Organization"` 實體] 類別目錄類型。 只有_de_、 _en_、 _es_、 _fr_和_zh-hans_支援 `"Quantity"`、`"Datetime"`、`"URL"`和 `"Email"` 類型的解壓縮。 如需詳細資訊，請參閱[文字分析 API 的語言和區域支援](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support)。  

| 輸出名稱     | 描述                   |
|---------------|-------------------------------|
| 人員      | 字串陣列，其中每個字串代表人員名稱。 |
| 位置  | 字串陣列，其中每個字串代表位置。 |
| 組織  | 字串陣列，其中每個字串代表組織。 |
| 數量  | 字串陣列，其中每個字串代表數量。 |
| 日期時間  | 字串陣列，其中每個字串代表日期時間 (如其在文字中所示) 值。 |
| URL | 字串陣列，其中每個字串代表 URL |
| 電子郵件 | 字串陣列，其中每個字串代表電子郵件 |
| namedEntities | 複雜類型的陣列，包含下列欄位： <ul><li>category</li> <li>值（實際機構名稱）</li><li>位移 (在文字中找到的位置)</li><li>信心（較高的值表示它更是真正的實體）</li></ul> |
| 實體 | 複雜類型陣列，其中包含從文字中擷取的實體相關豐富資訊，包含下列欄位 <ul><li> 名稱 (實際實體名稱。 這代表「標準化」格式)</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (實體的維基百科頁面連結)</li><li>bingId</li><li>類型 (已辨識實體的類別)</li><li>子類型 (僅適用於特定類別，可提供實體類型的漸進式檢視)</li><li> 符合 (包含的複雜集合)<ul><li>文字 (實體的原始文字)</li><li>位移 (找到的位置)</li><li>長度 (原始實體文字的長度)</li></ul></li></ul> |

##  <a name="sample-definition"></a>範例定義

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "minimumPrecision": 0.5,
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
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
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
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
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": 0.98
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>錯誤案例
如果文件的語言程式碼不受支援，則會傳回錯誤，且不會擷取任何實體。

## <a name="see-also"></a>另請參閱

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
