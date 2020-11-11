---
title: 快速入門：使用 Node.js 呼叫文字分析 REST API
titleSuffix: Azure Cognitive Services
description: 此快速入門示範如何使用 Node.js 取得資訊和程式碼範例，協助您在 Azure 認知服務中快速開始使用文字分析 API。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/06/2020
ms.author: aahi
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 912434f20d00d276bde23803f4560ebd528872d9
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365120"
---
# <a name="quickstart-use-nodejs-to-call-the-text-analytics-cognitive-service"></a>快速入門：使用 Node.js 來呼叫文字分析認知服務  
<a name="HOLTop"></a>

本文示範如何使用包含 Node.JS 的[文字分析 API](//go.microsoft.com/fwlink/?LinkID=759711) 來[偵測語言種類](#Detect)、[分析情感](#SentimentAnalysis)、[擷取關鍵片語](#KeyPhraseExtraction)以及[識別已連結實體](#Entities)。

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="Detect"></a>

## <a name="detect-language"></a>偵測語言種類

語言偵測 API 會使用[偵測語言方法](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) \(英文\) 來偵測文字文件的語言。

1. 在您最愛的 IDE (或您桌面上的資料夾) 中建立新的 Node.JS 專案。
1. 將下方提供的程式碼新增至新的 `.js` 檔案。
1. 將您的金鑰和端點複製到程式碼中。 
1. 從您的 IDE 或命令列來執行程式，例如 `npm start` 或 `node detect.js`。

```javascript
'use strict';

let https = require ('https');
subscription_key = "<paste-your-text-analytics-key-here>";
endpoint = "<paste-your-text-analytics-endpoint-here>";

let path = '/text/analytics/v3.0/languages';

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let get_language = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'text': 'This is a document written in English.' },
        { 'id': '2', 'text': 'Este es un document escrito en Español.' },
        { 'id': '3', 'text': '这是一个用中文写的文件' }
    ]
};

get_language(documents);
```

**語言偵測回應**

如以下範例所示，成功的回應會以 JSON 格式來傳回： 

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

情感分析 API 會使用 [Sentiment 方法](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)，偵測一組文字記錄中的情緒態度。 藉由分析原始文字而獲得有關於正面或負面情感的線索，情感分析可用來了解客戶對您的品牌或主題有何看法。 下列範例會為兩份文件提供評分，一份是英文，另一份則是西班牙文。

1. 在您最愛的 IDE (或您桌面上的資料夾) 中建立新的 Node.JS 專案。
1. 將下方提供的程式碼新增至新的 `.js` 檔案。
1. 將您的文字分析金鑰和端點複製到程式碼中。 
1. 從您的 IDE 或命令列來執行程式，例如 `npm start` 或 `node sentiment.js`。

```javascript
'use strict';

let https = require ('https');

subscription_key = "<paste-your-text-analytics-key-here>";
endpoint = "<paste-your-text-analytics-endpoint-here>";

let path = '/text/analytics/v3.0/sentiment';

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let get_sentiments = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'language': 'en', 'text': 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
        { 'id': '2', 'language': 'es', 'text': 'Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.' },
    ]
};

get_sentiments(documents);
```

**情感分析回應**

如果評分較接近 1.0，表示結果為正面，如果評分較接近 0.0，則表示為負面。
如以下範例所示，成功的回應會以 JSON 格式來傳回：

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

關鍵片語擷取 API 會使用[關鍵片語方法](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)從文字文件擷取關鍵片語。 關鍵片語擷取可用來快速識別文件或文字的重點。 以下範例會擷取英文和西班牙文文件的關鍵片語。

1. 在您最愛的 IDE (或您桌面上的資料夾) 中建立新的 Node.JS 專案。
1. 將下方提供的程式碼新增至新的 `.js` 檔案。
1. 將您的文字分析金鑰和端點複製到程式碼中。 
1. 從您的 IDE 或命令列來執行程式，例如 `npm start` 或 `node key-phrases.js`。

```javascript
'use strict';

let https = require ('https');

subscription_key = "<paste-your-text-analytics-key-here>";
endpoint = "<paste-your-text-analytics-endpoint-here>";

let path = '/text/analytics/v3.0/keyPhrases';

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let get_key_phrases = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'language': 'en', 'text': 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
        { 'id': '2', 'language': 'es', 'text': 'Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.' },
        { 'id': '3', 'language': 'en', 'text': 'The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I\'ve ever seen.' }
    ]
};

get_key_phrases(documents);
```

**關鍵片語擷取回應**

如以下範例所示，成功的回應會以 JSON 格式來傳回： 

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

## <a name="identify-linked-entities"></a>識別已連結實體

實體 API 會使用[實體方法](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634)來識別文字文件中的已知實體。 [實體](../how-tos/text-analytics-how-to-entity-linking.md)可從像是「北美洲」的文字擷取字組，再提供此字組的類型和/或維基百科連結給您。 「北美洲」的類型為 `location`，而維基百科的連結為 `https://en.wikipedia.org/wiki/United_States`。  以下範例會識別英文文件的實體。

1. 在您最愛的 IDE (或您桌面上的資料夾) 中建立新的 Node.JS 專案。
1. 將下方提供的程式碼新增至新的 `.js` 檔案。
1. 將您的文字分析金鑰和端點複製到程式碼中
1. 從您的 IDE 或命令列來執行程式，例如 `npm start` 或 `node entities.js`。

```javascript
'use strict';

let https = require ('https');

subscription_key = "<paste-your-text-analytics-key-here>";
endpoint = "<paste-your-text-analytics-endpoint-here>";

let path = '/text/analytics/v3.0/entities/recognition/general';

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let get_entities = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'language': 'en', 'text': 'Microsoft is an It company.' }
    ]
};

get_entities(documents);
```

**實體擷取回應**

如以下範例所示，成功的回應會以 JSON 格式來傳回：

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