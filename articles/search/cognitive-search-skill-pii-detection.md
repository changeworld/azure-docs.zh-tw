---
title: 'PII 偵測認知技能 (預覽) '
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜尋中，從擴充管線中的文字解壓縮和遮罩個人資訊。 此技能目前為公開預覽狀態。
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: acacf617d3f1d9ab891d08b32fc2dfb14deb64a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540518"
---
# <a name="pii-detection-cognitive-skill"></a>PII 偵測認知技能

> [!IMPORTANT] 
> 此技能目前為公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 目前沒有入口網站或 .NET SDK 支援。

**PII 偵測**技能會從輸入文字中解壓縮個人資訊，並提供您遮罩的選項。 這項技能會使用認知服務中[文字分析](../cognitive-services/text-analytics/overview.md)所提供的機器學習模型。

> [!NOTE]
> 當您透過增加處理頻率、新增更多文件或新增更多 AI 演算法來擴展範圍時，您必須[連結可計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中呼叫 API，以及在 Azure 認知搜尋的文件萃取階段中擷取影像時，都會產生費用。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 影像擷取定價的說明請見 [Azure 認知搜尋定價頁面](https://azure.microsoft.com/pricing/details/search/)。

## <a name="odatatype"></a>@odata.type

Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>資料限制

記錄的大小上限應該是 50,000 個字元 (以 [`String.Length`](/dotnet/api/system.string.length) 為測量單位)。 如果您需要在將資料傳送至技能之前將資料進行區塊，請考慮使用 [文字分割技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能參數

參數會區分大小寫，而且全部都是選擇性的。

| 參數名稱     | 描述 |
|--------------------|-------------|
| `defaultLanguageCode` |    輸入文字的語言代碼。 目前僅 `en` 支援。 |
| `minimumPrecision` | 介於0.0 到1.0 之間的值。 如果輸出) 中 (的信賴分數低於 `piiEntities` 設定的 `minimumPrecision` 值，則不會傳回或遮罩該實體。 預設值為 0.0。 |
| `maskingMode` | 參數，提供各種方法來遮罩輸入文字中偵測到的個人資訊。 支援下列選項： <ul><li>`none` (預設) ：不會發生遮罩，也 `maskedText` 不會傳回輸出。 </li><li> `redact`：從輸入文字中移除偵測到的實體，而不會取代已刪除的值。 在此情況下，輸出中的位移 `piiEntities` 會相對於原始文字，而不是遮罩文字。 </li><li> `replace`：使用參數中指定的字元取代偵測到的實體 `maskingCharacter` 。 字元將會重複為所偵測到之實體的長度，因此位移會正確地對應至輸入文字和輸出 `maskedText` 。</li></ul> |
| `maskingCharacter` | 當參數設定為時，用來將文字遮罩的字元 `maskingMode` `replace` 。 以下是支援的選項： `*` (預設) 、 `#` 、 `X` 。 只有在 `null` `maskingMode` 未設定為時，這個參數才能是 `replace` 。 |

## <a name="skill-inputs"></a>技能輸入

| 輸入名稱      | 描述                   |
|---------------|-------------------------------|
| `languageCode`    | 選擇性。 預設值為 `en`。  |
| `text`          | 要分析的文字。          |

## <a name="skill-outputs"></a>技能輸出

| 輸出名稱      | 描述                   |
|---------------|-------------------------------|
| `piiEntities` | 複雜類型的陣列，包含下列欄位： <ul><li>將實際 PII (為已解壓縮的文字) </li> <li>type</li><li>亞</li><li>分數 (更高的值表示更可能是真實的實體) </li><li> (至輸入文字的位移) </li><li>長度</li></ul> </br> [您可以在這裡找到可能的類型和子類型。](../cognitive-services/text-analytics/named-entity-types.md?tabs=personal) |
| `maskedText` | 如果 `maskingMode` 設定為以外的值，則 `none` 此輸出將會是在輸入文字上執行之遮罩的字串結果（如選取的所述） `maskingMode` 。  如果 `maskingMode` 設定為 `none` ，將不會顯示此輸出。 |

## <a name="sample-definition"></a>範例定義

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

## <a name="sample-input"></a>範例輸入

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

## <a name="sample-output"></a>範例輸出

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

在這項技能的輸出中，為實體傳回的位移會直接從 [文字分析 API](../cognitive-services/text-analytics/overview.md)傳回，這表示如果您要使用它們來為原始字串編制索引，您應該使用 .net 中的 [system.globalization.stringinfo>](/dotnet/api/system.globalization.stringinfo) 類別，以便將正確的內容解壓縮。  [您可以在這裡找到更多詳細資料。](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="errors-and-warnings"></a>錯誤和警告

如果不支援檔的語言代碼，則會傳回警告，而且不會解壓縮任何實體。
如果您的文字是空的，則會傳回警告。
如果文字超過 50,000 個字元，則只會分析前 50,000 個字元，並且會發出警告。

如果技能傳回警告，輸出 `maskedText` 可能會是空的，這可能會影響任何預期輸出的下游技能。 基於這個理由，請務必調查在寫入您的技能集定義時，所有與遺漏輸出相關的警告。

## <a name="see-also"></a>另請參閱

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)