---
title: 語言偵測認知技能
titleSuffix: Azure Cognitive Search
description: 評估非結構化文字，並針對每一筆記錄傳回語言識別項，其分數表示在 Azure 認知搜尋的 AI 擴充管線中分析的強度。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8439788c63ec1b9feaea148ab52aba498791dc12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76045029"
---
#   <a name="language-detection-cognitive-skill"></a>語言偵測認知技能

**語言偵測**技能會偵測輸入文字的語言，並針對要求所提交的每份檔報告單一語言代碼。 語言代碼各配有一個分數，表示分析的強度。 這項技能會使用認知服務中[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)所提供的機器學習模型。

當您需要提供文字的語言作為其他技能的輸入時 (例如[情感分析技能](cognitive-search-skill-sentiment.md)或[文字分割技能](cognitive-search-skill-textsplit.md))，這項功能就特別有用。

語言偵測會利用 Bing 的自然語言處理程式庫，超過文字分析列出的[支援語言和區域](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support)數目。 不會發行確切的語言清單，但會包含所有廣泛讀出的語言，加上變種、方言，以及某些地區和文化特性語言。 如果您的內容是以較不常用的語言表示，您可以[嘗試語言偵測 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) ，以查看它是否傳回程序代碼。 對於無法偵測到的語言，會產生 `unknown` 回應。

> [!NOTE]
> 當您藉由增加處理頻率、新增更多檔或新增更多 AI 演算法來擴展範圍時，您將需要[附加可計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中呼叫 API，以及在 Azure 認知搜尋的文件萃取階段中擷取影像時，都會產生費用。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 影像擷取定價的說明請見 [Azure 認知搜尋定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>資料限制
記錄的大小上限應為50000個字元，如所測量[`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)。 如果您需要在將資料傳送到語言偵測技能之前先將其分解，您可以使用[文字分割技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-inputs"></a>技能輸入

這些參數會區分大小寫。

| 輸入     | 描述 |
|--------------------|-------------|
| text | 要分析的文字。|

## <a name="skill-outputs"></a>技能輸出

| 輸出名稱    | 描述 |
|--------------------|-------------|
| languageCode | 所識別語言的 ISO 6391 語言代碼。 例如，"en"。 |
| languageName | 語言的名稱。 例如，"English"。 |
| score | 0 到 1 之間的值。 正確識別語言的可能性。 如果句子有混合的語言，分數可能會小於 1。  |

##  <a name="sample-definition"></a>範例定義

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
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
```


##  <a name="sample-output"></a>範例輸出

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>錯誤案例
如果文字以不支援的語言表示，則會產生錯誤，而且不會傳回語言識別項。

## <a name="see-also"></a>另請參閱

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
