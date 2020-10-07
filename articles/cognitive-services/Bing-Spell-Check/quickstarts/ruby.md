---
title: 快速入門：使用 REST API 和 Ruby 檢查拼字 - Bing 拼字檢查
titleSuffix: Azure Cognitive Services
description: 開始使用 Bing 拼字檢查 REST API 和 Ruby 來檢查拼字和文法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.openlocfilehash: 0b29ba1b09123784bfbac6fda4f5a1c6953f4e49
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327386"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>快速入門：使用 Bing 拼字檢查 REST API 和 Ruby 進行檢查拼字

使用本快速入門，透過 Ruby 第一次呼叫 Bing 拼字檢查 REST API。 此簡單應用程式會將要求傳送至 API 並傳回建議的修正清單。 

雖然此應用程式是以 Ruby 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb) 上找到此應用程式的原始程式碼

## <a name="prerequisites"></a>Prerequisites

* [Ruby 2.4 或更新版本](https://www.ruby-lang.org/en/downloads/) (英文)。

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在您最愛的編輯器或 IDE 中建立新的 Ruby 檔案，然後新增下列需求： 

    ```ruby
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. 針對您的訂用帳戶金鑰、端點 URI 和路徑建立變數。 您可以使用下列程式碼中的全域端點，或使用 Azure 入口網站中針對您的資源所顯示的[自訂子網域](../../../cognitive-services/cognitive-services-custom-subdomains.md)端點。 建立要求參數：

   1. 使用 `=` 運算子，將您的市場代碼指派給 `mkt` 參數。 市場代碼是您提出要求的國家/區域。 

   1. 使用 `&` 運算子新增 `mode` 參數，然後指派拼字檢查模式。 模式可以是 `proof` (攔截大部分的拼字/文法錯誤) 或 `spell` (攔截大部分的拼字檢查錯誤，但沒有多少文法錯誤)。 

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>傳送拼字檢查要求

1. 從您的主機 URI、路徑和參數字串建立 URL。 設定其查詢，以包含您想要拼字檢查的文字。

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. 使用先前建構的 URI 來建立要求。 將您的金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。

    ```ruby
    request = Net::HTTP::Post.new(uri)
    request['Content-Type'] = "application/x-www-form-urlencoded"
    request['Ocp-Apim-Subscription-Key'] = key
    ```

3. 傳送要求。

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

4. 取得 JSON 回應，並將它列印到主控台。 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
    ```

## <a name="run-the-application"></a>執行應用程式

建置並執行專案。 如果您使用命令列，請使用下列命令來執行應用程式：

   ```bash
   ruby <FILE_NAME>.rb
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
