---
title: 快速入門：使用 Python 來呼叫文字分析 API
titleSuffix: Azure Cognitive Services
description: 此快速入門示範如何取得資訊和程式碼範例，協助您在 Azure 認知服務中快速開始使用文字分析 API。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 34d1b62ed97b966c000ff81e8f7676c30338b6a1
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876762"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>快速入門：使用 Python REST API 呼叫文字分析認知服務 
<a name="HOLTop"></a>

透過此快速入門，開始使用文字分析 REST API 和 Python 來分析語言。 本文示範如何[偵測語言](#Detect)、[分析情感](#SentimentAnalysis)、[擷取關鍵片語](#KeyPhraseExtraction)，以及[識別已連結的實體](#Entities)。

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>必要條件

* [Python 3.x](https://python.org)

* Python Requests 程式庫
    
    您可以使用此命令來安裝程式庫：

    ```console
    pip install --upgrade requests
    ```

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

在您最愛的編輯器或 IDE 中建立新的 Python 應用程式。 在您的檔案中新增下列匯入項目。

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
```

為資源的 Azure 端點和訂用帳戶金鑰建立變數。
    
```python
import os

subscription_key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```

下列各節說明如何呼叫 API 的各項功能。

<a name="Detect"></a>

## <a name="detect-languages"></a>偵測語言

將 `/text/analytics/v3.0/languages` 附加至文字分析基底端點，以形成語言偵測 URL。 例如： `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`
    
```python
language_api_url = endpoint + "/text/analytics/v3.0/languages"
```

對 API 的承載是由 `documents` 清單所組成，其為包含 `id` 和 `text`  屬性的元組。 `text` 屬性會儲存要分析的文字，而 `id` 可以是任何值。 

```python
documents = {"documents": [
    {"id": "1", "text": "This is a document written in English."},
    {"id": "2", "text": "Este es un document escrito en Español."},
    {"id": "3", "text": "这是一个用中文写的文件"}
]}
```

使用 Requests 程式庫將文件傳送給 API。 將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭，然後使用 `requests.post()` 傳送要求。 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>輸出

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "English",
                "iso6391Name": "en",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "2",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "3",
            "detectedLanguage": {
                "name": "Chinese_Simplified",
                "iso6391Name": "zh_chs",
                "confidenceScore": 1.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>分析情感

若要偵測一組文件的情感 (範圍介於正或負之間)，請將 `/text/analytics/v3.0/sentiment` 附加至文字分析基底端點，以形成語言偵測 URL。 例如： `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`
    
```python
sentiment_url = endpoint + "/text/analytics/v3.0/sentiment"
```

如同語言偵測範例，建立具有 `documents` 索引鍵的字典，而該索引鍵是由文件清單所組成。 每份文件都是一個由 `id`、要分析的 `text`，以及文字的 `language` 所組成的元組。 

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."}
]}
```

使用 Requests 程式庫將文件傳送給 API。 將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭，然後使用 `requests.post()` 傳送要求。 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>輸出

文件的情感分數會介於 0.0 和 1.0 之間，分數越高表示情感越正面。

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 102,
                    "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                }
            ],
            "warnings": []
        },
        {
            "id": "2",
            "sentiment": "negative",
            "confidenceScores": {
                "positive": 0.02,
                "neutral": 0.05,
                "negative": 0.93
            },
            "sentences": [
                {
                    "sentiment": "negative",
                    "confidenceScores": {
                        "positive": 0.02,
                        "neutral": 0.05,
                        "negative": 0.93
                    },
                    "offset": 0,
                    "length": 92,
                    "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>擷取關鍵片語
 
若要從一組文件中擷取關鍵片語，請將 `/text/analytics/v3.0/keyPhrases` 附加至文字分析基底端點，以形成語言偵測 URL。 例如： `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`
    
```python
keyphrase_url = endpoint + "/text/analytics/v3.0/keyphrases"
```

此文件集合與用於情感分析範例的集合相同。

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
    {"id": "3", "language": "en",
        "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."}
]}
```

使用 Requests 程式庫將文件傳送給 API。 將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭，然後使用 `requests.post()` 傳送要求。 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>輸出

```json
{
    "documents": [
        {
            "id": "1",
            "keyPhrases": [
                "HDR resolution",
                "new XBox",
                "clean look"
            ],
            "warnings": []
        },
        {
            "id": "2",
            "keyPhrases": [
                "Carlos",
                "notificacion",
                "algun problema",
                "telefono movil"
            ],
            "warnings": []
        },
        {
            "id": "3",
            "keyPhrases": [
                "new hotel",
                "Grand Hotel",
                "review",
                "center of Seattle",
                "classiest decor",
                "stars"
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>識別實體

若要識別文字文件中清楚的實體 (人員、地點和事物)，請將`/text/analytics/v3.0/entities/recognition/general` 附加至文字分析基底端點，以形成語言偵測 URL。 例如： `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`
    
```python
entities_url = endpoint + "/text/analytics/v3.0/entities/recognition/general"
```

如先前的範例，建立文件的集合。 

```python
documents = {"documents": [
    {"id": "1", "text": "Microsoft is an It company."}
]}
```

使用 Requests 程式庫將文件傳送給 API。 將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭，然後使用 `requests.post()` 傳送要求。

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
pprint(entities)
```

### <a name="output"></a>輸出

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "text": "Microsoft",
                    "category": "Organization",
                    "offset": 0,
                    "length": 9,
                    "confidenceScore": 0.86
                },
                {
                    "text": "IT",
                    "category": "Skill",
                    "offset": 16,
                    "length": 2,
                    "confidenceScore": 0.8
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [文字分析與 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>另請參閱 

 [文字分析概觀](../overview.md)  
 [常見問題集 (FAQ)](../text-analytics-resource-faq.md)
