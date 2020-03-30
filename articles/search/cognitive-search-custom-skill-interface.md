---
title: 自訂技能的介面定義
titleSuffix: Azure Cognitive Search
description: Azure 認知搜索中 AI 擴充管道中 Web api 自訂技能的自訂資料提取介面。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 78f5f6eda28bed164668445b5671dad92f8dedd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77500260"
---
# <a name="how-to-add-a-custom-skill-to-an-azure-cognitive-search-enrichment-pipeline"></a>如何向 Azure 認知搜索豐富管道添加自訂技能

Azure 認知搜索中的[豐富管道](cognitive-search-concept-intro.md)可以從[內置的認知技能](cognitive-search-predefined-skills.md)以及您親自創建並添加到管道的[自訂技能](cognitive-search-custom-skill-web-api.md)中組合起來。 在本文中，瞭解如何創建自訂技能，該技能公開允許將其包含在 AI 擴充管道中的介面。 

建置自訂技能可讓您插入內容獨有的轉換。 自訂技能會獨立執行，可套用在任何所需的擴充步驟。 例如，您可以定義欄位特定的自訂實體、建立自訂的分類模型以區分商務和財務合約和文件，或者新增語音辨識技能以深入觸及音訊檔案來了解相關內容。 有關分步示例，請參閱[示例：為 AI 富集創建自訂技能](cognitive-search-create-custom-skill-example.md)。

 無論您需要哪一種自訂功能，都有一個簡單且清楚的介面，可將自訂技能連接到擴充管線的其餘部分。 要包含[技能集](cognitive-search-defining-skillset.md)中的唯一需求，便是以在技能集內可整體取用的方式，接受輸入並發出輸出的能力。 本文著重於擴充管線所需的輸入和輸出格式。

## <a name="web-api-custom-skill-interface"></a>Web API 自訂技能介面

如果您未在 30 秒內傳回回應，自訂 WebAPI 技能端點便會依預設而逾時。 索引管線是同步的，且如果未在該時段內收到回應，則索引會產生逾時錯誤。  通過設置超時參數，可以將超時配置為長達 230 秒：

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 230 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT230S",
```

確保 URI 是安全的 （HTTPS）。

目前與自訂技能互動的唯一機制是透過 Web API 介面。 Web API 的需求必須符合本節所描述的需求。

### <a name="1--web-api-input-format"></a>1. Web API 輸入格式

Web API 必須接受要處理的記錄陣列。 每一筆記錄都必須包含「屬性包」，也就是提供給 Web API 的輸入。 

假設您要建立簡單的擴充程式，以識別合約文字中提及的第一個日期。 在此範例中，技能接受單一輸入 *contractText* 做為合約文字。 技能也有單一輸出，也就是合約的日期。 為了讓擴充程式更有趣，以多部分複雜類型的形式來傳回此 *contractDate*。

您的 Web API 應該準備好接收一批輸入記錄。 *values* 陣列的每個成員都代表特定記錄的輸入。 每筆記錄都必須有下列元素：

+ *recordId* 成員，此為特定記錄的唯一識別碼。 當擴充程式傳回結果時，必須提供此 *recordId*，才能讓呼叫者比對記錄結果與輸入。

+ *data* 成員，基本上是每筆記錄的輸入欄位包。

為了更具體，根據上面的範例，您的 Web API 應該會預期如下的要求：

```json
{
    "values": [
      {
        "recordId": "a1",
        "data":
           {
             "contractText": 
                "This is a contract that was issues on November 3, 2017 and that involves... "
           }
      },
      {
        "recordId": "b5",
        "data":
           {
             "contractText": 
                "In the City of Seattle, WA on February 5, 2018 there was a decision made..."
           }
      },
      {
        "recordId": "c3",
        "data":
           {
             "contractText": null
           }
      }
    ]
}
```
事實上，您的服務可能會有數百筆或數千筆記錄的呼叫，而不是只像下面所顯示的三筆記錄。

### <a name="2-web-api-output-format"></a>2. Web API 輸出格式

輸出的格式是一組包含 *recordId* 和屬性包的記錄。 

```json
{
  "values": 
  [
      {
        "recordId": "b5",
        "data" : 
        {
            "contractDate":  { "day" : 5, "month": 2, "year" : 2018 }
        }
      },
      {
        "recordId": "a1",
        "data" : {
            "contractDate": { "day" : 3, "month": 11, "year" : 2017 }                    
        }
      },
      {
        "recordId": "c3",
        "data" : 
        {
        },
        "errors": [ { "message": "contractText field required "}   ],  
        "warnings": [ {"message": "Date not found" }  ]
      }
    ]
}
```

這個特定的範例只有一個輸出，但您可以輸出多個屬性。 

### <a name="errors-and-warning"></a>錯誤和警告

如上述範例所示，您可以針對每一筆記錄傳回錯誤和警告訊息。

## <a name="consuming-custom-skills-from-skillset"></a>取用技能集的自訂技能

當您建立 Web API 擴充程式時，可以在要求中描述 HTTP 標頭和參數。 下面的程式碼片段顯示了如何將請求參數和*可選*HTTP 標頭描述為技能集定義的一部分。 HTTP 標頭不是要求，但它們允許您向技能添加其他配置功能，並從技能集定義中設置它們。

```json
{
    "skills": [
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill calls an Azure function, which in turn calls TA sentiment",
        "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/DateExtractor?language=en",
        "context": "/document",
        "httpHeaders": {
            "DateExtractor-Api-Key": "foo"
        },
        "inputs": [
          {
            "name": "contractText",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "contractDate",
            "targetName": "date"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>後續步驟

本文介紹了將自訂技能集成到技能集所需的介面要求。 按一下以下連結以瞭解有關自訂技能和技能集組合的詳細資訊。

+ [權力技能：自訂技能的寶庫](https://github.com/Azure-Samples/azure-search-power-skills)
+ [示例：為 AI 擴充創建自訂技能](cognitive-search-create-custom-skill-example.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [建立技能集 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [如何對應豐富型欄位](cognitive-search-output-field-mapping.md) (英文)
