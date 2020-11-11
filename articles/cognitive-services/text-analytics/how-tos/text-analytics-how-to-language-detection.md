---
title: 使用文字分析 REST API 來偵測語言
titleSuffix: Azure Cognitive Services
description: 使用 Azure 認知服務中的文字分析 REST API 來偵測語言。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 10/16/2020
ms.author: aahi
ms.openlocfilehash: a3ef198c79683786a7d6fb36a46cd0c989071c9e
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93346888"
---
# <a name="example-detect-language-with-text-analytics"></a>範例：使用文字分析來偵測語言

此 API 的[語言偵測](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)功能會針對每份文件評估文字輸入，並傳回語言識別碼，其中含有指出分析強度的分數。

此功能對於收集任意文字且語言為未知的內容存放區而言非常實用。 您可以剖析此分析的結果，以判斷輸入文件中使用的是哪一種語言。 回應也會傳回分數來反映對模型的信賴度。 該分數值介於 0 到 1之間。

「語言偵測」功能適用於多種不同的語言、變體、方言，以及某些區域性/文化語言。 此功能的確切語言清單不會發行。

如果您有以較不常用的語言表示的內容，您可以嘗試使用「語言偵測」功能，看它是否會傳回代碼。 對於無法偵測到的語言，會產生 `unknown` 回應。

> [!TIP]
> 文字分析也會提供可用來偵測語言的 Linux 型 Docker 容器映像，好讓您可以在接近資料的位置[安裝和執行文字分析容器](text-analytics-how-to-install-containers.md)。

## <a name="preparation"></a>準備

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

您必須具有此格式的 JSON 文件：識別碼和文字。

每份文件的大小必須低於 5,120 個字元。 每個集合最多可以有 1,000 個項目 (識別碼)。 集合會在要求本文中提交。 下列範例是您可能提交來進行語言偵測的內容範例：

```json
{
    "documents": [
        {
            "id": "1",
            "text": "This document is in English."
        },
        {
            "id": "2",
            "text": "Este documento está en inglés."
        },
        {
            "id": "3",
            "text": "Ce document est en anglais."
        },
        {
            "id": "4",
            "text": "本文件为英文"
        },
        {
            "id": "5",
            "text": "Этот документ на английском языке."
        }
    ]
}
```

## <a name="step-1-structure-the-request"></a>步驟 1:建立要求結構

如需有關要求定義的詳細資訊，請參閱[呼叫文字分析 API](text-analytics-how-to-call-api.md)。 為了方便起見，我們將重申下列各點：

+ 建立 POST 要求。 若要檢閱適用於此要求的 API 文件，請參閱[語言偵測 API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) \(英文\)。

+ 設定適用於語言偵測的 HTTP 端點。 使用 Azure 文字分析資源或具現化的[文字分析容器](text-analytics-how-to-install-containers.md)。 您必須在 URL 中納入 `/text/analytics/v3.0/languages`。 例如： `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages` 。

+ 設定要求標頭以包含文字分析作業的[存取金鑰](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。

+ 在要求主體中，提供您準備用於此分析的 JSON 文件集合。

> [!Tip]
> 使用 [Postman](text-analytics-how-to-call-api.md) 或開啟 [文件](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) \(英文\) 中的 **API 測試主控台** 來建立要求結構，並將它 POST 到服務。

## <a name="step-2-post-the-request"></a>步驟 2:張貼要求

分析會在接收要求時執行。 如需您每分鐘和每秒鐘可以傳送的要求大小和數量資訊，請參閱概觀中的[資料限制](../overview.md#data-limits)一節。

請記得，服務是無狀態的。 您的帳戶中並不會儲存任何資料。 結果會在回應中立即傳回。


## <a name="step-3-view-the-results"></a>步驟 3：View the results

所有的 POST 要求都會傳回 JSON 格式的回應，包含識別碼和偵測到的屬性。

輸出會立即傳回。 您可以將結果串流至接受 JSON 的應用程式，或將輸出儲存到本機系統上的檔案。 然後，將輸出匯入應用程式，以便用來排序、搜尋和操作資料。

範例要求的結果看起來應該類似下列 JSON。 請注意，它是一份包含多個項目的文件。 輸出的語言是英文。 語言識別項包括易記名稱和 [ISO 639-1](https://www.iso.org/standard/22109.html) \(英文\) 格式的語言代碼。

1\.0 的正面分數表示最高的分析信賴等級。

```json
{
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.99,
                "iso6391Name":"en",
                "name":"English"
            },
            "id":"1",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"es",
                "name":"Spanish"
            },
            "id":"2",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"fr",
                "name":"French"
            },
            "id":"3",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"zh_chs",
                "name":"Chinese_Simplified"
            },
            "id":"4",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"ru",
                "name":"Russian"
            },
            "id":"5",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

### <a name="ambiguous-content"></a>模稜兩可的內容

在某些情況下，可能難以根據輸入來區分語言。 您可以使用 `countryHint` 參數來指定 2 個字母的國碼/區域碼。 根據預設，API 會使用 "US" 作為預設 countryHint，若要移除此行為，您可以將此值設定為空字串 `countryHint = ""`，以重設此參數。

例如，"Impossible" 是英文和法文通用的詞彙，如果提供上下文有限的話，回應將會以 "US" 國家/區域的提示為準。 如果文字的來源已知是來自法國，這一點也可作為一項提示。

**輸入**

```json
{
    "documents": [
        {
            "id": "1",
            "text": "impossible"
        },
        {
            "id": "2",
            "text": "impossible",
            "countryHint": "fr"
        }
    ]
}
```

服務現已有更多上下文可做出更好的判斷： 

**輸出**

```json
{
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.62,
                "iso6391Name":"en",
                "name":"English"
            },
            "id":"1",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"fr",
                "name":"French"
            },
            "id":"2",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

如果分析器無法剖析輸入，它會傳回 `(Unknown)`。 例如，如果您提交的文字區塊僅包含阿拉伯數字。

```json
{
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.0,
                "iso6391Name":"(Unknown)",
                "name":"(Unknown)"
            },
            "id":"1",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

### <a name="mixed-language-content"></a>混合式語言內容

相同文件中的混合式語言內容會傳回佔據最多內容的語言，但其正面評等將會較低。 評等會反映評量的臨界強度。 在下列範例中，輸入會混合英文、西班牙文和法文。 分析器會計算每個區段中的字元來判斷出主要的語言。

**輸入**

```json
{
    "documents": [
        {
            "id": "1",
            "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
        }
    ]
}
```

**輸出**

結果輸出由主要語言組成，其分數小於 1.0，這表示信賴度較低。

```json
{
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.94,
                "iso6391Name":"es",
                "name":"Spanish"
            },
            "id":"1",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

## <a name="summary"></a>摘要

在此文章中，您已了解使用 Azure 認知服務中的文字分析進行語言偵測的概念與工作流程。 已說明及示範下列幾點：

+ [語言偵測](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)適用於多種不同的語言、變體、方言，以及某些區域性或文化語言。
+ 要求主體中的 JSON 文件包含識別碼和文字。
+ 使用對您訂用帳戶有效的個人化[存取金鑰和端點](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)，將要求 POST 到 `/languages` 端點。
+ 回應輸出包含每個文件識別碼的語言識別碼。 輸出可以串流處理到任何接受 JSON 的應用程式。 範例應用程式包括 Excel 與 Power BI 等等。

## <a name="see-also"></a>另請參閱

* [文字分析概觀](../overview.md)
* [使用文字分析用戶端程式庫](../quickstarts/text-analytics-sdk.md)
* [新功能](../whats-new.md)
