---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: c180a5d751b44346760706b1bd3d1e8acb70099d
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "71837552"
---
[!INCLUDE [Prerequisites](prerequisites-nodejs.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>建立專案，並匯入所需的模組

使用您慣用的 IDE 或編輯器建立新的專案。 然後，將下列程式碼片段複製到您的專案中名為 `detect.js` 的檔案。

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> 如果您未曾使用這些模組，則必須先加以安裝，再執行您的程式。 若要安裝這些套件，請執行：`npm install request uuidv4`。

需有這些模組，才能建構 HTTP 要求，以及建立 `'X-ClientTraceId'` 標頭的唯一識別碼。

## <a name="set-the-subscription-key-and-endpoint"></a>設定訂用帳戶金鑰和端點

此範例會嘗試從這些環境變數：`TRANSLATOR_TEXT_SUBSCRIPTION_KEY` 和 `TRANSLATOR_TEXT_ENDPOINT` 中讀取您的翻譯工具文字訂用帳戶金鑰和端點。 如果您不熟悉環境變數，可以將 `subscriptionKey` 和 `endpoint` 設為字串，並為條件陳述式加上註解。

請將下列程式碼複製到您的專案中：

```javascript
var key_var = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('Please set/export the following environment variable: ' + key_var);
}
var subscriptionKey = process.env[key_var];
var endpoint_var = 'TRANSLATOR_TEXT_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('Please set/export the following environment variable: ' + endpoint_var);
}
var endpoint = process.env[endpoint_var];
```

## <a name="configure-the-request"></a>設定要求

`request()` 方法 (可透過要求模組取得) 可讓我們傳遞 HTTP 方法、URL、要求參數、標頭和 JSON 內文作為 `options` 物件。 在此程式碼片段中，我們將設定要求：

```javascript
let options = {
    method: 'POST',
    baseUrl: endpoint,
    url: 'detect',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'Salve, mondo!'
    }],
    json: true,
};
```
要驗證要求，最簡單的方式是將您的訂用帳戶金鑰以 `Ocp-Apim-Subscription-Key` 標頭的形式傳入，我們在此範例中即採用此方式。 或者，您可以將訂用帳戶金鑰替換為存取權杖，並將存取權杖連同 `Authorization` 標頭傳入，以驗證您的要求。

如果您使用認知服務多服務訂用帳戶，您也必須在要求標題中包含 `Ocp-Apim-Subscription-Region`。

如需詳細資訊，請參閱[驗證](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。

## <a name="make-the-request-and-print-the-response"></a>提出要求並列印回應

接著，我們將使用 `request()` 方法建立要求。 它會採用我們在上一節中建立的 `options` 物件作為第一個引數，然後列印美化的 JSON 回應。

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> 在此範例中，我們會在 `options` 物件中定義 HTTP 要求。 不過，要求模組也支援便利方法，例如 `.post` 和 `.get`。 如需詳細資訊，請參閱[便利方法](https://github.com/request/request#convenience-methods)。

## <a name="put-it-all-together"></a>組合在一起

如此，您就建立了一個簡單的程式，會呼叫翻譯工具文字 API 並傳回 JSON 回應。 現在，請執行您的程式：

```console
node detect.js
```

## <a name="sample-response"></a>範例回應

執行範例之後，您應該會看到下列內容列印到終端機：

> [!NOTE]
> 請在此[語言清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)中尋找國家/地區縮寫。

```json
[
    {
        "alternatives": [
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "pt",
                "score": 1.0
            },
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "en",
                "score": 1.0
            }
        ],
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "it",
        "score": 1.0
    }
]
```

## <a name="clean-up-resources"></a>清除資源

如果您已將訂用帳戶金鑰硬式編碼到您的程式中，請務必在完成本快速入門後移除訂用帳戶金鑰。

## <a name="next-steps"></a>後續步驟

查看 API 參考，以了解您可以使用翻譯工具文字 API 執行的所有作業。

> [!div class="nextstepaction"]
> [API 參考](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
