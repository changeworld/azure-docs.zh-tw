---
title: 呼叫文字分析 API
titleSuffix: Azure Cognitive Services
description: 本文說明如何呼叫 Azure 認知服務文字分析 REST API 和 Postman。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: aahi
ms.openlocfilehash: 7b035af85e250d97fb05625bf386bec8dc94a74c
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505251"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>如何呼叫文字分析 REST API

在本文中，我們會使用文字分析 REST API 和 [Postman](https://www.postman.com/downloads/) 來示範重要的概念。 API 提供數個同步和非同步端點，以使用服務的功能。 

## <a name="using-the-api-asynchronously"></a>以非同步方式使用 API

從3.1 版開始-preview. 3，文字分析 API 提供兩個非同步端點： 

* `/analyze`文字分析的端點可讓您在一個 API 呼叫中，使用多個文字分析功能來分析同一組文字檔。 在過去，若要使用多個功能，您必須為每個作業進行個別的 API 呼叫。 當您需要使用一個以上的文字分析功能來分析大型檔集時，請考慮這項功能。

* `/health`健全狀況文字分析的端點，可以從臨床檔中將相關的醫療資訊解壓縮並加上標籤。  

請參閱下表，以瞭解哪些功能可以用非同步方式使用。 請注意，只有一些功能可以從 `/analyze` 端點呼叫。 

| 功能 | 同步 | 非同步 |
|--|--|--|
| 語言偵測 | ✔ |  |
| 情感分析 | ✔ |  |
| 意見挖掘 | ✔ |  |
| 關鍵片語擷取 | ✔ | ✔* |
| 命名實體辨識 (包括 PII 和 PHI)  | ✔ | ✔* |
| 適用于 health (容器) 的文字分析 | ✔ |  |
| 適用于 health (API) 的文字分析 |  | ✔  |

`*` -透過端點以非同步方式呼叫 `/analyze` 。


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

## <a name="prerequisites"></a>Prerequisites


> [!NOTE]
> * 如果您想要使用或端點，您將需要使用標準 (S) [定價層](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) 的文字分析 `/analyze` 資源 `/health` 。

1.  首先，移至 [Azure 入口網站](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) 並建立新的文字分析資源（如果您還沒有的話）。 如果您想要使用或端點，請選擇 **標準 (S) 定價層** `/analyze` `/health` 。 `/analyze`端點包含在您的[定價層](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)中。

2.  選取您要用於端點的區域。  請注意 `/analyze` ，和 `/health` 端點僅適用于下欄區域：美國西部2、美國東部2、美國中部、北歐和西歐。

3.  建立文字分析資源，然後移至頁面左側的 [金鑰和端點] 分頁。 複製金鑰，以便稍後在呼叫 Api 時使用。 您稍後會將其新增為 `Ocp-Apim-Subscription-Key` 標頭的值。


<a name="json-schema"></a>

## <a name="api-request-format"></a>API 要求格式

#### <a name="synchronous"></a>[同步](#tab/synchronous)

針對所有同步作業，API 要求的格式都相同。 檔會以原始非結構化文字形式提交在 JSON 物件中。 不支援 XML。 JSON 架構包含以下所述的元素。

| 元素 | 有效值 | 必要？ | 使用方式 |
|---------|--------------|-----------|-------|
|`id` |資料類型是字串，但實際上文件識別碼通常是整數。 | 必要 | 系統會使用您所提供的識別碼作為輸出的結構。 語言代碼、關鍵片語及情感分數會針對要求中的每個識別碼產生。|
|`text` | 非結構化原始文字，最多5120個字元。 | 必要 | 針對語言偵測，文字可以透過任何語言表示。 針對情感分析、關鍵片語擷取及實體識別，文字必須為[支援的語言](../language-support.md)。 |
|`language` | 適用於[支援語言](../language-support.md)的 2 個字元 [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) 代碼 | 不定 | 針對情感分析、關鍵片語擷取及實體連結為必要，針對語言偵測為選擇性。 排除它將不會產生錯誤，但分析的效果會因此而減弱。 語言代碼應該對應至您提供的 `text`。 |

以下是同步文字分析端點的 API 要求範例。 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="analyze"></a>[分析](#tab/analyze)

> [!NOTE]
> 文字分析用戶端程式庫的最新發行前版本可讓您使用用戶端物件來呼叫非同步分析作業。 您可以在 GitHub 上找到範例：
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

此 `/analyze` 端點可讓您選擇要在單一 API 呼叫中使用的支援文字分析功能。 此端點目前支援：

* 關鍵片語擷取 
* 命名實體辨識 (包括 PII 和 PHI) 

| 元素 | 有效值 | 必要？ | 使用方式 |
|---------|--------------|-----------|-------|
|`displayName` | String | 選擇性 | 用來當做作業唯一識別碼的顯示名稱。|
|`analysisInput` | 包含 `documents` 下欄欄位 | 必要 | 包含您要傳送之檔的資訊。 |
|`documents` | 包含 `id` 以下的和 `text` 欄位 | 必要 | 包含所傳送之每份檔的資訊，以及檔的原始文字。 |
|`id` | String | 必要 | 您提供的識別碼是用來結構輸出。 |
|`text` | 非結構化原始文字，最多125000個字元。 | 必要 | 必須是英文語言，這是目前唯一支援的語言。 |
|`tasks` | 包含下列文字分析功能： `entityRecognitionTasks` `keyPhraseExtractionTasks` 或 `entityRecognitionPiiTasks` 。 | 必要 | 您要使用的一或多個文字分析功能。 請注意， `entityRecognitionPiiTasks` 具有 `domain` 可設定為或的選擇性參數 `pii` `phi` 。 如果未指定，系統會預設為 `pii` 。 |
|`parameters` | 包含 `model-version` 以下的和 `stringIndexType` 欄位 | 必要 | 此欄位包含在您選擇的上述功能工作中。 它們包含您想要使用之模型版本的相關資訊，以及索引類型。 |
|`model-version` | String | 必要 | 指定您想要使用的模型版本。  |
|`stringIndexType` | String | 必要 | 指定符合您程式設計環境的文字解碼器。  支援的類型為 `textElement_v8` (預設) 、 `unicodeCodePoint` 、 `utf16CodeUnit` 。 如需詳細資訊，請參閱 [文字位移文章](../concepts/text-offsets.md#offsets-in-api-version-31-preview) 。  |
|`domain` | String | 選擇性 | 只適用于做為工作的參數 `entityRecognitionPiiTasks` ，而且可以設定為 `pii` 或 `phi` 。 如果未指定，則預設為 `pii` 。  |

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}

```

#### <a name="text-analytics-for-health"></a>[健康情況的文字分析](#tab/health)

健康情況託管 API 文字分析的 API 要求格式與其容器的格式相同。 檔會以原始非結構化文字形式提交在 JSON 物件中。 不支援 XML。 JSON 架構包含以下所述的元素。  請填寫並提交 [認知服務要求表單](https://aka.ms/csgate) ，以要求存取文字分析的健康狀態公開預覽。 您將不需支付文字分析的健康情況。 

| 元素 | 有效值 | 必要？ | 使用方式 |
|---------|--------------|-----------|-------|
|`id` |資料類型是字串，但實際上文件識別碼通常是整數。 | 必要 | 系統會使用您所提供的識別碼作為輸出的結構。 |
|`text` | 非結構化原始文字，最多5120個字元。 | 必要 | 請注意，目前僅支援英文文字。 |
|`language` | 適用於[支援語言](../language-support.md)的 2 個字元 [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) 代碼 | 必要 | `en`目前僅支援。 |

以下是文字分析健康情況端點的 API 要求範例。 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> 請參閱 [資料和速率限制](../concepts/data-limits.md) 一文，以取得將資料傳送至文字分析 API 的速率和大小限制的相關資訊。


## <a name="set-up-a-request"></a>設定要求 

在 Postman (或另一個 web API 測試控管) 中，新增您想要使用之功能的端點。 使用下表來尋找適當的端點格式，並取代 `<your-text-analytics-resource>` 為您的資源端點。 例如：

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[同步](#tab/synchronous)

| 功能 | 要求類型 | 資源端點 |
|--|--|--|
| 語言偵測 | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| 情感分析 | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| 意見挖掘 | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| 關鍵片語擷取 | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| 命名實體辨識-一般 | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| 命名實體辨識-PII | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| 命名實體辨識-PHI | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="analyze"></a>[分析](#tab/analyze)

| 功能 | 要求類型 | 資源端點 |
|--|--|--|
| 提交分析作業 | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze` |
| 取得分析狀態和結果 | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>` |

#### <a name="text-analytics-for-health"></a>[健康情況的文字分析](#tab/health)

| 功能 | 要求類型 | 資源端點 |
|--|--|--|
| 提交健全狀況工作的文字分析  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs` |
| 取得作業狀態和結果 | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |
| 取消工作 | 刪除 | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |

--- 

在您擁有端點之後，請在 Postman (或另一個 web API 測試控管) ：

1. 選擇您想要使用之功能的要求類型。
2. 從上表中，貼上您想要的適當操作的端點。
3. 設定三個要求標頭：

   + `Ocp-Apim-Subscription-Key`：您的存取金鑰（從 Azure 入口網站取得）
   + `Content-Type`: application/json
   + `Accept`: application/json

    如果您使用的是 Postman，則您的要求看起來應該類似下列螢幕擷取畫面（假設有 `/keyPhrases` 端點）。
    
    ![具有端點和標頭的要求螢幕擷取畫面](../media/postman-request-keyphrase-1.png)
    
4. 針對 **主體** 格式選擇 **raw**
    
    ![具有 [body] \(主體\) 設定的要求螢幕擷取畫面](../media/postman-request-body-raw.png)

5. 以有效的格式貼上部分 JSON 檔。 使用上述 **API 要求格式** 一節中的範例，如需詳細資訊和範例，請參閱下列主題：

      + [語言偵測](text-analytics-how-to-language-detection.md)
      + [關鍵片語擷取](text-analytics-how-to-keyword-extraction.md)
      + [情感分析](text-analytics-how-to-sentiment-analysis.md)
      + [實體辨識](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>傳送要求

提交 API 要求。 如果您對同步端點進行呼叫，回應將會立即顯示為單一 JSON 檔，其中包含要求中所提供的每個檔識別碼的專案。

如果您對非同步或端點進行呼叫 `/analyze` `/health` ，請確認您收到202回應碼。 您將需要取得回應以查看結果：

1. 在 API 回應中， `Operation-Location` 從標頭尋找，以識別您傳送給 API 的作業。 
2. 為您所使用的端點建立 GET 要求。 請參閱 [上表](#set-up-a-request) 中有關端點格式的表格，並查看 [API 參考檔](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus)。 例如：

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>`

3. 將加入 `Operation-Location` 至要求。

4. 回應將會是單一 JSON 檔，其中包含要求中所提供的每個檔識別碼的專案。

請注意， `/analyze` `/health` 在上述步驟2中的 GET 要求結果，從建立作業的時間開始，有24小時的時間可供使用。  此時間會 `expirationDateTime` 以 GET 回應中的值表示。  在這段時間之後，結果會被清除，而且無法再供抓取。    

## <a name="example-api-responses"></a>範例 API 回應
 
# <a name="synchronous"></a>[同步](#tab/synchronous)

同步端點回應會依您使用的端點而有所不同。 請參閱下列文章以取得範例回應。

+ [語言偵測](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [關鍵片語擷取](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [情感分析](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [實體辨識](text-analytics-how-to-entity-linking.md#view-results)

# <a name="analyze"></a>[分析](#tab/analyze)

如果成功，對端點的 GET 要求 `/analyze` 會傳回包含所指派工作的物件。 例如 `keyPhraseExtractionTasks` 。 這些工作包含來自適當文字分析功能的回應物件。 如需詳細資訊，請參閱下列文章。

+ [關鍵片語擷取](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [實體辨識](text-analytics-how-to-entity-linking.md#view-results)


```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```

# <a name="text-analytics-for-health"></a>[健康情況的文字分析](#tab/health)

請參閱下列文章，以取得健全狀況非同步 API 回應的文字分析詳細資訊：

+ [健康情況的文字分析](text-analytics-for-health.md#hosted-asynchronous-web-api-response)


--- 

## <a name="see-also"></a>另請參閱

* [文字分析概觀](../overview.md)
* [常見問題集 (FAQ)](../text-analytics-resource-faq.md)</br>
* [文字分析產品頁面](//go.microsoft.com/fwlink/?LinkID=759712)
* [使用文字分析用戶端程式庫](../quickstarts/client-libraries-rest-api.md)
* [新功能](../whats-new.md)
