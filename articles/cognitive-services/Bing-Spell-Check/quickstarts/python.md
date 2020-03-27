---
title: 快速入門：使用 REST API 和 Python 進行拼字檢查 - Bing 拼字檢查
titleSuffix: Azure Cognitive Services
description: 透過本快速入門開始使用 Bing 拼字檢查 REST API 來檢查拼字和文法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 6b0977628f7c3d971804d8597f42425608028081
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "75448461"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>快速入門：使用 Bing 拼字檢查 REST API 和 Python 進行拼字檢查

使用本快速入門，第一次呼叫 Bing 拼字檢查 REST API。 這個簡單的 Python 應用程式會將要求傳送至 API，並傳回建議的更正清單。 雖然此應用程式是以 Python 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py) 上找到此應用程式的原始程式碼

## <a name="prerequisites"></a>Prerequisites

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>初始化應用程式

1. 在您慣用的 IDE 或編輯器中建立新的 Python 專案，以及新增下列匯入陳述式。

   ```python
   import requests
   import json
   ```

2. 為您要進行拼字檢查的文字、您的訂用帳戶金鑰和 Bing 拼字檢查端點建立變數。 您可以使用下方的全域端點，也可以使用 Azure 入口網站中針對您的資源所顯示的[自訂子網域](../../../cognitive-services/cognitive-services-custom-subdomains.md)端點。

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>建立要求的參數

1. 建立以 `text` 作為索引鍵、並以您的文字作為值的新字典。

    ```python
    data = {'text': example_text}
    ```

2. 新增要求的參數。 在 `mkt=` 之後附加您的市場代碼。 市場代碼是您提出要求的國家/地區。 另外，在 `&mode=` 之後附加拼字檢查模式。 模式是 `proof` (攔截大部分的拼字/文法錯誤) 或 `spell` (攔截大部分的拼字檢查，但沒有多少文法錯誤)。

    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. 將 `Content-Type` 標頭和您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>傳送要求並讀取回應

1. 使用要求程式庫傳送 POST 要求。

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. 取得 JSON 回應，並加以列印。

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```


## <a name="run-the-application"></a>執行應用程式

如果您使用命令列，請使用下列命令來執行應用程式。

```bash
python <FILE_NAME>.py
```

## <a name="example-json-response"></a>範例 JSON 回應

如以下範例所示，成功的回應會以 JSON 格式來傳回：

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立單頁 Web 應用程式](../tutorials/spellcheck.md)

- [什麼是 Bing 拼字檢查 API？](../overview.md)
- [Bing 拼字檢查 API v7 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
