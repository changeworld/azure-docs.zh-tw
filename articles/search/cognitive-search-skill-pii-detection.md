---
title: PII 偵測認知技能（預覽）
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜尋中，從擴充管線中的文字解壓縮和遮罩個人識別資訊。 此技能目前為公開預覽狀態。
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: bec993c2b59aa03195b78a02668baf3f5fac6695
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85080750"
---
#    <a name="pii-detection-cognitive-skill"></a>PII 偵測認知技能

> [!IMPORTANT] 
> 此技能目前為公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 目前沒有入口網站或 .NET SDK 支援。

**PII 偵測**技能會從輸入文字中填入個人識別資訊，並讓您選擇以各種方式從該文字遮罩它。 這項技能會使用認知服務中[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)所提供的機器學習模型。

> [!NOTE]
> 當您透過增加處理頻率、新增更多文件或新增更多 AI 演算法來擴展範圍時，您必須[連結可計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中呼叫 API，以及在 Azure 認知搜尋的文件萃取階段中擷取影像時，都會產生費用。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 影像擷取定價的說明請見 [Azure 認知搜尋定價頁面](https://azure.microsoft.com/pricing/details/search/)。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>資料限制
記錄的大小上限應該是 50,000 個字元 (以 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length) 為測量單位)。 如果您需要在將資料傳送至技能之前先將其分解，請考慮使用[文字分割技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能參數

參數會區分大小寫，而且全都是選擇性的。

| 參數名稱     | 描述 |
|--------------------|-------------|
| `defaultLanguageCode` |    輸入文字的語言代碼。 目前只有才 `en` 支援。 |
| `minimumPrecision` | 介於0.0 和1.0 之間的值。 如果信賴分數（在輸出中 `piiEntities` ）低於設定 `minimumPrecision` 值，則不會傳回或遮罩該實體。 預設值為 0.0。 |
| `maskingMode` | 提供各種方式來遮罩輸入文字中偵測到之 PII 的參數。 支援下列選項： <ul><li>`none`（預設值）：這表示不會執行任何遮罩，也 `maskedText` 不會傳回輸出。 </li><li> `redact`：此選項會從輸入文字中移除偵測到的實體，而不會將其取代為任何專案。 請注意，在此情況下，輸出中的位移 `piiEntities` 會與原始文字相關，而不是遮罩文字。 </li><li> `replace`：此選項會將偵測到的實體取代為參數中指定的字元 `maskingCharacter` 。  字元將會重複為偵測到之實體的長度，因此位移會正確對應至輸入文字和輸出 `maskedText` 。</li></ul> |
| `maskingCharacter` | 如果參數設定為，將用來遮罩文字的字元 `maskingMode` `replace` 。 支援下列選項： `*` （預設）、 `#` 、 `X` 。 只有在 `null` `maskingMode` 未設定為時，這個參數才可以是 `replace` 。 |


## <a name="skill-inputs"></a>技能輸入

| 輸入名稱      | 描述                   |
|---------------|-------------------------------|
| `languageCode`    | 選擇性。 預設值為 `en`。  |
| `text`          | 要分析的文字。          |

## <a name="skill-outputs"></a>技能輸出

| 輸出名稱      | Description                   |
|---------------|-------------------------------|
| `piiEntities` | 複雜類型的陣列，包含下列欄位： <ul><li>文字（已解壓縮的實際 PII）</li> <li>類型</li><li>類型</li><li>分數（較高的值表示較可能是真正的實體）</li><li>offset （在輸入文字中）</li><li>長度</li></ul> </br> [您可以在這裡找到可能的類型和子類型。](https://docs.microsoft.com/azure/cognitive-services/text-analytics/named-entity-types?tabs=personal) |
| `maskedText` | 如果 `maskingMode` 設定為以外的值，則 `none` 此輸出將會是在輸入文字上執行之遮罩的字串結果，如選取的所述 `maskingMode` 。  如果 `maskingMode` 設定為 `none` ，則不會出現此輸出。 |

##    <a name="sample-definition"></a>範例定義

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
      }
    ]
  }
```
##    <a name="sample-input"></a>範例輸入

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
           }
      }
    ]
}
```

##    <a name="sample-output"></a>範例輸出

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

請注意，在這項技能的輸出中，針對實體傳回的位移會直接從[文字分析 API](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)傳回，這表示如果您要使用它們來編制原始字串的索引，您應該使用 .net 中的[system.globalization.stringinfo>](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8)類別，以便解壓縮正確的內容。  [您可以在這裡找到更多詳細資料。](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-and-warning-cases"></a>錯誤和警告案例
如果不支援檔的語言代碼，則會傳回警告，而且不會解壓縮任何實體。
如果您的文字是空白的，則會產生警告。
如果文字超過 50,000 個字元，則只會分析前 50,000 個字元，並且會發出警告。

如果技能傳回警告，則輸出 `maskedText` 可能是空的。  這表示，如果您預期輸出會存在，以供輸入之後的技能使用，則不會如預期般運作。 撰寫技能集定義時，請記住這一點。

## <a name="see-also"></a>另請參閱

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
