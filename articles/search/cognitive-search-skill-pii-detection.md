---
title: PII 檢測認知技能（預覽）
titleSuffix: Azure Cognitive Search
description: 從 Azure 認知搜索中的豐富管道中的文本中提取和遮罩個人識別資訊。 此技能當前處於公共預覽版中。
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: f21200bc6f5b25f3330f5bb87c0843caa5a84e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298888"
---
#    <a name="pii-detection-cognitive-skill"></a>PII 檢測認知技能

> [!IMPORTANT] 
> 此技能當前處於公共預覽版中。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 當前沒有門戶或 .NET SDK 支援。

**PII 檢測**技能從輸入文本中提取個人識別資訊，並允許您選擇以各種方式從該文本中遮罩這些資訊。 這項技能會使用認知服務中[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)所提供的機器學習模型。

> [!NOTE]
> 隨著通過增加處理頻率、添加更多文檔或添加更多 AI 演算法來擴大範圍，您需要[附加計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中呼叫 API，以及在 Azure 認知搜尋的文件萃取階段中擷取影像時，都會產生費用。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 影像擷取定價的說明請見 [Azure 認知搜尋定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>資料限制
記錄的最大大小應為 50，000 個字元（以[`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)） 如果需要在將資料發送到技能之前拆分資料，請考慮使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能參數

參數區分大小寫，所有參數都是可選的。

| 參數名稱     | 描述 |
|--------------------|-------------|
| defaultLanguageCode |    輸入文字的語言代碼。 目前，僅`en`支援。 |
| minimumPrecision | 介於 0.0 和 1.0 之間的值。 如果置信度分數（在`piiEntities`輸出中）低於設定`minimumPrecision`值，則不返回或遮罩實體。 預設值為 0.0。 |
| 遮蔽模式 | 提供各種方法在輸入文本中遮罩檢測到的 PII 的參數。 支援下列選項： <ul><li>`none`（預設）：這意味著不會執行任何掩蔽，`maskedText`也不會返回輸出。 </li><li> `redact`：此選項將從輸入文本中刪除檢測到的實體，而不是將其替換為任何內容。 請注意，在這種情況下，輸出中的`piiEntities`偏移量將與原始文本相關，而不是與蒙版文本相關。 </li><li> `replace`： 此選項將用`maskingCharacter`參數中給出的字元替換檢測到的實體。  字元將重複到檢測到的實體的長度，以便偏移量將正確對應于輸入文本和輸出`maskedText`。</li></ul> |
| 遮蔽字元 | 如果參數設置為，則用於遮罩文本的`maskingMode`字元。 `replace` 支援以下選項： `*` （預設）， `#`. `X` 僅當未設置為`null``maskingMode``replace`時，才能使用此參數。 |


## <a name="skill-inputs"></a>技能輸入

| 輸入名稱      | 描述                   |
|---------------|-------------------------------|
| languageCode    | 選擇性。 預設值為 `en`。  |
| text          | 要分析的文字。          |

## <a name="skill-outputs"></a>技能輸出

| 輸出名稱      | 描述                   |
|---------------|-------------------------------|
| 皮實體 | 複雜類型的陣列，包含下列欄位： <ul><li>文本（提取的實際 PII）</li> <li>type</li><li>亞</li><li>分數（值越高，意味著它更有可能是一個真正的實體）</li><li>偏移量（輸入文本中）</li><li>長度</li></ul> </br> [可在此處找到可能的類型和子類型。](https://docs.microsoft.com/azure/cognitive-services/text-analytics/named-entity-types?tabs=personal) |
| 蒙面文本 | 如果`maskingMode`設置為 以外的`none`值，則此輸出將是所選`maskingMode`文本上執行的掩蔽的字串結果。  如果`maskingMode`設置為`none`，則此輸出將不存在。 |

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

請注意，此技能輸出中實體返回的偏移量直接從[文本分析 API](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)返回，這意味著如果您使用它們索引到原始字串中，則應使用 .NET 中的[StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8)類來提取正確的內容。  [更多詳情可在此處找到。](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-and-warning-cases"></a>錯誤和警告案例
如果不支援文檔的語言代碼，則返回警告，並且不提取任何實體。
如果您的文字是空白的，則會產生警告。
如果文字超過 50,000 個字元，則只會分析前 50,000 個字元，並且會發出警告。

如果技能返回警告，則輸出`maskedText`可能為空。  這意味著，如果您希望輸出存在，以便輸入到以後的技能中，則該輸出將不能按預期工作。 編寫技能集定義時，請記住這一點。

## <a name="see-also"></a>另請參閱

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
